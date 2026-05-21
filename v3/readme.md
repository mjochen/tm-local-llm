## An Elegant Alternative: The Entrypoint Wrapper Script

If you *don't* want the model to bloating their built image size (which makes the image over 1 GB to transfer or store), you can teach them the **Runtime Initialization Pattern** instead.

Instead of downloading during `docker build`, they use a standard `entrypoint.sh` script that runs every time the container boots up. It checks the volume, downloads the model if it's missing, and then hands control over to Ollama.

### 1. The Entrypoint Script (`entrypoint.sh`)

```bash
#!/bin/sh

# Start Ollama in the background
ollama serve &

# Wait for Ollama to respond
echo "Waiting for Ollama server to wake up..."
while ! ollama list > /dev/null 2>&1; do
  sleep 1
done

# Check if our target model directory exists in the volume
if ! ollama list | grep -q "llama3.2:1b"; then
  echo "Model not found in volume. Downloading..."
  ollama pull llama3.2:1b
else
  echo "Model already exists in volume. Skipping download."
fi

# Bring Ollama back to the foreground so the container stays alive
wait

```

### 2. The Clean `Dockerfile`

```dockerfile
FROM ollama/ollama:latest

COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

ENTRYPOINT ["/entrypoint.sh"]

```

### Why this alternative is fantastic for students:

* **The Image stays tiny:** The built Docker image is only a few megabytes larger than the base Ollama image because it contains no model data.
* **The Volume does its job:** The model is downloaded directly into the mounted volume on the very first boot.
* **Fast Subsequent Boots:** On the second `docker compose up`, the script sees the model in the volume, skips the download entirely, and boots in under two seconds.

Which of these two philosophies feels like a better fit for your grading structure: having them build an independent, self-contained "heavy" image, or teaching them clean volume persistence with a startup script?