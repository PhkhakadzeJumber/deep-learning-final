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

## Important parameters for Train / Validation
- Loss function: Cross-Entropy Loss (padding tokens ignored)
- Optimizer: AdamW
- Learning rate:
  - separate learning rates for encoder CNN and decoder Transformer
  - lower LR for pretrained CNN to preserve learned visual features
  - higher LR for Transformer decoder to learn caption generation effectively
- Learning rate scheduler: used to adapt the learning rate during training for better convergence

## Training / Validation Loop + Important Model Details
- Images are encoded by the pretrained CNN, and the resulting visual features are projected to the decoder embedding dimension
- Projected image features are provided to the Transformer decoder as encoder memory and are used in cross-attention layers
- Tokenized caption sequences, augmented with positional encodings, are input to the decoder and processed through masked self-attention to model word order and linguistic dependencies
- In each decoder layer:
  - Self-attention learns relationships between caption tokens within the same caption sequence
  - Cross-attention aligns caption token representations (queries) with the corresponding image features (keys and values)
- Since images and captions are batched in aligned order, each caption attends only to its paired image features during cross-attention
- Query, key, and value projection matrices are learned end-to-end, enabling the model to learn how to attend to relevant visual regions for each generated word
- The decoder outputs a probability distribution over the vocabulary for next-token prediction, and captions are generated token by token until the EOS token is produced, with cross-entropy loss computed against the ground-truth captions



