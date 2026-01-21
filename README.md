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
- Image augmentation was avoided. Applying a transformation to an image may require changing the caption assigned to it. E.g., if the caption mentions the color of an object and color shift is applied to the image, the caption needs to changed; if the caption mentions the direction of some object and rotation is applied to the image, the caption needs to be changed. There is no simple way of changing the caption given the image and the transformation

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

## Results
- To evaluate the performance of our model well and how well it generalizes, we additionally calculated perplexity metric (dependent on loss) and rough estimate of BLEU-1 score, measuring the overlap of single words between generated caption and real true caption additionally (rough estimate because it takes first element in each batch, we did that because if we had calculated real BLEU-1, meaning for all elements, it would have taken a lot more additional time)  
- We take the best model overall (measured by having the least validation loss)
- Taking all these things into consideration, the final model that we have gotten has stats: Train Loss: 2.7064, Train PPL: 14.98, Val Loss: 3.3201, Val PPL: 27.66, Val BLEU-1 (approx): 0.3899
- This result suggests that even though our model is very good, it seems that there is quite some room for improvement (val loss of 3.32 is not small obviously), the problem is the data size, to improve these results we need larger data, flicker8k is a small dataset when talking about image captioning task and does not let us produce better results, the result we got, tried many things afterwards to improve but failed, we thought was the limitation of this dataset.


| Epoch | Train Loss | Train PPL | Val Loss | Val PPL | Val BLEU-1 | LR (CNN) | LR (Transformer) |
|-------|------------|-----------|----------|---------|------------|----------|------------------|
| 01 | 4.4325 | 84.15 | 3.8031 | 44.84 | 0.3977 | 0.000010 | 0.000100 |
| 02 | 3.5952 | 36.42 | 3.5472 | 34.72 | 0.3753 | 0.000010 | 0.000100 |
| 03 | 3.3060 | 27.28 | 3.4299 | 30.87 | 0.3809 | 0.000010 | 0.000100 |
| 04 | 3.1050 | 22.31 | 3.3708 | 29.10 | 0.3862 | 0.000010 | 0.000100 |
| 05 | 2.9498 | 19.10 | 3.3375 | 28.15 | 0.4185 | 0.000010 | 0.000100 |
| 06 | 2.8203 | 16.78 | 3.3247 | 27.79 | 0.4159 | 0.000010 | 0.000100 |
| 07 | 2.7064 | 14.98 | 3.3201 | 27.66 | 0.3899 | 0.000010 | 0.000100 |
| 08 | 2.6053 | 13.54 | 3.3207 | 27.68 | 0.3947 | 0.000010 | 0.000100 |
| 09 | 2.5089 | 12.29 | 3.3264 | 27.84 | 0.3879 | 0.000010 | 0.000100 |
| 10 | 2.4216 | 11.26 | 3.3398 | 28.21 | 0.3984 | 0.000010 | 0.000100 |
| 11 | 2.3396 | 10.38 | 3.3577 | 28.72 | 0.3972 | 0.000005 | 0.000050 |
| 12 | 2.2122 | 9.14 | 3.3734 | 29.18 | 0.3872 | 0.000005 | 0.000050 |
| 13 | 2.1622 | 8.69 | 3.3868 | 29.57 | 0.3911 | 0.000005 | 0.000050 |
| 14 | 2.1172 | 8.31 | 3.4119 | 30.32 | 0.3858 | 0.000005 | 0.000050 |
| 15 | 2.0765 | 7.98 | 3.4345 | 31.02 | 0.3763 | 0.000003 | 0.000025 |

