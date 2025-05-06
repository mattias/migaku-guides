This guide will go through, step by step, how to install Yujin and subs2cia from scratch on a Windows 11 machine.

## Prequisites

### Windows Terminal
I recommend installing Windows Terminal on windows, it's a modern Terminal from Microsoft, it can be installed through the Microsoft Store here:
* [Windows Terminal](https://apps.microsoft.com/detail/9n0dx20hk701)

### WSL (Windows Subsystem for Linux)
WSL is required to be able to jump into a Linux environment on Windows.

Follow these commands in the Windows Terminal:
* wsl --install -d Ubuntu
* Restart Windows Terminal
* Done

* [WSL install Youtube video](https://www.youtube.com/watch?v=zZf4YH4WiZo)
* [WSL](https://learn.microsoft.com/en-us/windows/wsl/install)

## Start up an Ubuntu terminal in Windows Terminal
To do this, use the little down arrow next to the + icon in the top left corner next to the default terminal that starts with the application. If this is the first time starting Ubuntu, follow the installation instructions setting up your user + password. The password will be used when doing admin commands in linux.

## Installing Yujin
To install Yujin, run the following commands in the Ubuntu Terminal, one line at a time.
When prompted to continue, press Y as instructed.

```
sudo apt update && sudo apt install ffmpeg
sudo apt install python-is-python3
sudo apt install python3-pip
sudo apt install python3-venv
cd ~
python3 -m venv condensed_audio
source condensed_audio/bin/activate
mkdir repos && cd repos
git clone https://github.com/FabioBaroni/Yujin.git
cd Yujin
pip install -U openai-whisper
chmod +x yujin.sh
```

### Using Yujin
Before using Yujin the python environment needs to be activated.
```
cd ~ && source condensed_audio/bin/activate
```
Now you can run commands from the README: https://github.com/FabioBaroni/Yujin
Usually just ./yujin.sh when in the Yujin directory.
You get there by using the cd command:
```
cd ~/repos/Yujin
```

Note that webm doesns't seem to work with Yujin, so if a video gets downloaded as webm you have to convert it to mp4 using ffmpeg:
https://gist.github.com/maximebories/91d79c0ec25fd3f990615dcf355de8eb

## Installing subs2cia
After having installed Yujin most of the stuff is already in place, so just run the following command.
```
cd ~ && source condensed_audio/bin/activate
pip3 install subs2cia
```

### Using subs2cia
Before using subs2cia the python environment needs to be activated.
```
cd ~ && source condensed_audio/bin/activate
```
Now you can run commands from the README: https://github.com/dxing97/subs2cia

## Installing yt-dlp
This little command-line tool is used to download videos from youtube and other places that it supports. The installation is quite simple.

```
cd ~ && source condensed_audio/bin/activate
pip3 install -U "yt-dlp[default]"
```

Here is an example on how to download the following video: https://www.youtube.com/watch?v=CLrcP68Ou7c&list=PLmhqv13HLg3cbsm544qtmFrfpmHQ3RfJz&index=54&ab_channel=ComprehensibleJapanese

This is a link from the Migaku Japanese Playlist, Late Beginner. The first part in the URL, after "watch", is the important part, you want the value after "v=" up until the first ampersand (&). That would be; CLrcP68Ou7c

This is the code you use in yt-dlp like this:
```
cd ~/repos/Yujin
yt-dlp -o conversation-with-noriko-sensei CLrcP68Ou7c
```

Now you have downloaded this video in to ~/repos/Yujin. Should be a file named something like conversation-with-noriko-sensei.mkv. From here you can run Yujin
```
./yujin.sh
```

Follow the prompts and select L for local whisper and watch it work.
By the end you will have the transcript and condensed audio in the condensed_audio folder (~/repos/Yujin/condensed_audio)

This is what you can do now to copy the condensed_audio folder over to your windows drive:
```
cp -r ~/repos/Yujin/condensed_audio/ /mnt/c/
```
Now on your c drive you should see this folder.

Clean up in the Yujin folder:
```
rm -rf ~/repos/Yujin/condensed_audio/
rm ~/repos/Yujin/conversation-with-noriko-sensei*
```

For this particular example video above, there isn't much time saved by condensing because they pretty much talk nonstop.

## SubPlz

To use SubPlz they recommend running it in docker, so first we need to install docker.
See instructions here: https://docs.docker.com/desktop/setup/install/windows-install/

Pick the Microsoft store one and use Docker Desktop with WSL.

After the installation is done, start it up have it run in the background.

Now, the only thing you have to do to use SubPlz is to run this command:

```
docker run -it --rm --name subplz \
--gpus all \
-v /mnt/d/sync/Ghost_Hunt_1/:/sync \
-v /mnt/d/SyncCache:/app/SyncCache \
-v /mnt/d/ModelCache:/config/.cache \
kanjieater/subplz:latest \
gen -d "/sync/" --model large-v3
```

Here is the breakdown of the command and what you have to change:

`-v /mnt/d/sync/Ghost_Hunt_1/:/sync`
The /mnt/d/sync/Ghost_Hunt_1/ is the path to the folder on your harddrive with the audiobook or video files. It is equivalent to D:\sync\Ghost_Hunt_1\
So you have to create these folders and use the drive letter you have. Rename them as suited.
The "/sync" part at the end is where the above folder should connect to in docker, which would be the /sync/ folder there.

`-v /mnt/d/SyncCache:/app/SyncCache`
This is to connect a local folder to the docker folder for the sync cache stuff. Because after this "run once" command stops, everything in the virtual machine will be removed like it was never there. So with this it can save the files locally instead of directly in the virtual machine.

`-v /mnt/d/ModelCache:/config/.cache`
Same here, but for the whisper model and other related caches.

`gen -d "/sync/" --model large-v3`
This is the final part of the command and this will generate subtitles for the contents in /sync/, which we "mounted" earlier using `-v /mnt/d/sync/Ghost_Hunt_1/:/sync`. It's basically a shortcut to `/mnt/d/sync/Ghost_Hunt_1/`. I hope that explains it easily enough.

There are other commands apart from `gen`. All available commands can be found through the link below in the README file.

*  [kanjieater/SubPlz](https://github.com/kanjieater/SubPlz)

## Bootlegged Migaku Player
You need to install the Tampermonkey extension in Chrome (if using Chrome) and then install this script: https://gist.github.com/SirOlaf/173c08fee56b2b073a3aa87a76c0fdf8

Then go to a youtube video that has auto-generated japanese subs to be able to use it properly. I use this one: https://www.youtube.com/watch?v=JGjE9GhuLDk

You should be able to see some custom loaders in video options of Migaku. If not, reload the page and try again. 

Now use the subtitles from SubPlz and load the video/audio the subs were generated from.
