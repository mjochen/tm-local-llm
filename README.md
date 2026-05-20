# tm-local-llm

Setup for running a local LLM.

The goal is to get a working LLM that will help us program C#-code and will avoid giving code but help understanding the programming steps.

## GPU?

The BIG QUESTION: can I use a GPU in docker? Well, supposing you have an Nvidia-GPU in your laptop, it all depends on your operating system:

* Ubuntu (or another linux), docker natively installed:
    * Cleanest, most stable setup with the lowest overhead. Install the correct drivers and pass the GPU to the container. Works perfectly...
* Windows, docker installed using WSL:
    * A good compromise. Microsoft and NVIDIA built a highly optimized driver-forwarding layer specifically for WSL2. The Windows host driver natively projects CUDA capabilities straight into the WSL2 Moby VM without any complex manual PCI passthrough.
* Windows, docker installed in Linux-VM in VirtualBox or Hyper-V:
    * Nope. GPU passthrough to a VM in Windows, even in Hyper-V installed on server 2025 it's tricky, on Windows 11 it's impossible.

All this is when you have an NVIDIA-GPU in your laptop. If you have an AMD GPU it's possible but less straightforward than with an NVIDIA, if you are using integrated graphics GPU passthrough is off the table, even when linux is your main OS (because there is no GPU to pass through).

So the bottomline: in this course we'll be using small LLM's and do inference using CPU. If you have GPU in your laptop, or even better, if you have a gaming-PC at home that you can repurpose to become an LLM-host, the commands are mostly the same but you have to add "--gpus all" to some of them.

## The model -> June 2026. Probably different by January 2027.

That being said, we need to choose a model. We are choosing a model based on these specs:

* No GPU
* 8GB Ram

The CPU is really any CPU made in the last 5 years. Within these specs we could use...

| Model | Disk Size | Approx. CPU Speed | Best For |
| --- | --- | --- | --- |
| **Qwen 2.5:0.5b** (or Qwen 3) | ~350 MB | ~35 tokens/sec ⚡ | **Ultimate speed.** Instant deployment, tiny footprint. Perfect if the model is just a "dummy backend" to test DevOps pipelines. |
| **Llama 3.2:1b** (or Gemma 3:1b) | ~800 MB | ~18 tokens/sec 🚀 | **The Sweet Spot.** Fits comfortably into the RAM headroom of an 8GB laptop while giving a much faster response than a 2B model. |
| **Gemma 2:2b** | ~1.6 GB | ~10 tokens/sec 🐢 | **Balanced assistant.** Great code-generation and conversational quality for its size, but notably slower on mid-range student laptops. |

We'll be installing Llama 3.2:1b. It's small and will give a response that makes sense. If you have the specs feel free to upgrade your model (once again, all the commands remain the same). So don't despair if your laptop is on the lower end of the spectrum; this course is being written in a VM with 8GB ram, using no GPU and 8 virtual cores.