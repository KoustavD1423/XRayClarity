# XRayClarity: Classification and Gradient-based Localization of Chest Radiographs

![cover](./assets/cover.png)

## Badges
- **Dataset Preparation** ![badge](https://img.shields.io/badge/Dataset--Preparation-Complete-green)
- **Model Fine-tuning** ![badge](https://img.shields.io/badge/Model--Fine--tuning-Complete-green)
- **Evaluation** ![badge](https://img.shields.io/badge/Evaluation-Complete-green)
- **Grad-CAM Localization** ![badge](https://img.shields.io/badge/Grad--CAM--Localization-Complete-green)
- **Documentation** ![badge](https://img.shields.io/badge/Documentation-Complete-green)
- **Inference Script** ![badge](https://img.shields.io/badge/Inference--Script-Complete-green)

## Contents
- [Introduction](#introduction)
- [Overview](#overview)
- [Steps](#steps)
- [Results](#results)
- [Usage](#usage)
- [Conclusions](#conclusions)
- [References](#references)

## Introduction
> A team of radiologists from New Orleans studied the usefulness of Chest Radiographs for diagnosing COVID-19 compared to the reverse-transcription polymerase chain reaction (RT-PCR) and found out they could aid rapid diagnosis, especially in areas with limited testing facilities [[1]](https://pubs.rsna.org/doi/10.1148/ryct.2020200280 "A Characteristic Chest Radiographic Pattern in the Setting of the COVID-19 Pandemic").<br>
> Another study found out that the radiographs of different viral cases of pneumonia are comparative, and they overlap with other infectious and inflammatory lung diseases, making it hard for radiologists to recognize COVID‐19 from other viral pneumonia cases [[2]](https://pubs.rsna.org/doi/10.1148/rg.2018170048 "Radiographic and CT Features of Viral Pneumonia").<br>
> This project aims to make the former study a reality while dealing with the intricacies in the latter, with the help of Deep Learning.<br>

## Overview
> The project uses the COVID-19 Radiography Database [[3]](https://www.kaggle.com/tawsifurrahman/covid19-radiography-database) as its dataset.
> It has a total of `21165` Chest X-Rays (CXRs) belonging to 4 different classes (`COVID-19`, `Lung Opacity`, `Normal` and `Viral Pneumonia`).<br>
> Three top-scoring CNN architectures, __VGG-16__ [[4]](https://arxiv.org/abs/1409.1556 "Very Deep Convolutional Networks for Large-Scale Image Recognition"), __ResNet-18__ [[5]](https://arxiv.org/abs/1409.1556 "Deep Residual Learning for Image Recognition") and __DenseNet-121__ [[6]](https://arxiv.org/abs/1608.06993 "Densely Connected Convolutional Networks"), trained on the ImageNet Dataset [[7]](http://image-net.org/), were chosen for __fine-tuning__ on the dataset.<br>
> The results obtained from the different architectures were then evaluated and compared.<br>
> Finally, with the help of __Gradient-weighted Class Activation Maps__ (Grad-CAM) [[8]](https://arxiv.org/abs/1610.02391 "Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization") the affected areas in CXRs were localized.<br>

* ___Note:___ The dataset and the trained models can be found in [here](https://drive.google.com/drive/folders/14L8wd-d2a3lvgqQtwV-y53Gsnn6Ud2-w?usp=sharing).<br>

## Steps
> 1. [Dataset Exploration](./1_data_exploration.ipynb "1_data_exploration.ipynb")
> 2. [Split the dataset](./split_dataset.py "split_dataset.py")
>    |Type|COVID-19|Lung Opacity|Normal|Viral Pneumonia|Total|
>    |:-|-:|-:|-:|-:|-:|
>    |Train|3496|5892|10072|1225|20685|
>    |Val|60|60|60|60|240|
>    |Test|60|60|60|60|240|
> 3. [Fine-tune VGG-16, ResNet-18 and DenseNet-121](./2_finetune_models.ipynb "2_finetune_models.ipynb")
>    1. [Define Transformations](./utils.py#L15-L33)
>    2. [Handle imbalanced dataset with Weighted Random Sampling (Over-sampling)](./2_finetune_models.ipynb "2_finetune_models.ipynb/<cell 3>")
>    3. [Prepare the Pre-trained models](./networks.py "networks.py")
>    4. [Fine-tune step with Early-stopping](./utils.py#L83-L151)
>       - |Hyper-parameters||
>         |:-|-:|
>         |Learning rate|`0.00003`|
>         |Batch Size|`32`|
>         |Number of Epochs|`25`|
>       - |Loss Function|Optimizer|
>         |:-:|:-:|
>         |`Categorical Cross Entropy`|`Adam`|
>    5. [Plot running losses & accuracies](./plot_utils.py#L8-L42)
>       - |Model|Summary Plot|
>         |:-:|:-:|
>         |VGG-16|![vgg_plot](./assets/vgg_plot.png)|
>         |ResNet-18|![res_plot](./assets/resnet_plot.png)|
>         |DenseNet-121|![dense_plot](./assets/densenet_plot.png)|
> 4. [Results Evaluation](./3_evaluate_results.ipynb "3_evaluate_results.ipynb")
>    1. [Plot confusion matrices](./plot_utils.py#L45-L69)
>    2. [Compute test-set Accuracy, Precision, Recall & F1-score](./utils.py#L64-L80)
>    3. [Localize using Grad-CAM](./grad_cam.py)
> 5. [Inference](./overlay_cam.py)

## Results

<table>
<tr>
<th></th>
<th>VGG-16</th>
<th>ResNet-18</th>
<th>DenseNet-121</th>
</tr>
<tr>
<td>

|__Pathology__|
|:-|
|COVID-19|
|Lung Opacity|
|Normal|
|Viral Pneumonia|

</td>
<td>

|Accuracy|Precision|Recall|F1-Score|
|-:|-:|-:|-:|
|0.9956|0.9833|1.0000|0.9916|
|0.9582|0.8833|0.9464|0.9138|
|0.9622|0.9667|0.8923|0.9280|
|0.9913|0.9833|0.9833|0.9833|
            
</td>
<td>

|Accuracy|Precision|Recall|F1-Score|
|-:|-:|-:|-:|
|0.9871|0.9667|0.9830|0.9748|
|0.9664|0.8667|1.0000|0.9286|
|0.9664|1.0000|0.8823|0.9375|
|0.9957|1.0000|0.9836|0.9917|
            
</td>
<td>

|Accuracy|Precision|Recall|F1-Score|
|-:|-:|-:|-:|
|0.9957|0.9833|1.0000|0.9916|
|0.9623|0.9167|0.9322|0.9244|
|0.9623|0.9500|0.9047|0.9268|
|0.9957|0.9833|1.0000|0.9916|
            
</td>
</tr>
<tr>
<td>

|TL;DR|
|:-|
|Train set|
|Test set|

</td>
<td>

|Total Correct Predictions|Total Accuracy|
|-:|-:|
|20362|98.44%|
|229|__95.42%__|

</td>
<td>

|Total Correct Predictions|Total Accuracy|
|-:|-:|
|20639|99.78%|
|230|__95.83%__|

</td>
<td>

|Total Correct Predictions|Total Accuracy|
|-:|-:|
|20540|99.30%|
|230|__95.83%__|

</td>
</tr>
<tr>
<td>Confusion Matrices</td>
<td>

![vgg_confmat](./assets/vgg.png)

</td>
<td>

![res_confmat](./assets/resnet.png)

</td>
<td>

![dense_confmat](./assets/densenet.png)

</td>
</tr>
</table>

- __Localization with Gradient-based Class Activation Maps__
> |![original](./assets/original.jpg)|![vgg_cam](./assets/vgg16_camnewnorm.jpg)|![res_cam](./assets/resnet18_camnewnorm.jpg)|![dense_cam](./assets/densnet121_camnewnorm.jpg)|
> |:-:|:-:|:-:|:-:|
> |<sup>_COVID-19 infected CXR_</sup>|<sup>_VGG-16_normal_</sup>|<sup>_ResNet-18_normal_</sup>|<sup>_DenseNet-121_normal_</sup>|
> |![original](./assets/original.jpg)|![vgg_cam](./assets/vgg16_camcovid.jpg)|![res_cam](./assets/resnet18_camcovid.jpg)|![dense_cam](./assets/densenet121_camcovid.jpg)|
> |:-:|:-:|:-:|:-:|
> |<sup>_COVID-19 infected CXR_</sup>|<sup>_VGG-16_infected_</sup>|<sup>_ResNet-18_infected_</sup>|<sup>_DenseNet-121_infected_</sup>|

## Usage
> - Clone the repository
> ```bash
> git clone 'https://github.com/priyavrat-misra/xrays-and-gradcam.git' && cd xrays-and-gradcam/
> ```
> - Install dependencies
> ```bash
> pip install -r requirements.txt
> ```
> - Using `argparse` script for inference
> ```bash
> python overlay_cam.py --help
> ```
> ```
> usage: GradCAM on Chest X-Rays [-h] [-i IMAGE_PATH]
>                                [-l {covid_19,lung_opacity,normal,pneumonia}]
>                                -m {vgg16,resnet18,densenet121}
>                                [-o OUTPUT_PATH]
> 
> Overlays given label's CAM on a given Chest X-Ray.
> 
> optional arguments:
>   -h, --help            show this help message and exit
>   -i IMAGE_PATH, --image-path IMAGE_PATH
>                         Path to chest X-Ray image.
>   -l {covid_19,lung_opacity,normal,pneumonia}, --label {covid_19,lung_opacity,normal,pneumonia}
>                         Choose from covid_19, lung_opacity, normal &
>                         pneumonia, to get the corresponding CAM. If not
>                         mentioned, the highest scoring label is considered.
>   -m {vgg16,resnet18,densenet121}, --model {vgg16,resnet18,densenet121}
>                         Choose from vgg16, resnet18 or densenet121.
>   -o OUTPUT_PATH, --output-path OUTPUT_PATH
>                         Format: "<path> + <file_name> + .jpg"
> ```
> - An example
> ```bash
> python overlay_cam.py --image-path ./assets/original.jpg --label covid_19 --model resnet18 --output-path ./assets/dense_cam.jpg
> ```
> ```
> GradCAM generated for label "covid_19".
> GradCAM masked image saved to "./assets/res_cam.jpg".
> ```

## Future Scope of Improvement

1. **Larger Dataset Collection**:
   - Increase the diversity and volume of the dataset by including more CXRs from different sources and regions to improve generalizability and robustness.

2. **Data Augmentation Techniques**:
   - Implement advanced data augmentation techniques to artificially expand the training dataset and reduce overfitting.

3. **Ensemble Learning**:
   - Combine the predictions of multiple models to enhance the overall performance and reduce the variance of the predictions.

4. **Hyperparameter Optimization**:
   - Use techniques like Grid Search or Random Search to find the optimal set of hyperparameters for each model to improve accuracy further.

5. **Model Architecture Exploration**:
   - Experiment with other state-of-the-art architectures and custom architectures designed specifically for medical imaging.

6. **Explainability and Interpretability**:
   - Integrate more explainability techniques alongside Grad-CAM to make the model’s decision process more transparent and understandable to radiologists.

7. **Cross-validation**:
   - Implement k-fold cross-validation to ensure the model’s performance is consistent and reliable across different subsets of the data.

8. **Deployment and Integration**:
   - Develop a user-friendly web or mobile application for real-time CXR classification and localization that can be used by healthcare professionals in clinical settings.

9. **Multi-modal Learning**:
   - Integrate clinical data (like patient history, symptoms, etc.) with image data to create a multi-modal model that provides more accurate and comprehensive diagnoses.

10. **Collaboration with Radiologists**:
    - Collaborate closely with radiologists to validate the model's predictions and gain insights for further improvements based on their expert feedback.


## Why This Project is Revolutionary

1. **Rapid Diagnosis**:
   - The project enables quick and accurate diagnosis of COVID-19 and other lung pathologies from chest radiographs, which is critical in areas with limited access to RT-PCR testing.

2. **Cost-Effective**:
   - Utilizing chest X-rays, which are widely available and inexpensive, makes it accessible for widespread use, especially in low-resource settings.

3. **Enhanced Radiologist Efficiency**:
   - Assists radiologists by automating the initial screening process, allowing them to focus on more complex cases and reduce their workload.

4. **Improved Accuracy**:
   - Fine-tuning state-of-the-art CNN architectures on a specialized dataset enhances diagnostic accuracy, potentially reducing misdiagnosis.

5. **Explainability with Grad-CAM**:
   - Provides visual explanations for the model’s predictions, helping clinicians understand and trust the AI system’s decisions.

6. **Scalability**:
   - The approach can be extended to diagnose other diseases from different types of medical images, making it a versatile tool in the medical imaging domain.

7. **Empowerment of Remote Areas**:
   - Enables remote and underserved regions to perform reliable diagnoses, bridging the gap in healthcare disparities.

8. **Innovation in Medical Imaging**:
   - Combines cutting-edge deep learning techniques with practical medical applications, pushing the boundaries of what’s possible in medical imaging.

9. **Research and Development**:
   - Contributes valuable insights and methodologies to the growing field of AI in healthcare, encouraging further research and innovation.
  
## Conclusions
> - DenseNet-121 having only `7.98 Million` parameters did relatively better than VGG-16 and ResNet-18, with `138 Million` and `11.17 Million` parameters respectively.
> - Increase in model's parameter count doesn’t necessarily achieve better results, but increase in residual connections might.
> - Oversampling helped in dealing with imbalanced data to a great extent.
> - Fine-tuning helped substantially by dealing with the comparatively small dataset and speeding up the training process.
> - GradCAM aided in localizing the areas in CXRs that decides a model's predictions.
> - The models did a good job distinguishing various infectious and inflammatory lung diseases, which is rather hard manually, as mentioned earlier.

## References
> - [1] David L. Smith, John-Paul Grenier, Catherine Batte, and Bradley Spieler. [A Characteristic Chest Radiographic Pattern in the Setting of the COVID-19 Pandemic](https://pubs.rsna.org/doi/10.1148/ryct.2020200280). Radiology: Cardiothoracic Imaging 2020 2:5.
> - [2] Hyun Jung Koo, Soyeoun Lim, Jooae Choe, Sang-Ho Choi, Heungsup Sung, and Kyung-Hyun Do. [Radiographic and CT Features of Viral Pneumonia](https://pubs.rsna.org/doi/10.1148/rg.2018170048). RadioGraphics 2018 38:3, 719-739.
> - [3] Tawsifur Rahman, Muhammad Chowdhury, Amith Khandakar. [COVID-19 Radiography Database](https://www.kaggle.com/tawsifurrahman/covid19-radiography-database). Kaggle.
> - [4] Karen Simonyan, Andrew Zisserman. [Very Deep Convolutional Networks for Large-Scale Image Recognition](https://arxiv.org/abs/1409.1556). arxiv:1409.1556v6.
> - [5] Kaiming He, Xiangyu Zhang, Shaoqing Ren, Jian Sun. [Deep Residual Learning for Image Recognition](https://arxiv.org/abs/1512.03385). arxiv:1512.03385v1.
> - [6] Gao Huang, Zhuang Liu, Laurens van der Maaten, Kilian Q. Weinberger. [Densely Connected Convolutional Networks](https://arxiv.org/abs/1608.06993). arxiv:1608.06993v5.
> - [7] Deng, J. et al., 2009. [Imagenet: A large-scale hierarchical image database. In 2009 IEEE conference on computer vision and pattern recognition](http://image-net.org/). pp. 248–255.
> - [8] Ramprasaath R. Selvaraju, Michael Cogswell, Abhishek Das, Ramakrishna Vedantam, Devi Parikh, Dhruv Batra. [Grad-CAM: Visual Explanations from Deep Networks via Gradient-based Localization](https://arxiv.org/abs/1610.02391). arXiv:1610.02391v4.
