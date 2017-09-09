#Google Assistant Running on a Raspberry Pi Zero W

## Why?
Because it's cheap and I like hacking around to save money to use awesome tech ;)

## What you need
- Raspberry Pi Zero
- Micro SD Card with at least 8gb
- Micro USB Power cable
- Mini HDMI to HDMI
- USB Microphone
- HDMI/USB speaker
- A screen

## Instructions

1. Get the Raspbian OS from the Raspbeery Pi website. Use the beginner's guide to install Raspbian on your SD card. Install the Raspbian Pi.
2. Setting up wifi. Doing this on the terminal
  - `cd /etc` to go to the etc directory where most Linux settings are there
  - `cd network` to go into the network folder
  - `sudo nano interfaces` or `sudo vi interfaces` to edit the network system file
  - Edit the following lines
    - Delete the line `iface eth0 inet manual` as there is no ethernet port for the Raspberry Pi Zero W
    - Change the line `iface wlan0 inet manual` to `iface wlan0 inet dhcp` so that the router can auto assign an IP address to the Raspberry Pi Zero
    - Delete the xpa-conf line below the line you have modified because the extra configuration is not needed
    - Delete the lines related to the `wlan1` because the Raspberry Pi has only one wifi card onboard.
    - Add a tabbed line below the wlan0 `wpa-ssid "<your wifi name>"` where `<your wifi name` is the network's name you want to connect to
    - Add a tabbed line below the wpa-ssid `wpa-psk "<the wifi password>"` where `<the wifi password>` is the wifi password for the network
    - Save by Ctrl-X and Y to save the file and exit. Then reboot the Raspberry Pi by typing `reboot` in the terminal.
3. `sudo apt-get update` to update your packages on Linux.
4. There is a known issue with the Raspberry Pi Zero W with not working with USB hubs. To change this we need to `cd /boot` and `sudo nano config.txt`. We then need to add a new line to the end of this file. `max_usb_current=1` is the line needed to be added to the end of the file. This would cause the Raspberry Pi to give more current to the usb ports.
5. You would need to connect the microphone to the Raspberry Pi Zero W in order for this to work. When you have connected the microphone to the Raspbeery Pi Zero W, type `arecord -l` to the console to see that the microphone is connected to the Raspberry Pi. In my case, the microphone is connected to card 1 and tell me that AT2020USB microphone is connected.
6. You would also need to connect a playback device, such as a speaker to the Raspberry Pi so that the Google Assistant can talk back to you. When this is connected, type `aplay -l` to check if the device is connected. In this, you can see that the HDMI should be connected to the Raspberry Pi, so that means you can use the monitor or a TV to output the audio when the Google Assistant is speaking.
  - Sometimes you may want to use a different playback device such as a USB speaker. To do this you can type `cd /home/pi` and create a file by typing `nano ./.asoundrc`. You would need to type the following:
  ```
  pcm.!default {
    type asym
    capture.pcm "mic"
    playback.pcm "speaker"
  }

  pcm.mic {
    type plug
    slave {
        pcm "hw:1,0"
    }
  }

  pcm.speaker {
    type plug
    slave {
        pcm "hw:1,0"
    }
  }

  // OR you can do this command if you're using HDMI output to the TV

  pcm.both {
    type asym
    capture.pcm "mic"
  }

  pcm.mic {
    type plug
    slave {
        pcm "hw:1,0"
    }
  }
  ```

    type which means telling the raspberry pi not to use the default setting. and the rest means what type of speaker and mic you are using, which can be seen  when you type `arecord -l` and `aplay -l`

    

7. Test the speaker by typing `speaker-test -t wav` the audio will then play on the speaker. In my case it played on my TV.
8. Test the microphone by typing `arecord --format=S16_LE --duration=5 --rate=16k --file-type=raw out.raw`. You can then play this back on your speaker by typing `aplay --format=S16_LE --rate=16k out.raw`
9. Typing `alsamixer` in the command line would let you to set the volume higher or lower

10. Install python3 because the Google Assistant uses that to communicate with the Google services: `sudo apt-get install python3-dev python3-venv`. This will grab the python 3 and the python virtual environment.
11. `python3 -m env venv` will create a Python virtual environment 
12. `env/bin/python -m pip install --upgrade setuptools` install the python setup Python development tools to the python virtual environment. Think of this installing nodejs on your Mac/Windows computer/
13. We also need to install git on the raspberry pi so that we can clone the Google Assistant repo. `sudo apt-get install git`
14. Let's open our python env `source env/bin/activate`.
15. We need to clone the repo so that we can use the Google Assistant SDK source code `git clone https://github.com/googlesamples/assistant-sdk-python`.
16. `cp -r assistant-sdk-python/google-assistant-sdk/googlesamples/assistant/grpc ./ZeroAssistant` would copy the example folder from the repo into it's own ZeroAssistant folder
17. Google Assistant needs us to login into the Google Developer network so that we can use it as our voice assistant. To do that we need to install a OAuth tool on our Raspberry Pi. `python -m pip install --upgrade google-auth-oauthlib[tool]` (`python -m pip install --upgrade oauthlib[tool]`)
18. You would need to set up a Google developer account so that you can login into Google as well as managing your Google assistant: The instructions for that can be found [here](https://developers.google.com/assistant/sdk/develop/python/config-dev-project-and-account). Copy the client secret json to the raspberry pi using the scp command or download it from the raspberry pi.
19. We can now use the Google Auth tool to authenticate this: `google-oauthlib-tool --client-secrets <path-to-json-file> --scope https://www.googleapis.com/auth/assistant-sdk-prototype --save --headless`. Follow the instructions given.

20. Install the dependencies to use the GRPC (for use of the ZeroAssistant) on the Raspberry Pi Zero. `sudo apt-get install portaudio19-dev libffi-dev libssl-dev`
21. `pip install google-assistant-grpc`, this would install the GRPC. This would take ages. Possibly 1 hour.
22. Go into the ZeroAssistant folder and do the following commands: `pip install --upgrade -r requirements.txt` which would install all the python packages that are need to run the GRPC. This would take a while so be patient.
23. We need to install one more command that isn't part of the Google RPC. `pip install --upgrade urllib3` or if that doesn't work `pip install --upgrade -r urllib3 ` this correct a issue with the last install.
24. We need to use some helpers which would help the raspberry pi easier with listening to our voice and projecting the audio: `python3 -m audio_helpers`
25. To talk to the assistant: `python -m pushtotalk`










