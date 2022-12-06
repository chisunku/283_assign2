# CMPE 283 Assignment 2 

## Work Distribution

Ujwala Mote:
    - Watched professors videos. Learnt about vmx, cpuid and exits from intel SDM.
    - Used assignment 1 instance. 
    - Cloned the repo to get the latest Linux kernel.
    - Changed cpuid.c and vmx.c for 0x4FFFFFFC leaf.
    - Installed an ubuntu VM.
    - Tested the code changes with test.c and cpuid with leaf 0x4FFFFFFC.

Chinmayi Sunku: 
    - Watched the video posted by professor and referred the intel SDM.
    - Used the instance created by assignment 1 which had nested virtulization enabled.
    - cloned the latest linux kernel and built it.
    - Made changes to cpuid.c and vmx.c for 0x4FFFFFFD leaf
    - Installed a VM (nested VM) over it.
    - Ran the test.c to generate the output.
    - Checked the output using cpuid as well.

## GCP instance config

## Enabling nested virtualization on the GCP instance 
    - Generate the yaml file for the instance using cmd:
        gcloud compute instances export instance-4 \
        --destination=instance-4.yml \
        --zone=us-central1-a
    - Make changes in the yaml file 
        advancedMachineFeatures:
 		  enableNestedVirtualization: true
    - Update the VM with the new config. Run the following cmd: 
        gcloud compute instances update-from-file instance-4 \
        --source=/home/ujwalabalbhim_mote/instance-4.yml \
        --most-disruptive-allowed-action=RESTART \
        --zone=us-central1-a
## Create and build kernel module:
    - Clone the git repo : git clone https://github.com/torvalds/linux.git
    - Check linux version by running command : 
        uname –a
    - Install kernel package : 
        sudo apt-get install build-essential kernel-package fakeroot libncurses5-dev libssl-dev ccache bison flex libelf-dev
    - Go into kenrel by cd command : cd linux
    - copy the config: cp /boot/config-$(uname -r) ./.config
    - Then run sudo make oldconfig and keep default for all the config.
    - Kernel build: (we have 8 cpu’s)
        - make -j 8 modules  -- build modules 
        - make -j 8  --- build kernel
        - sudo make INSTALL_MOD_STRIP=1 modules_install
        - sudo make install
        (above step will take time at the first time to be completed)
    - Before the kernel build 
 <img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819104-f4afb6af-0f6f-41da-9039-565423b8747d.png">
    
    - Once the build is done run sudo reboot
    - And then check the uname –a which will be new version of linux.

## Make the code changes as per the assignment requirement
    - Edit the cupid.c file and vmx.c file then rebuild kernel using commands:
    - sudo make modules && sudo make -j 8 modules_install
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819381-baa495c3-85c6-4492-82a2-05036920782b.png">

## Code changes
    - Edit the arch/x86/kvm/cupid.c file and arch/x86/kvm/vmx/vmx.c 
    - Rebuild the kernel: 
        sudo make modules && sudo make -j 8 modules_install

## check for kvm
    - lsmod | grep kvm
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819511-015c9208-689a-4c85-a2bb-ecccf11bc09e.png">
    
    - if present do the following:
        sudo rmmod  kvm_intel
        sudo rmmod  kvm
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819562-3403f42f-25e8-4c92-bbe4-5b9cc92c1f80.png">

    - modprobe kvm and kvm_intel:
        sudo modprobe kvm
        sudo modprobe kvm_intel
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819611-d1a26134-4a68-4a4f-b947-6524dd614022.png">

## Test:

### Install nested VM

    - sudo apt-get install cpu-checker
    - sudo apt update
    - sudo apt-get install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils
    - sudo kvm-ok
    - sudo reboot
    - sudo apt -y install bridge-utils cpu-checker libvirt-clients libvirt-daemon qemu qemu-kvm
    - sudo virt-install --name ubuntu-guest --os-variant ubuntu20.04 --vcpus 2 --ram 2048 --location           http://ftp.ubuntu.com/ubuntu/dists/focal/main/installer-amd64/ --network bridge=virbr0,model=virtio --graphics none --extra-args='console=ttyS0,115200n8 serial'

Note:
    If installation gets stuck with error KVM guest hangs at Escape character is ^]
    then follow - https://base64.co.za/kvm-guest-hangs-at-escape-character-is-solved/

### After installation run below commands
    - sudo apt-get update
    - sudo apt-get install cpuid
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819667-2eb71df1-8eb8-4001-9d53-397cb04c7623.png">

## Outputs

    1. To check total number of exits run command cpuid -1 -l 0x4ffffffc. It will give hexadecimal number which we need to convert to decimal using command echo $((16#hexNum))
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819709-01093b05-f194-4436-b3fd-f390f7c93f5f.png">

    2. to check total number of cycles run command cpuid -1 -l 0x4ffffffd. It will give hexadecimal number which we need to convert to decimal using command echo $((16#hexNum))
<img width="468" alt="image" src="https://user-images.githubusercontent.com/112213523/205819810-d8163a52-f5eb-446d-af11-807d0dca4bd3.png">

    - with test file:
![WhatsApp Image 2022-12-05 at 8 50 42 PM](https://user-images.githubusercontent.com/112213523/205819900-6ce642c0-077c-4507-a34e-414bda812216.jpeg)









