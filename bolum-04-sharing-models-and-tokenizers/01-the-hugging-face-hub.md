# Hugging Face Hub

[Hugging Face Hub](https://huggingface.co/) –- ana web sitemiz –- herkesin yeni, en son teknoloji (state-of-the-art) modelleri ve veri kümelerini keşfetmesini, kullanmasını ve bunlara katkıda bulunmasını sağlayan merkezi bir platformdur. Halka açık 10.000'den fazla modeli barındırır. Bu bölümde modellere odaklanacağız ve veri kümelerine 5. Bölüm'de (Chapter 5) göz atacağız.

Hub'daki modeller 🤗 Transformers veya sadece NLP ile sınırlı değildir. Örnek vermek gerekirse NLP için [Flair](https://github.com/flairNLP/flair) ve [AllenNLP](https://github.com/allenai/allennlp), konuşma (speech) için [Asteroid](https://github.com/asteroid-team/asteroid) ve [pyannote](https://github.com/pyannote/pyannote-audio), ve görüntüleme (vision) için [timm](https://github.com/rwightman/pytorch-image-models) modelleri mevcuttur.

Bu modellerin her biri, sürüm kontrolüne (versioning) ve tekrarlanabilirliğe (reproducibility) olanak tanıyan bir Git deposu olarak barındırılmaktadır. Hub'da bir model paylaşmak, modeli topluluğa açmak ve onu kolayca kullanmak isteyen herkesin erişimine sunmak anlamına gelir. Bu da kullanıcıların kendi modellerini eğitmelerine gerek kalmadan model paylaşımını ve kullanımını basitleştirir.

Buna ek olarak, Hub'da bir model paylaşmak, o model için barındırılan (hosted) bir Çıkarım API'sini (Inference API) otomatik olarak kullanıma açar (deploy). Topluluktaki herkes, özel girdiler (custom inputs) ve uygun araçları (widgets) kullanarak modeli doğrudan kendi sayfasında test etmekte özgürdür.

En iyi yanı, Hub'daki herhangi bir halka açık modeli paylaşmanın ve kullanmanın tamamen ücretsiz olmasıdır! Modelleri gizli (private) olarak paylaşmak isterseniz [Ücretli planlar](https://huggingface.co/pricing) da mevcuttur.

Aşağıdaki video Hub'da nasıl gezineceğinizi göstermektedir:

[![Hugging Face Hub Gezinti Videosu](https://img.youtube.com/vi/XvSGPZFEjDY/0.jpg)](https://www.youtube.com/watch?v=XvSGPZFEjDY)

*(Videoyu izlemek için yukarıdaki görsele veya [bu bağlantıya](https://www.youtube.com/watch?v=XvSGPZFEjDY) tıklayabilirsiniz.)*

Hugging Face Hub'da depolar oluşturup yöneteceğimizden dolayı bu kısmı takip edebilmek için bir huggingface.co hesabına sahip olmanız gereklidir: [hesap oluştur](https://huggingface.co/join)
