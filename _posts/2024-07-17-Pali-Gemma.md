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

This paper lays out in quite some detail what works when it comes to training the latest generation of multimodal texk and image models.

Looking back over the last 5 years, many have tried different ways to incorporate the two modalities to enable a model that can deal in both language and images in either the same representation space, or the marrying of specialist models to bridge the gap. The authors sum up the progression:

| Generation        | Key Models                                     | Description                                                                                                                                        | Key Features                                                                               |
| ----------------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| First Generation  | CLIP, ALIGN, ConVIRT, VirTex                   | Extension of large-scale classification pretraining to leverage web data without human labeling, using caption embeddings from language encoders.  | Replaces fixed class sets with caption embeddings, uses language encoders similar to BERT. |
| Second Generation | Generative encoder-decoder models (akin to T5) | Unification of captioning and question-answering tasks via generative encoder-decoder modeling, leveraging progress in generative language models. | Combines captioning and QA tasks, often backed by generative language model advancements.  |
| Scaled-Up Models  | Flamingo, BLIP-2, PaLI                         | Further scaling of second-generation models.                                                                                                       | Enhanced capabilities and performance through scaling up.                                  |
| Recent Advances   | Gemini, GPT-4, Moondream                       | Introduction of “instruction tuning” to make raw models more user-friendly, along with systematic studies to identify important factors in VLMs.   | Instruction tuning for improved usability, systematic studies on VLM effectiveness.        |

## The Google Journey

The Google team go through the progress they have made in the space, with explicit reference to the size and architectures used aolng the way

<div class="mermaid">
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

