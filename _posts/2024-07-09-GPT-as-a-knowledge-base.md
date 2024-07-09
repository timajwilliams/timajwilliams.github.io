---
layout: post
title: "GPTs as a Knowledge Base"
author: "Tim"
tags: Tutorial GPT API
excerpt_separator: <!--more-->
hidden: false
---

Leveraging GPTs for Effective Knowledge Base Management
<!--more-->

Notes on building effective knowledge bases as a custom GPT

### Leveraging GPTs for Effective Knowledge Base Management

In our recent meeting, we discussed guidelines and best practices for using GPT models to enhance knowledge bases. Below is a consolidated overview of the key points and recommendations from OpenAI:

#### Working with Images

- **Uploading and Reading Images:** GPT cannot open images from PDFs or external files directly. It requires images to be uploaded in standard formats such as PNG or JPG.
- **Image Output Limitations:** Due to safety considerations, GPT cannot output images directly. This is a safety measure to prevent something or other - unclear

#### Providing Instructions to GPT

- **Explicit Guidance:** Treat a GPT like a new intern. Provide detailed instructions on how to source information and include examples of good interactions. For instance, specify whether tasks should be done sequentially or in parallel.
- **Workflow Structuring:** Break down workflows into separate, manageable tasks. This can involve using different GPTs for different stages, such as knowledge gathering, processing, and final response generation. Make sure to explicitly link these tasks for better fine-tuning and outcomes.

#### Handling Tasks

- **Task Scope:** GPTs are best suited for short, focused tasks that can be completed in about five minutes. Ensure that problems are broken down into small, manageable tasks.

#### File Processing

- **CSV Files:** Files uploaded as `.csv` are processed in the code interpreter, not for semantic search. For semantic retrieval, convert files to PDF format. Text files are processed in the backend by OpenAI for semantic retrieval (this isn't mentioned in the GPT builder)


- **Text and Table Files:** Pure text files and well-structured tables are handled effectively, especially if they are under 40 pages. Larger documents are chunked and processed, which may cause inconsistencies.

#### Link Management

- **Security Concerns:** if you get your GPT to output links for more information, they get sanitized to prevent the GPT from generating harmful links. If necessary, instruct GPT to put a link in a code block so that it is not clickable, reducing the risk. GPTs are happy to copy links verbatim from your source data because they are technically not generating them. I guess there must be some legalese behind this decision but its not a dealbreaker.

#### Semantic Search and Information Processing

- **Context Management:** The model processes information contextually, up to a 128k token limit. Retrieval-Augmented Generation (RAG) makes the model more factual and relevant by retrieving necessary information, and using that as reference when generating.
- **Concept-Based Retrieval:** Semantic search can surface relevant document chunks based on concepts rather than keywords, enhancing the relevance of the information retrieved.
- **Context Stuffing:** If you need your GPT to be aware of something, you need to get the information as text into the conversation history. the easiest way is to get files or data to be read out directly by the conversation.
- **Avoiding Context Stuffing:** If you use custom actions to load data from an external source, you will need to ensure context stuffing. Semantic search helps avoid this by retrieving and integrating information more seamlessly - only using relevant chunks of your data vs using all of your data.
- **Semantic Search:** Is hidden from view and not customisable, but we are told it exists..

#### Workflow Integration

- **Separate GPTs in a Chain:** Workflows can be broken down to operate separately, with different GPTs invoking each other in a chain. This requires making the linkage explicit.
  - **Fine-Tuning:** Each task is easier to fine-tune individually.
  - **Improved Outcomes:** This approach can improve the final outcome.
  - **Examples:** Use one GPT for knowledge gathering, another for processing, and a third for generating the final response.
  - **Mentioning GPTs:** You can @ mention other GPTs, though this currently works only if the other GPT is saved in your sidebar.


#### Proof of Concept (POC)

- **Using GPTs for POC:** Before committing engineering resources, use GPTs to create a proof of concept. This approach can save time and resources while demonstrating the potential value and effectiveness of the proposed solution.


