---
layout: post
title: "PaliGemma"
author: "Tim"
tags: Paper Multimodal Image Text
excerpt_separator: <!--more-->
mermaid: true
hidden: false
---

A treasure trove of findings in the multimodel text and image space
<!--more-->

This paper lays out in quite some detail what works when it comes to training the latest generation of multimodal text and image models.

Looking back over the last 5 years, many have tried different ways to incorporate the two modalities to enable a model that can deal in both language and images in either the same representation space, or the marrying of specialist models to bridge the gap. The authors sum up the progression:

| Generation        | Key Models                                     | Description                                                                                                                                        | Key Features                                                                               |
| ----------------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| First Generation  | CLIP, ALIGN, ConVIRT, VirTex                   | Extension of large-scale classification pretraining to leverage web data without human labeling, using caption embeddings from language encoders.  | Replaces fixed class sets with caption embeddings, uses language encoders similar to BERT. |
| Second Generation | Generative encoder-decoder models (akin to T5) | Unification of captioning and question-answering tasks via generative encoder-decoder modeling, leveraging progress in generative language models. | Combines captioning and QA tasks, often backed by generative language model advancements.  |
| Scaled-Up Models  | Flamingo, BLIP-2, PaLI                         | Further scaling of second-generation models.                                                                                                       | Enhanced capabilities and performance through scaling up.                                  |
| Recent Advances   | Gemini, GPT-4, Moondream                       | Introduction of “instruction tuning” to make raw models more user-friendly, along with systematic studies to identify important factors in VLMs.   | Instruction tuning for improved usability, systematic studies on VLM effectiveness.        |

## The Google Journey

The Google team go through the progress they have made in the space, with explicit reference to the size and architectures used along the way

<div class="mermaid" style="float: right; margin-left: 20px;">
%%{init: {'theme': 'base', 'themeVariables': { 'fontFamily': 'monospace'}}}%%
graph TD
A[<strong>PaLI</strong><br><font size=2>17B parameters<br>Image: ViT-e 4B<br>Text: mT5-XXL 13B<br> </font>]
--> B[<strong>PaLI-X</strong><br><font size=2>54B parameters<br>Image: ViT-22B<br>Text: 32B UL2<br> </font>]
A --> C[<strong>PaLM-E</strong><br><font size=2>540B+ parameters<br>Image: ViT-22B<br>Text: 540B PaLM<br> </font>]
B --> D[<strong>PaLI-3</strong><br><font size=2>5B parameters<br>Image: 2B ViT-G/14<br>Text: 3B UL2<br> </font>]
C --> D
D --> E[<strong>PaLI-Gemma</strong><br><font size=2> < 3B parameters<br>Image: 400M SigLIP<br>Text: 2B Gemma<br> </font>]

style A fill:#69e375,stroke:#333,stroke-width:2px
style B fill:#fd9b32,stroke:#333,stroke-width:2px
style C fill:#fd9b32,stroke:#333,stroke-width:2px
style D fill:#e56be5,stroke:#333,stroke-width:2px
style E fill:#5dedeb,stroke:#333,stroke-width:2px
</div>

## PaLI-Gemma
PaLI-Gemma is the latest model in Google's pathway of multimodal advancements. It features fewer than 3 billion parameters, with a SigLIP image encoder (400M) and Gemma text model (2B). This model stands out for its efficiency and performance across various tasks.

The Models are trained to be a versatile and broadly knowledgeable base model that is effective to transfer, the base models need to be transferred to serve their intended final purpose. The tasks that the models are intended to transfer well on are: Image Classification, Captioning, Visual QA, Dialogue. There is scope for further tasks which are not necessarilty text output (think detection, instance segmentation, panoptic segmentation, depth prediction, colorization..) but they are not developed here.

# Training In Stages

The training of PaliGemma follows the same steps as previous PaLI models, with only small modifications. Training consists of several stages

- Stage0: Unimodal pretraining - we use existing off-the-shelf components.
- Stage1: Multimodal pretraining - long pretraining on a carefully chosen mixture of multimodal tasks. Notably, nothing is frozen.
- Stage2: Resolution increase - short continued pretraining at higher resolution.
- Stage3: Transfer - turn the base model into a task-specific specialist.

