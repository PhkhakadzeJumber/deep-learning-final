# Deep Learning Final Project

## Group members who worked on this project
- Jumber Phkhakadze
- Irakli Diasamidze
- Devi Chikovani

## Dataset
- Dataset name: Flickr8k
- Data type: captions.txt having (image, caption) pairs, all images provided in Images folder

## Preprocessing
- Tokenization done with built in BPE tokenizer which is a subword type tokenizer
- Padding with collatefn function, needed because we need to stack all images and captions together to input them afterwards in the model, for doing that we need vectors gotten from tokenization of captions to have same lengths, so we use padding to max length vector in each batch
- Normalization of images, better for CNN that we use in the model to learn images faster, more stable and avoid exploding gradients

## Model Architecture
- Encoder: pretrained CNN (ResNet)
- Preparation layer: Transforms Restnet output features to match embedding layer size of decoder
- Decoder: Transformer decoder (3 layers)

## Important parameters for train / val loop
- Loss function: Cross-Entropy Loss
- Optimizer: AdamW
- Learning rate: Adaptive LR

## Training / Validation loop

