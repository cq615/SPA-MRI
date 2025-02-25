# Sampling-Pattern-Agnostic MRI Reconstruction through Adaptive Consistency Enforcement with Diffusion Model

_Code is based on the DPS repository (see `main` branch)_

## Abstract
In this paper, we propose a sampling-pattern-agnostic MRI reconstruction method via a diffusion model through adaptive consistency enforcement. Our approach effectively reconstructs high-fidelity images with varied under-sampled acquisitions, generalising across contrasts and acceleration factors regardless of sampling trajectories. We train and validate across all contrasts in the MICCAI 2024 Cardiac MRI Reconstruction Challenge (CMRxRecon) dataset for the “Random sampling CMR reconstruction” task. Evaluation results indicate that our proposed method significantly outperforms baseline methods.

## Prerequisites
- python 3.8

- pytorch 1.11.0

- CUDA 11.3.1

- nvidia-docker (if you use GPU in docker container)

It is okay to use lower version of CUDA with proper pytorch version.

Ex) CUDA 10.2 with pytorch 1.7.0

<br />

## Getting started 

### 1) Clone the repository

```
git clone https://github.com/cq615/SPA-MRI.git

cd SPA-MRI

git checkout challenge-validation-sub
```

<br />

### 2) Download pretrained checkpoint
From the [link](https://drive.google.com/drive/folders/1WhtgWQusNPilOg9jHKgigxfVLex8L__8?usp=sharing), download the checkpoint prefixed with "cmr2024" (trained on CMRxRecon2024 dataset) or "cmr2023" (trained on CMRxRecon2023 dataset) and specify the path in `configs/model_config.yaml` for the key, "model_path". See example below for the CMRxRecon2024 pretrained model:

```
mkdir models
mv {DOWNLOAD_DIR}/cmr2024_ema_0.9999_1000000.pt ./models/
```
{DOWNLOAD_DIR} is the directory that you downloaded checkpoint to.
Now, specify the checkpoint's path in model_config.yaml as explained above.


<br />


### 3) Set environment

Install dependencies

```
conda create -n DPS python=3.8

conda activate DPS

pip install -r requirements.txt

pip install torch==1.11.0+cu113 torchvision==0.12.0+cu113 torchaudio==0.11.0 --extra-index-url https://download.pytorch.org/whl/cu113
```

<br />

### [Option 2] Build Docker image

Install docker engine, GPU driver and proper cuda before running the following commands.

Dockerfile already contains command to clone external codes. You don't have to clone them again.

--gpus=all is required to use local GPU device (Docker >= 19.03)

```
docker build -t dps-docker:latest .

docker run -it --rm --gpus=all dps-docker
```

<br />

### 4) Inference

```
python3 sample_condition.py \
--model_config=configs/model_config.yaml \
--diffusion_config=configs/diffusion_config.yaml \
--task_config={TASK-CONFIG};
```


:speaker: For MRI reconstruction, use configs/recon.yaml

<br />

## Structure of task configurations
You need to specify your MRI data (.mat file) at data.root, your mask directory at data.mask_path--this config assumes you have one or more mask types within this mask directory--and specify the mask type at data.us_mask_type. Default is ./data/samples which contains three sample images from FFHQ validation set.

```
conditioning:
    method: # check candidates in guided_diffusion/condition_methods.py
    params:
        scale: 2

data:
    name: recon
    root: ./data/path/to/mat/file
    mask_path: ./data/path/to/mask/dir
    us_mask_type: ./data/path/to/mask/type/in/mask/dir
    single_file_eval: true

measurement:
    operator:
        name: recon # for MRI recon. See all candidates in guided_diffusion/measurements.py

noise:
    name:   # gaussian or poisson
    sigma:  # if you use name: gaussian, set this.
    (rate:) # if you use name: poisson, set this.
```

## Other Key Files
- `data/dataloader.py`: Contains `ReconDataset` class and other functions for loading and processing MRI data.
- `guided_diffusion/gaussian_diffusion.py`: Contains the innovations as detailed in our paper (see `q_sample_ddim` and `p_sample` in `DDIM` class)

## Citation
If you find our work interesting, please consider citing

```
@article{malyala2024sampling,
  title={Sampling-Pattern-Agnostic MRI Reconstruction through Adaptive Consistency Enforcement with Diffusion Model},
  author={Malyala, Anurag and Zhang, Zhenlin and Wang, Chengyan and Qin, Chen},
  journal={arXiv preprint arXiv:2409.14479},
  year={2024}
}
```