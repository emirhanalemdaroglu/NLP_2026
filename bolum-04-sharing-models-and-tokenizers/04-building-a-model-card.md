# Bir model kartı oluşturma[[building-a-model-card]]

<CourseFloatingBanner
    chapter={4}
    classNames="absolute z-10 right-0 top-0"
/>

Model kartı (model card), bir model deposunda muhtemelen model ve tokenizer dosyaları kadar önemli olan bir dosyadır. Topluluktaki diğer üyelerin yeniden kullanılabilirliğini (reusability) ve sonuçların tekrarlanabilirliğini (reproducibility) sağlayarak ve diğer üyelerin kendi eserlerini (artifacts) üzerine inşa edebilecekleri bir platform sunarak modelin temel tanımını oluşturur.

Eğitim ve değerlendirme (evaluation) sürecini belgelemek, başkalarının bir modelden ne beklemeleri gerektiğini anlamalarına yardımcı olur — ve kullanılan veriler ile yapılan ön işleme (preprocessing) ve son işleme (postprocessing) hakkında yeterli bilgi sağlamak, modelin hangi kısıtlamalara (limitations), önyargılara (biases) sahip olduğunun ve hangi bağlamlarda yararlı olup olmadığının belirlenmesini ve anlaşılmasını sağlar.

Bu nedenle, modelinizi açıkça tanımlayan bir model kartı oluşturmak çok önemli bir adımdır. Burada size bu konuda yardımcı olacak bazı ipuçları sunuyoruz. Model kartını oluşturmak, daha önce gördüğünüz *README.md* (bir Markdown dosyasıdır) aracılığıyla yapılır.

"Model kartı" konsepti, Google'ın bir araştırma yöneliminden kaynaklanmaktadır ve ilk olarak Margaret Mitchell ve diğerleri tarafından yazılan ["Model Cards for Model Reporting"](https://arxiv.org/abs/1810.03993) adlı makalede paylaşılmıştır. Burada yer alan pek çok bilgi bu makaleye dayanmaktadır ve tekrarlanabilirliğe, yeniden kullanılabilirliğe ve adalete değer veren bir dünyada model kartlarının neden bu kadar önemli olduğunu anlamak için makaleye bir göz atmanızı öneririz.

Model kartı genellikle modelin ne için olduğuna dair çok kısa, üst düzey bir genel bakışla başlar ve ardından aşağıdaki bölümlerde ek ayrıntılarla devam eder:

- Model açıklaması (Model description)
- Kullanım amacı ve sınırlamalar (Intended uses & limitations)
- Nasıl kullanılır (How to use)
- Sınırlamalar ve önyargı (Limitations and bias)
- Eğitim verileri (Training data)
- Eğitim prosedürü (Training procedure)
- Değerlendirme sonuçları (Evaluation results)

Bu bölümlerin her birinin neleri içermesi gerektiğine bir göz atalım.

### Model açıklaması[[model-description]]

Model açıklaması model hakkında temel detayları sağlar. Buna mimari, sürüm, bir makalede tanıtılıp tanıtılmadığı, orijinal bir uygulamasının (implementation) mevcut olup olmadığı, yazarı ve model hakkında genel bilgiler dahildir. Herhangi bir telif hakkı (copyright) burada belirtilmelidir. Eğitim prosedürleri, parametreler ve önemli sorumluluk retleri (disclaimers) hakkında genel bilgiler de bu bölümde belirtilebilir.

### Kullanım amacı ve sınırlamalar[[intended-uses-limitations]]

Burada, modelin uygulanabileceği diller, alanlar ve domainler de dahil olmak üzere, modelin tasarlanma amacı olan kullanım durumlarını açıklarsınız. Model kartının bu bölümü, modelin kapsamı dışında kaldığı bilinen veya optimalin altında (suboptimally) performans göstermesi muhtemel alanları da belgeleyebilir.

### Nasıl kullanılır[[how-to-use]]

Bu bölüm modelin nasıl kullanılacağına dair bazı örnekler içermelidir. Bu, `pipeline()` fonksiyonunun kullanımını, model ve tokenizer sınıflarının kullanımını ve yararlı olabileceğini düşündüğünüz diğer kodları sergileyebilir.

