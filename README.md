<div align="center">
<img src=assets/logo.jpg width="30%"/>
</div>

FastVideo is a lightweight framework for accelerating large video diffusion models.


https://github.com/user-attachments/assets/5fbc4596-56d6-43aa-98e0-da472cf8e26c




<p align="center">
    🤗 <a href="https://huggingface.co/FastVideo/FastMochi-diffusers" target="_blank">FastMochi</a> | 🤗 <a href="https://huggingface.co/FastVideo/FastHunyuan"  target="_blank">FastHunyuan</a>  | 🔍 <a href="https://discord.gg/REBzDQTWWt" target="_blank"> Discord </a>
</p> 

FastVideo currently offers: (with more to come)

- FastHunyuan and FastMochi: consistency distilled video diffusion models for 8x inference speedup.
- First open distillation recipes for video DiT, based on [PCM](https://github.com/G-U-N/Phased-Consistency-Model).
- Support distilling/finetuning/inferencing state-of-the-art open video DiTs: 1. Mochi 2. Hunyuan.
- Scalable training with FSDP, sequence parallelism, and selective activation checkpointing, with near linear scaling to 64 GPUs.
- Memory efficient finetuning with LoRA, precomputed latent, and precomputed text embeddings.

Dev in progress and highly experimental.

## 🎥 More Demos

Fast-Hunyuan comparison with original Hunyuan, achieving an 8X diffusion speed boost with the FastVideo framework.

https://github.com/user-attachments/assets/064ac1d2-11ed-4a0c-955b-4d412a96ef30

Comparison between OpenAI Sora, original Hunyuan and FastHunyuan

https://github.com/user-attachments/assets/d323b712-3f68-42b2-952b-94f6a49c4836




## Change Log

- ```2024/12/17```: `FastVideo` v0.1 is released.


## 🔧 Installation
The code is tested on Python 3.10.0, CUDA 12.1 and H100.
```
./env_setup.sh fastvideo
```

## 🚀 Inference
We recommend using a GPU with 80GB of memory. To run the inference, use the following command:

### FastHunyuan
```bash
# Download the model weight
python scripts/huggingface/download_hf.py --repo_id=FastVideo/FastHunyuan --local_dir=data/FastHunyuan --repo_type=model
# CLI inference
sh scripts/inference/inference_hunyuan.sh
```
You can also inference FastHunyuan in the [official Hunyuan github](https://github.com/Tencent/HunyuanVideo).

### FastMochi

```bash
# Download the model weight
python scripts/huggingface/download_hf.py --repo_id=FastVideo/FastMochi-diffusers --local_dir=data/FastMochi-diffusers --repo_type=model
# CLI inference
bash scripts/inference/inference_mochi_sp.sh
```


## 🎯 Distill
Our distillation recipe is based on [Phased Consistency Model](https://github.com/G-U-N/Phased-Consistency-Model). We did not find significant improvement using multi-phase distillation, so we keep the one phase setup similar to the original latent consistency model's recipe.
We use the [MixKit](https://huggingface.co/datasets/LanguageBind/Open-Sora-Plan-v1.1.0/tree/main/all_mixkit) dataset for distillation. To avoid running the text encoder and VAE during training, we preprocess all data to generate text embeddings and VAE latents.
Preprocessing instructions can be found [data_preprocess.md](docs/data_preprocess.md). For convenience, we also provide preprocessed data that can be downloaded directly using the following command:
```bash
python scripts/huggingface/download_hf.py --repo_id=FastVideo/HD-Mixkit-Finetune-Hunyuan --local_dir=data/HD-Mixkit-Finetune-Hunyuan --repo_type=dataset
```
Next, download the original model weights with:
```bash
python scripts/huggingface/download_hf.py --repo_id=FastVideo/hunyuan --local_dir=data/hunyuan --repo_type=model
```
To launch the distillation process, use the following commands:
```
bash scripts/distill/distill_mochi.sh # for mochi
bash scripts/distill/distill_hunyuan.sh # for hunyuan
```
We also provide an optional script for distillation with adversarial loss, located at `fastvideo/distill_adv.py`. Although we tried adversarial loss, we did not observe significant improvements.
## Finetune
### ⚡ Full Finetune
Ensure your data is prepared and preprocessed in the format specified in [data_preprocess.md](docs/data_preprocess.md). For convenience, we also provide a mochi preprocessed Black Myth Wukong data that can be downloaded directly:
```bash
python scripts/huggingface/download_hf.py --repo_id=FastVideo/Mochi-Black-Myth --local_dir=data/Mochi-Black-Myth --repo_type=dataset
```
Download the original model weights with:
```bash
python scripts/huggingface/download_hf.py --repo_id=genmo/mochi-1-preview --local_dir=data/mochi --repo_type=model
python scripts/huggingface/download_hf.py --repo_id=FastVideo/hunyuan --local_dir=data/hunyuan --repo_type=model
```
Then you can run the finetune with:
```
bash scripts/finetune/finetune_mochi.sh # for mochi
```
**Note that for finetuning, we did not tune the hyperparameters in the provided script**
### ⚡ Lora Finetune
Currently, we only provide Lora Finetune for Mochi model, the command for Lora Finetune is
```
bash scripts/finetune/finetune_mochi_lora.sh
```
### Minimum Hardware Requirement
- 40 GB GPU memory each for 2 GPUs with lora
- 30 GB GPU memory each for 2 GPUs with CPU offload and lora.
### Finetune with Both Image and Video
Our codebase support finetuning with both image and video. 
```bash
bash scripts/finetune/finetune_hunyuan.sh
bash scripts/finetune/finetune_mochi_lora_mix.sh
```
For Image-Video Mixture Fine-tuning, make sure to enable the --group_frame option in your script.


## Acknowledgement
We learned and reused code from the following projects: [PCM](https://github.com/G-U-N/Phased-Consistency-Model), [diffusers](https://github.com/huggingface/diffusers), [OpenSoraPlan](https://github.com/PKU-YuanGroup/Open-Sora-Plan), and [xDiT](https://github.com/xdit-project/xDiT).

We thank MBZUAI and Anyscale for their support throughout this project.
