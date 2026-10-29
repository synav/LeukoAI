<p>
  <img src="assets/ssmc%20logo.png" height="45" align="left"/>
  <img src="assets/mbzuai%20logo.png" height="65" align="right"/>
</p>
<br clear="both"/>

<p align="center">
  <img src="assets/LeukoAI%20logo.png" height="100"/><br/>
  <strong>LeukoAI — Morphology-Based Leukemia Prediction Tool</strong><br/>
  <em>Developed by Sheikh Shakhbout Medical City (SSMC) in collaboration with Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)</em>
</p>

<p align="center">
  <a href="https://doi.org/10.21037/jmai-24-74"><img src="https://img.shields.io/badge/Paper-JMAI%202025-blue" alt="Paper"/></a>
  <img src="https://img.shields.io/badge/Platform-Windows%2010%2F11-informational" alt="Windows"/>
  <img src="https://img.shields.io/badge/Use-Research%20only-red" alt="Research use only"/>
  <a href="https://creativecommons.org/licenses/by-nc-nd/4.0/"><img src="https://img.shields.io/badge/License-CC%20BY--NC--ND%204.0-lightgrey" alt="License"/></a>
</p>

> [!WARNING]
> **For research and educational use only.** LeukoAI is not a regulated medical device and must not be used for patient care or clinical decision-making.

---

## Overview

LeukoAI is a standalone Windows application that predicts the presence and type of leukemia from microscopic images of peripheral blood smears. It runs fully offline on the user's computer.

