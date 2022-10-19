---
title: "MCP: Self-supervised Pre-training for Personalized Chatbots with Multi-level Contrastive Sampling"
collection: publications
permalink: /publication/2022-10-21-paper-title-MCP
excerpt: 'This paper is about the Personalized Chatbots.'
date: 2022-10-21
venue: 'Findings of EMNLP'
paperurl: 'https://arxiv.org/abs/2210.08753'
citation: "Huang, Z., Dou, Z., Zhu, Y., & Ma, Z. (2022). MCP: Self-supervised Pre-training for Personalized Chatbots with Multi-level Contrastive Sampling. arXiv. https://doi.org/10.48550/arXiv.2210.08753. <i>Findings of EMNLP 2022</i>. 1(1)."
---

Personalized chatbots focus on endowing the chatbots with a consistent personality to behave like real users and further act as personal assistants.
Previous studies have explored generating implicit user profiles from the user's dialogue history for building personalized chatbots.
However, these studies only use the response generation loss to train the entire model, thus it is prone to suffer from the problem of data sparsity.
Besides, they overemphasize the final generated response's quality while ignoring the correlations and fusions between the user's dialogue history, leading to rough data representations and performance degradation.
To tackle these problems, we propose a self-supervised learning framework MCP for capturing better representations from users' dialogue history for personalized chatbots.  
Specifically, we apply contrastive sampling methods to leverage the supervised signals hidden in user dialog history, and generate the pre-training samples for enhancing the model.
We design three pre-training tasks based on three types of contrastive pairs from user dialogue history, namely response pairs, sequence augmentation pairs, and user pairs.
We pre-train the utterance encoder and the history encoder towards the contrastive objectives and use these pre-trained encoders for generating user profiles while personalized response generation.
Experimental results on two real-world datasets show a significant improvement in our proposed model MCP compared with the existing methods.

[Download paper here](https://arxiv.org/pdf/2210.08753.pdf)

Recommended citation: Huang, Z., Dou, Z., Zhu, Y., & Ma, Z. (2022). MCP: Self-supervised Pre-training for Personalized Chatbots with Multi-level Contrastive Sampling. arXiv. https://doi.org/10.48550/arXiv.2210.08753 <i>Findings of EMNLP</i>. 1(1).