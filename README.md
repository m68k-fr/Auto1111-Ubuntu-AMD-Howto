# Install guide to Automatic1111 on Ubuntu with AMD gpus

## Install Ubuntu 22.04.02 LTS from a USB Key
* standard intall. (no third party proprietary repositories)

## Update Ubuntu

```
sudo apt-get update
sudo apt-get dist-upgrade
```
Reboot.

## Install AMD drivers

Download **amdgpu-install_5.4.50406-1_all.deb Radeon™ Software for Linux® version 22.40.3** for Ubuntu 22.04.2 from:  
https://www.amd.com/fr/support/linux-drivers
```
sudo apt-get install ./amdgpu-install_5.4.50406-1_all.deb
```

Needed Prerequisites for ROCm:  
https://docs.amd.com/bundle/ROCm-Installation-Guide-v5.4/page/Introduction_to_ROCm_Installation_Guide_for_Linux.html
```
sudo apt-get install gnupg2 gawk curl
sudo usermod -a -G render $LOGNAME
sudo usermod -a -G video $LOGNAME
```

Install drivers:
```
sudo amdgpu-install --rocmrelease=5.4.6 --usecase=rocm,rocmdev,rocmdevtools,lrt,hip,hiplibsdk,mllib,mlsdk --vulkan=pro --no-32 --accept-eula
```

Reboot.

Check your ROCm info, your AMD gpu should be listed:
```
rocminfo
```

## Install Vulkan tools
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
sudo apt install python3.10-venv
sudo apt-get install libstdc++-12-dev
```
Edit the **~/.bashrc** file:  
Add **alias python=python3** at the end of your .bashrc file, save and close.
```
source ~/.bashrc
```

```
cd ~
git clone https://github.com/AUTOMATIC1111/stable-diffusion-webui
cd stable-diffusion-webui
python -m venv venv
source venv/bin/activate
python -m pip install --upgrade pip wheel
pip install torch torchvision --index-url https://download.pytorch.org/whl/rocm5.4.2
```
At the time beeing, pytorch for ROCm5.5 is not officially released, but a nightly version is available.  
If you want to install the 5.5 version, just replace the last line by this one:
```
pip install torch torchvision --index-url https://download.pytorch.org/whl/nightly/rocm5.5
```

## Running Auto1111

```
cd stable-diffusion-webui
source venv/bin/activate
python launch.py --precision full --no-half --medvram
```

## Running on low VRAM

```
cd stable-diffusion-webui
source venv/bin/activate
PYTORCH_HIP_ALLOC_CONF=garbage_collection_threshold:0.9,max_split_size_mb:512 python launch.py --precision full --no-half
```

## Running on RX6000 Series

```
cd stable-diffusion-webui
source venv/bin/activate
python launch.py
```
--precision full & --no-half are not needed on series 6000, and will save a lot of VRAM.  
If you don't generate big images, you can also skip the -medvram.
Pleasde note, first time generating an image could take some time.

## Recommended Auto1111 extensions and settings

* Recommended extensions: sd-web-ui-kitchen-theme, a1111-sd-webui-lycoris
* Recommended Setting: Live previews / Show live previews of the created image: off
* Recommended Setting: User interface / Quicksettings list: Add **sd_vae** and **CLIP_stop_at_last_layers**


## Note regarding external NTFS drives

If your external NTFS drives (windows) are not mounting properly:

```
sudo apt install exfat-fuse
```

## Upgrade to the hidden ROCm 5.6 (optional)

A hidden repo for the upcoming ROCm version is available, even if nothing was announced or made public.  
Only recommanded if you're not afraid of beta testing!

```
sudo amdgpu-install --rocmrelease=all --uninstall
wget http://repo.radeon.com/amdgpu-install/.5.6/ubuntu/jammy/amdgpu-install_5.6.50600-1_all.deb
sudo dpkg -i amdgpu-install_5.6.50600-1_all.deb
cd /etc/apt/sources.list.d/
sudo nano amdgpu.list
# change 5.6 to .5.6
sudo nano rocm.list
# change 5.6 to .apt_5.6
sudo amdgpu-install --usecase=rocm,rocmdev,rocmdevtools,lrt,hip,hiplibsdk,mllib,mlsdk --no-32
```
Even if keeping pytorch and torch vision for ROCm5.4.2 should work with ROCm5.6, it's highly recommended to compile them from source.  
Follow this guide for further instructions: [Compiling-Pytorch-for-ROCm](https://github.com/m68k-fr/Compiling-Pytorch-for-ROCm/)


