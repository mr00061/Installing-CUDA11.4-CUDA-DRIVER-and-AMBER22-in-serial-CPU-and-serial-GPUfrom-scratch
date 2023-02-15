# Installing-CUDA11.4-CUDA-DRIVER-and-AMBER22-in-serial-CPU-and-serial-GPUfrom-scratch
Installing CUDA11.4 CUDA-DRIVER, and AMBER22 in serial CPU and serial GPU from-scratch



I am writing this for the user who has linux ubuntu 20.04 version
Hi, I am very new user in amber. Here I am going to share my experience in detailed. I am showing how to install amber22 and ambertools from scratch. Please Do carefully as I say it here.


Step1: Pre-Installation work before install cuda 11

Check If you have cuda compatible cuda driver and cuda. Amber support cuda >=7 and <=11. Current cuda version is cuda 12 (not supported by amber). If you have cuda12 you need to uninstall it delete all cuda related file and cuda driver (cuda-toolkit). Even If you don’t install cuda, ubuntu automacally connect NVIDIA-DRM as display driver if it is connected your display monitor. First go to the following process

Verify which version of nvidia driver you have

$ lspci | grep -i nvidia
$ uname -m && cat /etc/*release 

Verify your Kernel version

$ uname -r

You can install kernel using following command

$ sudo apt-get install linux-headers-$(uname -r)

You can check current gcc installed in your machine

$ gcc –version

You can install gcc 

$ sudo apt-get -y install gcc

Before Installing cuda driver you should have performed following command in terminal

To uninstall uncompatible cuda version

$ sudo /usr/local/cuda-x.y/bin/cuda-uninstaller

To uinstall uncompatible cuda driver

$ sudo /usr/local/bin/nvidia-uninstaller

Then performed following command that will remove all cuda and nvidia dependency and library

$ sudo apt-get remove --purge '^nvidia-.*'

$ sudo apt-get remove --purge '^libnvidia-.*'

$ sudo apt-get remove --purge '^cuda-.*'

Delete the CUDA releated path from your ~/.bashrc (nano ~/.bashrc will open your shell script)

$ nano ~/.bashrc

delete the cuda related path and save the script then source it

$ source ~/.bashrc

Also delete cuda folder from your /usr/local/ directory

$ sudo rm -rf /usr/local/cuda*

Now you are ready for installation of cuda11.4 which is compatible for amber installation
You can download cudatoolkit anywhere in your computer. I downloaded all file in my Documents folder. Use the following command to get cuda 11.4. You can go to nvidia website which one you prefer to install, be carefull always choose local installation. If you go to other installation method, It will give you latest cuda with cuda driver. If you prefere cuda-11.4 with similar driver then perform following command


wget https://developer.download.nvidia.com/compute/cuda/11.4.0/local_installers/cuda_11.4.0_470.42.01_linux.run


It will download cudatoolkit11.4 which contains compatible drivers and cuda11.4. 
My recommendation do not run as recommended in nvidia instructed (if you are not an advance user). You cannot install display driver it will show nvidia-drm still in use error. Go as following command prompt


presss

CTRL+ALT+F3 

this will turnoff the display driver that in use
Your machine now on text mode. It will asked for your user ID and Password (that you set it up while installing ubuntu) as for root user
Then run

$ pwd 

will show you where are you now

$ modeprobe -r nvidia-drm

then go to your directory whiere you downloaded cuda_11.4.0_470.42.01_linux.run
then run (You may need to write whole line as tab button does not work in text mode)

$ ls

$ sudo sh cuda_11.4.0_470.42.01_linux.run

Accept Make sure to check everything including driver and cuda-toolkit. Now you have successfully installed your cuda-driver and cuda	11.4.
Open the ~/.bashrc script
add following line at the end of your bash script

$ nano ~/.bashrc

$export PATH="/usr/local/cuda-11.4/bin:$PATH"

export CUDA_HOME="/usr/local/cuda-11.4/:$CUDA_HOME"

export LD_LIBRARY_PATH="/usr/local/cuda-11.4/lib64:$LD_LIBRARY_PATH"

then source it

$ source ~/.bashrc

sudo reboot

it will bring your graphical version of ubunutu as it was before

Step2: Pre-installatin for AMBER

Before installing amber in your computer make sure you have correct version of of dependencies: refers to (https://ambermd.org/InstUbuntu.php)
Run following command

$ apt -y update

$ apt -y install tcsh make gcc gfortran flex bison patch bc wget xorg-dev libz-dev libbz2-dev

Currently, We do not need to manually configure the amber source code. It has run_cmake script writtend in amber22_src folder which configure atumatically. For that you should have cmake in your computer
You can install 

$ sudo apt-get -y install camke

It will install latest version of cmake in your computer.and it’s path will be set automaticlly. After or before installation you can check which cmake version you have

$ cmake –version

You may have some of the dependencies already there

Step3: Serial cpu version of amber22 and ambertools22 installation

Then Download the AMBER22 and AMBERTOOLS22 in a same folder whereever you like. Make sure they must have been extracted in same folder

$ tar xvfj Amber22.tar.bz2

$ tar xvfj AmberTools22.tar.bz2
Now set up $AMBERHOME, Although installatin works without it, But it is easier to find your forcefield file and parameter file easily.
Open ~/.bashrc script

$ nano ~/.bashrc

write following line (you can use pwd to get your director)

$ pwd

export AMBERHOME=/home/jewel/Documents/Software_Installed_Location/amber22

$ source ~/.bashrc
Before installing pmend.cuda(single gpu version) or pmemd.cuda.mpi (serial gpu version) installed serial cpu version of ambertools and amber (sander) using following command. Newer version of Amber does not need to configure as older version. It is straightforward….
Go to the 

$ cd $AMBERHOME
$ cd ../amber22_src/build

run following command

$ ./run_cmake

when it is done

$ make install 

Add following line in your bashrc script(instructed in your after installation)

$ nano ~/.bashrc

Add this:

test -f /home/jewel/Documents/Software_Installed_Location/amber22//amber.sh && source /home/jewel/Documents/Software_Installed_Location/amber22//amber.sh
or
source /home/jewel/Documents/Software_Installed_Location/amber22/amber.sh

save bash script, then

$ source ~/.bashrc

Then run the test command

$ cd $AMBERHOME

$ make test.serial

When it is done you are ready for step4

Step4: GPU version of pmemd.cuda installation

This should be straight forward, make sure set up -DCUDA=TRUE in the linux version of part of run_cmake

$ cd $AMBERHOME

$ cd ../amber22_src/build

$ nano run_cmake

then change -DUCDA=FALSE to -DECUDA=TRUE. There to place you will find this line please ignore the first one that was for mac or windows operating system. Second one is for linux. It was explicitey writen there

#for linux user only
#  Assume this is Linux:

  cmake $AMBER_PREFIX/amber22_src \
    -DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-11.4 \
    -DCMAKE_INSTALL_PREFIX=$AMBER_PREFIX/amber22 \
    -DCOMPILER=GNU  \
    -DMPI=FALSE -DCUDA=TRUE -DINSTALL_TESTS=TRUE \

In mycase while running ./run_cmake I got error massage that it is not finding cuda 11.4. Then I added another extra line 
-DCUDA_TOOLKIT_ROOT_DIR=/usr/local/cuda-11.4 \ in run_cmake file. You will not find this version.

After succesfull cmake configuration done run following command

$ make install

Then do the test run for pmemd.cuda

$ make test.cuda.serial

It will give you the command
If you follow these guideline you can install very quickly. It tooks me four days to figure out everything. Hopefully, you will find this interesting and usefull. If you have any question please put in the disscussion parts.
