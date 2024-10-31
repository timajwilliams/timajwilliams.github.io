---
layout: post
title: "OpenAI Dev Day 2024 London"
author: "Tim"
tags: Dev LLMs OpenAI
excerpt_separator: <!--more-->
mermaid: true
hidden: false
---

Openai in 2024 and beyond
<!--more-->
​
## Main Presentations
​
OpenAI held their first non US dev day in London \- It was a great opportunity to get in the room and chat with other developers and find out what we are all building on top of OpenAI and how they are building the models and tooling to meet our needs.
​
The day kicked off with discussion and demo from Olivier Godement (head of product) and Romain Huet (head of developer experience) \- we were shown the coding abilities of o1 \-  building a London Tube status app, and a backend to control a drone live on stage from a webpage. Both done live.
​
Realtime API was the prime focus of the day \- a demonstration of adding function calls to the realtime api to allow conversational agents able to leverage Twilio to make phone calls on your behalf, and making UIs for education with voice interaction.
​
There were presentations on how structured outputs were solved through research and the engineering required for that, followed by a run through of the fine tuning process to distill smaller models from the outputs of the larger models we use in production. The pipeline can all be run in the platform.openai.com UI
​
## Startup Presentations
​
Great 5-10 minute presentations from startups like [dust.tt](https://www.dust.tt/) and [cosine.sh](https://www.cosine.sh/) and how they are building effective platforms on top of the API:
​
#### [dust.tt](https://www.dust.tt/)
​
Conversational layer on top of all data sources: demo connecting google sheets, csv and snowflake for BI use cases.
​
#### [cosine.sh](https://www.cosine.sh/)
​
Text input for agentic code modification inside a repo. Specific fine tuning process used to create training data to distil a model for the very specific git patch activity that they wanted
​
#### Klarna
​
Prompt engineer at Klarna discussed the process they use when creating the system prompts for their LLM use cases, specific application to the user history chat which allows order query and refunds.
​
## Sam's AMA Session

The day concluded with a remote AMA with Sam Altman, who spoke candidly whilst sidestepping gracefully round some of the more tough questions clearly aiming to catch a soundbite.
​
### General Questions
​
* OpenAI has no plans to steamroller products built on top of their APIs \- the cases where that has happened so far have been where companies have built around deficiencies in weaker models. Assume that models will keep improving, and build to take advantage of this otherwise your solution can only ever be short lived.  
* Anyone starting a company now should really be looking to apply AI to a specific vertical \- where it can add value to an established niche. Just aim to be the best\!
​
### Future Outlook
​
* Emphasis on reasoning capabilities being the frontier  
* 2025 push toward next-gen systems  
* Focus on unlocking economic value   
* Shift from thinking about individual models to thinking about systems  
* Models are depreciating assets, They do pay for themselves in usage, but have a shelf life. Compounding team knowledge value in the research and engineering process
​
### Leadership Perspectives
​
* Balance between day-to-day and long-term planning  
* Focus on 10x improvements over 10% gains  
* Value of experience in infrastructure (as answer to question on employing the over 30s)  
* Set a high talent bar regardless of age  
* Importance of innovation and unlocking personal potential \- This will be the focus of his book if he ever gets there
​
### Agent Development
​
* Definition: Long duration tasks with minimal supervision  
* Evolution toward “Smart Senior Coworker” collaboration model  
* Q1 2025 expected capability improvements  
* Need for better terminology than “Agentic”  
* Practical applications like massive parallelization  
* Agents are still narrowly considered e.g. why just 1 agent calling 1 restaurant to make a booking when 300 can call 300 restaurants and offer the user choice \- will be ok when restaurants have AI applied to answer calls\!
​
### Worries
​
* Ensuring that research, engineering and product roadmaps can be run effectively in parallel to intersect at the right time in the future.  
* Bets on next products \- the 51:49 decisions that reach him *because* they are the hard decisions that filter up  
* Global semiconductor supply in the top 10% of worries
​
### Praise
​
* Respect for the Cursor team and the developer tool they are building  
* Arc browser getting a shoutout.