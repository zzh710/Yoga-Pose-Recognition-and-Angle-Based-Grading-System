# Yoga Pose Recognition and Angle-Based Grading System

A computer vision research project that integrates deep learning-based yoga pose classification, human pose estimation, and geometric joint-angle analysis to evaluate yoga posture quality and provide interpretable feedback.

**Project Type:** Academic Research Project  
**Institution:** Yuan Ze University, Taiwan  
**Publication:** [Read the Project Paper (PDF)](paper/書審專題.pdf)

---

## Overview

Yoga posture assessment traditionally relies on instructor observation, which can be difficult to scale in remote learning and home-practice environments. This project explores an automated approach to yoga pose recognition and posture evaluation using computer vision and deep learning.

The proposed system combines **ResNet-18** for RGB-based yoga pose classification with **MediaPipe Pose** for skeletal landmark detection. By comparing detected joint angles against reference angles for each yoga pose, the system calculates posture scores and generates visual and numerical feedback.

The research focuses on making posture assessment more interpretable by combining pose classification with geometric analysis rather than relying solely on classification labels.

## Key Features

- **Yoga Pose Classification:** Uses a ResNet-18 convolutional neural network trained on a curated subset of the Yoga-82 dataset.
- **Human Pose Estimation:** Uses MediaPipe Pose to detect 33 anatomical landmarks.
- **Joint-Angle Analysis:** Calculates geometric angles from skeletal keypoints and compares them with reference posture templates.
- **Non-Linear Posture Scoring:** Applies different penalties according to the magnitude of angular deviations.
- **Visual Feedback:** Uses OpenCV to display predicted pose labels, skeletal landmarks, joint angles, and posture scores.
- **Structured Result Logging:** Records frame-level classification results and posture evaluation metrics in CSV format.
- **Segment-Level Evaluation:** Uses majority voting across video frames to improve the consistency of pose classification.

## System Architecture

The proposed pipeline consists of six main stages:

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
Yoga Pose Classification
    |
    v
Reference Angle Comparison
    |
    v
Non-Linear Posture Scoring
    |
    v
OpenCV Visualization
    +
CSV Result Logging
```

### Technologies

| Technology | Role |
|---|---|
| Python | Main programming language |
| PyTorch | Deep learning framework |
| ResNet-18 | RGB-based yoga pose classification |
| MediaPipe Pose | Human skeletal landmark detection |
| OpenCV | Video processing and visual feedback |
| NumPy | Numerical and geometric computation |
| CSV | Structured evaluation result storage |

## Methodology

### 1. Yoga Pose Classification

A ResNet-18 model was trained using a curated subset of the Yoga-82 dataset, which contains more than 28,000 pose images. The study selected 10 yoga pose categories and incorporated approximately 200 additional images per pose for fine-tuning.

Rotation and horizontal-flipping augmentation were used to improve the model's ability to generalize across different image orientations.

### 2. Skeletal Landmark Detection

MediaPipe Pose extracts 33 anatomical landmarks, including the shoulders, elbows, hips, knees, and ankles.

These landmarks provide the coordinates needed for joint-angle calculation and posture evaluation. The paper reports that MediaPipe Pose can operate at over 25 FPS on standard CPUs under the evaluated conditions.

### 3. Geometric Joint-Angle Analysis

The system calculates joint angles from three skeletal keypoints. For points \(A\), \(B\), and \(C\), the angle at \(B\) is calculated using the dot product of vectors \(\mathbf{BA}\) and \(\mathbf{BC}\):

\[
\theta=\arccos\left(
\frac{\mathbf{BA}\cdot\mathbf{BC}}
{\|\mathbf{BA}\|\|\mathbf{BC}\|}
\right)
\]

Four angle features are used in the posture evaluation process:

- `body_line_angle`
- `knee_angle`
- `bent_leg_angle`
- `shoulder_angle`

The detected angles are compared with reference values derived from instructional yoga videos.

### 4. Non-Linear Posture Scoring

The system assigns scores based on the angular deviation between the detected posture and its reference angles.

| Angular Error | Scoring Rule | Score Range |
|---|---|---|
| ≤ 20° | \(100-0.5e\) | 90–100 |
| 20°–40° | \(90-(e-20)\) | 70–90 |
| 40°–60° | \(70-2(e-40)\) | 30–70 |
| > 60° | 0 | 0 |

Here, \(e\) represents the absolute angular error in degrees.

Smaller deviations receive relatively mild penalties, while larger deviations result in more substantial deductions. Individual angle scores are combined to produce an overall frame-level posture score.

### 5. Visual Feedback and Result Logging

OpenCV is used to annotate video frames with the predicted pose label, skeletal landmarks, measured angles, angular deviations, and posture scores.

The system also records frame-level evaluation results in CSV format, supporting further analysis of classification consistency and posture quality.

## Supported Yoga Poses

The study evaluates the following 10 yoga pose categories:

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

The paper reports the following classification results:

| Metric | Result |
|---|---:|
| Training Accuracy | 91.5% |
| Validation Accuracy | 82.0% |
| Training Epochs | 20 |
| Segment-Level Classification Accuracy | 85.1% |
| Annotated Evaluation Frames | 8,465 |

The ResNet-18 model achieved 91.5% training accuracy and 82.0% validation accuracy after 20 epochs.

To reduce frame-level prediction noise, the system applied segment-level majority voting, assigning each video segment the most frequently predicted pose label. The resulting segment-level classification accuracy was 85.1% across the 10 evaluated yoga poses.

These results demonstrate the potential of combining RGB-based pose classification with landmark-based geometric evaluation in an interpretable yoga posture assessment framework.

*Reported results are taken from the project paper and have not been independently reproduced for this repository.*

## Challenges and Design Considerations

The research discusses several challenges encountered during system development:

- **Pose Estimation Efficiency:** MediaPipe Pose was selected for its practical CPU performance, accessible Python API, and 33-landmark output.
- **Scoring Sensitivity:** A non-linear penalty function was introduced to better distinguish minor angular deviations from larger posture misalignments.
- **Symmetric Pose Classification:** Mirrored poses can be difficult to distinguish due to left-right symmetry and camera perspective.
- **Camera and Scale Variations:** Differences in recording distance and body proportions can introduce classification noise. Anchor-based normalization was explored to improve consistency.

## Research Paper

The full paper documents the system methodology, implementation, evaluation results, output examples, and discussion of technical challenges.

**[Read the Full Paper (PDF)](paper/書審專題.pdf)**

## Repository Scope

This repository currently serves as a **research documentation and project portfolio repository**. It contains the project paper rather than a complete, reproducible software implementation.

Source code, trained model weights, datasets, and environment configuration files are not included. Consequently, the reported results should be understood as results documented in the paper, not as results independently reproduced from this repository.

## Authors and Acknowledgements

- **Chen-Hao Chan (Oscar Chan)** — Project Lead and Co-Author
- **Ping-Chen Chiang** — Co-Author
- **Yu-Xian Chen** — Co-Author
- **Prof. Huang-Chia Shih** — Academic Advisor / Corresponding Author

Department of Electrical Engineering, Yuan Ze University, Taoyuan City, Taiwan.

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

*This project was developed as an academic research project on computer vision, deep learning, human pose estimation, and geometric posture evaluation.*
