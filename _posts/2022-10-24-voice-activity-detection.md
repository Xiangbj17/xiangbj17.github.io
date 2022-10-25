---
title: VAD Task -- Problem definition, current methods and datasets
date: 2022-10-24 09:13:00 +0800
categories: [Research, VAD]
tags: [Lab]
render_with_liquid: false
---



# TODO

1. See if <font color=red>Wav2Vec2</font> benifits feature representing more than spectrogram
   - [Propose] Alexei Baevski, Henry Zhou, Abdelrahman Mohamed, and Michael Auli. wav2vec 2.0: A framework for self-supervised learning of speech representations. *arXiv preprint arXiv:2006.11477*, 2020.
   - [Show benifits 1]Florian Eyben, Felix Weninger, Stefano Squartini, and Björn Schuller. Real-life Voice Activity Detection with LSTM Recurrent Neural Networks and an Application to Hollywood Movies. In *Proceedings of ICASSP 2013*, pages 483–487, Vancouver, Canada, May 2013a. IEEE.
   - [Show benifits 2]Aaron Keesing, Yun Sing Koh, and Michael Witbrock. Acoustic features and neural representations for categorical emotion recognition from speech. In *Proceedings of the 22nd Annual Conference of the International Speech Communication Association, Brno, Czech Republic*, pages 3415–3419, 2021.
   - [Show robustness 1] Siddique Latif, Rajib Rana, Sara Khalifa, Raja Jurdak, Junaid Qadir, and Björn W Schuller. Deep representation learning in speech processing: Challenges, recent advances, and future trends. *arXiv preprint arXiv:2001.00378*, 2020.
   - [Show robustness 2]Aaron Keesing, Yun Sing Koh, and Michael Witbrock. Acoustic features and neural representations for categorical emotion recognition from speech. In *Proceedings of the 22nd Annual Conference of the International Speech Communication Association, Brno, Czech Republic*, pages 3415–3419, 2021.
   - <font color=green>[Train in Multiple Domain]</font>Wei-Ning Hsu, Anuroop Sriram, Alexei Baevski, Tatiana Likhomanenko, Qiantong Xu, Vineel Pratap, Jacob Kahn, Ann Lee, Ronan Collobert, Gabriel Synnaeve, et al. Robust wav2vec 2.0: Analyzing domain shift in self-supervised pre-training. *arXiv preprint arXiv:2104.01027*, 2021.

2. Check: https://vocal.com/dereverberation/voice-activity-detection/

3. Check: https://speechbrain.github.io/

   - 2106.04624 *SpeechBrain: A General-Purpose Speech Toolkit*
   - Dataset involved
   - Several tasks suggested

   

# Datasets

|             Name             | Detail                                                       | from       | get  |
| :--------------------------: | ------------------------------------------------------------ | ---------- | ---- |
| the Common Voice (CV) corpus | 410k utterances<br />from 600 speakers<br />duration of 16.42 hours<br />provided with segmented speech utterances | 2209.11061 |      |
|           Audioset           | **Clip-level Label<br />**5000h+                             | 2105.04065 |      |
|        clean Aurora 4        |                                                              | 2105.04065 |      |
|           DCASE18            |                                                              | 2105.04065 |      |
|                              |                                                              |            |      |
|                              |                                                              |            |      |
|                              |                                                              |            |      |

# Evaluation Indicators

1. AUC (Area Under the Curve)
   - https://www.youtube.com/watch?v=OAl6eAyP-yo

2. FER
3. Event-F1

# 2209.11061

CROSS-DOMAIN VOICE ACTIVITY DETECTION WITH SELF-SUPERVISED REPRESENTATIONS (*Univ. Grenoble Alpes, Inria, CNRS* Grenoble, France)

> However, all studies reported **degraded performance when testing on domains different from those used for model learning**, showing that VAD systems based on deep learning architectures fail to generalise to new domains when represented by acoustic features of the speech signal.

## Data Pre-processing

1. Speech utterances of the Common Voice dataset were **concatenated and interspersed with a pause of randomly chosen duration.**

   - with a Gaussian distribution μ = 2.22 seconds, σ = 1.83 seconds.
   - The distribution of pause duration as observed in real conversations Ringeval et al. [2013] was approximated with a Gaussian distribution

2. Further combined with four different types of **noises**, and three different levels of noise.

   - 5 types of home noise: (i) White, (ii) Ads, (iii) Music, (iv) News, and (v) Talk shows.

   - noise being multiplied by a gain (gn) :

   - $$
     g_n=10^{log(g_s)-\frac{SNR}{20}}
     $$

   -  gs is the gain of the clean speech file.

## TOTAL Data

| speech type                                 |  train  | development |  test   |
| ------------------------------------------- | :-----: | :---------: | :-----: |
| Close-talking microphone<br />crowd-sourced | 2:08:07 |   2:08:04   | 2:09:03 |

