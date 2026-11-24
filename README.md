# AlzONet: Multiclass Alzheimer's Disease Classification Using CNN and Grad-CAM

## Abstract

Alzheimer's disease is a progressive neurodegenerative disorder that affects memory, cognitive abilities, and overall brain function. Early identification and accurate classification of disease severity are important research challenges in medical image analysis.

This research investigates a Convolutional Neural Network (CNN)-based approach for multiclass classification of Alzheimer's disease using brain Magnetic Resonance Imaging (MRI) scans. A custom CNN architecture, named **AlzONet**, is employed to distinguish between four diagnostic categories: Non-Demented, Very Mild Demented, Mild Demented, and Moderate Demented.

To investigate model interpretability, Gradient-weighted Class Activation Mapping (Grad-CAM) is integrated to visualize the image regions contributing to the model's predictions. Additionally, an ablation-based faithfulness experiment is conducted to examine the effect of masking highly activated regions on prediction confidence.

The experimental results demonstrate a test accuracy of **96.98%**, with further analysis conducted using class-wise precision, recall, F1-score, confusion matrix, Grad-CAM visualizations, and confidence-drop measurements.

## Research Objectives

The primary objectives of this research are:

- To investigate CNN-based multiclass classification of Alzheimer's disease using brain MRI images.
- To develop a custom CNN architecture capable of distinguishing between four stages of cognitive impairment.
- To address class imbalance using Synthetic Minority Over-sampling Technique (SMOTE).
- To investigate model interpretability through Grad-CAM visualizations.
- To evaluate classification performance using standard classification metrics.
- To investigate the faithfulness of Grad-CAM explanations through an ablation-based confidence-drop experiment.

## Classification Categories

The proposed approach considers four diagnostic categories:

| Class | Description |
|---|---|
| NonDemented | Non-demented |
| VeryMildDemented | Very mild dementia |
| MildDemented | Mild dementia |
| ModerateDemented | Moderate dementia |

## Dataset and Preprocessing

The research uses a brain MRI image dataset organized into four class-specific directories.

The preprocessing methodology includes:

1. **Image Loading:** MRI images are loaded using OpenCV in grayscale format.
2. **Image Resizing:** All images are resized to 100 × 100 pixels.
3. **Normalization:** Pixel intensity values are normalized to the range [0, 1].
4. **Data Splitting:** A stratified train-validation-test split is performed using a 70:15:15 ratio to preserve class distributions.
5. **Class Balancing:** SMOTE is applied exclusively to the training dataset to address class imbalance and generate synthetic samples.
6. **Reshaping:** Images are reshaped into a single-channel format suitable for CNN input.

The resulting dataset dimensions are:

| Dataset | Number of Images |
|---|---:|
| Training (after SMOTE) | 26,880 |
| Validation | 5,098 |
| Testing | 5,098 |
| Total original images | 33,984 |

The training dataset is balanced to 6,720 samples per class using SMOTE, while validation and test sets retain their original class distributions.

## Proposed Methodology: AlzONet

A custom Sequential CNN architecture, named AlzONet, is investigated for multiclass Alzheimer's disease classification.

### CNN Architecture

The architecture consists of four convolutional layers with progressively increasing filter dimensions, followed by fully connected layers.

| Layer | Configuration |
|---|---|
| Input | 100 × 100 × 1 |
| Convolutional Layer 1 | 16 filters, 3 × 3, ReLU |
| Max Pooling | 2 × 2 |
| Convolutional Layer 2 | 32 filters, 3 × 3, ReLU |
| Max Pooling | 2 × 2 |
| Dropout | 0.25 |
| Convolutional Layer 3 | 64 filters, 3 × 3, ReLU |
| Max Pooling | 2 × 2 |
| Dropout | 0.25 |
| Convolutional Layer 4 | 128 filters, 3 × 3, ReLU |
| Max Pooling | 2 × 2 |
| Dropout | 0.25 |
| Flatten | Feature vector |
| Dense Layer | 128 neurons, ReLU |
| Output Layer | 4 neurons, Softmax |

**Total trainable parameters:** 359,940.

### Model Training

The model is trained using the following configuration:

- Optimizer: Adam
- Learning Rate: 0.0001
- Loss Function: Sparse Categorical Crossentropy
- Batch Size: 32
- Maximum Epochs: 100
- Early Stopping: Patience of 10 epochs
- Learning Rate Scheduler: ReduceLROnPlateau
- Model Checkpointing: Best validation-loss weights

Early stopping restores the best validation-loss weights, while model checkpointing preserves the best-performing model.

## Explainable AI: Grad-CAM

To investigate the interpretability of the proposed CNN, Gradient-weighted Class Activation Mapping (Grad-CAM) is employed.

