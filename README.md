# Tugas Mata Kuliah Pengolahan Citra Digital
### Nama   : Endang Prayoga Hidayatulloh
### NIM    : 2006189
Referensi Repository
[mingcv/Bread](https://github.com/mingcv/Bread)

#### Di tujukan untuk memenuhi tugas Mata Kuliah Pengolahan Citra Digital 

## [Low-light Image Enhancement via Breaking Down the Darkness](https://arxiv.org/abs/2111.15557)
by Xiaojie Guo, Qiming Hu.

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/eprayoga/ImageEnhancenmentViaBread/blob/main/ImageEnhancement.ipynb) (Online Demo)

<!-- ![figure_tease](https://github.com/mingcv/Bread/blob/main/figures/figure_tease.png) --> 

### 1. Dependencies
* Python3
* PyTorch>=1.0
* OpenCV-Python, TensorboardX
* NVIDIA GPU+CUDA

### 2. Arsitektur Jaringan (Network Architecture) 
![figure_arch](https://github.com/mingcv/Bread/blob/main/figures/Bread_architecture_full.png)

### 3. Data Preparation

#### 3.1. Training dataset
* 485 low/high-light image pairs from our485 of [LOL dataset](https://daooshee.github.io/BMVC2018website/), each low image of which is augmented by our [exposure_augment.py](https://github.com/mingcv/Bread/blob/main/exposure_augment.py) to generate 8 images under different exposures.
* To train the MECAN (if it is desired), 559 randomly-selected multi-exposure sequences from [SICE](https://github.com/csjcai/SICE) are adopted.

#### 3.2. Tesing dataset
The images for testing can be downloaded in [this link](https://github.com/mingcv/Bread/releases/download/checkpoints/data.zip).

<!-- * 15 low/high-light image pairs from eval15 of [LOL dataset](https://daooshee.github.io/BMVC2018website/).
* 44 low-light images from DICM.
* 8 low-light images from NPE.
* 24 low-light images from VV. -->

### 4. Usage

#### 4.1. Training 
* Multi-exposure data synthesis: ```python exposure_augment.py```
* Train IAN: ```python train_IAN.py -m IAN --comment IAN_train --batch_size 1 --val_interval 1 --num_epochs 500 --lr 0.001 --no_sche```
* Train ANSN: ```python train_ANSN.py -m1 IAN -m2 ANSN --comment ANSN_train --batch_size 1 --val_interval 1 --num_epochs 500 --lr 0.001 --no_sche -m1w ./checkpoints/IAN_335.pth```
* Train CAN: ```python train_CAN.py -m1 IAN -m3 FuseNet --comment CAN_train --batch_size 1 --val_interval 1 --num_epochs 500 --lr 0.001 --no_sche -m1w ./checkpoints/IAN_335.pth```
* Train MECAN on SICE: ```python train_MECAN.py -m FuseNet --comment MECAN_train --batch_size 1 --val_interval 1 --num_epochs 500 --lr 0.001 --no_sche```
* Finetune MECAN on SICE and LOL datasets:  ```python train_MECAN_finetune.py -m FuseNet --comment MECAN_finetune --batch_size 1 --val_interval 1 --num_epochs 500 --lr 1e-4 --no_sche -mw ./checkpoints/FuseNet_MECAN_for_Finetuning_404.pth```

#### 4.2. Testing 
* *\[Tips\]: Using gamma correction for evaluation with parameter --gc; Show extra intermediate outputs with parameter --save_extra*
* Evaluation: ```python eval_Bread.py -m1 IAN -m2 ANSN -m3 FuseNet -m4 FuseNet --mef --comment Bread+NFM+ME[eval] --batch_size 1 -m1w ./checkpoints/IAN_335.pth -m2w ./checkpoints/ANSN_422.pth -m3w ./checkpoints/FuseNet_MECAN_251.pth -m4w ./checkpoints/FuseNet_NFM_297.pth```
* Testing: ```python test_Bread.py -m1 IAN -m2 ANSN -m3 FuseNet -m4 FuseNet --mef --comment Bread+NFM+ME[test] --batch_size 1 -m1w ./checkpoints/IAN_335.pth -m2w ./checkpoints/ANSN_422.pth -m3w ./checkpoints/FuseNet_MECAN_251.pth -m4w ./checkpoints/FuseNet_NFM_297.pth```
* Remove NFM: ```python test_Bread_NoNFM.py -m1 IAN -m2 ANSN -m3 FuseNet --mef -a 0.10 --comment Bread+ME[test] --batch_size 1 -m1w ./checkpoints/IAN_335.pth -m2w ./checkpoints/ANSN_422.pth -m3w ./checkpoints/FuseNet_MECAN_251.pth```

#### 4.3. Trained weights
Please refer to [our release](https://github.com/mingcv/Bread/releases/tag/checkpoints). 
