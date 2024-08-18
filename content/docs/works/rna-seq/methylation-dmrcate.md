---
author: ["kinda"]
title: "[알고리즘분석] DMRcate"
date: "2024-08-16"
tags: ["연구", "알고리즘분석"]
categories: ["연구"]
series: ["알고리즘분석"]
---

DMRcate 분석 파이프라인의 워크플로우 정리.

## 1. 데이터 로드

사용된 데이터셋은 1~100만 bp 길이의 유전체상에 존재하는 1000개의 CpG 데이터이다. 총 10개의 샘플이 존재한다.

```python
methylation_data
```
