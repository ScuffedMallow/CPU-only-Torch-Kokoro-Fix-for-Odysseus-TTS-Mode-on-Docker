# CPU-only-Torch-Kokoro-Fix-for-Odysseus-TTS-Mode-on-Docker
Odysseus Recommends Docker. This fix removes the mentions of a CUDA accessible device in the torch implementation of the tts_service and provides users with a guide to install dependencies needed to workaround the CUDA first style of implementation in kokoro. https://github.com/pewdiepie-archdaemon/ 
