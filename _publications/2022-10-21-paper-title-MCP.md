---
title: "MCP: Self-supervised Pre-training for Personalized Chatbots with Multi-level Contrastive Sampling"
collection: publications
permalink: /publication/2022-10-21-paper-title-MCP
excerpt: 'This paper is about the Personalized Chatbots.'
date: 2022-10-21
venue: 'Findings of EMNLP'
paperurl: 'https://arxiv.org/abs/2210.08753'
citation: "Zhaoheng Huang, Zhicheng Dou, Yutao Zhu, and Zhengyi Ma. 2022. MCP: Self-supervised Pre-training for Personalized Chatbots with Multi-level Contrastive Sampling. In Findings of the Association for Computational Linguistics: EMNLP 2022, pages 1030–1042, Abu Dhabi, United Arab Emirates. Association for Computational Linguistics."
---

## Abstract

Personalized chatbots focus on endowing the chatbots with a consistent personality to behave like real users and further act as personal assistants.
Previous studies have explored generating implicit user profiles from the user's dialogue history for building personalized chatbots.
However, these studies only use the response generation loss to train the entire model, thus it is prone to suffer from the problem of data sparsity.
Besides, they overemphasize the final generated response's quality while ignoring the correlations and fusions between the user's dialogue history, leading to rough data representations and performance degradation.
To tackle these problems, we propose a self-supervised learning framework MCP for capturing better representations from users' dialogue history for personalized chatbots.  
Specifically, we apply contrastive sampling methods to leverage the supervised signals hidden in user dialog history, and generate the pre-training samples for enhancing the model.
We design three pre-training tasks based on three types of contrastive pairs from user dialogue history, namely response pairs, sequence augmentation pairs, and user pairs.
We pre-train the utterance encoder and the history encoder towards the contrastive objectives and use these pre-trained encoders for generating user profiles while personalized response generation.
Experimental results on two real-world datasets show a significant improvement in our proposed model MCP compared with the existing methods.

## Meta-review
This paper uses contrastive objectives to pre-train multiple models to encode user profile of chatbot dialogue and use the profile to generate personalized responses. 
All reviewers agree that the idea is interesting and the paper is well written. The novelty of applying contrastive training to chatbot and the lack of experimental comparisons to systems with pre-trained models are **two major concerns**. 
The authors gave detailed explanation to the reviewers' concerns. 
I believe that using contrastive training in the specific area of chatbot personalization has **sufficient novelty** although the idea is not new. 
And the comparison to systems with pre-trained models is indeed important since most real world chatbot systems will involve PLM.

[Download paper here](/files/2022.findings-emnlp.73.pdf)