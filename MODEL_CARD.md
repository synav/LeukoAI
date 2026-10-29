# LeukoAI Model Card

**Version:** v1.0 (build `toolv9`, October 2025)
**Developers:** Dr Syed Naveed and team, Sheikh Shakhbout Medical City (SSMC), Abu Dhabi, with Mohamed bin Zayed University of Artificial Intelligence (MBZUAI)
**Reference:** Naveed S, et al. *Journal of Medical Artificial Intelligence*, 2025. doi:[10.21037/jmai-24-74](https://doi.org/10.21037/jmai-24-74)
**License:** CC BY-NC-ND 4.0
**Contact:** naveed3642003 (at) gmail (dot) com

---

## 1. Model details

LeukoAI is a hierarchical pipeline of three image classifiers, each a **DenseNet-121** convolutional neural network.

| Model | Task | Classes |
|---|---|---|
| Model 1 | Screening | Leukemia / No leukemia |
| Model 2 | Leukemia category | Acute / Chronic / No leukemia / Reactive |
| Model 3 | Leukemia subtype | ALL / AML / CLL / CML / No leukemia |

**Pipelines**

- **3-class pipeline:** Model 1 → Model 2. Output: acute, chronic, or no leukemia.
- **5-class pipeline:** Model 1 → Models 2 and 3, with a voting step that combines their labels for each image. Output: ALL, AML, CLL, CML, or no leukemia.
- **Patient-level result:** in the published study, image predictions from all fields of one slide were combined by a rule-based aggregation to give one diagnosis per patient.

**Training**

| Setting | Value |
|---|---|
| Input preprocessing | Zero-padded to square, resized to 448 × 448 (bilinear) |
| Optimiser | AdamW, learning rate 1e-3 |
| Batch size | 32 |
| Epochs | 300 |
| Loss | Categorical cross-entropy |
| Framework | PyTorch 1.13 |

**Weight files in the release**

| File | Model |
|---|---|
| `new_five.ckpt` | <!-- TODO --> |
| `del_new_bin_flip_scale.jit.ckpt` | <!-- TODO --> |

---

## 2. Intended use

**Intended for**

- Research into AI-assisted morphological assessment of peripheral blood smears.
- Education and demonstration of hierarchical deep-learning classification in hematology.

**Intended users:** hematologists, hematopathologists, laboratory scientists and AI researchers who can judge smear morphology themselves.

**Not intended for**

- Diagnosis, screening or treatment decisions for any patient.
- Use as a substitute for review by a qualified hematologist, flow cytometry, cytogenetics or molecular testing.
- Bone-marrow aspirates, pediatric samples, or rare leukemia subtypes.
- Images taken outside the conditions described in Section 3.

LeukoAI is **not a regulated medical device**.

---

## 3. Data

**Source:** retrospective, single-centre data from Sheikh Shakhbout Medical City, Abu Dhabi, collected January 2021 – August 2023. Adult patients only.

**Images:** high-power fields (1000×, oil immersion) of May-Grünwald-Giemsa-stained peripheral blood smears, captured manually from each slide. Each image is one field, not a whole-slide scan.

**Total:** 7,255 images from 162 patients.

| Split | ALL | AML | CLL | CML | No leukemia | Total |
|---|---|---|---|---|---|---|
| Training — images (patients) | 471 (9) | 1,149 (27) | 440 (7) | 404 (8) | 938 (27) | 3,402 (78) |
| Internal test — images (patients) | 20 (2) | 174 (7) | 191 (2) | 151 (2) | 344 (7) | 880 (20) |
| External validation — images (patients) | 346 (7) | 1,252 (22) | 225 (3) | 226 (4) | 924 (28) | 2,973 (64) |

- Training and internal test sets were split **by patient/slide** to prevent leakage.
- Training originally used seven classes. For external validation, **APML was merged into AML** and **reactive changes were merged into no leukemia**.

---

## 4. Performance

### Internal test set (image level, macro average)

| Model | Precision | Sensitivity | Specificity | F1 | Accuracy |
|---|---|---|---|---|---|
| Model 1 (binary) | 0.99 | 0.97 | 0.98 | 0.98 | 0.98 |
| Model 2 (4-class) | 0.81 | 0.81 | 0.94 | 0.81 | 0.81 |
| Model 3 (5-class) | 0.83 | 0.81 | 0.95 | 0.81 | 0.81 |

### External validation (patient level, 64 patients)

| Pipeline | Sensitivity | Specificity | F1 (macro) | Accuracy |
|---|---|---|---|---|
| 3-class | 0.94 | 0.95–0.96¹ | 0.94 | 0.92 |
| 5-class | 0.80 | 0.94 | 0.77 | 0.70–0.77¹ |

**5-class F1 by class:** CLL 1.00 · CML 1.00 · No leukemia ≈0.90 · AML 0.57 · ALL 0.36

¹ The article reports two different values for this metric in different sections.

### Comparison with pathologists (same 64 external patients)

| | 3-class | 5-class |
|---|---|---|
| AI vs. pathologists | Pipeline outperformed all three pathologists on every reported metric | Pathologists more accurate overall (0.84–0.88); pipeline equal or better only for CLL and CML |
| Agreement, AI vs. pathologists (Cohen's κ) | 0.74–0.77 | 0.52–0.53 |
| Agreement between pathologists (Cohen's κ) | 0.82–0.87 | 0.72–0.79 |

### Metrics not available

- ROC curves and AUC were not reported.
- Confidence intervals were not reported.
- Calibration of the output scores was not evaluated. Scores should not be read as probabilities.

---

## 5. Explainability

Grad-CAM heatmaps on external images suggested the models focus on white blood cells and blasts. This was a qualitative check only and was not measured. The current application does not display heatmaps.

---

## 6. Limitations and risks

- **ALL vs. AML is unreliable.** ALL had an external F1 of 0.36, and ALL had only 9 training patients. Reactive lymphocytes can resemble lymphoblasts and are a likely source of error.
- **Small class counts.** CLL and CML external results (F1 1.00) rest on 3 and 4 patients and may overstate real performance.
- **Single centre.** All data come from one hospital, one staining protocol and the same microscopes. The external validation set came from the same centre and period as the training data, so performance at other sites is unknown.
- **Narrow input conditions.** The models have only seen 1000× MGG peripheral smear fields. The application does not check image quality, magnification or stain, and will return a prediction for any image.
- **Single-image use.** Published patient-level results combine all fields from a slide. Predictions from a single image are less reliable (internal image-level 5-class accuracy 0.81).
- **Classes not covered.** APML is not reported separately. Rare subtypes, mixed-phenotype leukemias, lymphoma with blood involvement, and myelodysplastic syndromes were not studied.
- **Population.** Adults only; the patient population of one UAE centre may not represent other populations.
- **Label quality.** Reactive-cell labels may be inconsistent in the ground truth.

---

## 7. Ethical considerations

- Data were collected retrospectively at SSMC. <!-- TODO: add ethics committee approval number and consent waiver statement as published -->
- The application processes images locally and does not transmit or store patient data.
- An automated result could bias a reviewer. Users should form their own morphological impression before viewing the prediction.

---

## 8. Recommendations for users

1. Use only 1000× oil-immersion, MGG-stained peripheral smear fields of good focus.
2. Review predictions across several representative fields from the same slide.
3. Rely on the 3-class (acute / chronic / no leukemia) result; treat ALL vs. AML subtype results as unreliable.
4. Confirm every case with standard laboratory work-up.
5. Report errors and unusual results to the developers so they can inform future versions.

---

## 9. Version history

| Version | Date | Changes |
|---|---|---|
| v1.0 (`toolv9`) | October 2025 | First public release |
