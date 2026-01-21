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

## Results
- To evaluate the performance of our model model well and how well it generalizes, we additionally calculated perplexity metric (dependent on loss) and rough estimate BLEU-1 score, measuring the overlap of single words between generated caption and real true caption additionally (rought estimate because it takes First element in each batch, we did that because if we had calculated real BLEU-1, meaning for all elements, it would have taken a lot more additional time)  
- We take the best model overall (measured by having the least validation loss)
- Taking all these things into consideration, the final model that we have gotten has stats: Train Loss: 2.7064, Train PPL: 14.98, Val Loss: 3.3201, Val PPL: 27.66, Val BLEU-1 (approx): 0.3899
- This result suggests that even though our model is very good, it seems that there is quite some room for improvement (val loss of 3.32 is not small obviously), the problem is the data size, to improve these results we need larger data, flicker8k is a small dataset when talking about image captioning task and does not let us produce better results, the result we got, tried many things afterwards to improve but failed, we thought was the limitation of this dataset.
- to support our concerns with the real evidence, we ran the same code for flicker30k dataset and got much better results (val loss) proving our point.

📊 Epoch [1/15]
   Train Loss: 4.4325
   Train PPL:  84.15
   Val Loss:   3.8031
   Val PPL:    44.84
   Val BLEU-1 (approx): 0.3977
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [2/15]
   Train Loss: 3.5952
   Train PPL:  36.42
   Val Loss:   3.5472
   Val PPL:    34.72
   Val BLEU-1 (approx): 0.3753
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [3/15]
   Train Loss: 3.3060
   Train PPL:  27.28
   Val Loss:   3.4299
   Val PPL:    30.87
   Val BLEU-1 (approx): 0.3809
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [4/15]
   Train Loss: 3.1050
   Train PPL:  22.31
   Val Loss:   3.3708
   Val PPL:    29.10
   Val BLEU-1 (approx): 0.3862
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [5/15]
   Train Loss: 2.9498
   Train PPL:  19.10
   Val Loss:   3.3375
   Val PPL:    28.15
   Val BLEU-1 (approx): 0.4185
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [6/15]
   Train Loss: 2.8203
   Train PPL:  16.78
   Val Loss:   3.3247
   Val PPL:    27.79
   Val BLEU-1 (approx): 0.4159
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [7/15]
   Train Loss: 2.7064
   Train PPL:  14.98
   Val Loss:   3.3201
   Val PPL:    27.66
   Val BLEU-1 (approx): 0.3899
   LR (CNN):      0.000010
   LR (Transformer): 0.000100
✅ Best model saved!

📊 Epoch [8/15]
   Train Loss: 2.6053
   Train PPL:  13.54
   Val Loss:   3.3207
   Val PPL:    27.68
   Val BLEU-1 (approx): 0.3947
   LR (CNN):      0.000010
   LR (Transformer): 0.000100

📊 Epoch [9/15]
   Train Loss: 2.5089
   Train PPL:  12.29
   Val Loss:   3.3264
   Val PPL:    27.84
   Val BLEU-1 (approx): 0.3879
   LR (CNN):      0.000010
   LR (Transformer): 0.000100

📊 Epoch [10/15]
   Train Loss: 2.4216
   Train PPL:  11.26
   Val Loss:   3.3398
   Val PPL:    28.21
   Val BLEU-1 (approx): 0.3984
   LR (CNN):      0.000010
   LR (Transformer): 0.000100

📊 Epoch [11/15]
   Train Loss: 2.3396
   Train PPL:  10.38
   Val Loss:   3.3577
   Val PPL:    28.72
   Val BLEU-1 (approx): 0.3972
   LR (CNN):      0.000005
   LR (Transformer): 0.000050

📊 Epoch [12/15]
   Train Loss: 2.2122
   Train PPL:  9.14
   Val Loss:   3.3734
   Val PPL:    29.18
   Val BLEU-1 (approx): 0.3872
   LR (CNN):      0.000005
   LR (Transformer): 0.000050

📊 Epoch [13/15]
   Train Loss: 2.1622
   Train PPL:  8.69
   Val Loss:   3.3868
   Val PPL:    29.57
   Val BLEU-1 (approx): 0.3911
   LR (CNN):      0.000005
   LR (Transformer): 0.000050

📊 Epoch [14/15]
   Train Loss: 2.1172
   Train PPL:  8.31
   Val Loss:   3.4119
   Val PPL:    30.32
   Val BLEU-1 (approx): 0.3858
   LR (CNN):      0.000005
   LR (Transformer): 0.000050

📊 Epoch [15/15]
   Train Loss: 2.0765
   Train PPL:  7.98
   Val Loss:   3.4345
   Val PPL:    31.02
   Val BLEU-1 (approx): 0.3763
   LR (CNN):      0.000003
   LR (Transformer): 0.000025

✅ Training complete!
   
