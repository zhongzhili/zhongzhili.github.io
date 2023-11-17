---
title: "{{ replace .TranslationBaseName "-" " " | Zhongzhi }}"
date: {{ .Date }}
author: "{{ index $.Site.Params.lang.author $.Section}}"
slug:
draft: false
toc: false
---
