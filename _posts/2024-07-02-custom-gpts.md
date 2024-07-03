---
layout: post
title: "Custom GPTs"
author: "Tim"
tags: Tutorial GPT Prompting
excerpt_separator: <!--more-->
---

Creating custom GPTs with the latest version of GPT4o `gpt-4o-2024-05-13` can be a frustrating, here are some thoughts I shared with colleagues who were getting some odd behaviours
<!--more-->

When you need your GPT to interact with files you have given it, The GPT will call python functions in its code interpreter. Like many  developers, the code interpreter seems to take some shortcuts. Shortcuts taken by code interpreter can have some serious downstream implications to your GPTs ability to reason that you should be aware of.

## Files
Loading in a `.csv` file, the code interpreter uses a library called pandas. Pandas expects the column names to be in the first row of your csv, and it makes the GPT’s job a little easier if you can make these column names relevant to the data contained in that column e.g. “customer_feedback” vs “Q21”.
If renaming your columns is not possible, it is worth adding a little index in your system prompt if you know there is specific data that is useful.

## Pandas
We can see the functions called within the code interpreter, so anything the GPT is doing that you think is wrong, you’ll need to provide some instruction with reference to the correct function. I noticed that when looking at data in a column, the GPT calls a function that only sampled a small number of the rows, and then when it printed the rows, truncated the longer sentences behind an `…` character. This makes the text invisible to the GPT to be able to reason over, so watch out for things like `.head(20)`, `.sample(20)` or `[:20]` where the 20 indicates “only show me 20 examples”.
If you see these happening, instruct your GPT in the system prompt to be more thorough.

Likewise, for example when looking at text in a column, the gpt might call `table[‘customer_feedback’]` which will again cut short the printed text examples and make the hidden text unknown to the GPT - tell the GPT to call `.values` on columns it needs to read, and this will avoid truncation.

## Reasoning
The GPT tends to try and write functions to perform reasoning in the code interpreter, but they can be a little simple and hamper the true thinking ability of the GPT to reason in more abstract terms. I noticed when trying to label things the GPT created a function that went line by line and did a simple text search which will miss a lot of the nuance. I’ve found that if you ask the GPT to output text from the code interpreter, think, reason and then perform an action you can avoid most of this behaviour.

## TL;DR
A quick rule of thumb: if you can’t see information in a chat, GPT can’t either, so you need to make sure you give it the best chance of getting information out of your files, and into the chat before you can really get the best ’thinking’ out of your GPT
