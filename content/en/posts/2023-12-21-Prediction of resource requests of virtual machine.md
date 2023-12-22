---
title: Prediction of resource requests of virtual machine
date: 2023-12-21
author: Zhongzhi Li
slug: first-post
draft: false
toc: true
categories:
  - test
tags:
  - article
  - English
---

{{<block class="reminder">}}

{{<end>}}

## Problems that may be faced in VM request sequence prediction
Various types of virtual machines

May be requested in batches

Additional requirements (fault domain)

Potential for sudden bursts (diversity of tenant requests)

## Current research status of VM sequence generation

<p style="text-align: justify;">VM sequence generation is a key root technology that affects many key downstream businesses such as simulation platforms and capacity expansion recommendations. Some current research methods mainly include LSTM-based timing learning methods, Bootstrap-based historical real sequence resampling methods, and clustering-based resampling methods. Sampled data-driven approach. The advantage of the above method is that it can highly restore history and has good generalization and abstraction capabilities. However, the disadvantages are decoupled prediction (relying on basic business input), no information system, low model complexity, and low predictability. Based on the current technological development, forecasting large models is a feasible direction for forecasting. It is a complex information system modeling problem. The focus is on using Transformer-based large models to integrate business information from various sources and build a forecasting model with a complex information ecology.</p>

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231221-1.png" width="600">}}

## Challenges and difficulties in VM sequence generation

<p style="text-align: justify;">First of all, the prediction problem has true random uncertainty, which changes the nature of the problem compared to the traditional NLP large model with rigorous and complete semantic logic. The focus of the true randomness problem is that the information (the basis of the model space) is complex and difficult to represent, rather than pure model complexity. Unless the VM sequence essentially follows something with strict logic like natural language, data induction from an infinite dimensional numerical space may be a false proposition. The feasible proposition is how to integrate large-scale, effective NLP information with complete semantic logic into the numerical fitting and sequence generation process to build a complex prediction ecosystem with multi-modal information integration.</p>

## Proposed method

<p style="text-align: justify;">On the one hand, you can use the original GPT model based on business data Finetune to create a Chat-business numerical Fusion GPT model that adapts to the business, or simply combine macro/micro statistical analysis to make a Prompt Q&A model Embed with Complex information knowledge. On the other hand, consider blending static and time-varying variables together. Among them, static variables refer to variables that do not change with time, such as the physical location of the virtual machine, while time-varying variables refer to variables that change with time, which include time-varying variables that can be observed in the past (changes in historical VM request resources) and Time-varying variables in the future are known a priori (both the past and the future are known, such as holidays, company development trends, etc.).</p>

[The Reference articles](https://pdf.sciencedirectassets.com/271676/1-s2.0-S0169207021X00040/1-s2.0-S0169207021000637/main.pdf?X-Amz-Security-Token=IQoJb3JpZ2luX2VjEIX%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEaCXVzLWVhc3QtMSJIMEYCIQDDXCMeqQll%2B7Vuaqsipg0SGThZZZEtPmJU%2BzQnG7tiNAIhAKiQhOFj74d5GCe2jeczOZw0yJrvU7WBF95Y3qJkyv2cKrsFCP7%2F%2F%2F%2F%2F%2F%2F%2F%2F%2FwEQBRoMMDU5MDAzNTQ2ODY1IgzPxnG7W16IyqyQqXwqjwXkyW%2FyQxoLma8YUgxHes4Bi3k69M%2FS9QkabpMg8N%2F4dk5lCThiQuyns1QqF%2BvG%2Bmz3h0B3MgtJaA4PFxwKe5DjohvUTawG8EzPb93Wsmicmw0C6nUWZDEkciFCaphYKXWIF50xxCCY%2Fm0fxDRVhH8gKxTNXVXu0rkKY9MELBpz0E46HrquO1fqetTVPLLd%2F2vi5nn1nen0rIHtIuaRM0Oyyv84ejmcRwPEx2rNo74VZqih%2FuK96KgQTB7BCHPSy3ZxAybWFLapFhG8ISWytpDQ36gkYmNMxssdXQmkUbM%2FU9NRKOJF8BDqrpVbnvWDTA8gTOtMNrbtX0v3igCke2mJEhUy%2FRaqhFUNKCnfmUKdFX9zgdwQYXC273GUoIo8WJUAcB50mRCdPPvSB6LWUiGEzsndeyPXJdUHcdp9YkO3CcIcdF8Q9vTNQh%2FKpbn2aVkig9ZgKt5izHom5ItMGOIJb6RrHo8QuULy%2BM%2FC76cKRQEIXfjwpERGqxSCRGvFfA3anHlxZ828j3G0E5dKuybCXRng4prBAxY6OypXP%2FTrMbpJk9DfzkmGwBCsD3TjkoiI1N5h1236W9MA9TDgWE4RH7I5vQLWx%2FohXsntZBdCzs%2BIUio4RM97o3s%2FJsoHhqY4HfGXwiKf0MnPI8dDP39EI8xDDoFFWSKNf2x0CN%2FUuArbXIBcRfrhSxnbEo6CFokRxR56dj0VgsbvRAfjoqBsIEef4CksgRtdx7niDBkpAaFN6m%2BGSQIRmOOTj5HwWIu9GTcX7I7ohMVSGob0E6skhWjqOCIzzQQC41vMRA9ZXf10w237DGy%2FfKHTWS1AOWULNoQzvHGhK9GcaPoGJM5Cuu%2BQrzN3By4VgJMuZKn8MKuFj6wGOrABKJn3oTnBoS1PSNaaYHfz37r7gjqsI5LbjXGLCsRvPdltO8fwbz6Q9LCxAg0VskAx%2BMTUTRCS8XXRfOfqZOhkDBHJZbnPEu%2FUCdaCmI1OhbEriaG49MSbNbSUpI7HhBUfA32yHGYTx4c67ag4PYry%2BlXBGywxISVa7kKoslgJ60IzAxpTt5a6zqu%2FpKqgCpaJR4EcfQ1%2BeKNCK47PHKgKcQnUZ6hxHAVsLWawZE2aGMo%3D&X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Date=20231221T060953Z&X-Amz-SignedHeaders=host&X-Amz-Expires=300&X-Amz-Credential=ASIAQ3PHCVTYVNXP4ZEE%2F20231221%2Fus-east-1%2Fs3%2Faws4_request&X-Amz-Signature=c0699eac7d0f60393d1fd20b265bb9c2e8b9d916a036e0edada26f5f3a2af913&hash=3bc8c357ff0ae5c68b784f5b76e7d5f9b058e79fef7f9240b97564e7671e33a8&host=68042c943591013ac2b2430a89b270f6af2c76d8dfd086a07176afe7c76c2c61&pii=S0169207021000637&tid=spdf-3c186525-db41-4667-b9fb-e254c91bf56d&sid=e462ebf375e077433c6833452f3df358528egxrqa&type=client&tsoh=d3d3LnNjaWVuY2VkaXJlY3QuY29t&ua=0e0b5e52590504515654&rr=838df5726a735dea&cc=hk)

{{<figure src="https://raw.githubusercontent.com/zhongzhili/zhongzhili.github.io/master/content/en/fig/20231221-2.png" width="600">}}








