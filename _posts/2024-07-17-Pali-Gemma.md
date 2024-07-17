---
layout: post
title: "PaliGemma"
author: "Tim"
tags: Paper Multimodal Image Text
excerpt_separator: <!--more-->
hidden: false
---

A treasure trove of findings in the multimodel text and image space
<!--more-->

This paper lays out in quite some detail what works when it comes to training the latest generation of multimodal texk and image models.

Looking back over the last 5 years, many have tried different ways to incorporate the two modalities to enable a model that can deal in both language and images in either the same representation space, or the marrying of specialist models to bridge the gap

| Generation        | Key Models                                     | Description                                                                                                                                        | Key Features                                                                               |
| ----------------- | ---------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| First Generation  | CLIP, ALIGN, ConVIRT, VirTex                   | Extension of large-scale classification pretraining to leverage web data without human labeling, using caption embeddings from language encoders.  | Replaces fixed class sets with caption embeddings, uses language encoders similar to BERT. |
| Second Generation | Generative encoder-decoder models (akin to T5) | Unification of captioning and question-answering tasks via generative encoder-decoder modeling, leveraging progress in generative language models. | Combines captioning and QA tasks, often backed by generative language model advancements.  |
| Scaled-Up Models  | Flamingo, BLIP-2, PaLI                         | Further scaling of second-generation models.                                                                                                       | Enhanced capabilities and performance through scaling up.                                  |
| Recent Advances   | Gemini, GPT-4, Moondream                       | Introduction of “instruction tuning” to make raw models more user-friendly, along with systematic studies to identify important factors in VLMs.   | Instruction tuning for improved usability, systematic studies on VLM effectiveness.        |