Grad-CAM generates class-specific activation heatmaps by utilizing gradients flowing into the final convolutional layer.

In this research:

- The final convolutional layer (`conv_block4_gradcam_target`) is used as the Grad-CAM target layer.
- Gradient information is used to calculate the importance of feature-map channels.
- Activation heatmaps are generated and resized to the original MRI image dimensions.
- Heatmaps are overlaid on MRI images to visualize regions contributing to classification predictions.
- Class-wise Grad-CAM visualizations are generated to examine model attention across all four categories.

This analysis provides visual insights into the model's decision-making process and supports the investigation of model interpretability.

## Ablation-Based Faithfulness Evaluation

In addition to visual explanation, an ablation-based experiment is performed to investigate the influence of Grad-CAM-highlighted regions on model predictions.

The experiment follows these steps:

1. Generate a Grad-CAM heatmap for an MRI image.
2. Resize the heatmap to the original image dimensions.
3. Identify the top 10% most highly activated pixels.
4. Mask the selected pixels by replacing their intensity values with zero.
5. Re-evaluate the modified image using the trained CNN.
6. Measure the change in prediction confidence before and after masking.

Two measurements are considered:

- **Absolute Confidence Drop:** Difference between the original and ablated prediction confidence.
- **Percentage Confidence Drop:** Relative reduction in prediction confidence, with low-baseline-confidence samples excluded from percentage-based summaries.

A batch ablation experiment is conducted on 100 randomly selected test samples using a fixed random seed.

This experiment provides an additional quantitative investigation of the relationship between Grad-CAM-highlighted regions and model predictions.

## Experimental Results

### Classification Performance

The proposed AlzONet model achieves the following performance on the held-out test dataset:

| Metric | Score |
|---|---:|
| Test Accuracy | 96.98% |
| Macro Precision | 97% |
| Macro Recall | 97% |
| Macro F1-Score | 97% |
| Weighted F1-Score | 97% |

### Class-wise Performance

| Classification Category | Precision | Recall | F1-Score |
|---|---:|---:|---:|
| Mild Demented | 0.97 | 0.99 | 0.98 |
| Moderate Demented | 1.00 | 1.00 | 1.00 |
| Non-Demented | 0.96 | 0.96 | 0.96 |
| Very Mild Demented | 0.96 | 0.94 | 0.95 |

### Ablation Experiment Findings

The ablation experiment evaluates 100 test samples by masking the most highly activated Grad-CAM regions.

The observed mean absolute confidence drops, grouped by predicted class, are:

| Predicted Category | Mean Absolute Confidence Drop |
|---|---:|
| Mild Demented | 0.3973 |
| Moderate Demented | 0.6027 |
| Non-Demented | 0.6079 |
| Very Mild Demented | 0.3412 |

These measurements indicate changes in model confidence following the removal of highly activated image regions. They provide an additional quantitative perspective on the model's reliance on Grad-CAM-highlighted regions.

## Research Contributions

The key aspects investigated in this research include:

- A custom CNN architecture (AlzONet) for four-class Alzheimer's disease classification.
- Application of SMOTE to address class imbalance in the training data.
- Integration of Grad-CAM for visual interpretation of CNN classification decisions.
- Class-wise visualization of model activation regions.
- An ablation-based experiment to quantitatively investigate the faithfulness of Grad-CAM explanations.
- Comprehensive evaluation using classification metrics and confidence-drop analysis.

## Technologies and Libraries

- Python
- TensorFlow / Keras
- OpenCV
- NumPy
- Pandas
- Scikit-learn
- Imbalanced-learn (SMOTE)
- Matplotlib
- Seaborn
- Google Colab

## Experimental Environment

The research implementation is conducted using Google Colab, with Google Drive used for dataset access.

The implementation is provided as a Jupyter Notebook.

### Execution Overview

1. Open the research notebook in Google Colab.
2. Mount Google Drive.
3. Place the MRI dataset inside the `MyDrive/dataset` directory.
4. Ensure the four class directories are available.
5. Execute the notebook cells sequentially.
6. Review classification metrics, confusion matrix, Grad-CAM visualizations, and ablation experiment results.

## Limitations and Future Research

Although the experimental results demonstrate promising classification performance, several aspects require further investigation:

- Evaluation on independent and external MRI datasets to assess generalization.
- Investigation of different CNN architectures and transfer-learning approaches.
- Further analysis of Grad-CAM localization and explanation reliability.
- Comparison with additional explainability methods.
- Evaluation across different imaging conditions and patient populations.

## Research Disclaimer

This work is an academic research investigation into deep learning-based MRI image classification and explainability. The proposed model is experimental and is not intended to provide clinical diagnoses or replace professional medical assessment.

## Citation

If you use or reference this research, please cite the associated research work when its publication details become available.
