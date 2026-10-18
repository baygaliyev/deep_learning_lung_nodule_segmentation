# Comparative Analysis of Deep Neural Networks for Lung Nodule Segmentation

Course project for **Neural Networks and Deep Learning** (University of Pisa, PhD programme),
delivered December 2024 — **joint work by Arman Zafaranchi and Gurban Aliyev**.

We train and compare three U-Net variants for 2D lung-nodule segmentation on CT scans and
report Dice, sensitivity, precision, false-positive rate and F1, together with a qualitative
comparison of the predicted masks.

## Results

| Model | Dice | Sensitivity | Precision | FPR | F1 |
|---|---|---|---|---|---|
| Res U-Net | 83 | 73 | 78 | 2.44 | 75 |
| Attention U-Net | 86 | 85 | 88 | 3.16 | 86 |
| **Attention Res U-Net** | **90** | **88** | **92** | **0.5** | **88** |

Attention Res U-Net gives the best overall segmentation quality and the lowest false-positive
rate, striking the best balance between sensitivity and precision.

## Method

1. **Region of interest.** The lung parenchyma is segmented first with the pre-trained
   [LungQuant](https://github.com/pedemonte/LungQuant) cascade (AlexNet bounding box + U-Net),
   and the resulting masks are applied to the raw CT to remove irrelevant anatomy.
2. **Nodule segmentation.** The lung ROI is fed to three architectures sharing the same base,
   so the effect of the added components is isolated:
   - **Res U-Net** — U-Net with residual convolutional blocks (gradient flow / feature reuse).
   - **Attention U-Net** — U-Net with attention gates on the skip connections.
   - **Attention Res U-Net** — residual blocks *and* attention gates.

## Data

[LUNA16](https://luna16.grand-challenge.org/) (derived from [LIDC-IDRI](https://wiki.cancerimagingarchive.net/display/Public/LIDC-IDRI)):
888 low-dose thoracic CT scans with nodule annotations from four radiologists. The notebooks use a
Kaggle mirror of the ROI-extracted slices (`/kaggle/input/newnodules/...`); see below to reproduce.

## Repository contents

```
res_unet.ipynb              # Res U-Net
attention_unet.ipynb        # Attention U-Net
attention_res_unet.ipynb    # Attention Res U-Net
report/                     # project report (PDF)
```

Notebooks are the submitted [Kaggle](https://www.kaggle.com/) notebooks (TensorFlow / Keras).
Dataset paths point at the Kaggle environment; to rerun, attach a LUNA16-derived nodule-slice
dataset at the same mount point (or change `path` / `mask_path` in the first cell of each notebook).

## Attribution & related work

This is a **joint course project**. The architecture implementations and the wider pipeline were
developed by **Arman Zafaranchi**, who maintains the extended, cleaned-up versions of this work as
part of the HEAL ITALIA project:

- [Ar-Zafaranchi/Lung-Segmentation](https://github.com/Ar-Zafaranchi/Lung-Segmentation) — LungQuant-based ROI extraction
- [Ar-Zafaranchi/Lung-Nodule-Segmentation-2D](https://github.com/Ar-Zafaranchi/Lung-Nodule-Segmentation-2D) — 2D Attention Res U-Net pipeline
- [Ar-Zafaranchi/Lung-Nodule-Segmentation-3D-SwinUnetr](https://github.com/Ar-Zafaranchi/Lung-Nodule-Segmentation-3D-SwinUnetr) — 3D Swin UNETR
- [Ar-Zafaranchi/CT-Preprocessing-Pipeline](https://github.com/Ar-Zafaranchi/CT-Preprocessing-Pipeline) — resampling, ROI extraction, normalization, CLAHE

Please refer to those repositories for the maintained implementation. This repository preserves
the original course submission as an artefact.

## References

1. R. Li et al., "Multistage Attention ResU-Net for semantic segmentation of fine-resolution remote sensing images," *IEEE GRSL*, 2021.
2. O. Oktay, "Attention U-Net: Learning where to look for the pancreas," *arXiv:1804.03999*, 2018.
3. O. Ronneberger, P. Fischer, T. Brox, "U-Net: Convolutional networks for biomedical image segmentation," *MICCAI*, 2015.
4. Z. Zhang, Q. Liu, Y. Wang, "Road extraction by deep residual U-Net," *IEEE GRSL*, 2018.
5. F. Lizzi et al., "Quantification of pulmonary involvement in COVID-19 pneumonia: an upgrade of the LungQuant software for lung CT segmentation," *EPJ Plus*, 2023.
