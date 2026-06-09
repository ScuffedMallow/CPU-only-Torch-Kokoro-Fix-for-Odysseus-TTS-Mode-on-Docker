# CPU-only-Torch-Kokoro-Fix-for-Odysseus-TTS-Mode-on-Docker

Odysseus Recommends Docker.

https://github.com/pewdiepie-archdaemon/odysseus/discussions/3691

How to install Odysseus with Docker Desktop on Windows and Workaround issues with GPU Visibility by USING lmstudio(windows) and patching the TTS implementation with a cpu-only "torch" version of tts_service.py.

https://github.com/pewdiepie-archdaemon/odysseus/discussions/3691

This fix removes the mentions of a CUDA accessible device in the torch implementation of the tts_service and provides users with a guide to install dependencies needed to workaround the CUDA first style of implementation in kokoro. 

https://github.com/pewdiepie-archdaemon/ 



Here are the steps to achieve container-side text-to-speech on docker desktop without CUDA or ROCM:

1 install git

2 install wsl (windows)

3 install docker desktop

4 open powershell(windows) admin mode
5 [cd /Users/$USER] to make the installation accessible
6 [git clone https://github.com/pewdiepie-archdaemon/odysseus.git]
7 [cd odysseus]
8 [cp .env.example .env]

9 Put the "services" folder into your odysseus folder, then navigate to "/Users/$USER/services/tts/"  to find "tts_service.py" delete it and replace it by renaming tts_service.DOCKERPREBUILD.py to tts_service.py.

10 [docker compose build --build-arg INSTALL_OPTIONAL=true]
11 [docker compose up -d]

12 go into docker desktop, go to the Containers tab, select the odysseus-1 container.

13 open the "exec" terminal and paste [pip install torch --index-url https://download.pytorch.org/whl/cpu]
14 restart the container

15 open the "exec" terminal and paste [pip install kokoro soundfile]
16 restart the container

17 navigate to /Users/$USER/odysseus/data/ and open the settings.json with a text editor and change the values to 
	"tts_enabled": true,
  	"tts_provider": "local",
  	"tts_model": "kokoro",
  	"tts_voice": "af_heart",
  	"tts_speed": "1",

*Troubleshooting* :

Once you followed the steps above, even for linux,

18 You have to restart the container to complete the install of kokoro.

19 Trigger the tts_service by prompting any model while TTS Mode is On.

20 go to the container logs you should see an error message saying the language en-core-... was not found (in the container logs, if you dont see it search the logs for tts mentions). When you see that error odysseus will download the missing files on its own.
21 Leave it alone for 5 mins.

22 Restart the container and toggle tts mode on in the browser.

**IF DONE IN THAT ORDER IT SHOULD NOW BE WORKING** 
	Ask a model something so its response triggers the tts service.


*It should be working after 2 restarts on its own if you try to use it normally.*

** 	Restarting the odysseus-1 container is very important to update the environment and app with the correct information so the next step in the installation can call the right foundation when installing kokoro. Failure to follow the restarting schedule can lead to a corrupted installation. 



If you have tried everything and it is not working:

Delete the whole thing(containers, images, builds, volumes and the "odysseus" folder)

Start fresh from step 4 [git clone https://github.com/pewdiepie-archdaemon/odysseus.git] **


Message from this vdmkenny on github
           ** I made a tutorial on how to workaround gpu visibilty issues(for docker installs with CPU-TTS) and using amd gpus with kv cache quantization from windows for LLM serving #3691

        I just got a couple of notifications of you posting this on a number of different issues identically, please don't spam.

    im just trying to raise awareness for a workaround. its a common issue. the fix for common folks so its easier to find the fix while team pewds runs down the Pull Requests

Awareness is different from looking up every GPU related issue and spamming the same comment. Be warned.
It creates more noise for me (part of team Pewds). **
Thats the issue the patch is for! I am just trying to help people with limited compute/compatibility issues (raspberry and normal computers without 5090s) run this odysseus without special knowledge with tts enabled on CPU with a docker installation.