The separation of training into these phases is key: 
## Stage 0 
Takes off the shelf single modality Models: 
    - SigLIP for the Image encoder 
    - Gemma 2B for the Language Model

### Tokens
- The image is passed through the image encoder, which turns it into a sequence of N<sub>img</sub> tokens.
- The text is converted into N<sub>txt</sub>  tokens using Gemma’s SentencePiece tokenizer
- The text tokens are embedded with Gemma’s vocabulary embedding layer.
- The image tokens are projected with the (zero initialized) linear projection.
- The sequence of input tokens to the decoder is created.

## Stage 1
Combines unimodal models and trains them on diverse vision-language tasks, aiming to create a versatile base model rather than just aligning modalities. Unlike common practice, PaliGemma doesn't freeze the image encoder during this stage, allowing it to learn spatial and relational understanding. To mitigate potential degradation, a slow linear warm-up is used for the image encoder's learning rate. The model is trained at 224px resolution with 256 image tokens and 128 text tokens for 1 billion examples, ensuring a broad coverage of visual knowledge, concepts, cultures, and languages.


## Stage 2
Focuses on increasing image resolution to enhance performance on tasks requiring finer visual details. The model is trained on two additional checkpoints: 448x448 and 896x896 pixel resolutions. This stage uses fewer examples but increases information density, with 50M examples for 448px and 10M for 896px. It maintains the same task mixture as Stage 1 but emphasizes high-resolution tasks and extends text sequence length to 512 tokens. This allows the model to handle more complex visual tasks like detailed object detection, segmentation, and text reading in images, addressing the growing recognition of resolution importance in vision-language models.

## Stage 3
Focuses on transfer learning, adapting the pre-trained model (available in 224px, 448px, and 896px resolutions) to specific tasks or use cases. This stage involves fine-tuning the model for various applications, from specialized tasks like COCO Captions or Video Captioning to more general instruction or chat tuning. The transfer process uses a unified recipe with adjustable hyper-parameters, including resolution, epochs, learning rate, and dropout. PaliGemma demonstrates versatility by adapting to tasks involving multiple images or video frames, encoding them separately and concatenating the tokens. This stage showcases the model's effectiveness across academic benchmarks and its potential for broader applications beyond standard tasks.

### Fine Tuning Stage 3 Recipe
In decreasing order of importance

| Parameter          | Values                         |
| ------------------ | ------------------------------ |
| Resolution         | **224**, 448, 896              |
| Epochs             | **1, 3, 10**, 30, 100          |
| Learning-rate      | 3e-5, **1e-5**, 3e-6           |
| Label-smoothing    | **0.0**, 0.1, 0.3              |
| Dropout in the LLM | **0.0**, 0.1, 0.3              |
| Weight decay       | **0.0** or 0.1 × learning-rate |
| Freeze ViT         | **false**, true                |
| Beam-search        | May benefit captioning         |

Recommended initial attempt value in bold.

# Ablations
The huge Value in this paper is the density of information provided in the ablations, here is where the treasure lies


    <p>Key observations from ablation studies include:</p>

    <ol>
        <li>Prefix-LM with task-prefix supervision on suffix tokens is effective for VLM pretraining.</li>
        <li>New token initialization with small Gaussian noise performs better than matching pretrained embeddings' average.</li>
        <li>Freezing different parts during pretraining impacts performance differently:
            <ul>
                <li>Not freezing any part yields the best results.</li>
                <li>Freezing the language model significantly hurts performance.</li>
            </ul>
        </li>
        <li>Linear connectors outperform MLP connectors slightly.</li>
        <li>Using a SigLIP image encoder is more sample-efficient than raw image patches.</li>
        <li>Higher resolution generally improves performance due to increased information content and model capacity.</li>
        <li>Separate checkpoints for different resolutions are recommended over user-specified resolutions alone.</li>
        <li>Stage-specific mixture re-weighting helps slightly but isn't crucial for base models intended for fine-tuning.</li>
        <li>Model stability shows that most tasks can achieve near full-data scores even with limited examples (e.g., within 10% using only 4k examples).</li>
        <li>Annotations in images work as well as textual prompts for indicating specific elements to be captioned or analyzed.</li>
    </ol>