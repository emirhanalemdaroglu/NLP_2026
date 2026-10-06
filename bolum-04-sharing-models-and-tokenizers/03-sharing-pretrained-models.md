<FrameworkSwitchCourse {fw} />

# Önceden eğitilmiş modelleri paylaşma[[sharing-pretrained-models]]

{#if fw === 'pt'}

<CourseFloatingBanner chapter={4}
  classNames="absolute z-10 right-0 top-0"
  notebooks={[
    {label: "Google Colab", value: "https://colab.research.google.com/github/huggingface/notebooks/blob/master/course/en/chapter4/section3_pt.ipynb"},
    {label: "Aws Studio", value: "https://studiolab.sagemaker.aws/import/github/huggingface/notebooks/blob/master/course/en/chapter4/section3_pt.ipynb"},
]} />

{:else}

<CourseFloatingBanner chapter={4}
  classNames="absolute z-10 right-0 top-0"
  notebooks={[
    {label: "Google Colab", value: "https://colab.research.google.com/github/huggingface/notebooks/blob/master/course/en/chapter4/section3_tf.ipynb"},
    {label: "Aws Studio", value: "https://studiolab.sagemaker.aws/import/github/huggingface/notebooks/blob/master/course/en/chapter4/section3_tf.ipynb"},
]} />

{/if}

Aşağıdaki adımlarda, önceden eğitilmiş modelleri 🤗 Hub'da paylaşmanın en kolay yollarına göz atacağız. Aşağıda inceleyeceğimiz üzere, modelleri doğrudan Hub üzerinde paylaşmayı ve güncellemeyi kolaylaştıran araçlar ve yardımcı programlar mevcuttur.

<Youtube id="9yY3RB_GSPM"/>

Model eğiten tüm kullanıcıları, bu modelleri toplulukla paylaşarak katkıda bulunmaya teşvik ediyoruz — çok özel veri kümelerinde eğitilmiş olsalar bile modelleri paylaşmak başkalarına yardımcı olacak, onlara zaman ve bilgi işlem kaynakları (compute resources) tasarrufu sağlayacak ve faydalı eğitilmiş eserlere erişim sunacaktır. Buna karşılık, siz de başkalarının yaptığı çalışmalardan yararlanabilirsiniz!

Yeni model depoları (repositories) oluşturmanın üç yolu vardır:

- `push_to_hub` API'sini kullanmak
- `huggingface_hub` Python kütüphanesini kullanmak
- Web arayüzünü kullanmak

Bir depo oluşturduktan sonra, git ve git-lfs aracılığıyla buraya dosya yükleyebilirsiniz. Sonraki bölümlerde model depoları oluşturma ve bunlara dosya yükleme konusunda size rehberlik edeceğiz.


## `push_to_hub` API'sini kullanmak[[using-the-pushtohub-api]]

{#if fw === 'pt'}

<Youtube id="Zh0FfmVrKX0"/>

{:else}

<Youtube id="pUh5cGmNV8Y"/>

{/if}

Dosyaları Hub'a yüklemenin en basit yolu `push_to_hub` API'sinden yararlanmaktır.

Daha ileri gitmeden önce, `huggingface_hub` API'sinin kim olduğunuzu ve hangi ad alanlarına (namespaces) yazma erişiminiz olduğunu bilmesi için bir kimlik doğrulama belirteci (authentication token) oluşturmanız gerekir. `transformers`ın yüklü olduğu bir ortamda olduğunuzdan emin olun (bkz. [Kurulum](/course/chapter0)). Eğer bir not defterindeyseniz (notebook), giriş yapmak için aşağıdaki fonksiyonu kullanabilirsiniz:

```python
from huggingface_hub import notebook_login

notebook_login()
```

Bir terminalde şunu çalıştırabilirsiniz:

```bash
huggingface-cli login
```

Her iki durumda da, Hub'a giriş yapmak için kullandığınız kullanıcı adı ve şifreniz istenecektir. Henüz bir Hub profiliniz yoksa, [buradan](https://huggingface.co/join) bir tane oluşturmalısınız.

Harika! Artık kimlik doğrulama belirteciniz önbellek (cache) klasörünüzde saklanıyor. Hadi birkaç depo oluşturalım!

{#if fw === 'pt'}

Eğer bir modeli eğitmek için `Trainer` API'si ile ilgilendiyseniz, bunu Hub'a yüklemenin en kolay yolu `TrainingArguments` öğenizi tanımlarken `push_to_hub=True` olarak ayarlamaktır:

```py
from transformers import TrainingArguments

training_args = TrainingArguments(
    "bert-finetuned-mrpc", save_strategy="epoch", push_to_hub=True
)
```

`trainer.train()` işlevini çağırdığınızda, `Trainer` daha sonra modelinizi kendi ad alanınızdaki (namespace) bir depoya her kaydedildiğinde (burada her epoch'ta) Hub'a yükleyecektir. Bu depo, seçtiğiniz çıktı dizini gibi adlandırılacaktır (burada `bert-finetuned-mrpc`) ancak `hub_model_id = "farkli_bir_isim"` ile farklı bir isim seçebilirsiniz.

Modelinizi üyesi olduğunuz bir organizasyona yüklemek için, onu `hub_model_id = "organizasyonum/repo_adim"` ile aktarmanız yeterlidir.

Eğitiminiz bittikten sonra, modelinizin son sürümünü yüklemek için son bir `trainer.push_to_hub()` işlemi yapmalısınız. Bu işlem aynı zamanda ilgili tüm meta verilerle birlikte kullanılan hiperparametreleri ve değerlendirme sonuçlarını raporlayan bir model kartı da (model card) oluşturacaktır! İşte böyle bir model kartında bulabileceğiniz içeriğe bir örnek:

<div class="flex justify-center">
  <img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/model_card.png" alt="An example of an auto-generated model card." width="100%"/>
</div>

{:else}

Modelinizi eğitmek için Keras kullanıyorsanız, onu Hub'a yüklemenin en kolay yolu `model.fit()` fonksiyonunu çağırdığınızda bir `PushToHubCallback` iletmektir:

```py
from transformers import PushToHubCallback

callback = PushToHubCallback(
    "bert-finetuned-mrpc", save_strategy="epoch", tokenizer=tokenizer
)
```

Daha sonra `model.fit()` çağrınıza `callbacks=[callback]` eklemelisiniz. Geri arama (callback) daha sonra modelinizi her kaydedildiğinde (burada her epoch'ta) ad alanınızdaki bir depoya Hub'a yükleyecektir. Bu depo, seçtiğiniz çıktı dizini gibi adlandırılacaktır (burada `bert-finetuned-mrpc`) ancak `hub_model_id = "farkli_bir_isim"` ile farklı bir isim seçebilirsiniz.

Modelinizi üyesi olduğunuz bir organizasyona yüklemek için, sadece `hub_model_id = "organizasyonum/repo_adim"` ile geçirin.

{/if}

Daha alt düzeyde Model Hub'a erişim; modeller, tokenizer'lar (belirteçleyiciler) ve yapılandırma (configuration) nesneleri üzerinde doğrudan kendi `push_to_hub()` yöntemleri (method) aracılığıyla yapılabilir. Bu yöntem, hem deponun oluşturulmasını hem de model ve tokenizer dosyalarının doğrudan depoya itilmesini (pushing) halleder. Aşağıda göreceğimiz API'nin aksine manuel bir işleme gerek yoktur.

Nasıl çalıştığına dair bir fikir edinmek için önce bir model ve bir tokenizer oluşturalım:

{#if fw === 'pt'}
```py
from transformers import AutoModelForMaskedLM, AutoTokenizer

checkpoint = "camembert-base"

model = AutoModelForMaskedLM.from_pretrained(checkpoint)
tokenizer = AutoTokenizer.from_pretrained(checkpoint)
```
{:else}
```py
from transformers import TFAutoModelForMaskedLM, AutoTokenizer

checkpoint = "camembert-base"

model = TFAutoModelForMaskedLM.from_pretrained(checkpoint)
tokenizer = AutoTokenizer.from_pretrained(checkpoint)
```
{/if}

Bunlarla istediğinizi yapmakta özgürsünüz — tokenizer'a token'lar (belirteçler) ekleyin, modeli eğitin, ona ince ayar (fine-tune) yapın. Ortaya çıkan modelden, ağırlıklardan (weights) ve tokenizer'dan memnun kaldığınızda, doğrudan `model` nesnesi üzerinde bulunan `push_to_hub()` yönteminden yararlanabilirsiniz:

```py
model.push_to_hub("dummy-model")
```

Bu, profilinizde yeni `dummy-model` deposunu oluşturacak ve içini model dosyalarınızla dolduracaktır.
Tüm dosyaların artık bu depoda mevcut olması için aynı şeyi tokenizer ile de yapın:

```py
tokenizer.push_to_hub("dummy-model")
```

Bir organizasyona aitseniz, o organizasyonun ad alanına (namespace) yükleme yapmak için `organization` (organizasyon) argümanını belirtmeniz yeterlidir:

```py
tokenizer.push_to_hub("dummy-model", organization="huggingface")
```

Belirli bir Hugging Face belirteci (token) kullanmak isterseniz, bunu `push_to_hub()` yönteminde de belirtmekte özgürsünüz:

```py
tokenizer.push_to_hub("dummy-model", organization="huggingface", use_auth_token="<TOKEN>")
```

Şimdi yeni yüklediğiniz modelinizi bulmak için Model Hub'a gidin: *https://huggingface.co/user-or-organization/dummy-model*.

"Files and versions" (Dosyalar ve sürümler) sekmesine tıklayın ve dosyaların aşağıdaki ekran görüntüsündeki gibi görünür olduğunu göreceksiniz:

{#if fw === 'pt'}
<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/push_to_hub_dummy_model.png" alt="Dummy model containing both the tokenizer and model files." width="80%"/>
</div>
{:else}
<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/push_to_hub_dummy_model_tf.png" alt="Dummy model containing both the tokenizer and model files." width="80%"/>
</div>
{/if}

> [!TIP]
> ✏️ **Kendiniz Deneyin!** `bert-base-cased` kontrol noktası (checkpoint) ile ilişkili modeli ve tokenizer'ı alın ve `push_to_hub()` yöntemini kullanarak bunları kendi ad alanınızdaki bir depoya (repo) yükleyin. Silmeden önce deponun sayfanızda düzgün göründüğünden emin olmak için iki kez kontrol edin.

Gördüğünüz gibi `push_to_hub()` yöntemi, belirli bir depoya veya organizasyon ad alanına (namespace) yükleme yapmayı ya da farklı bir API belirteci (token) kullanmayı mümkün kılan çeşitli argümanları kabul eder. Nelerin mümkün olduğu hakkında bir fikir edinmek için doğrudan [🤗 Transformers belgelerinde (documentation)](https://huggingface.co/transformers/model_sharing) bulunan yöntem spesifikasyonuna bir göz atmanızı öneririz.

`push_to_hub()` yöntemi, Hugging Face Hub'a doğrudan bir API sunan [`huggingface_hub`](https://github.com/huggingface/huggingface_hub) Python paketi tarafından desteklenmektedir. Bu paket 🤗 Transformers'a ve [`allenlp`](https://github.com/allenai/allennlp) gibi diğer birkaç makine öğrenimi kütüphanesine entegre edilmiştir. Bu bölümde 🤗 Transformers entegrasyonuna odaklansak da, bunu kendi kodunuza veya kütüphanenize entegre etmek basittir.

Yeni oluşturduğunuz deponuza nasıl dosya yükleyeceğinizi görmek için son bölüme atlayın!

## `huggingface_hub` Python kütüphanesini kullanmak[[using-the-huggingfacehub-python-library]]

`huggingface_hub` Python kütüphanesi, model ve veri kümesi (dataset) hub'ları için bir dizi araç sunan bir pakettir. Hub üzerindeki depolar hakkında bilgi almak ve bunları yönetmek gibi yaygın görevler için basit yöntemler ve sınıflar sağlar. Bu depoların içeriğini yönetmek ve Hub'ı projelerinize ve kütüphanelerinize entegre etmek için git'in üzerinde çalışan basit API'ler sağlar.

`push_to_hub` API'sini kullanmaya benzer şekilde bu, API belirtecinizin (token) önbelleğinize (cache) kaydedilmesini gerektirecektir. Bunu yapmak için önceki bölümde belirtildiği gibi CLI'dan `login` komutunu kullanmanız gerekecektir (Google Colab'da çalıştırıyorsanız bu komutların başına `!` karakterini eklediğinizden emin olun):

```bash
huggingface-cli login
```

`huggingface_hub` paketi, amacımız için yararlı olan çeşitli yöntemler ve sınıflar sunar. Öncelikle, depo oluşturma, silme ve diğer işlemleri yönetmek için birkaç yöntem vardır:

```python no-format
from huggingface_hub import (
    # User management
    login,
    logout,
    whoami,

    # Repository creation and management
    create_repo,
    delete_repo,
    update_repo_visibility,

    # And some methods to retrieve/change information about the content
    list_models,
    list_datasets,
    list_metrics,
    list_repo_files,
    upload_file,
    delete_file,
)
```


Ayrıca, yerel bir depoyu (local repository) yönetmek için çok güçlü olan `Repository` sınıfını sunar. Bunlardan nasıl yararlanılacağını anlamak için sonraki birkaç bölümde bu yöntemleri ve bu sınıfı inceleyeceğiz.

Hub üzerinde yeni bir depo oluşturmak için `create_repo` yöntemi kullanılabilir:

```py
from huggingface_hub import create_repo

create_repo("dummy-model")
```

Bu işlem kendi ad alanınızda (namespace) `dummy-model` deposunu oluşturacaktır. İsterseniz `organization` argümanını kullanarak deponun hangi organizasyona ait olması gerektiğini belirtebilirsiniz:

```py
from huggingface_hub import create_repo

create_repo("dummy-model", organization="huggingface")
```

Bu, o kuruluşa üye olduğunuz varsayılarak, `huggingface` ad alanında `dummy-model` deposunu oluşturacaktır.
Yararlı olabilecek diğer argümanlar şunlardır:

- `private`: deponun başkaları tarafından görünüp görünmeyeceğini belirtmek için.
- `token`: önbelleğinizde saklanan belirteci verilen bir belirteçle geçersiz kılmak isterseniz.
- `repo_type`: bir model yerine bir veri kümesi (`dataset`) veya bir alan (`space`) oluşturmak isterseniz. Kabul edilen değerler `"dataset"` ve `"space"` şeklindedir.

Depo oluşturulduktan sonra içine dosyalar eklemeliyiz! Bunun yapılabileceği üç yolu görmek için sonraki bölüme geçin.


## Web arayüzünü kullanmak[[using-the-web-interface]]

Web arayüzü, depoları doğrudan Hub'da yönetmek için araçlar sunar. Arayüzü kullanarak kolayca depolar oluşturabilir, dosyalar (büyük olanlar bile!) ekleyebilir, modelleri keşfedebilir, diff'leri (farkları) görselleştirebilir ve çok daha fazlasını yapabilirsiniz.

Yeni bir depo oluşturmak için [huggingface.co/new](https://huggingface.co/new) adresini ziyaret edin:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/new_model.png" alt="Page showcasing the model used for the creation of a new model repository." width="80%"/>
</div>

İlk olarak, deponun sahibini belirleyin: Bu siz veya bağlı olduğunuz organizasyonlardan herhangi biri olabilir. Bir organizasyon seçerseniz, model organizasyonun sayfasında yer alacak ve organizasyonun her üyesi depoya katkıda bulunma olanağına sahip olacaktır.

Daha sonra modelinizin adını girin. Bu aynı zamanda deponun adı da olacaktır. Son olarak, modelinizin halka açık (public) mı yoksa özel (private) mi olmasını istediğinizi belirtebilirsiniz. Özel modeller genel görünümden gizlenir.

Model deponuzu oluşturduktan sonra şöyle bir sayfa görmelisiniz:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/empty_model.png" alt="An empty model page after creating a new repository." width="80%"/>
</div>

Burası modelinizin barındırılacağı (hosted) yerdir. İçini doldurmaya başlamak için, doğrudan web arayüzünden bir README dosyası ekleyebilirsiniz.

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/dummy_model.png" alt="The README file showing the Markdown capabilities." width="80%"/>
</div>

README dosyası Markdown formatındadır — çılgınca şeyler yapmakta özgürsünüz! Bu bölümün üçüncü kısmı bir model kartı (model card) oluşturmaya ayrılmıştır. Model kartları, başkalarına modelin neler yapabileceğini anlattığınız yer oldukları için modelinize değer katmada büyük önem taşırlar.

"Files and versions" (Dosyalar ve sürümler) sekmesine bakarsanız, orada henüz fazla dosya olmadığını göreceksiniz — sadece yeni oluşturduğunuz *README.md* ve büyük dosyaları takip eden *.gitattributes* dosyası.

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/files.png" alt="The 'Files and versions' tab only shows the .gitattributes and README.md files." width="80%"/>
</div>

Daha sonra bazı yeni dosyaların nasıl ekleneceğine bir göz atacağız.

## Model dosyalarını yükleme[[uploading-the-model-files]]

Hugging Face Hub'da dosyaları yönetme sistemi, normal dosyalar için git'e ve daha büyük dosyalar için git-lfs'ye ([Git Büyük Dosya Depolama - Git Large File Storage](https://git-lfs.github.com/)) dayanmaktadır. 

Bir sonraki bölümde dosyaları Hub'a yüklemenin üç farklı yolunun üzerinden geçeceğiz: `huggingface_hub` aracılığıyla ve git komutları aracılığıyla.

### `upload_file` yaklaşımı[[the-uploadfile-approach]]

`upload_file` kullanmak, sisteminize git ve git-lfs kurulmasını gerektirmez. Dosyaları HTTP POST isteklerini kullanarak doğrudan 🤗 Hub'a iter (pushes). Bu yaklaşımın bir kısıtlaması (limitation), boyutu 5GB'den büyük olan dosyaları işlememesidir.
Eğer dosyalarınız 5GB'den büyükse, lütfen aşağıda detaylandırılan diğer iki yöntemi izleyin.

API aşağıdaki gibi kullanılabilir:

```py
from huggingface_hub import upload_file

upload_file(
    "<path_to_file>/config.json",
    path_in_repo="config.json",
    repo_id="<namespace>/dummy-model",
)
```

Bu işlem, `<path_to_file>` yolunda bulunan `config.json` dosyasını `dummy-model` deposunun kök (root) dizinine `config.json` olarak yükleyecektir.
Yararlı olabilecek diğer argümanlar şunlardır:

- `token`: önbelleğinizde saklanan belirteci verilen bir belirteçle geçersiz kılmak isterseniz.
- `repo_type`: bir model yerine bir veri kümesi (`dataset`) veya bir alan (`space`) deposuna yüklemek isterseniz. Kabul edilen değerler `"dataset"` ve `"space"` şeklindedir.


### `Repository` sınıfı[[the-repository-class]]

`Repository` sınıfı yerel bir depoyu (local repository) git benzeri bir şekilde yönetir. İhtiyaç duyduğumuz tüm özellikleri sağlamak için git ile yaşanabilecek zorlukların çoğunu soyutlar (abstracts). 

Bu sınıfı kullanmak için git ve git-lfs'nin kurulu olması gerekir, bu nedenle başlamadan önce git-lfs'yi (kurulum talimatları için [buraya](https://git-lfs.github.com/) bakın) kurduğunuzdan ve ayarladığınızdan emin olun. 

Yeni oluşturduğumuz depoyla oynamaya başlamak için, uzak (remote) depoyu klonlayarak onu yerel bir klasörde başlatmakla (initialising) işe koyulabiliriz:

```py
from huggingface_hub import Repository

repo = Repository("<path_to_dummy_folder>", clone_from="<namespace>/dummy-model")
```

Bu işlem çalışma dizinimizde `<path_to_dummy_folder>` klasörünü oluşturdu. `create_repo` aracılığıyla depo oluşturulurken (instantiating) oluşturulan tek dosya bu olduğundan bu klasör sadece `.gitattributes` dosyasını içerir.

Bu noktadan itibaren, geleneksel git yöntemlerinden bazılarını kullanabiliriz:

```py
repo.git_pull()
repo.git_add()
repo.git_commit()
repo.git_push()
repo.git_tag()
```

Ve diğerleri! Mevcut tüm yöntemlere genel bir bakış atmak için [burada](https://github.com/huggingface/huggingface_hub/tree/main/src/huggingface_hub#advanced-programmatic-repository-management) bulunan `Repository` dokümantasyonuna (belgelerine) göz atmanızı öneririz.

Şu anda, hub'a itmek (push) istediğimiz bir modelimiz ve bir tokenizer'ımız var. Depoyu başarıyla klonladık, bu nedenle dosyaları bu depo içine kaydedebiliriz.

İlk olarak en son değişiklikleri çekerek (pulling) yerel klonumuzun güncel olduğundan emin oluyoruz:

```py
repo.git_pull()
```

Bu işlem tamamlandıktan sonra model ve tokenizer dosyalarını kaydediyoruz:

```py
model.save_pretrained("<path_to_dummy_folder>")
tokenizer.save_pretrained("<path_to_dummy_folder>")
```

`<path_to_dummy_folder>` artık tüm model ve tokenizer dosyalarını içermektedir. Dosyaları hazırlama aşamasına (staging area) ekleyerek, commitleyerek (commit) ve hub'a iterek olağan git iş akışını (workflow) takip ediyoruz:

```py
repo.git_add()
repo.git_commit("Add model and tokenizer files")
repo.git_push()
```

Tebrikler! Hub'a ilk dosyalarınızı yeni ittiniz.

### Git tabanlı yaklaşım[[the-git-based-approach]]

Bu, dosya yüklemek için çok temel (barebones) bir yaklaşımdır: Bunu doğrudan git ve git-lfs ile yapacağız. Zorlukların çoğu önceki yaklaşımlar tarafından soyutlanmıştır, ancak aşağıdaki yöntemde birkaç püf noktası (caveats) vardır, bu nedenle daha karmaşık bir kullanım senaryosunu (use-case) izleyeceğiz.

Bu sınıfı kullanmak git ve git-lfs'nin kurulu olmasını gerektirir, bu yüzden başlamadan önce [git-lfs](https://git-lfs.github.com/)'nin (kurulum talimatları için buraya bakın) kurulu ve ayarlanmış olduğundan emin olun. 

İlk olarak git-lfs'yi başlatarak (initializing) başlayın:

```bash
git lfs install
```

```bash
Updated git hooks.
Git LFS initialized.
```

Bu işlem yapıldıktan sonra ilk adım model deponuzu klonlamaktır:

```bash
git clone https://huggingface.co/<namespace>/<your-model-id>
```

Kullanıcı adım `lysandre` ve model adı olarak `dummy` kullandım, bu yüzden benim için komut şu şekilde sonuçlanır:

```
git clone https://huggingface.co/lysandre/dummy
```

Artık çalışma dizinimde *dummy* adında bir klasör var. Klasörün içine `cd` ile girebilir ve içindekilere göz atabilirim:

```bash
cd dummy && ls
```

```bash
README.md
```

Deponuzu az önce Hugging Face Hub'ın `create_repo` metodunu kullanarak oluşturduysanız, bu klasör sadece gizli bir `.gitattributes` dosyası içermelidir. Web arayüzünü kullanarak bir depo oluşturmak için önceki bölümdeki talimatları izlediyseniz, klasör burada gösterildiği gibi gizli `.gitattributes` dosyasının yanında tek bir *README.md* dosyası içermelidir.

Bir yapılandırma dosyası (configuration file), bir kelime dağarcığı dosyası (vocabulary file) veya temelde birkaç megabaytın altındaki herhangi bir dosya gibi normal boyutlu bir dosya eklemek, tıpkı git tabanlı herhangi bir sistemde yapılacağı gibi yapılır. Ancak daha büyük dosyaların *huggingface.co*'a itilebilmesi için git-lfs aracılığıyla kaydedilmesi (registered) gerekir. 

Kukla (dummy) depomuza commitlemek (göndermek) istediğimiz bir modeli ve tokenizer'ı oluşturmak için biraz Python'a dönelim:

{#if fw === 'pt'}
```py
from transformers import AutoModelForMaskedLM, AutoTokenizer

checkpoint = "camembert-base"

model = AutoModelForMaskedLM.from_pretrained(checkpoint)
tokenizer = AutoTokenizer.from_pretrained(checkpoint)

# Do whatever with the model, train it, fine-tune it...

model.save_pretrained("<path_to_dummy_folder>")
tokenizer.save_pretrained("<path_to_dummy_folder>")
```
{:else}
```py
from transformers import TFAutoModelForMaskedLM, AutoTokenizer

checkpoint = "camembert-base"

model = TFAutoModelForMaskedLM.from_pretrained(checkpoint)
tokenizer = AutoTokenizer.from_pretrained(checkpoint)

# Do whatever with the model, train it, fine-tune it...

model.save_pretrained("<path_to_dummy_folder>")
tokenizer.save_pretrained("<path_to_dummy_folder>")
```
{/if}

Artık bazı model ve tokenizer eserlerini (artifacts) kaydettiğimize göre, *dummy* klasörüne tekrar bir göz atalım:

```bash
ls
```

{#if fw === 'pt'}
```bash
config.json  pytorch_model.bin  README.md  sentencepiece.bpe.model  special_tokens_map.json tokenizer_config.json  tokenizer.json
```

Dosya boyutlarına (örneğin `ls -lh` ile) bakarsanız, modelin durum sözlüğü (state dict) dosyasının (*pytorch_model.bin*) 400 MB'den fazla boyutuyla tek aykırı değer (outlier) olduğunu göreceksiniz.

{:else}
```bash
config.json  README.md  sentencepiece.bpe.model  special_tokens_map.json  tf_model.h5  tokenizer_config.json  tokenizer.json
```

Dosya boyutlarına (örneğin `ls -lh` ile) bakarsanız, modelin durum sözlüğü (state dict) dosyasının (*t5_model.h5*) 400 MB'den fazla boyutuyla tek aykırı değer (outlier) olduğunu göreceksiniz.

{/if}

> [!TIP]
> ✏️ Depoyu web arayüzünden oluştururken, *.gitattributes* dosyası otomatik olarak *.bin* ve *.h5* gibi belirli uzantılara sahip dosyaları büyük dosyalar olarak kabul edecek şekilde ayarlanır ve git-lfs herhangi bir kuruluma (setup) gerek kalmadan bunları izler (track). 

Artık devam edebilir ve geleneksel Git depolarında yaptığımız gibi ilerleyebiliriz. `git add` komutunu kullanarak tüm dosyaları Git'in hazırlama ortamına (staging environment) ekleyebiliriz:

```bash
git add .
```

Daha sonra şu anda hazırlanan (staged) dosyalara bir göz atabiliriz:

```bash
git status
```

{#if fw === 'pt'}
```bash
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
  modified:   .gitattributes
	new file:   config.json
	new file:   pytorch_model.bin
	new file:   sentencepiece.bpe.model
	new file:   special_tokens_map.json
	new file:   tokenizer.json
	new file:   tokenizer_config.json
```
{:else}
```bash
On branch main
Your branch is up to date with 'origin/main'.

Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
  modified:   .gitattributes
  	new file:   config.json
	new file:   sentencepiece.bpe.model
	new file:   special_tokens_map.json
	new file:   tf_model.h5
	new file:   tokenizer.json
	new file:   tokenizer_config.json
```
{/if}

Benzer şekilde, `status` (durum) komutunu kullanarak git-lfs'nin doğru dosyaları izlediğinden (tracking) emin olabiliriz:

```bash
git lfs status
```

{#if fw === 'pt'}
```bash
On branch main
Objects to be pushed to origin/main:


Objects to be committed:

	config.json (Git: bc20ff2)
	pytorch_model.bin (LFS: 35686c2)
	sentencepiece.bpe.model (LFS: 988bc5a)
	special_tokens_map.json (Git: cb23931)
	tokenizer.json (Git: 851ff3e)
	tokenizer_config.json (Git: f0f7783)

Objects not staged for commit:


```

Tüm dosyaların bir işleyici (handler) olarak `Git`'e sahip olduğunu görebiliriz, `LFS`'ye sahip *pytorch_model.bin* ve *sentencepiece.bpe.model* hariç. Harika!

{:else}
```bash
On branch main
Objects to be pushed to origin/main:


Objects to be committed:

	config.json (Git: bc20ff2)
	sentencepiece.bpe.model (LFS: 988bc5a)
	special_tokens_map.json (Git: cb23931)
	tf_model.h5 (LFS: 86fce29)
	tokenizer.json (Git: 851ff3e)
	tokenizer_config.json (Git: f0f7783)

Objects not staged for commit:


```

Tüm dosyaların bir işleyici (handler) olarak `Git`'e sahip olduğunu görebiliriz, `LFS`'ye sahip *t5_model.h5* hariç. Harika!

{/if}

Şimdi son adımlara geçelim, commitlemek (commit) ve *huggingface.co* uzak (remote) deposuna itmek (pushing):

```bash
git commit -m "First model version"
```

{#if fw === 'pt'}
```bash
[main b08aab1] First model version
 7 files changed, 29027 insertions(+)
  6 files changed, 36 insertions(+)
 create mode 100644 config.json
 create mode 100644 pytorch_model.bin
 create mode 100644 sentencepiece.bpe.model
 create mode 100644 special_tokens_map.json
 create mode 100644 tokenizer.json
 create mode 100644 tokenizer_config.json
```
{:else}
```bash
[main b08aab1] First model version
 6 files changed, 36 insertions(+)
 create mode 100644 config.json
 create mode 100644 sentencepiece.bpe.model
 create mode 100644 special_tokens_map.json
 create mode 100644 tf_model.h5
 create mode 100644 tokenizer.json
 create mode 100644 tokenizer_config.json
```
{/if}

İnternet bağlantınızın hızına ve dosyalarınızın boyutuna bağlı olarak itme (pushing) işlemi biraz zaman alabilir:

```bash
git push
```

```bash
Uploading LFS objects: 100% (1/1), 433 MB | 1.3 MB/s, done.
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 12 threads
Compressing objects: 100% (9/9), done.
Writing objects: 100% (9/9), 288.27 KiB | 6.27 MiB/s, done.
Total 9 (delta 1), reused 0 (delta 0), pack-reused 0
To https://huggingface.co/lysandre/dummy
   891b41d..b08aab1  main -> main
```

{#if fw === 'pt'}
Bu işlem bittiğinde model deposuna bir göz atarsak, yakın zamanda eklenen tüm dosyaları görebiliriz:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/full_model.png" alt="The 'Files and versions' tab now contains all the recently uploaded files." width="80%"/>
</div>

Kullanıcı arayüzü (UI), model dosyalarını ve commit'leri keşfetmenize ve her commit tarafından ortaya çıkan farkları (diff) görmenize olanak tanır:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/diffs.gif" alt="The diff introduced by the recent commit." width="80%"/>
</div>
{:else}
Bu işlem bittiğinde model deposuna bir göz atarsak, yakın zamanda eklenen tüm dosyaları görebiliriz:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/full_model_tf.png" alt="The 'Files and versions' tab now contains all the recently uploaded files." width="80%"/>
</div>

Kullanıcı arayüzü (UI), model dosyalarını ve commit'leri keşfetmenize ve her commit tarafından ortaya çıkan farkları (diff) görmenize olanak tanır:

<div class="flex justify-center">
<img src="https://huggingface.co/datasets/huggingface-course/documentation-images/resolve/main/en/chapter4/diffstf.gif" alt="The diff introduced by the recent commit." width="80%"/>
</div>
{/if}
