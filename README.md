# OpenVetSimScripts
A repository for file management scripts made for OpenVetSim

## Introduction
These scripts were designed for use with the OpenVetSim project made by Cornell. They were created to solve a simple problem: OpenVetSim keeps it's video files on the computer's hard drive, and there is no way to change the location.

These scripts are designed to be run as systemd services on linux (the VetSim pc runs Ubuntu)

## The Scripts
### 1. vetsim_delete_old_videos
This script deletes old videos from their default location on the vetsim PC. 
    

```bash
    #!/bin/bash
    #David Rhoads, December 2019
    #Script to delete old video files
    #Replace /path/to/directory with actual path to video folder. 
    #Be sure folder is actually there before running the script
    #-mtime checks the time since the last modification of the file
    #-type -f tells the program it should look at files
    #-delete tells the program to delete the files (surprise :-))

    echo "This program deletes files older than a specified date at a specified location."
    while true
    do
        find '/path/to/directory' -mtime +30 -type f -delete
        sleep 30
    done
```

### 2. vetsim_copy_video_files
This script copies videos to an external device. This is useful if you want to store the videos on an external hard drive

```bash
#!/bin/bash

#################################
# vetsim_copy_video_files       #
# Written by David Rhoads       #
# December 13, 2019             #
#################################

# https://github.com/kd2flz/OpenVetSimScripts 

#This script copies video files from a specified location to another specified location
#It can be set to continuously scan the folder for new files
#I would reccomend installing grsync (sudo apt install grsync), and using it's built in dry run feature to generate the correct script
#However, if you feel confident, you can also install rsync modify the below script to reflect the correct paths for your device


echo "This program copies files from an a specified folder on your hard drive to an external device."
while true
do
    rsync -r -n -t -v --progress --ignore-existing --modify-window=1 -z -s /path/to/directory /media/user/device_name/folder
     #Change the number after sleep to reflect the seconds you want the script to wait before running rsync again
    sleep 10
done
```

## Using Rsync
This script uses rsync to sync new files to the device. It periodically runs the rsync command. The frequency the command is run can be changed by changing the number after sleep. 

I would recomend installing the gui wrapper for rsync - grsync, as it allows you to select the correct folders and files, and a myriad of other options, and then generates the correct rsync command for you (I haven't found a way to automate grsync itself yet, if you know of one, please let me know.)

You can install grsync with
```
sudo apt install grsync
```

If you're a command line junkie, and the mere thought of using the gui gives you the heebie jeebies, have no fear, rsync can be installed by 

```
sudo apt install rsync
```
I take no responsibility for anything that happens if you use plain old rsync. However, the rsync man page may be of some help https://linux.die.net/man/1/rsync. 

## Setting up the scripts as a service
This section is currently in development. My initial thought was to make the scripts executable and then just set them to run on startup from Ubuntu's graphical "startup applications" utility. Howmsoever (I know that's not a word), this does not seem to be working. 

I will continue to test this. However, my plan B is to make a systemd service that automatically runs these scripts.

### Creating the systemd services
Open a terminal using [ctrl][alt][T] (some linux distributions use [Super][T] instead).

Type cd /etc/systemd/system to change into the systemd directory. 

To create the actual service, type

```
sudo nano vetsim_delete_videos.service
```
The nano text editor should open in the terminal. Paste the following into the terminal by using right click and paste:
```
[Unit]
Description=delete old vetsim videos service
After=network.target
StartLimitIntervalSec=0
[Service]
Type=simple
Restart=always
RestartSec=1
User=username
ExecStart=/home/username/Desktop/VetSimScripts/vetsim_delete_old_videos

[Install]
WantedBy=multi-user.target
```
Turn on the service using 
```
sudo systemctl start vetsim_delete_videos.service
```
Enable the service to run on boot:
```
sudo systemctl enable vetsim_delete_videos.service
```