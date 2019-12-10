# OpenVetSimScripts
A repository for file management scripts made for OpenVetSim

## Introduction
These scripts were designed for use with the OpenVetSim project made by Cornell. They were created to solve a simple problem: OpenVetSim keeps it's video files on the computer's hard drive, and there is no way to change the location.

These scripts are designed to be run as systemd services on linux (the VetSim pc runs Ubuntu)

## The Scripts
### 1. vetsim_delete_old_videos
This script deletes old videos from their default location on the vetsim PC. 
    

    #!/bin/bash
    #David Rhoads, December 2019
    #Script to delete old video files
    #Replace /path/to/directory with actual path to video folder. Be sure folder is actually there before running the script
    #-mtime checks the time since the last modification of the file
    #-type -f tells the program it should look at files
    #-delete tells the program to delete the files (surprise :-))

    echo "This program deletes files older than a specified date at a specified location."
    while true
    do
        find '/path/to/directory' -mtime +30 -type f -delete
        sleep 30
    done


### 2. vetsim_copy_video_files
This script copies videos to an external device. This is usefull if you want to store the videos on an external hard drive