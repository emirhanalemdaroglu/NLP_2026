---
language: fr
tags:
- camembert
- fill-mask
- tensorflow
license: mit
---

# CamemBERT Deneme Modeli (TensorFlow)

Bu model, NLP Dersi Bölüm 4 uygulaması kapsamında Hugging Face Hub'a yüklenmiştir.

## Model Açıklaması
- **Geliştirici:** Emirhan Alemdaroğlu
- **Temel Model:** `camembert-base` (TensorFlow)
- **Görev:** Maskeli Dil Modelleme (Fill-Mask)
- **Dil:** Fransızca
- **Çatı:** TensorFlow / Keras

## Kullanım Örneği
```python
from transformers import pipeline

mask_filler = pipeline("fill-mask", model="emirhanalemdaroglu/dummy-camembert-tf")
print(mask_filler("Le camembert est <mask /> :)"))
```
