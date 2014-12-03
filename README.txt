*******************************************************************************
                      (             )  (               
                      )\ )   (   ( /(  )\      (  (    
                     (()/(  ))\  )\())((_) (   )\))(   
                      ((_))/((_)((_)\  _   )\ ((_)()\  
                      _| |(_))  | |(_)| |((_)_(()((_)
                    / _` |/ -_) | '_ \| |/ _ \\ V  V /
                    \__,_|\___| |_.__/|_|\___/ \_/\_/

*******************************************************************************

# Run {list} of apps to rm bloatware based on text list using adb
# Copyright 2014 queenTea (@queenTea4qnNet)
# usage: deblow {app list} 
#
#   This program is free software; you can redistribute it and/or modify
#   it under the terms of the GNU General Public License as published by
#   the Free Software Foundation; either version 3 of the License, or
#   (at your option) any later version."
#
#   This program is distributed in the hope that it will be useful,
#   but WITHOUT ANY WARRANTY; without even the implied warranty of
#   MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#   GNU General Public License for more details."
#
#   You should have received a copy of the GNU General Public License
#   along with this program; if not, write to the Free Software Foundation,
#   Inc., 51 Franklin Street, Fifth Floor, Boston, MA 02110-1301  USA"

deblow
======

Script to remove bloatware based on an a list of apps. Uses ADB and CWM method.

======

Usage: deblow {app list}

======

Requirements:

1) Read the warnings below.
2) CWM Recovery w/ ADB
3) USB Drivers for your Phone
4) Rooted
5) Enable USB Debugging on phone
6) Connect to computer with ADB installed (see Initial setup for ADB install)

Warnings:

BACK UP YOUR PHONE FIRST. This stuff can screw up, protect yourself; you can never have too many backups.  

Apps are removed from '/system/app/' folder on phone. Support for other file paths is planned but not yet supported.

REQUIRED READING: http://forum.xda-developers.com/showthread.php?t=1645301 This script is essentially an Ubuntu adaptation of the above process to remove unwanted apps from your phone, including bloatware that cannot easily be removed from the phone OS.

This script will not handle multiple devices on the adb server.

Was written/tested in Xubuntu 13.10.

======

Walkthrough:

* * * * * * * * * * * * 
Initial Setup:
* * * * * * * * * * * * 
If needed, install adb:
   user@xubuntu:~/ sudo add-apt-repository ppa:phablet-team/tools
   user@xubuntu:~/ sudo apt-get update
   user@xubuntu:~/ sudo apt-get install android-tools-adb


 1) Download or clone the git repository and extract it to a file, say '~/deblow' (any file names or paths referenced I will use throughout the walkthroughs, simply substitute your own values; any terminal commands will be prefixed with user@xubuntu:~$ and indented). 
 
 2) Ensure deblow has execution permission:
      
      user@xubuntu:~/deblow$ sudo chmod 755 deblow.sh


(Optional) Add the deblow directory to your system path:
 
 1) Open .bashrc using a text editor, in a terminal (ctrl+alt+t):
   
   user@xubuntu:~/$ sudo cp ~/.bashrc ~/.bashrc.bkup
   user@xubuntu:~/$ sudo nano ~/.bashrc
   
 2) add 'export PATH=~/deblow:$PATH' to the bottom of the file then use 'ctrl+o' to save and 'ctrl+x' to exit.
 
 3) Restart the terminal or enter
    
   user@xubuntu:~/$ source ~/.bashrc
   
 You should now be able to type use deblow from the CLI without specifying its path. You can verify by moving to your home directory and entering:
 
   user@xubuntu:~/$ deblow
 
 Which should return: 'usage: deblow {app list}'

 
              * * * * * * * * * * * * * * * * * * * * *
Next: Move to Verify your device is connecting to adb:
              * * * * * * * * * * * * * * * * * * * * *
 
 
* * * * * * * * * * * * * * * * * * * * *
Verify your device is connecting to adb:
* * * * * * * * * * * * * * * * * * * * *

 1) Ensure your device is in USB debugging mode, plug it into your computer and if necessary authorize your computers fingerprint for USB Debugging.

 2) Open a terminal and check adb's status:
      
      user@xubuntu:~/$ adb devices
      
     You should see something which ends with:
      ..
      ..
      List of devices attached
      ********        device
    
    This means adb is running and connected to your device.
 
 3) Kill the adb server, we just want to check that we can connect but later we sections of this guide are written as though the server is not started. (I had some issues with adb and using server sessions when the device was unplugged and plugged back in, if you are having adb to device connection issues, try restarting adb 'adb kill-server; adb start-server' and try again.) 

      user@xubuntu:~/$ adb kill-server

      
              * * * * * * * * * * * *      * * * * * * * * * * * *
Next: Move to  Using a premade list:   or  Creating your own list:
              * * * * * * * * * * * *      * * * * * * * * * * * *
              
              
* * * * * * * * * * * * 
 Using a premade list:
* * * * * * * * * * * * 

 1a) Copy From Other Source: Open a new text document and paste list into it.  Each app to be removed should be on its own line, WITHOUT a path.  Save the list as whatever you want, like bloatList. Its easiest to place in the same folder as deblow.sh but not required.
 
 1b) If you have a list that already meets the above requirements, simply save it and remember where it is.

 2) Ensure your device is in USB debugging mode, plug it into your computer and if necessary authorize your computers fingerprint for USB Debugging.

 3) Open a terminal and navigate to deblow's home using one of these methods:
   (3a): Start a terminal using "ctrl+alt+t"
   
      user@xubuntu:~/$ cd ~/deblow
      
   (3b): Open the folder in your File manager, hold shift and write click, then select 'Open Terminal Here'
  
  NOTE: if you have added ~/deblow to your $PATH you do not need to be in the deblow directory, you will not need to be in ~/deblow nor preface the script name with '~/deblow/' or '../' however this walkthrough will continue as though this has not been completed.

  
              * * * * * * * * * * * *     
Next: Move to     Running deblow   
              * * * * * * * * * * * * 
 
 
* * * * * * * * * * * * 
Creating your own list:
* * * * * * * * * * * * 

 1) Ensure your device is in USB debugging mode and plug it into your computer.

 2) Open a terminal and check adb's status:
      
      user@xubuntu:~/$ adb devices
    
 3) Reboot to recovery: 

      user@xubuntu:~/$ adb reboot recovery
    
    Your phone will reboot into Recovery mode, once it has, move to the next step:
 
 4) Mount the file system using:
 
      user@xubuntu:~/$ adb shell mount /system
 
 5) List the installed apps and write the data to a text file and copy that file for our bloat list.   

      user@xubuntu:~/$ adb shell ls -1 /system/app/ > fullList
      user@xubuntu:~/$ cp fullList bloatList
      
 6) Open the list, and remove any entries you do not want to remove.  Be very careful when assesing wether to keep or get rid of something, remember you can always freeze the app if you are unsure and remove it later if everything still functions as you need. When you are finished the list be sure it has one entry per line for each .app or .odex you want to remove, then save it.

 7) Reboot your phone if you need to use it while setting up your list and kill the adb server:
 
      user@xubuntu:~/$ adb reboot
      user@xubuntu:~/$ adb kill-server

  
              * * * * * * * * * * * *     
Next: Move to     Running deblow   
              * * * * * * * * * * * * 
 
 
* * * * * * * * * * * * 
    Running Deblow:
* * * * * * * * * * * *

I am assuming that you have just completed either step 5 of "Using a premade list:" or step 7 of "Creating your own list:" This is where we are starting from:
   - Our phone has CWM Recovery w/ ADB and has been backed up
   - USB Drivers installed for your Phone
   - Phone has been Rooted
   - Enable USB Debugging on phone and permission
   - Phone is on in normal functioning mode.
   
 1) First, connect your phone to the PC

 2) Start a terminal window using (ctrl+alt+t) and enter:

      user@xubuntu:~/$ deblow bloatList
      
 3) Follow the prompts on screen; you will need to answer yes to all the questions; except maybe the last:
      
      Restart the server? Y
      
      Reboot to recovery? Y
      
      Mount system? Y
      
      Reboot Phone? Y
      
  4) If any errors occur, the error output from adb will be displayed in terminal. If you see no errors, congratulations, you've stopped your phone from blowing!  
  
  
Thanks for using deblow!

      
* * * * * * * * * * * * 
  Bumps in the road:
* * * * * * * * * * * * 

I found most of the issues I had while doing this for myself were solved by restarting the adb server:
      user@xubuntu:~/$ adb kill-server
      user@xubuntu:~/$ adb start-server
      
      
Problem: ADB sees device when it is in normal operation but does not once in CWM.

Solution: Change default permissions for SuperSU to 'Grant.'  Be sure to change this back to your preferred setting when finished.


* * * * * * * * * * * * 
       Contact:
* * * * * * * * * * * * 

Twitter: https://twitter.com/queenTea4qnNet
Github: https://github.com/msashlee