| duration          |   Ads   |  Music  |  News   | Talk sHows |
| ----------------- | :-----: | :-----: | :-----: | :--------: |
| Length (hh:mm:ss) | 1:38:43 | 1:52:09 | 1:21:04 |  1:21:27   |



## Method

### 1. Utilizing different Wav2Vec2 Models For Feature Represent

![image-20221024102641849](/Users/bajianxiang/Desktop/Before/website/assets/img/pics/image-20221024102641849.png)

### 2. VAD Model

- A GRU model, followed by a linear layer, to map the hidden size of the GRU to the number of outputs (here one)
- Tangent hyperbolic function(tanh) to map the output to the range of [−1, +1], **with −1 and +1 representing non-speech frames and speech frames, respectively.**
- Tried **fine-tuning** our W2V2 models for the task of VAD, i.e. we did not freeze any W2V2 parameter while training the VAD. (But faced convergence issues that require further investigations.)

## Results And Comments

![image-20221024105258591](/Users/bajianxiang/Desktop/Before/website/assets/img/pics/image-20221024105258591.png)

1. VAD models based on W2V2 representations are **language dependent**.
2. The W2V2 large models used here, which use a normalisation layer, **do not perform better than the smaller** W2V2 base models, which **do not use any normalisation**.



# Multitask_2103.01661

Comment: Results are not convincable and architecture is too simple, but there is still some thing useful(Like cross-attention, VAD labeling toolkit and online inference propose.)

## Basic

1. The model is firstly pre-trained with wav2vec 2.0 framework.
2. Then, ASR and VAD tasks are jointly trained with a MTL(multi-task learning) technique.
   1. To **reduce the computational cost of VAD**, we only use the output features from the bottom feature extraction module of the network architecture to perform the VAD task.
   2. This design is more consistent with human cognition process and infants’ language learning process: In **Jusczyk’s study [20]**: <u>infants firstly have the ability to detect the language sound patterns before they recognize words.</u>
3. Propose a **cross-task attention module** to learn interactive information between ASR and VAD.
4. Use **chunk-hopping mechanism** to support **online** processing.
   1. Enables the model to encode on segmented frame chunks one after another.

## Model

### The Overall Architecture

![image-20221025154122032](/Users/bajianxiang/Library/Application Support/typora-user-images/image-20221025154122032.png)

### Chunk-hopping Mechanism

![image-20221025154420868](/Users/bajianxiang/Library/Application Support/typora-user-images/image-20221025154420868.png)

​	To adapt to different real-time demands, the sizes of **chunks are randomly set from 0.5 second to 3 seconds** and the <u>spliced chunks are all fixed to 0.5 second.</u>

### Loss Function

1. For ASR: CTC Loss
2. For VAD: Cross Entropy Loss 

### Online Inference Algorithm

<img src="/Users/bajianxiang/Library/Application Support/typora-user-images/image-20221025155042423.png" alt="image-20221025155042423" style="zoom:50%;" />

## Experiments

### 2-Stages:

1. Train a **single task ASR model** by finetuning the wav2vec 2.0 pre- trained model in the first stage.
2. Train our **multi-task model** by finetuning the ASR model **from the first stage**.(Using chunk-hopping mechanism )

### Obtain the labels of VAD:

- Use the [silero-vad toolkit](https://github.com/snakers4/silero-vad) to generate annotations from speech.

### Datasets

- HKUST Mandarin Chinese conversational telephone speech recognition (HKUST)
  - HKUST consists of long conversations with speech and non-speech parts. 
- Librispeech[27] dataset
  - 1000h of training data split into ”clean-100h”, ”clean-360h” and ”other- 500h” subsets
  - development and testsets that are split into simple (“clean”) and harder (“other”) subsets. 
  - To reduce the experiment cost, we only train the model with the “clean-100h” part of the training data. 

### Wave2Vec Model

- We use publicly released pre-trained wav2vec2.0 base model
  - composed of a seven-block CNN feature ex- tracter and a 12-layer transformer encoder.
- Fine-Tuning Stage
  - follow the settings described in original Paper

### Hardware

All the experiments are performed on 4 GeForce RTX 2080 Ti GPUs.

## Results

Compare the proposed **MTL method** with the VAD system trained with **the same CNN architecture(Single Task Learning)** in the proposed model architecture.

3 Metrix:

- detection error rate (DetER): measures the fraction of time that is not attributed correctly to speech or to non-speech and is computed as:
  $$
  DetER=\frac{N_{false\_alarm}+N_{miss}}{N_{total}}
  $$

  - false alarm is the number of false positive speech predictions,
  - miss is the number of false negtive speech predictions
  - total is the total number of predictions.

- False alarm rate(FA)

- Missed detection rate(Miss)

![image-20221025160925533](/Users/bajianxiang/Library/Application Support/typora-user-images/image-20221025160925533.png)