### Eğitim verileri[[training-data]]

Bu bölüm modelin hangi veri kümesi/kümeleri üzerinde eğitildiğini belirtmelidir. Veri kümelerinin (datasets) kısa bir açıklaması da memnuniyetle karşılanır.

### Eğitim prosedürü[[training-procedure]]

Bu bölümde eğitimin tekrarlanabilirlik (reproducibility) açısından yararlı olan tüm ilgili yönlerini (aspects) açıklamalısınız. Buna veriler üzerinde yapılan herhangi bir ön işleme ve son işleme işleminin yanı sıra modelin eğitildiği epoch (dönem) sayısı, batch size (yığın boyutu), learning rate (öğrenme oranı) ve benzeri ayrıntılar dahildir.

### Değişkenler ve metrikler[[variable-and-metrics]]

Burada değerlendirme (evaluation) için kullandığınız metrikleri ve ölçtüğünüz (mesuring) farklı faktörleri açıklamalısınız. Hangi metriğin(lerin), hangi veri kümesinde ve veri kümesinin hangi parçasında (split) kullanıldığını belirtmek, modelinizin performansını diğer modellerinkiyle karşılaştırmayı kolaylaştırır. Bunlar hedeflenen kullanıcılar ve kullanım durumları (use cases) gibi önceki bölümlere uygun şekilde (informed by) yapılandırılmalıdır.

### Değerlendirme sonuçları[[evaluation-results]]

Son olarak, modelin değerlendirme veri kümesinde (evaluation dataset) ne kadar iyi performans gösterdiğine dair bir gösterge sağlayın. Model bir karar eşiği (decision threshold) kullanıyorsa, ya değerlendirmede kullanılan karar eşiğini sağlayın ya da amaçlanan kullanımlar için farklı eşiklerde değerlendirme ile ilgili ayrıntılar verin.

## Örnek[[example]]

İyi hazırlanmış (well-crafted) model kartlarına birkaç örnek için aşağıdakilere göz atın:

- [`bert-base-cased`](https://huggingface.co/bert-base-cased)
- [`gpt2`](https://huggingface.co/gpt2)
- [`distilbert`](https://huggingface.co/distilbert-base-uncased)

Farklı organizasyonlardan ve şirketlerden daha fazla örnek [burada](https://github.com/huggingface/model_card/blob/master/examples.md) mevcuttur.

## Not[[note]]

Model kartları model yayınlarken bir zorunluluk (requirement) değildir ve bir tane oluştururken yukarıda açıklanan tüm bölümleri dahil etmeniz gerekmez. Bununla birlikte, modelin açıkça belgelenmesi (explicit documentation) yalnızca gelecekteki kullanıcılara fayda sağlayabilir, bu nedenle mümkün olduğunca çok bölümü bilginiz ve yeteneğiniz dahilinde doldurmanızı öneririz.

## Model kartı meta verileri[[model-card-metadata]]

Hugging Face Hub'da biraz keşif yaptıysanız, bazı modellerin belirli kategorilere ait olduğunu görmüşsünüzdür: bunları görevlere, dillere, kütüphanelere ve daha fazlasına göre filtreleyebilirsiniz. Bir modelin ait olduğu kategoriler, model kartı başlığına eklediğiniz meta verilere (metadata) göre belirlenir.

Örneğin, [`camembert-base` model kartına](https://huggingface.co/camembert-base/blob/main/README.md) bir göz atarsanız, model kartı başlığında aşağıdaki satırları görebilirsiniz:

```
---
language: fr
license: mit
datasets:
- oscar
---
```

Bu meta veri (metadata) Hugging Face Hub tarafından ayrıştırılır (parsed) ve bu model daha sonra Oscar veri kümesinde eğitilmiş, MIT lisanslı bir Fransızca model olarak tanımlanır.

[Tam model kartı spesifikasyonu](https://github.com/huggingface/hub-docs/blame/main/modelcard.md), dillerin, lisansların, etiketlerin (tags), veri kümelerinin (datasets), metriklerin ve modelin eğitim sırasında elde ettiği değerlendirme (evaluation) sonuçlarının belirtilmesine olanak tanır.
