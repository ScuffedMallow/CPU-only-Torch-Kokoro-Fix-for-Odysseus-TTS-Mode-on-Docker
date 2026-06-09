# CPU-only-Torch-Kokoro-Fix-for-Odysseus-TTS-Mode-on-Docker

Odysseus Recommends Docker. 
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

Troubleshooting* :

Once you followed the steps above, even for linux,

18 You have to restart the container to complete the install of kokoro.

19 Trigger the tts_service by prompting any model while TTS Mode is On.

20 go to the container logs you should see an error message saying the language en-core-... was not found (in the container logs, if you dont see it search the logs for tts mentions). When you see that error odysseus will download the missing files on its own.
21 Leave it alone for 5 mins.

22 Restart the container and toggle tts mode on in the browser.

IF DONE IN THAT ORDER IT SHOULD NOW BE WORKING** 
	Ask a model something so its response triggers the tts service.


*It should be working after 2 restarts on its own if you try to use it with normally.*

**The whole thing about restarting the container is very important because it update the environment with the correct variables so the next install can call the right variables when it installs. failure to follow the restarting schedule will lead to corrupted installation. That is when you want to delete the whole thing and start fresh from the git clone https://github.com/pewdiepie-archdaemon/odysseus.git step**
