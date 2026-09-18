# Lect-Former: Localized Self-Attention Skip Connections for Whiteboard Content Extraction (DAS 2026)
Code and tools used for the paper: **["Lect-Former: Localized Self-Attention Skip Connections for Whiteboard Content Extraction" (DAS 2026)](https://link.springer.com/chapter/10.1007/978-3-032-36207-0_16)**

**Lect-Former** is a model for extracting handwritten content from lecture videos at the pixel level, producing clean foreground masks for downstream lecture-video analysis. 

Building on our previous work, **[FCN-LectureNet](https://ieeexplore.ieee.org/document/9494351)**, Lect-Former improves pixel-level foreground extraction as an upstream component for lecture-video summarization, indexing, and retrieval.

<p align="center">
  <img src="bin_res.png" width="500">
</p>

<p align="center">
  <em>Examples of Lect-Former predictions. Left: input images. Right: extracted foreground content.</em>
</p>

## Dataset

The LectureMath Ext (2026) Dataset can be downloaded from here: [LectureMath Ext (2026) Dataset](https://www.dropbox.com/scl/fi/6srviqnl5iuumzew88fqr/LectureMath_Ext_bin_annotations.zip?rlkey=82yxh4u8npstbixhyr4n6l8m0&dl=0)

> [!NOTE]
> UPDATE (09/09/2026): 
>  - Code used for training is now available.
>  - Code used for evaluation is partially available.
>  - Additional evaluation scripts, pretrained/trained weights and missing evaluation data will be made available soon.

# Key Features & Methodology

<p align="center">
  <img src="lectformer_arch.png" width="75%">
</p>

- **Localized Self-Attention Skip Connections:** Lect-Former follows a U-Net-style encoder-decoder architecture and replaces selected skip connections with localized self-attention modules to incorporate spatial context efficiently.

- **Three-Branch Prediction:** The network uses one main branch for pixel-level text segmentation and two auxiliary branches for region-level text detection and text-free background estimation, providing additional guidance for foreground extraction.

<p align="center">
  <img src="training_protocol.png" width="75%">
</p>

- **Three-Stage Training Protocol:** The progressive training strategy helps the model learn text-aware representations before fine-tuning on the target lecture-video domain, where only limited handwritten lecture data is available.


# Results & Metrics
Final evaluation on the extended **LectureMath dataset** uses standard document-image binarization metrics from **[H-DIBCO](https://vc.ee.duth.gr/h-dibco2016/benchmark/?utm_source)**:

These metrics evaluate both pixel-level foreground accuracy and the structural quality of the binarized handwritten-content output.

| Model | PSNR ↑ | DRD ↓ | F1 ↑ | Pseudo F1 ↑ |
|---|---:|---:|---:|---:|
| FCN-LectureNet | 21.84 | 9.11 | 82.94 | 84.37 |
| **Lect-Former** | **22.96** | **6.69** | **85.91** | **87.84** |

> Additional ablation studies on pretraining objectives, localized self-attention implementations, and auxiliary-branch fusion strategies are reported in the paper. 


# Usage Guide =======WORKING NOW

## Installation
1. Clone the repository:
```bash
git clone https://github.com/kdavila/LectFormer_DAS2026.git
cd LectFormer_DAS2026
```
2. Create and activate a virtual environment.

3. Install the required dependencies: =======MAKE REQUIREMENTS FILE
```bash
pip install -r requirements.txt
```
> [!NOTE]
> A CUDA-capable NVIDIA GPU is strongly recommended for training and evaluation.

## Dataset Preparation

[MS-COCO 2017](), [LSVT](), [LectureMath Ext](#dataset), [TextSeg]() / [Total-Text]()

Download the required datasets and update their paths in the corresponding configuration file under `configs/`.

```bash
prep codes
```

## Traning
Lect-Former is trained progressively through three stages:

### Stage 1
Pretrains reconstruction using MS-COCO 2017.
```bash
python <stage1_script.py> <config>
```

### Stage 2
Loads the Stage 1 weights and jointly trains the text-detection, background-estimation, and binarization branches using LSVT.
```bash
python <stage2_script.py> <config>
```

### Stage 3
Loads the pretrained model and fine-tunes the complete network on LectureMath Ext.
```bash
python <stage3_script.py> <config>
```

## Evaluation
Run the evaluation script using the trained Stage 3 model:
```bash
EVALUATION CODE
```
Final LectureMath evaluation uses the H-DIBCO 2016 binarization metrics described above.

# Configuration
The `configs/` folder contains JSON configuration files for **Lect-Former** and the **FCN-LectureNet baseline** experiments.

The main configuration sections are:
- **`General`**: Device and output directory settings.
- **`Network`**: Model architecture, prediction heads, localized self-attention skip connections, normalization, and activation settings.
- **`Evaluation`**: Output settings for saved evaluation predictions.
- **`Training`**: Stage 3 LectureMath fine-tuning, including data augmentation, optimization, sampling, checkpoints, and pretrained model paths.
- **`Ablation`**: Dataset, training, and evaluation settings used for TextSeg/Total-Text ablation experiments.
- **`Pretraining`**: Stage 1 reconstruction and Stage 2 text-deletion pretraining settings.

Before running the scripts, update the **device**, **dataset paths**, **output paths**, and **pretrained model paths** according to your local environment. 

> [!TIP]
> For reproducibility, the training crop size is set to **256 x 256**. In our experiments, larger crop sizes improved performance, but also increased GPU memory usage and training time.


# Citation

If you find this code or dataset work useful, please cite our paper:

```bibtex
@inproceedings{davila2026lect,
  title={Lect-Former: Localized Self-attention Skip Connections for Whiteboard Content Extraction},
  author={Davila, Kenny and Xu, Fei and Ro, Sunyoung and Song, Min},
  booktitle={International Workshop on Document Analysis Systems},
  pages={263--281},
  year={2026},
  organization={Springer}
}
```
