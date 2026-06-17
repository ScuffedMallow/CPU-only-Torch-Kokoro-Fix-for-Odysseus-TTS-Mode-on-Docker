# CPU-only-Torch-Kokoro-Fix-for-Odysseus-TTS-Mode-on-Docker
<img width="3412" height="907" alt="Screenshot 2026-06-10 031136" src="https://github.com/user-attachments/assets/44c8b4bf-b58e-43b7-873e-adfc6ee0701a" />

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

17 navigate to /Users/$USER/odysseus/src/ and open the settings.py with a text editor and change the values to 
	"tts_enabled": true,
  	"tts_provider": "local",
  	"tts_model": "kokoro",
  	"tts_voice": "af_heart",
  	"tts_speed": "1"
	"stt_enabled": True,
    "stt_provider": "local",
    "stt_model": "base",
    "stt_language": "en",

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

After installing torch and kokoro with pip on the Odysseus-1 container of the stack in the "exec" tab

<img width="3421" height="603" alt="Screenshot 2026-06-10 030448" src="https://github.com/user-attachments/assets/d72ec39a-fa00-4c1e-8cec-fc8ac1a65373" />
<img width="3426" height="688" alt="Screenshot 2026-06-10 030552" src="https://github.com/user-attachments/assets/5508853b-0448-4f05-96e4-94e7dc46a9ae" />

MANUALLY CHECK THE settings VALUES AT 2 different "/app/src/settings.py" and "/app/data/settings.json" locations FILES IN THE DOCKER FILE SYSTEM

<img width="3098" height="1250" alt="Screenshot 2026-06-10 024546" src="https://github.com/user-attachments/assets/91a8af2a-6323-4f77-accb-4701fe056071" />
<img width="3126" height="1365" alt="Screenshot 2026-06-10 024903" src="https://github.com/user-attachments/assets/53614838-bcff-4e46-9bc4-ea70cb7c1243" />



and make sure the tts_service content matches the content of tts_service.DOCKERPREBUILD.py

<img width="3112" height="1361" alt="Screenshot 2026-06-10 025425" src="https://github.com/user-attachments/assets/9a9db3d9-3a81-4e1b-b78d-28c35250272c" />
If you find the values to not be correct, you can try to set them manually by editing the files in the docker file system or using nano in native linux.
<img width="3412" height="907" alt="Screenshot 2026-06-10 031136" src="https://github.com/user-attachments/assets/44c8b4bf-b58e-43b7-873e-adfc6ee0701a" />

This patch is to run tts on pi/computers/containers without internal access to a gpu (CUDA compatible availability with CUDA or ROCM needed for the unpatched tts_service)
You have to run LLMs on a different server than the internal cookbook llama.cpp provided with Odysseus. (lmstudio on the localhost or a different machine entirely)
You need at least 8gb RAM to host the agent stack with CPU-torch for kokoro and STT with faster-whisper "small" model.

