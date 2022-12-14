# Ships Classification using PyTorch

This project was carried out for the [Leonardo Labs Kaggle Competition](https://www.kaggle.com/competitions/sapienza-training-camp-2022/overview) and was able to achieve the 2nd place with a private score of 0.98102. The goal was to classify images of ships according to their type (7 different classes). 

## Friendly Reminder

If you use or take inspiration from this repository please cite with this link: [santurini/Ships-Classification-with-Neural-Networks-Ensemble](https://github.com/santurini/Ships-Classification-with-Neural-Networks-Ensemble)

Your support will be truly appreciated and feel free to contact me at my following links or just send me an email:
- [Linkedin](https://www.linkedin.com/in/arturo-ghinassi-50b8a0219/)
- [Kaggle](https://www.kaggle.com/santurini)
- ghinassi.1863151@studenti.uniroma1.it

## Approach

The strategy chosen was to train different neural network architectures and then obtain a single prediction by majority voting. Other strategies that have been adopted include training an ensemble of neural networks after combining the outputs and the use of **Visual Transformers**. 

## Dataset

In the original training set there are images out of context that must be removed, this process has already been done by us, in the following csv file only the files and the respective categories of images concerning ships are listed:

```python
!gdown 1AobspqrM3TYKw3DvWUpsH3GHhCCMMovg
```

The dataset can be downloaded with the following command:

```python
!gdown 1hukMWTFj2aSqx2jBh42R-Y6UXrSw60Nj
```

Here are some examples:

|battleships|coast-guard|containerships|cruise-ships           |drilling-rigs|motor-yachts|submarines|
|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|:-------------------------:|
|<img width="244" src="https://user-images.githubusercontent.com/91251307/190616163-c5efa3af-3ba4-46c5-a165-2b8c10992c7f.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190616552-8b9e4424-8627-47e0-9333-66492a971017.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190616944-4df17a42-12b2-46f8-bd74-47e5025be104.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190617257-88529bf0-9e37-4b79-854f-bd2ab0c47cbe.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190617425-ff803acb-de1b-4634-b0b0-9bd132654f8f.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190617596-531c9c00-9767-456f-8d49-ef4e8eee4929.jpg">|<img width="244" src="https://user-images.githubusercontent.com/91251307/190617746-4d946024-9aa1-4b36-8e6d-264fddafbff7.jpg">|

## Pipeline

### Setup

The adopted loss criterion was the [Cross-Entropy Loss](https://pytorch.org/docs/stable/generated/torch.nn.CrossEntropyLoss.html).
The Optimizer was [Adam](https://pytorch.org/docs/stable/generated/torch.optim.Adam.html) for the first 10 epoques to get closer to the local maxima and then [SGD](https://pytorch.org/docs/stable/generated/torch.optim.SGD.html#torch.optim.SGD) for 5 more epoques to have a smoother convergence with a [Lambda scheduler](https://pytorch.org/docs/stable/generated/torch.optim.lr_scheduler.LambdaLR.html).
We chose a small batch size equal to 8 in order to reduce the computational time and have a faster convergence.

### Models

| Models       | Paper           | Link  |
| :-------------------------- |:--------------------------|:--------------------------|
| ViT B 16     | <https://arxiv.org/abs/2010.11929> |https://github.com/lukemelas/PyTorch-Pretrained-ViT|
| Resnet152    | <https://arxiv.org/abs/1512.03385>      |https://pytorch.org/vision/0.12/generated/torchvision.models.resnet152.html|
| ConvNeXt | <https://arxiv.org/abs/2201.03545>      |https://pytorch.org/vision/stable/models/convnext.html|
| ResNeXt | <https://arxiv.org/abs/1611.05431>      |https://pytorch.org/hub/pytorch_vision_resnext/|
| SE-ResNeXt | <https://arxiv.org/abs/1709.01507v4>      |https://rwightman.github.io/pytorch-image-models/models/seresnext/|
| Xception | <https://arxiv.org/abs/1610.02357>      |https://rwightman.github.io/pytorch-image-models/models/xception/|

### Data Augmentation

Given the low number of training samples and the presence of black and white images, it was essential to normalize the images and apply data augmentation to obtain more. The transformations applied were not overwhelming as we tried to maintain a certain consistency with the test set to better train the model, below are the transformations used:

```python
input_size = (224, 224)

train_transform = T.Compose([T.Resize(input_size), 
                              T.ToTensor(), 
                              T.Normalize(mean=(0.5, 0.5, 0.5), std=(0.5, 0.5, 0.5))])

t1 = T.Compose([T.Resize(input_size), 
                T.RandomHorizontalFlip(p = 1),
                T.ToTensor(), 
                T.Normalize(mean=(0.5, 0.5, 0.5), std=(0.5, 0.5, 0.5))])
```

<br />
<br />
<p align="center">
    <img src="https://user-images.githubusercontent.com/50860347/147412786-183da6b0-990f-4016-9f2e-0719d8066f5b.png" style="width: 100%"/>
<p>

<br />
