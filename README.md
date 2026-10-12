# Yoga Pose Recognition and Angle-Based Grading System

An AI-powered computer vision research project that combines deep learning-based yoga pose classification, human pose estimation, and geometric joint-angle analysis to evaluate yoga postures and generate interpretable feedback.

<p align="center">
  <a href="paper/Yoga-Pose-Grading-System.pdf">
    <strong>Read the Full Research Paper (PDF)</strong>
  </a>
</p>

![Python](https://img.shields.io/badge/Python-3.8%2B-blue)
![PyTorch](https://img.shields.io/badge/PyTorch-ResNet--18-EE4C2C)
![MediaPipe](https://img.shields.io/badge/MediaPipe-Pose-0097A7)
![OpenCV](https://img.shields.io/badge/OpenCV-Computer%20Vision-5C3EE8)
![Research](https://img.shields.io/badge/Project-Academic%20Research-6A5ACD)

## Overview

Accurate yoga posture is essential for effective practice, yet traditional assessment often depends on instructor observation. This can make consistent feedback difficult to obtain in home-based and remote learning environments.

This project investigates an automated approach to yoga pose recognition and posture assessment by integrating RGB-based image classification with skeletal landmark analysis.

The system combines **ResNet-18** for yoga pose classification and **MediaPipe Pose** for human landmark detection. By comparing detected joint angles with reference angles for each pose, it estimates posture quality and provides visual and numerical feedback.

The goal is to bridge the gap between pose recognition and posture evaluation through an interpretable, geometry-based scoring mechanism.

## Key Features

- **Deep Learning-Based Pose Recognition:** Classifies 10 yoga poses using a ResNet-18 model trained on a curated subset of the Yoga-82 dataset.
- **Human Pose Estimation:** Extracts 33 anatomical landmarks using MediaPipe Pose.
- **Geometric Joint-Angle Analysis:** Measures selected body and joint angles from skeletal coordinates.
- **Non-Linear Posture Scoring:** Applies tiered penalties based on deviations from reference angles.
- **Visual Feedback:** Uses OpenCV to display predicted pose labels, skeletal landmarks, joint angles, and posture scores.
- **Structured Evaluation Records:** Exports frame-level predictions and posture metrics to CSV files.
- **Temporal Prediction Aggregation:** Applies segment-level majority voting to improve classification consistency across video frames.

## System Architecture

The proposed system follows a sequential processing pipeline:

```text
Video Input
    |
    v
Frame Preprocessing
    |
    v
MediaPipe Pose
33 Landmark Detection
    |
    v
ResNet-18
RGB Pose Classification
    |
    v
Reference Angle Comparison
    |
    v
Non-Linear Posture Scoring
    |
    v
Visual Feedback and CSV Logging
```

### Technology Stack

| Technology | Purpose |
|---|---|
| Python | Main programming language |
| PyTorch | Deep learning framework |
| ResNet-18 | RGB-based yoga pose classification |
| MediaPipe Pose | Skeletal landmark detection |
| OpenCV | Video processing and visual annotation |
| NumPy | Numerical and geometric calculations |
| CSV | Structured evaluation data |

## Methodology

### 1. Yoga Pose Classification

The study uses a ResNet-18 convolutional neural network to classify yoga poses from RGB images.

The training process uses a curated subset of the Yoga-82 dataset, which contains more than 28,000 images. Ten pose categories were selected for the project, with approximately 200 additional images per pose collected for fine-tuning.

Rotation and horizontal-flipping augmentation were applied to improve generalization across variations in image orientation.

### 2. Human Pose Estimation

MediaPipe Pose detects 33 anatomical landmarks, including the shoulders, elbows, hips, knees, and ankles.

These landmarks provide the spatial information required for geometric angle calculation and posture evaluation. The research reports processing speeds exceeding 25 FPS on standard CPUs under the evaluated conditions.

### 3. Geometric Joint-Angle Analysis

The system calculates angles using three skeletal keypoints. Given points \(A\), \(B\), and \(C\), the angle at point \(B\) is calculated as:

\[
\theta=\arccos\left(
\frac{\mathbf{BA}\cdot\mathbf{BC}}
{\|\mathbf{BA}\|\|\mathbf{BC}\|}
\right)
\]

The study defines four key angle features:

- `body_line_angle`
- `knee_angle`
- `bent_leg_angle`
- `shoulder_angle`

Detected angles are compared against reference values derived from instructional yoga videos.

### 4. Non-Linear Posture Scoring

To quantify posture alignment, the system calculates angular deviations between detected joint angles and their corresponding reference values.

Let \(e\) represent the absolute angular error in degrees. The scoring function is:

\[
S(e)=
\begin{cases}
100-0.5e, & 0\le e\le20\\
90-(e-20), & 20<e\le40\\
70-2(e-40), & 40<e\le60\\
0, & e>60
\end{cases}
\]

| Angular Error | Scoring Rule | Score Range |
|---|---|---|
| 0°–20° | \(100-0.5e\) | 90–100 |
| 20°–40° | \(90-(e-20)\) | 70–90 |
| 40°–60° | \(70-2(e-40)\) | 30–70 |
| Above 60° | 0 | 0 |

Individual angle scores are combined to estimate the overall posture quality of each frame. This approach is designed to tolerate small deviations while penalizing larger misalignments more strongly.

The resulting score is a heuristic measure of geometric alignment rather than a clinical assessment or a universal measure of yoga proficiency.

### 5. Visual Feedback and Result Logging

The system uses OpenCV to annotate video frames with pose predictions, skeletal landmarks, measured joint angles, angular deviations, and posture scores.

Frame-level evaluation data are also recorded in CSV format, allowing classification results and posture metrics to be reviewed after processing.

## Evaluated Yoga Poses

The study evaluates 10 yoga pose categories:

1. Bridge Pose
2. Downward-Facing Dog
3. Cobra Pose
4. Extended Side Angle Pose
5. Fish Pose
6. Tree Pose
7. Warrior I
8. Warrior II
9. Warrior III
10. Cat-Cow Pose

## Experimental Results

The research reports the following results for pose classification and evaluation:

| Evaluation Metric | Result |
|---|---:|
| Training Accuracy | 91.5% |
| Validation Accuracy | 82.0% |
| Training Epochs | 20 |
| Segment-Level Classification Accuracy | 85.1% |
| Annotated Evaluation Frames | 8,465 |

The ResNet-18 model achieved 91.5% training accuracy and 82.0% validation accuracy after 20 epochs.

To reduce frame-level prediction noise, segment-level majority voting was applied. Each video segment was assigned the most frequently predicted pose label, achieving a reported segment-level classification accuracy of 85.1% across the 10 evaluated categories.

These findings demonstrate the potential of combining deep learning-based image classification with geometric posture evaluation.

*All reported metrics are taken from the research paper and have not been independently reproduced in this repository.*

## Challenges and Technical Considerations

The research addresses several challenges in practical yoga pose assessment:

- **Inference Efficiency:** MediaPipe Pose was selected for its practical CPU performance and accessible Python interface.
- **Scoring Sensitivity:** A non-linear penalty function was designed to distinguish small deviations from larger alignment errors.
- **Symmetric Pose Confusion:** Mirrored poses may produce similar visual patterns, making classification more challenging.
- **Camera and Scale Variations:** Changes in camera distance, perspective, and apparent body proportions can introduce classification errors.

The study also discusses data augmentation and anchor-based pose normalization as approaches to improving classification consistency.

## Research Paper

The full paper provides further details on the methodology, system design, experimental evaluation, visual outputs, and technical challenges.

**[Read the Full Paper — Yoga Pose Recognition and Angle-Based Grading System](paper/Yoga-Pose-Grading-System.pdf)**

## Repository Scope

This repository is intended as a **research documentation and project portfolio page**.

The published paper is provided as the primary project artifact. Source code, trained model weights, datasets, and environment configuration files are not included in this repository.

As a result, the reported experimental results are documented research findings rather than independently reproducible results from the public repository.

## Authors and Contributions

- **Chen-Hao Chan (Oscar Chan)** — Project Lead and Co-Author
- **Ping-Chen Chiang** — Co-Author
- **Yu-Xian Chen** — Co-Author
- **Prof. Huang-Chia Shih** — Academic Advisor / Corresponding Author

**Affiliation:** Department of Electrical Engineering, Yuan Ze University, Taoyuan City, Taiwan.

## Citation

If you reference this work, please cite the project paper:

```bibtex
@article{chan2025yoga,
  title   = {Yoga Pose Recognition and Angle-Based Grading System},
  author  = {Chan, Chen-Hao and Chiang, Ping-Chen and Chen, Yu-Xian and Shih, Huang-Chia},
  journal = {Department of Electrical Engineering, Yuan Ze University},
  year    = {2025}
}
```

---

*Research focus: Computer Vision · Deep Learning · Human Pose Estimation · Geometric Posture Evaluation*
