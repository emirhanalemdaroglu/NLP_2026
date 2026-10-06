<FrameworkSwitchCourse {fw} />

# Önceden eğitilmiş (pretrained) modellerin kullanımı[[using-pretrained-models]]

{#if fw === 'pt'}

<CourseFloatingBanner chapter={4}
  classNames="absolute z-10 right-0 top-0"
  notebooks={[
    {label: "Google Colab", value: "https://colab.research.google.com/github/huggingface/notebooks/blob/master/course/en/chapter4/section2_pt.ipynb"},
    {label: "Aws Studio", value: "https://studiolab.sagemaker.aws/import/github/huggingface/notebooks/blob/master/course/en/chapter4/section2_pt.ipynb"},
]} />

{:else}

<CourseFloatingBanner chapter={4}
  classNames="absolute z-10 right-0 top-0"
  notebooks={[
    {label: "Google Colab", value: "https://colab.research.google.com/github/huggingface/notebooks/blob/master/course/en/chapter4/section2_tf.ipynb"},
    {label: "Aws Studio", value: "https://studiolab.sagemaker.aws/import/github/huggingface/notebooks/blob/master/course/en/chapter4/section2_tf.ipynb"},
]} />

{/if}

Model Hub, uygun modeli seçmeyi basitleştirir, böylece onu herhangi bir alt akış (downstream) kütüphanesinde kullanmak sadece birkaç satır kodla yapılabilir. Bu modellerden birini gerçekte nasıl kullanacağımıza ve topluluğa nasıl katkıda bulunacağımıza bir göz atalım.

Maske doldurma (mask filling) yapabilen, Fransızca tabanlı bir model aradığımızı varsayalım.

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/camembert.gif" alt="Selecting the Camembert model." width="80%"/>
</div>

Denemek için `camembert-base` kontrol noktasını (checkpoint) seçiyoruz. Kullanmaya başlamak için tek ihtiyacımız olan tanımlayıcı `camembert-base`'dir! Önceki bölümlerde gördüğünüz gibi, `pipeline()` fonksiyonunu kullanarak onu oluşturabiliriz (instantiate):

```py
from transformers import pipeline

camembert_fill_mask = pipeline("fill-mask", model="camembert-base")
results = camembert_fill_mask("Le camembert est <mask> :)")
```

```python out
[
  {'sequence': 'Le camembert est délicieux :)', 'score': 0.49091005325317383, 'token': 7200, 'token_str': 'délicieux'}, 
  {'sequence': 'Le camembert est excellent :)', 'score': 0.1055697426199913, 'token': 2183, 'token_str': 'excellent'}, 
  {'sequence': 'Le camembert est succulent :)', 'score': 0.03453313186764717, 'token': 26202, 'token_str': 'succulent'}, 
  {'sequence': 'Le camembert est meilleur :)', 'score': 0.0330314114689827, 'token': 528, 'token_str': 'meilleur'}, 
  {'sequence': 'Le camembert est parfait :)', 'score': 0.03007650189101696, 'token': 1654, 'token_str': 'parfait'}
]
```

Gördüğünüz gibi, bir pipeline (boru hattı) içine model yüklemek son derece basittir. Dikkat etmeniz gereken tek şey, seçilen kontrol noktasının kullanılacağı görev için uygun olmasıdır. Örneğin, burada `camembert-base` kontrol noktasını `fill-mask` (maske doldurma) boru hattına yüklüyoruz ki bu tamamen uygundur. Fakat bu kontrol noktasını `text-classification` (metin sınıflandırma) boru hattına yükleseydik, sonuçlar hiçbir anlam ifade etmezdi çünkü `camembert-base` başlığı (head) bu görev için uygun değildir! Uygun kontrol noktalarını seçmek için Hugging Face Hub arayüzündeki görev seçiciyi (task selector) kullanmanızı öneririz:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/tasks.png" alt="The task selector on the web interface." width="80%"/>
</div>

Kontrol noktasını doğrudan model mimarisini kullanarak da oluşturabilirsiniz:

{#if fw === 'pt'}
```py
from transformers import CamembertTokenizer, CamembertForMaskedLM

tokenizer = CamembertTokenizer.from_pretrained("camembert-base")
model = CamembertForMaskedLM.from_pretrained("camembert-base")
```

Bununla birlikte, tasarımları gereği mimariden bağımsız oldukları için (architecture-agnostic) bunun yerine [`Auto*` sınıflarını (classes)](https://huggingface.co/transformers/model_doc/auto?highlight=auto#auto-classes) kullanmanızı öneririz. Önceki kod örneği kullanıcıları CamemBERT mimarisinde yüklenebilecek kontrol noktalarıyla sınırlarken, `Auto*` sınıflarını kullanmak kontrol noktalarını değiştirmeyi basitleştirir:

```py
from transformers import AutoTokenizer, AutoModelForMaskedLM

tokenizer = AutoTokenizer.from_pretrained("camembert-base")
model = AutoModelForMaskedLM.from_pretrained("camembert-base")
```
{:else}
```py
from transformers import CamembertTokenizer, TFCamembertForMaskedLM

tokenizer = CamembertTokenizer.from_pretrained("camembert-base")
model = TFCamembertForMaskedLM.from_pretrained("camembert-base")
```

Bununla birlikte, tasarımları gereği mimariden bağımsız oldukları için bunun yerine [`TFAuto*` sınıflarını (classes)](https://huggingface.co/transformers/model_doc/auto?highlight=auto#auto-classes) kullanmanızı öneririz. Önceki kod örneği kullanıcıları CamemBERT mimarisinde yüklenebilecek kontrol noktalarıyla sınırlarken, `TFAuto*` sınıflarını kullanmak kontrol noktalarını değiştirmeyi basitleştirir:

```py
from transformers import AutoTokenizer, TFAutoModelForMaskedLM

tokenizer = AutoTokenizer.from_pretrained("camembert-base")
model = TFAutoModelForMaskedLM.from_pretrained("camembert-base")
```
{/if}

> [!TIP]
> Önceden eğitilmiş (pretrained) bir modeli kullanırken, nasıl eğitildiğini, hangi veri kümelerinde eğitildiğini, sınırlarını ve önyargılarını (biases) kontrol ettiğinizden emin olun. Tüm bu bilgiler model kartında (model card) belirtilmiş olmalıdır.
