# 🚀 Bölüm 4: Modelleri ve Tokenizer'ları Paylaşma — Uygulama Laboratuvarı

**Takım:** NLP04 (2026 Güz)  
**Ders:** Doğal Dil İşleme (NLP)  
**Konu:** Hugging Face Hub, Model Paylaşımı, `push_to_hub` API ve Model Kartı  

---

## 📌 Not Defterleri (Notebooks)

Klasör içerisinde her iki derin öğrenme çatısı için açık ve anlaşılır şekilde adlandırılmış iki uygulama bulunmaktadır:

| Dosya Adı | Çatı (Framework) | Açıklama |
|---|---|---|
| 🔵 **[`main_pytorch.ipynb`](./main_pytorch.ipynb)** | **PyTorch** | `transformers`, `torch`, `Trainer` entegrasyonu ve PyTorch tensörleri ile çalışır. |
| 🟠 **[`main_tensorflow.ipynb`](./main_tensorflow.ipynb)** | **TensorFlow / Keras** | `transformers`, `tensorflow`, Keras `PushToHubCallback` ve TF tensörleri ile çalışır. |

---

## 🧪 Uygulama İçeriği ve Akışı

Her iki not defteri de şu adımları baştan sona eksiksiz uygular:

1. **Ortam Kurulumu:** `transformers`, `torch` / `tensorflow`, `huggingface_hub`, `datasets` kütüphanelerinin kurulumu.
2. **Model Kullanımı:** `camembert-base` modeli ile Fransızca maske doldurma (`pipeline("fill-mask")`) ve `Auto*` sınıfları.
3. **Manuel Çıkarım (Inference):** Pipeline olmadan doğrudan tensörler üzerinden en yüksek olasılıklı 5 kelimenin tahmini.
4. **Hub Kimlik Doğrulama:** `notebook_login()` ile etkileşimli belirteç (token) girişi.
5. **Model Paylaşımı (`push_to_hub`):**
   - **Bölüm 3 Köprüsü:** Eğitim sırasında otomatik yükleme (`Trainer(push_to_hub=True)` veya `PushToHubCallback`).
   - **Bölüm 4'ün Hızlı Yolu:** Doğrudan `model.push_to_hub()` ve `tokenizer.push_to_hub()`.
6. **Programatik Yönetim:** `huggingface_hub` kütüphanesi ile `create_repo` ve `upload_file`.
7. **Git Tabanlı Akış:** `Repository` sınıfı kullanım şablonu.
8. **Model Kartı:** Otomatik `README.md` model kartı oluşturulması.

---

## 💻 Nasıl Çalıştırılır?

### Yöntem 1: Google Colab (Kurulum Gerektirmez)
Notebook'ları Google Colab'a yükleyip ilk hücredeki `!pip install` komutunu çalıştırarak doğrudan kullanabilirsiniz.

### Yöntem 2: Yerel Bilgisayarda (Sanal Ortam / venv)
Projeyi GitHub'dan indiren herkes aşağıdaki adımlarla sıfır hata ile çalıştırabilir:

```bash
# 1. Sanal ortam oluşturun ve aktif edin
python -m venv env
# Windows:
env\Scripts\activate
# Mac / Linux:
# source env/bin/activate

# 2. Gerekli kütüphaneleri tek komutla kurun
pip install -r requirements.txt

# 3. İlgili not defterini başlatın
jupyter notebook main_pytorch.ipynb
# veya
jupyter notebook main_tensorflow.ipynb
```

> **🔑 Token Notu:** Modelleri test etmek, indirmek ve çalıştırmak için herhangi bir token gerekmez. Yalnızca kendi modelinizi Hugging Face profilinize yüklemek (`push_to_hub`) istediğinizde ücretsiz bir Hugging Face "Write Token"ı gereklidir.
