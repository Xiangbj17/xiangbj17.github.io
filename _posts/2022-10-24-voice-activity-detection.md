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

|                             Name                             | Detail                                                       | from       | get  |
| :----------------------------------------------------------: | ------------------------------------------------------------ | ---------- | ---- |
| French partition<br /> (cv-corpus-7.0-2021-07-21) <br />of the Common Voice (CV) corpus | 410k utterances<br />from 600 speakers<br />duration of 16.42 hours<br />provided with segmented speech utterances | 2209.11061 |      |
|                                                              |                                                              |            |      |
|                                                              |                                                              |            |      |

# Evaluation Indicators

1. AUC (Area Under the Curve)
   - https://www.youtube.com/watch?v=OAl6eAyP-yo



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



# Teacher_student_2105.04065

