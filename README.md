# Install guide to Automatic1111 & Shark on Ubuntu with AMD gpus

## Install Ubuntu 22.04 LTS from a USB Key
* standard intall. (no third party proprietary repositories)

## Update Ubuntu

```
sudo apt-get update
sudo apt-get dist-upgrade
```
Reboot.

## Install AMD drivers

Download **amdgpu-install_5.4.50403-1_all.deb Radeon™ Software for Linux® version 22.40.3** for Ubuntu 22.04.2 from:  
https://www.amd.com/fr/support/linux-drivers
```
sudo apt-get install ./amdgpu-install_5.4.50403-1_all.deb
```

Needed Prerequisites for ROCm:  
https://docs.amd.com/bundle/ROCm-Installation-Guide-v5.4/page/Introduction_to_ROCm_Installation_Guide_for_Linux.html
```
sudo apt-get install wget gnupg2 gawk curl
sudo usermod -a -G render $LOGNAME
sudo usermod -a -G video $LOGNAME
```

Install drivers:
```
sudo amdgpu-install --rocmrelease=5.4.3 --usecase=rocm,rocmdev,rocmdevtools,lrt,hip,hiplibsdk,mllib,mlsdk --vulkan=pro --no-32 --accept-eula
```

Check your ROCm info, your AMD gpu should be listed:
```
rocminfo
```

Reboot.

## Install Vulkan SDK
```
sudo apt install vulkan-tools
```
Check your Vulkan info, your AMD gpu should be listed:
```
vulkaninfo --summary
```

## Install Auto1111

```
sudo apt-get install git
sudo apt-get install python3
sudo apt install python3.10-venv
sudo apt-get install libstdc++-12-dev
vim ~/.bashrc
```
Add **alias python=python3** at the end of your .bashrc file, save and close.

Restart the terminal.
```
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
python -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip wheel
pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/rocm5.4.2
```

## Running Auto1111

```
cd stable-diffusion-webui
source venv/bin/activate
python launch.py --precision full --no-half --medvram
```

## If External NTFS drives are not mounting properly

```
sudo apt install exfat-fuse
sudo apt install ntfs-3g
```
