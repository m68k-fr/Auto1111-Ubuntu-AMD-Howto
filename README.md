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

## Running on low VRAM

```
PYTORCH_HIP_ALLOC_CONF=garbage_collection_threshold:0.9,max_split_size_mb:512 python launch.py --precision full --no-half
```

## Running on RX6800XT

```
PYTORCH_HIP_ALLOC_CONF=garbage_collection_threshold:0.9,max_split_size_mb:512 python launch.py

100%|██████████████████████████████████████████████████████████████| 35/35 [00:06<00:00,  5.08it/s]
100%|██████████████████████████████████████████████████████████████| 35/35 [01:12<00:00,  2.08s/it]
Total progress: 100%|██████████████████████████████████████████████| 70/70 [01:21<00:00,  1.16s/it]
Total progress: 100%|██████████████████████████████████████████████| 70/70 [01:21<00:00,  2.09s/it]
Time taken: 1m 21.29s
Torch active/reserved: 11682/15814 MiB, Sys VRAM: 15878/16368 MiB (97.01%)
```

```
python launch.py --medvram

100%|██████████████████████████████████████████████████████████████| 35/35 [00:08<00:00,  4.23it/s]
100%|██████████████████████████████████████████████████████████████| 35/35 [01:03<00:00,  1.82s/it]
Total progress: 100%|██████████████████████████████████████████████| 70/70 [01:14<00:00,  1.06s/it]
Total progress: 100%|██████████████████████████████████████████████| 70/70 [01:14<00:00,  1.82s/it]
Time taken: 1m 14.18s
Torch active/reserved: 11072/15676 MiB, Sys VRAM: 15740/16368 MiB (96.16%)
```


