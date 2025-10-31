<p align="center">
  <img src="assets/LeukoAI logo.png" height="90"/><br/>
  <sub><strong>LeukoAI — Morphology-Based Leukemia Prediction Tool</strong></sub>
</p>

<p align="center">
  <strong>Hierarchical deep learning for leukemia classification from peripheral blood smear images</strong><br/>
  <em>Developed by Sheikh Shakhbout Medical City (SSMC) in collaboration with Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)</em><br/>
  Research prototype • Windows standalone (.exe) • Explainable AI for digital hematopathology
</p>

<p align="center">
  <img src="assets/ssmc logo.png" height="40" style="margin-right:1cm;"/>
  <img src="assets/mbzuai logo.png" height="40"/>
</p>



## License
This work is licensed under the [Creative Commons Attribution-NonCommercial-NoDerivatives 4.0 International License](https://creativecommons.org/licenses/by-nc-nd/4.0/).
You may use and share the software for non-commercial research purposes with proper citation.  
No modifications or commercial uses are permitted.
© 2025 Dr Syed Naveed. 

Please **cite the following publication** when using the tool:
Naveed S et al., *J Med Artif Intell*, 2025.



**Developed by:** Dr Syed Naveed and team, Sheikh Shakhbout Medical City (SSMC), Abu Dhabi  
**Collaborators:** Mohamed Bin Zayed University of Artificial Intelligence (MBZUAI) and Hematology & Oncology Departments  
**Version:** v1.0  |  **Build Date:** October 2025

---

## 🔍 Overview
LeukoAI is a standalone deep-learning application that predicts leukemia and its major subtypes from peripheral-blood smear morphology images.  
It uses a hierarchical convolutional neural-network model trained on >10 000 expert-annotated images.  
Outputs are displayed instantly as class probabilities and diagnostic labels.

---

## ⚙️ System Requirements
- **OS:** Windows 10 or 11 (64-bit)  
- **CPU:** Intel i5 or higher (AVX support recommended)  
- **GPU (optional):** NVIDIA CUDA capable for faster inference  
- **RAM:** ≥ 8 GB  
- **Storage:** ≈ 2 GB free space

---

## 🚀 How to Run
1. Download and extract `toolv9.zip`.  
2. Double-click `toolv9.exe`.  
3. When prompted, upload a peripheral-smear image (JPEG / PNG).  
4. The application will display predicted diagnosis and confidence scores.  
*(No internet connection required after download.)*

---

## 📊 Model Information
- **Framework:** PyTorch 1.13 (LTS)  
- **Model weights:** `new_five.ckpt` and `del_new_bin_flip_scale.jit.ckpt`  
- **Training dataset:** > 10 000 labeled images (AML, ALL, CML, CLL, Normal and Reactive smears)  
- **Performance:** Accuracy ≈ 94 %, AUC ≈ 0.97 (on held-out validation set)

---

## 🧩 Included Files
- `toolv9.exe` – Main application  
- `*.ckpt` – Trained weights  
- `*.dll`, `*.pyd` – Python runtime and dependencies  
- `logo*.png` – Application branding

---

## ⚠️ Disclaimer
This software is provided **for research and educational purposes only.**  
It is **not a regulated medical device** and must not be used for patient care or clinical decision-making.  
No personally identifiable health information is processed or stored.  
Use is at the user’s own risk.

---

## 🧾 Citation
If you use LeukoAI in academic work, please cite:  

> Naveed S, et al. “Novel Hierarchical Deep-Learning Models Predict Type of Leukemia from Whole-Slide Microscopic Images of Peripheral Blood.”  
> *Journal of Medical Artificial Intelligence*, March 2025.   
> DOI: 10.21037/jmai-24-74

---

## 🧠 Acknowledgements
Department of Health – Abu Dhabi for research support; King Hussein Cancer Center (Jordan) for dataset collaboration; and all hematologists and BMT physicians across the UAE who contributed clinical data.  
Special thanks to the MBZUAI team for technical development and validation support.

---

## 📬 Contact
Dr Syed Naveed, FRCPath (Hematology)  
Sheikh Shakhbout Medical City – Abu Dhabi, UAE  
ORCID: [0000-0002-8172-8129](https://orcid.org/0000-0002-8172-8129)  
Email: naveed3642003 (at) gmail (dot) com

