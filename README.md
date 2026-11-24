# breast-ultrasound-annotation-checklist
A practical checklist for deciding whether a breast ultrasound dataset is ready for AI training.
reast Ultrasound Annotation Checklist

A practical, open checklist for AI teams to decide whether an annotated breast ultrasound dataset is ready for model training.

Why this exists: A model is only as reliable as its labels. In breast ultrasound, lesion margins are subtle, image quality depends on the operator, and malignant cases are rare, so annotation errors cost more than in most computer vision tasks.

For a full written explanation, see the guide on breast ultrasound annotation for AI and what makes a dataset training-ready.

Table of Contents
What is breast ultrasound annotation?
Why ultrasound is hard to annotate
Dataset readiness checklist
Example annotation guideline template
Measuring annotation quality
Common mistakes
Further reading and services
Contributing
What is breast ultrasound annotation?

Breast ultrasound annotation is the process of labeling ultrasound images so AI models can learn to identify structures, abnormalities, and lesions. Typical targets include:

Tumor boundaries (benign vs. malignant)
Cysts and fibroadenomas
Calcifications
Tissue density variations
Lymph node involvement

Unlike simple bounding-box labeling in general computer vision, this work needs clinical judgment, because lesion margins are often irregular and easy to misclassify without domain expertise.

Why ultrasound is hard to annotate
Challenge	Why it matters
Low signal-to-noise ratio	Speckle and acoustic shadowing blur lesion boundaries
Operator variability	Probe angle and technician skill change how a lesion looks
Subtle malignancy indicators	Irregular margins, posterior shadowing, and microlobulation need trained eyes
Class imbalance	Malignant cases are far fewer than benign ones
Dataset readiness checklist

Copy this into your project tracker and tick items off before training.

People
[ ] Annotators are radiologists or trained medical annotators
[ ] Annotators understand BI-RADS and lesion characteristics
[ ] A clinical reviewer signs off on final labels
Process
[ ] Written annotation guidelines exist and are versioned
[ ] Guidelines define how lesion boundaries are drawn
[ ] Guidelines define how ambiguous cases are escalated
[ ] Guidelines define how multi-reader disagreements are resolved
[ ] The classification system (for example BI-RADS) is stated
Quality
[ ] Inter-rater reliability (IRR) was measured
[ ] Peer review was completed
[ ] Final clinical validation was completed
[ ] Edge cases and rare lesion types were reviewed
Data
[ ] Data covers diverse patient demographics
[ ] Data covers different breast densities
[ ] Data covers more than one scanner or equipment type
[ ] Class imbalance was assessed and documented
[ ] Structured metadata is included (age range, lesion size, BI-RADS category, scan settings)
Compliance
[ ] Images and metadata are fully de-identified
[ ] Privacy and data-handling requirements are documented
[ ] Annotation decisions are documented for traceability
Example annotation guideline template

A starting point you can adapt. This is illustrative, not a clinical standard.

yaml
guideline_version: "1.0"
modality: breast_ultrasound
classification_system: BI-RADS

labels:
  - name: lesion
    geometry: polygon
    attributes:
      - margin        # circumscribed | indistinct | angular | microlobulated | spiculated
      - shadowing     # none | posterior_shadowing | posterior_enhancement
      - birads        # category assigned by reviewer
  - name: calcification
    geometry: point_or_polygon
  - name: lymph_node
    geometry: polygon

rules:
  boundary: "Trace the visible lesion edge; do not include acoustic shadow."
  ambiguous_case: "Flag and escalate to senior radiologist."
  disagreement: "Resolve by adjudication; record final decision and reason."

review:
  stages:
    - initial_annotation
    - peer_review
    - clinical_validation
Measuring annotation quality

Two common checks:

Inter-rater agreement for categorical labels

Cohen's kappa for two raters:

kappa = (p_o - p_e) / (1 - p_e)

where p_o is observed agreement and p_e is agreement expected by chance. For more than two raters, consider Fleiss' kappa.

Overlap for segmentation masks

Dice coefficient between two annotators' masks A and B:

Dice = 2 * |A ∩ B| / (|A| + |B|)
python
import numpy as np

def dice(a: np.ndarray, b: np.ndarray) -> float:
    a, b = a.astype(bool), b.astype(bool)
    total = a.sum() + b.sum()
    if total == 0:
        return 1.0
    return 2.0 * np.logical_and(a, b).sum() / total

Low agreement usually points to unclear guidelines, not just careless annotators.

Common mistakes
Using annotators with no medical imaging background
Skipping inter-rater reliability validation
Changing labeling rules between batches
Ignoring rare lesion types
Too few malignant examples
Poor documentation of annotation decisions

These problems often surface only after deployment, when retraining on corrected data is costly.

Further reading and services
Full guide: Breast Ultrasound Annotation for AI: What Makes a Dataset Training-Ready?
Professional medical image labeling: Annotation of Medical Images by Pareidolia Systems
Contributing

Suggestions are welcome. Open an issue or pull request to add checklist items, improve the template, or share lessons from your own annotation projects.

Disclaimer

This repository is for educational purposes only. It is not medical advice and not a clinical or regulatory standard. Always follow the requirements that apply to your project and jurisdiction.
