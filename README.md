# Deep Learning Final Project

## Group members who worked on this project
- Jumber Phkhakadze
- Irakli Diasamidze
- Devi Chikovani

## Dataset
- Dataset name: Flickr8k
- Data type: captions.txt having (image, caption) pairs, all images provided in Images folder

## Preprocessing
- Tokenization was previously done with built in BPE tokenizer which is a subword type tokenizer, but now we have pretrained Glove embeddings for captions which is better in general.
- Padding with collatefn function, needed because we need to stack all images and captions together to input them afterwards in the model, for doing that we need vectors gotten from tokenization of captions to have same lengths, so we use padding to max length vector in each batch
- Normalization of images, better for CNN that we use in the model to learn images faster, more stable and avoid exploding gradients
- Image augmentation was avoided. Applying a transformation to an image may require changing the caption assigned to it. E.g., if the caption mentions the color of an object and color shift is applied to the image, the caption needs to changed; if the caption mentions the direction of some object and rotation is applied to the image, the caption needs to be changed. There is no simple way of changing the caption given the image and the transformation

## Model Architecture
- Encoder: pretrained CNN (ResNet)
- Projection layer: Transforms Restnet output features to match embedding layer size of decoder
- Decoder: Transformer decoder (3 layers)
- Number of params: 44 million total 

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


| Epoch | Train Loss | Train PPL | Val Loss | Val PPL | LR (CNN) | LR (Transformer) |
|-------|------------|-----------|----------|---------|----------|------------------|
| 01 | 4.7184 | 111.99 | 4.0327 | 56.41 | 0.000010 | 0.000100 |
| 02 | 3.8730 | 48.08 | 3.7273 | 41.56 | 0.000010 | 0.000100 |
| 03 | 3.6124 | 37.06 | 3.5654 | 35.36 | 0.000010 | 0.000100 |
| 04 | 3.4461 | 31.38 | 3.4749 | 32.29 | 0.000010 | 0.000100 |
| 05 | 3.3214 | 27.70 | 3.3995 | 29.95 | 0.000010 | 0.000100 |
| 06 | 3.2215 | 25.07 | 3.3607 | 28.81 | 0.000010 | 0.000100 |
| 07 | 3.1363 | 23.02 | 3.3273 | 27.86 | 0.000010 | 0.000100 |
| 08 | 3.0624 | 21.38 | 3.2977 | 27.05 | 0.000010 | 0.000100 |
| 09 | 2.9942 | 19.97 | 3.2847 | 26.70 | 0.000010 | 0.000100 |
| 10 | 2.9337 | 18.80 | 3.2683 | 26.27 | 0.000010 | 0.000100 |
| 11 | 2.8758 | 17.74 | 3.2597 | 26.04 | 0.000010 | 0.000100 |
| 12 | 2.8254 | 16.87 | 3.2595 | 26.04 | 0.000010 | 0.000100 |
| 13 | 2.7747 | 16.03 | 3.2480 | 25.74 | 0.000010 | 0.000100 |
| 14 | 2.7276 | 15.30 | 3.2489 | 25.76 | 0.000010 | 0.000100 |
| 15 | 2.6838 | 14.64 | 3.2493 | 25.77 | 0.000010 | 0.000100 |