It implements the hierarchical deep-learning pipeline described in [Naveed S et al., *J Med Artif Intell* 2025](https://doi.org/10.21037/jmai-24-74):

1. **Step 1 – Screening:** leukemia vs. no leukemia.
2. **Step 2 – Classification:** acute vs. chronic leukemia (3-class result), and ALL / AML / CLL / CML (5-class subtype result).

The app analyses all field images from one patient's slide, shows a case-level diagnosis, and saves the prediction and score for every image to a CSV file.

For the full description of the data, intended use and limitations, see the **[Model Card](MODEL_CARD.md)**.

---

## Read this before using the results

- **Use the right kind of image.** The models were trained only on **1000× oil-immersion, May-Grünwald-Giemsa (MGG)-stained** peripheral smear fields from adult patients. Other magnifications, stains, bone-marrow aspirates, or non-smear images will still produce a prediction, but it is not meaningful.
- **Load a whole case, not a single image.** LeukoAI analyses a **folder containing all field images from one patient's slide** and combines them into one case-level diagnosis, as in the published study. Put each patient's images in a separate folder.
- **Capture enough fields.** With the default *Image count threshold* of 3, the app reports leukemia only when **more than 3** images are classified as leukemic. A folder with 3 or fewer images can therefore never be reported as leukemia.
- **The 3-class result is the more reliable output.** Acute vs. chronic vs. no leukemia performed well on external data. Subtype separation between **ALL and AML is weak** (see below).
- **Scores are not calibrated probabilities.** A high class score does not mean the stated chance that the prediction is correct.

---

## Performance

Results from the published study. External validation used 2,973 images from 64 patients, evaluated per patient (all images from a slide combined).

| Output | Sensitivity | Specificity | F1 (macro) | Accuracy |
|---|---|---|---|---|
| **3-class** (acute / chronic / no leukemia) | 0.94 | 0.95–0.96¹ | 0.94 | 0.92 |
| **5-class** (ALL / AML / CLL / CML / no leukemia) | 0.80 | 0.94 | 0.77 | 0.70–0.77¹ |

**5-class F1 by class (external):** CLL 1.00 · CML 1.00 · No leukemia ≈0.90 · AML 0.57 · **ALL 0.36**

- In the 3-class task the pipeline outperformed all three participating pathologists, and no acute or chronic case was labelled as normal.
- In the 5-class task, pathologists had higher overall accuracy (0.84–0.88). The pipeline matched or exceeded them only for CLL and CML.
- CLL and CML results are based on small numbers of external patients (3 and 4) and should be interpreted with caution.

¹ The article reports two different values for this metric in different sections.

---

## System requirements

- **OS:** Windows 10 or 11 (64-bit)
- **CPU:** Intel i5 or higher (AVX support recommended)
- **RAM:** ≥ 8 GB
- **Storage:** ≈ 0.5 GB download, ≈ 1.3 GB after extraction
- **GPU:** not used; the bundled PyTorch is CPU-only

---

## Download and run

1. Download `toolv9.zip` from the **[Releases](https://github.com/synav/LeukoAI/releases)** page.
2. Optional: verify the download. In PowerShell run `Get-FileHash .\toolv9.zip -Algorithm SHA256` and compare with the checksum below.
3. Extract the zip to a folder.
4. Double-click `toolv9.exe`. Windows SmartScreen may show a warning because the application is not code-signed; choose **More info → Run anyway** only if you downloaded it from this repository.
5. Optionally adjust the **Image count threshold** slider (default 3; see *How the case diagnosis is made*).
6. Click **Select Folder** and choose the folder holding one patient's smear images (JPEG, PNG or TIFF).
7. The app shows the case diagnosis and up to three **images of interest** (the fields that most influenced the result).
8. A CSV file with the prediction for every image is saved **inside the selected folder**, named after the folder.

No internet connection is required after download.

**SHA-256 (`toolv9.zip`):** `<!-- TODO: add checksum -->`

---

## Package contents

| File | Purpose |
|---|---|
| `toolv9.exe` | Main application |
| `del_new_bin_flip_jit_scale.ckpt` | Model 1 – leukemia vs no leukemia (DenseNet-121, 2 classes) |
| `del_new_acnr_flip_scale_jit.ckpt` | Model 2 – acute / chronic / no leukemia / reactive (DenseNet-121, 4 classes) |
| `new_five.ckpt` | Model 3 – ALL / AML / CLL / CML / no leukemia (DenseNet-121, 5 classes) |
| `*.dll`, `*.pyd` | Python runtime and dependencies |
| `logo*.png` | Application branding |

Models were trained with PyTorch Lightning 1.5.5. The application runs on Python 3.8 and PyTorch 1.12.1 (CPU). Despite the `jit` in two file names, all three are standard PyTorch Lightning checkpoints, not TorchScript files.

---

## How the case diagnosis is made

For every image in the folder, all three models are run. The case result is then decided by counting images:

1. **Leukemia or not:** if the number of images Model 1 calls *leukemia* is **greater than the threshold** (default 3), the case is leukemic; otherwise it is reported as **No leukemia**.
2. **Acute or chronic:** if **two or more** images are called *chronic* by Model 2, the case is **chronic**; otherwise **acute**.
3. **Subtype:** the subtype is the one with the most images by Model 3 among CLL/CML (chronic) or ALL/AML (acute). If the counts are tied, CLL or ALL is reported.

Images of interest are the three images with the highest Model 1 leukemia score (acute) or Model 2 chronic score (chronic).

---

## Privacy

Images are processed locally on your computer. LeukoAI does not upload images and does not store patient-identifiable information. Avoid using images that contain identifiers burned into the image.

---

## Citation

If you use LeukoAI in academic work, please cite:

> Naveed S, et al. Novel hierarchical deep learning models predict type of leukemia from whole slide microscopic images of peripheral blood. *Journal of Medical Artificial Intelligence*. 2025. doi:[10.21037/jmai-24-74](https://doi.org/10.21037/jmai-24-74)

GitHub's **"Cite this repository"** button (from [`CITATION.cff`](CITATION.cff)) provides APA and BibTeX formats.

---

## License

This work is licensed under the [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License](https://creativecommons.org/licenses/by-nc-nd/4.0/). You may use and share it for non-commercial research purposes with proper citation. No modifications or commercial uses are permitted.

© 2025 Dr Syed Naveed.

---

## Developers and acknowledgements

**Developed by:** Dr Syed Naveed and team, Sheikh Shakhbout Medical City (SSMC), Abu Dhabi
**Collaborators:** Mohamed bin Zayed University of Artificial Intelligence (MBZUAI) and the Hematology & Oncology Departments, SSMC

Special thanks to the MBZUAI team for technical development and validation support.

**Version:** v1.0 (build `toolv9`) · **Build date:** October 2025

---

## Contact

Dr Syed Naveed, FRCPath (Hematology)
Sheikh Shakhbout Medical City, Abu Dhabi, UAE
ORCID: [0000-0002-8172-8129](https://orcid.org/0000-0002-8172-8129)
Email: naveed3642003 (at) gmail (dot) com
