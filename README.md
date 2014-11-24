# Android geliştirme için örnek alınası davranışlar

Buradaki dersler Futurice'nin Android geliştiricilerinden öğrenildi. Buradaki rehberleri takip ederek tekerleği yeniden icat etmekten kaçınabilirsiniz. Windows Phone ya da iOS geliştirmeyle ilgileniyorsanız [**iOS için Örnek Alınası Davranışlar**](https://github.com/futurice/ios-good-practices) veya [**Windows Phone için Örnek Alınası Davranışlar**](https://github.com/futurice/win-client-dev-good-practices) belgelerine bir bakın.

Geribildirimler başımızın üstüne ama öncelikle [bu belgeye](https://github.com/futurice/android-best-practices/tree/master/CONTRIBUTING.md) bir göz atın.

[![Android Arsenal](https://img.shields.io/badge/Android%20Arsenal-android--best--practices-brightgreen.svg?style=flat)](https://android-arsenal.com/details/1/1091)

## Özet

#### Gradle'ı ve onun tavsiye edilen proje yapısını kullanın
#### Parolaları ve gizli kalması gereken bilgileri gradle.properties'in içine koyun
#### Kendi HTTP istemcisini yazmayın, Volley ya da Retrofit kütüphanelerini kullanın.
#### JSON verilerini ayrıştırmak (parse) için Jackson kütüphanesini kullanın
#### Ağ işleri ve görseller için Volley veya Retrofit+OkHttp+Picasso kullanın
#### Guava'dan kaçının ve *65k metot limiti* nedeniyle birkaç kütüphaneden fazlasını kullanmayın
#### UI ekranı sunmak için Fragment'ları kullanın
#### Fragment'ları yönetmek için Activity'leri kullanın
#### Layout XML'leri koddur; doğru düzgün organize edin
#### Style kullanın ve layout XML'lerinde mükerrer attibute kullanmaktan kaçının
#### Kocaman bir tanesiyle uğraşmak yerine birden fazla style dosyası kullanın
#### colors.xml dosyanızı kısa tutun, tekrarlamayın, sadece palet tanımlamanız bile yeterli*
#### dimens.xml dosyanız da tekrarlama yapmayın, genel sabitleri tanımlamaya bakın
#### ViewGroup'lar için uzun uzadıya hiyerarşiler kurmayın
#### WebView'larda istemci tarafı işlemlerden kaçının ve sızıntılara dikkat edin
#### Roboelectris ile Activity'leri, Fragment'ları ve View'ları test etmekten kaçının


----------

### Android SDK
[Android SDK](https://developer.android.com/sdk/installing/index.html?pkg=tools)'nızı ev dizininizde bir yere veya uygulamadan bağımsız başka bir yerde tutun. Bazı IDE'ler SDK ile beraber gelir ve SDK IDE ile aynı dizinde olabilir. Bu durum IDE'yi güncelleyeceğinizde (ya da yeniden kuracağınızda) hiç de iyi bir şey değildir. Aynı şekilde IDE değiştireceğinizde. Aynı şekilde IDE'niz sizin kullanıcınızda (root altında değil) çalışacaksa SDK'yı sistem seviyesinde ve sudo izinleri gerektirecek bir yere almaktan da kaçının.

### İnşa sistemi (Build system)

İnşa sistemi konusundaki öntanımlı seçeneğiniz [Gradle](http://tools.android.com/tech-docs/new-build-system) olsun. Ant daha sınırlı çalışır ve geveze (verbose) çalışır - çıktısı çoktur. Gradle ile şunlar çok kolaydır:

- Uygulamanızın farklı varyantlarını/sürümlerini inşa etmek
- Betik gibi kısa kısa yapılarla basit görevler oluşturmak
- Bağımlılıkları yönetmek ve indirmek
- Keystore'ları özelleştirmek
- ve daha fazlası

Android'in Gradle eklentisi yeni standart inşa sistemi olarak Google tarafından aktif bir biçimde geliştirilmektedir.

### Proje yapısı

Çok bilinen iki seçeneğiniz var: eski Ant & Eclipse ADT proje yapısı ya da yeni Gradle & Android Studio proje yapısı. Biz yeni proje yapısını kullanıyor olsak bile hangisini kullanıp kullanmayacağınızı siz seçebilirsiniz. Eskisini seçseniz bile `build.gradle` dosyasıyla beraber bir Gradle yapılandırması sağlamaya çalışın.

Eski yapı:

```
eski-yapi
├─ assets
├─ libs
├─ res
├─ src
│  └─ com/futurice/project
├─ AndroidManifest.xml
├─ build.gradle
├─ project.properties
└─ proguard-rules.pro
```

Yeni yapı:

```
yeni-yapi
├─ falanca-kutuphanesi
├─ app
│  ├─ libs
│  ├─ src
│  │  ├─ androidTest
│  │  │  └─ java
│  │  │     └─ com/futurice/project
│  │  └─ main
│  │     ├─ java
│  │     │  └─ com/futurice/project
│  │     ├─ res
│  │     └─ AndroidManifest.xml
│  ├─ build.gradle
│  └─ proguard-rules.pro
├─ build.gradle
└─ settings.gradle
```

Yeni yapının en temel farklı açıkça ayrılmış 'source set'leridir (`main`, `androidTest`) ki bu Gradle'ın bir konseptidir. Bu sayede örneğin `src` dizininin altına, uygulamanızın ücretli ve ücretsiz sürümlerinin kaynak kodlarını tutacak 'parali' ve 'bedava' isimli dizinler şeklinde source set'ler koyabilirsiniz.

Üst seviye bir `app` dizini, uygulamanıza referans olarak gösterdiğiniz kütüphane projelerini (bkz: `falanca-kutuphanesi`) uygulamanızdan ayırt etmek açısından kullanışlıdır. `settings.gradle` dosyası da daha sonra  `app/build.gradle` dosyasında referans olarak kullanabileceğiniz bu kütüphane projelerinin referanslarını tutar.

### Gradle yapılandırması

**Genel yapı.** Bunun için [Google'ın Android için Gradle rehberi](http://tools.android.com/tech-docs/new-build-system/user-guide)ni takip edin.

**Kısa kısa görevler.** Betikler (shell, Python, Perl, etc) yerine Gradle'ın içinde de görevler oluşturabilirsiniz. Daha fazla ayrıntı için sadece [Gradle'ın belgelendirmesi](http://www.gradle.org/docs/current/userguide/userguide_single.html#N10CBF)ni okumanız yeterli.

**Parolalar.** Uygulamanızın `build.gradle` dosyasında, uygulamanızın canlı (release) inşası için `signingConfigs`i tanımlamanız gerekecek. Burada şunları yapmaktan kaçının:

_Böyle yapmayın_. Bu şekilde bu parola bilgisi sürüm kontrol sisteminde (Git gibi) görünür.

```groovy
signingConfigs {
    release {
        storeFile file("myapp.keystore")
        storePassword "parola123456"
        keyAlias "anahtar"
        keyPassword "parola123456"
    }
}
```

Bunun yerine sürüm kontrol sistemine _eklememeniz_ gereken bir `gradle.properties` dosyası oluşturun:

```
KEYSTORE_PASSWORD=parola123456
KEY_PASSWORD=parola123456
```

Bu dosya otomatik olarak Gradle tarafından içeri aktarılır ve bu sayede tıpkı şuradaki gibi `build.gradle` dosyasında kullanabilirsiniz:

```groovy
signingConfigs {
    release {
        try {
            storeFile file("myapp.keystore")
            storePassword KEYSTORE_PASSWORD
            keyAlias "anahtar"
            keyPassword KEY_PASSWORD
        }
        catch (ex) {
            throw new InvalidUserDataException("Ops! gradle.properties içinde KEYSTORE_PASSWORD ve KEY_PASSWORD tanımlamanız gerekiyor.")
        }
    }
}
```

**jar dosyalarını `import` edeceğinize Maven'ın bağımlılık çözümlemesini tercih edin.** Eğer elle projenize jar dosyalarını eklerseniz, bir yerden sonra onların eklediğiniz gibi kalmış, `2.1.1` gibi sürümleriyle başbaşa kalacaksınız. Jar'ları indirmek ve güncellemelerini yönetmek çok hantal bir çözüm ve Maven bu problemi çok güzel çözüyor ve aynı şekilde Android'in Gradle inşaları da böyle yürüyor. İsterseniz sürümler için `2.1.+` gibi kapsamlar tanımlayabilirsiniz ve bu sayede Maven bu şablona uyan en son sürümleri güncelleme işini otomatik olarak yönetir. Örnek:

```groovy
dependencies {
    compile 'com.netflix.rxjava:rxjava-core:0.19.+'
    compile 'com.netflix.rxjava:rxjava-android:0.19.+'
    compile 'com.fasterxml.jackson.core:jackson-databind:2.4.+'
    compile 'com.fasterxml.jackson.core:jackson-core:2.4.+'
    compile 'com.fasterxml.jackson.core:jackson-annotations:2.4.+'
    compile 'com.squareup.okhttp:okhttp:2.0.+'
    compile 'com.squareup.okhttp:okhttp-urlconnection:2.0.+'
}
```

### IDE'ler ve metin editörleri

**İstediğiniz editörü kullanın ama proje yapısıyla güzelce uymasına da dikkat edin.** Editörler kişisel seçimdir ve editörünüzü proje yapısı ve inşa sistemine uyumlu çalıştırmak sizin sorumluluğunuzdur.

Şu an Google tarafından geliştirilme, Gradle'a yakınlık, öntanımlı olarak yeni proje yapısını kullanmak gibi sebeplerden ötürü en çok önerilen IDE [Android Studio](https://developer.android.com/sdk/installing/studio.html). Beta aşamasından çıktı ve Android geliştirmeye epey de uygun bir halde.

Dilerseniz [Eclipse ADT](https://developer.android.com/sdk/installing/index.html?pkg=adt) de kullanabilirsiniz fakat inşa işleri için Ant'yi ve eski proje yapısını kullandığından dolayı yapılandırmak zorunda kalırsınız. İsterseniz Vim, Sublime Text veya Emacs gibi düz metin editörlerini bile kullanabilirsiniz. Fakat bu sefer de Gradle'ı ve komut satırından `adb`yi kullanmanız gerekecek. Eğer Eclipse'in Gradle ile olan entegrasyonu sizde çalışmazsa projenizi inşa etmek için komut satırını kullanmak ya da Android Studio'ya göç etmek seçenekleriniz arasında.

Ne kullanırsanız kullanın, sadece Gradle ve yeni proje yapısının uygulama inşa etmek için resmî yol olarak kalacağını bilin ve sürüm kontrol sistemlerine editör-spesifik yapılandırma dosyalarınızı eklemekten kaçının. Örneğin Ant inşa sisteminin `build.xml` dosyasını eklemekten kaçının. İnşa yapılandırmalarını Ant'nin içinde değiştiriyorsanız özellikle `build.gradle`ı güncel tutmayı unutmayın. Aynı zamanda diğer geliştiricilere bir iyilik yapın ve onları tercih ettikleri araçları değiştirmeye zorlamayın.

### Kütüphaneleler

**[Jackson](http://wiki.fasterxml.com/JacksonHome)**, nesneleri JSON'a vs çeviren bir Java kütüphanesidir. [Gson](https://code.google.com/p/google-gson/) bu problemi çözen gözde bir seçim olsa bile, Jackson'ı JSON işlemenin şu alternatif yollarını desteklediği için daha akıllıca buluyoruz: akıştırma (streaming), bellek içi ağaç modeli (in-memory tree model) ve geleneksel JSON-POJO veri bağlama (data-binding) desteği. Diğer seçenekler: [Json-smart](https://code.google.com/p/json-smart/) ve [Boon JSON](https://github.com/RichardHightower/boon/wiki/Boon-JSON-in-five-minutes)

**Ağ işleri, önbellekleme ve görseller.** Arkayüzdeki (backend) sunuculara istekte bulunma işlerini yapmak için kendi istemcinizi yazmanın yanında mutlaka hesaba katmanız gereken, rüştünü ispatlamış çözümler var.  [Volley](https://android.googlesource.com/platform/frameworks/volley) veya [Retrofit](http://square.github.io/retrofit/)'i bu amaçla kullanabilirsiniz. Volley aynı zamanda görselleri yüklemek ve önbelleklemek için helper metotlar da sunuyor. Eğer Retrofit'i kullanacaksanız, görselleri yükleme ve önbellekleme işleri için [Picasso](http://square.github.io/picasso/)'yu ve verimli HTTP istekleri için [OkHttp](http://square.github.io/okhttp/)'yi kullanabilirsiniz. Retrofit, Picasso ve OkHttp üçlüsü aynı şirket tarafından geliştirilmiştir ve bir diğerini güzelce tamamlar. [OkHttp aynı zamanda Volley ile bağlantı kurmakta da kullanılabilir](http://stackoverflow.com/questions/24375043/how-to-implement-android-volley-with-okhttp-2-0/24951835#24951835).

**RxJava** bir Reaktif Programlama kütüphanesidir. Bir başka deyişle eşzamansız olayları (asynchronous events) yönetir. Güçlü ve umut vaat eden bir yaklaşımı vardır ki bu da onun en çok karıştırılan yanıdır. Bu kütüphaneyi tüm uygulama mimarisine uygulamaya çalışmadan önce biraz temkinli yaklaşmanızı öneriyoruz. RxJava kullanarak yaptığımız bazı projeler var. Yardıma ihtiyacınız varsa şu insanlardan biriyle konuşmayı deneyin: Timo Tuominen, Olli Salonen, Andre Medeiros, Mark Voit, Antti Lammi, Vera Izrailit, Juha Ristolainen. Bu abiler sayesinde bu konuda yazılmış blog yazılarımızı okuyabilirsiniz: [[1]](http://blog.futurice.com/tech-pick-of-the-week-rx-for-net-and-rxjava-for-android), [[2]](http://blog.futurice.com/top-7-tips-for-rxjava-on-android), [[3]](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754), [[4]](http://blog.futurice.com/android-development-has-its-own-swift).

Eğer Rx ile hiç tecrübeniz yoksa onu sadece API'den dönen yanıtlara uygulamayı deneyin. Bir başka seçenek olarak, tıklama veya bir arama kutusuna yazma gibi basit UI olaylarının yönetimlerine uygulamayı deneyebilirsiniz. Rx yetenekleriniz konusunda iddialıysanız ve onu tüm mimarinize uygulamak istiyorsanız, onunla yaptığınız tüm cambazlıkları Javadoc olarak yazmaya çalışın. RxJava'ya aşina olmayan bir başka programcının proje bakımında çok zor zamanlar geçirebileceğini aklınızdan çıkarmayın. Ona kodunuzu ve Rx'i anlamak konusunda en iyisini yapın - [@oncekiyazilimci](https://twitter.com/oncekiyazilimci)'lardan olmayın.

**[Retrolambda](https://github.com/evant/gradle-retrolambda)** Lambda ifadesi söz dizimini Android'de ve JDK8 öncesi platformlarda kullanabilmenizi sağlayan bir Java kütüphanesidir. Örneğin RxJava ile birlikte fonksiyonel bir tarz kullanıyorsanız kodunuzu okunaklı ve sıkı tutmanıza yardımcı olur. Kullanmak için JDK8 kurun ve bunu "Android Studio Project Structure" diyalogundan SDK Location'ınız olarak ayarlayın. Ardından `JAVA8_HOME` ve `JAVA7_HOME` çevresel değişkenlerinizi ayarlayın ve **projenizin kökündeki** build.gradle dosyasına şunu ekleyin:

```groovy
dependencies {
    classpath 'me.tatarka:gradle-retrolambda:2.4.+'
}
```

ve her modülün build.gradle dosyasında da şu değişikliği yapın:

```groovy
apply plugin: 'retrolambda'

android {
    compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
}

retrolambda {
    jdk System.getenv("JAVA8_HOME")
    oldJdk System.getenv("JAVA7_HOME")
    javaVersion JavaVersion.VERSION_1_7
}
```

Android Studio size Java8 lambdaları için kod asistanlığı yapar. Eğer Lambdalar konusunda yeniyseniz başlamak için şu tavsiyelere uyun:

- Sadece bir metot içeren arayüz sınıfları (interface class) birer "lambda dostu"dur ve daha sıkı bir söz dizimiyle 'katlanabilirler' (foldable)
- Parametreler ve bunun gibi şeylerde sorun olursa, normal, bildiğiniz bir anonim dahili sınıf yazın ve Android Studio'ya sizin yerinize onu bir lambda ifadesi içinde 'katlamasını' söyleyin.

**dex metot sınırlamasına dikkat edin ve çok fazla kütüphane kullanmaktan kaçının.** Android uygulamaları bir dex dosyası olarak paketlenirken, aynı zamanda 65536 adet referanslanmış metot sınırlamasına da uğrar. [[1]](https://medium.com/@rotxed/dex-skys-the-limit-no-65k-methods-is-28e6cb40cf71) [[2]](http://blog.persistent.info/2014/05/per-package-method-counts-for-androids.html) [[3]](http://jakewharton.com/play-services-is-a-monolith/). Bu sınırı geçerseniz derleme sırasında fatal error görürsünüz. Bu sebepten dolayı en az miktarda kütüphane kullanın ve kütüphanelerin sınırın altında kaldığını belirleyebilmek için [dex-method-counts](https://github.com/mihaip/dex-method-counts) gibi araçları kullanın. Özellikle Guava kütüphanesini kullanmaktan kaçının - 13 binin üzerinde metot içeriyor.

### Activity'ler ve Fragment'lar

[Fragment](http://developer.android.com/guide/components/fragments.html)'lar Android'de bir UI ekranı gerçeklerken öntanımlı seçeneğiniz olmalı. Fragment'lar uygulamanızda bir araya getirilen yeniden kullanılabilir kullanıcı arayüzleridir. Kullanıcıya ekran arayüzleri sunarken [Activity](http://developer.android.com/guide/components/activities.html)'leri kullanmak yerine Fragmet kullanmanızı öneriyoruz. Bunun birkaç nedeni şöyle:

- **Çok bölmeli layout'lar için çözümdür.** Fragment'lar öncelikle telefon uygulamalarını tablet ekranlarına uyumlu hale getirmek için tanıtıldı ve böylece  A veya B bölmelerinden herhangi biri tüm telefon ekranını kaplayabiliyorken, bir tablet ekranında ikisini bir arada tutabiliyorsunuz. Eğer uygulamanızı en başından beri Fragment kullanarak yazarsanız, daha sonra farklı form faktörlerine uyumlu hale getirmek sizin için zor olmayacaktır.

- **Ekranlar arası iletişime uygun.** Android'in API'si bir Activity'den diğer Activity'ye kompleks verileri (örn: bazı Java nesnelerini) taşıma konusunda uygun bir yol sunmuyor. Fragment'lar ile Activity'nin bir örneğini (instance) onun 'çocuk' fragmentları arasındaki iletişim için bir kanal olarak kullanabilirsiniz.

- **Fragment'lar sadece UI'dan ibaret olmayan genel bir çözümdür.** İsterseniz bir [Fragment'ı UI olmadan](http://developer.android.com/guide/components/fragments.html#AddingWithoutUI) Activity için arkaplan işlerini yapacak şekilde kullanabilirsiniz. Fragment'ları değiştirmek için Activity'nin içinde bir iş mantığı oturtmak yerine [iş mantığı içeren bir fragment](http://stackoverflow.com/questions/12363790/how-many-activities-vs-fragments/12528434#12528434) oluşturarak bu fikri ileriye taşıyabilirsiniz.

- **ActionBar bile Fragment içerisinden yönetilebilir.** Sırf ActionBar'ı yönetmek için UI'ı olmayan bir Fragment oluşturma yolunu seçebilirsiniz veya o an görünen Fragment'ın eylemlerine uygun öğeleri, üstündeki Activity'nin ActionBar'ına ekleyebilirsiniz. [Daha fazlasını buradan](http://www.grokkingandroid.com/adding-action-items-from-within-fragments/) bulabilirsiniz.

Hep söylendiği gibi biz de [matruşka hataları](http://delyan.me/android-s-matryoshka-problem/)na neden olabilen kapsamlı bir [iç içe-nested fragment](https://developer.android.com/about/versions/android-4.2.html#NestedFragments) kullanımını **önermiyoruz**. İç içe Fragment'ları sadece kullanmanıza değecek yerlerde (örneğin, ekrana benzeyen Fragment'lar olarak, parmak hareketleriyle yana kaydırılan ViewPager'ın içindeki Fragment'lar şeklinde) veya epey bilgi sahibiyseniz kullanın.

Mimari olarak en tepede uygulamanızın üst seviye bir Activity'si olmalı. Bu Activity iş mantığıyla ilgili Fragment'ların çoğunu içermeli. Aynı zamanda destek amaçlı başka Activity'ler de kullanabilirsiniz ama onların ana Activity ile iletişimi basit ve [`Intent.setData()`](http://developer.android.com/reference/android/content/Intent.html#setData(android.net.Uri)) veya [`Intent.setAction()`](http://developer.android.com/reference/android/content/Intent.html#setAction(java.lang.String)) veya benzeri metotlarla sınırlıdır.

### Java Paketlerinin Yapısı

Android uygulamalarınızdaki Java yapısı, aşağı yukarı [Model-View-Controller](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) yapısına benzeyebilmeli. Android'de, [Fragment ve Activity aslında birer Controller sınıftır](http://www.informit.com/articles/article.aspx?p=2126865). Diğer taraftan bariz bir biçimde kullanıcı arayüzünün bir parçasıdır ki bu yüzden View katmanı gibidirler.

Tüm bu nedenlerle Fragment'ları (veya Activity'leri) Controller veya View olarak kesin bir sınıflamaya sokmak zor.
En iyisi kendi `fragments` paketlerinde kalması. Önceki bölümdeki önerileri uyguladığınız müddetçe, Activity'ler üst seviye bir pakette kalabilir. Eğer 2 veya 3'ten fazla Activity'nizin olmasını planlıyorsanız, bir de `activities` paketiniz olmalı.

Bir diğer yandan, paket yapınız tipik MVC yapısına da benzeyebilmeli. API yanıtlarından gelen JSON'ları saklayabileceğiniz POJO'lar (Plain Old Java Object) içeren `models` paketi olmalı; özel View sınıflarınınızı, bildirimlerinizi, widget'larınızı vs bulunduran bir `views` paketi de olmalı. **Adapter**'lar birer gri maddedir. Veri ile View'lar arasında yaşar ve `getView()` metodu aracılığıyla bazı View'ları dışarı göndermeleri gerekir. View'lar ile bu kadar içli dışlı çalıştığından dolayı `views` paketinin altına bir de `adapters` alt paketi oluşturmalısınız. Bunları tamamen iyilik olsu diye söylüyoruz, unutmayın.

Bazı controller sınıfları uygulama geneli çalışır ve Android sistemine yakındır. Böyle sınıflar `managers` paketinin içinde durabilir. Çok amaçlı veri işleme sınıfları `utils` paketinde durabilir. Arkayüz (backend) ile etkileşimden sorumlu sınıflar da `network` paketinde durabilir.

Hepsini hesaba katarsak arkayüze en yakından kullanıcıya en yakın sınıfları şöyle sıralarız:

```
com.futurice.project
├─ network
├─ models
├─ managers
├─ utils
├─ fragments
└─ views
   ├─ adapters
   ├─ actionbar
   ├─ widgets
   └─ notifications
```

### Resource'lar

**Naming.** Follow the convention of prefixing the type, as in `type_foo_bar.xml`. Examples: `fragment_contact_details.xml`, `view_primary_button.xml`, `activity_main.xml`.

**Organizing layout XMLs.** If you're unsure how to format a layout XML, the following convention may help.

- One attribute per line, indented by 4 spaces
- `android:id` as the first attribute always
- `android:layout_****` attributes at the top
- `style` attribute at the bottom
- Tag closer `/>` on its own line, to facilitate ordering and adding attributes.
- Rather than hard coding `android:text`, consider using [Designtime attributes](http://tools.android.com/tips/layout-designtime-attributes) available for Android Studio.

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    >

    <TextView
        android:id="@+id/name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentRight="true"
        android:text="@string/name"
        style="@style/FancyText"
        />

    <include layout="@layout/reusable_part" />

</LinearLayout>
```

As a rule of thumb, attributes `android:layout_****` should be defined in the layout XML, while other attributes `android:****` should stay in a style XML. This rule has exceptions, but in general works fine. The idea is to keep only layout (positioning, margin, sizing) and content attributes in the layout files, while keeping all appearance details (colors, padding, font) in styles files.

The exceptions are:

- `android:id` should obviously be in the layout files
- `android:orientation` for a `LinearLayout` normally makes more sense in layout files
- `android:text` should be in layout files because it defines content
- Sometimes it will make sense to make a generic style defining `android:layout_width` and `android:layout_height` but by default these should appear in the layout files

**Use styles.** Almost every project needs to properly use styles, because it is very common to have a repeated appearance for a view. At least you should have a common style for most text content in the application, for example:

```xml
<style name="ContentText">
    <item name="android:textSize">@dimen/font_normal</item>
    <item name="android:textColor">@color/basic_black</item>
</style>
```

Applied to TextViews:

```xml
<TextView
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@string/price"
    style="@style/ContentText"
    />
```

You probably will need to do the same for buttons, but don't stop there yet. Go beyond and move a group of related and repeated `android:****` attributes to a common style.

**Split a large style file into other files.** You don't need to have a single `styles.xml` file. Android SDK supports other files out of the box, there is nothing magical about the name `styles`, what matters are the XML tags `<style>` inside the file. Hence you can have files `styles.xml`, `styles_home.xml`, `styles_item_details.xml`, `styles_forms.xml`. Unlike resource directory names which carry some meaning for the build system, filenames in `res/values` can be arbitrary.

**`colors.xml` is a color palette.** There should be nothing else in your `colors.xml` than just a mapping from a color name to an RGBA value. Do not use it to define RGBA values for different types of buttons.

*Don't do this:*

```xml
<resources>
    <color name="button_foreground">#FFFFFF</color>
    <color name="button_background">#2A91BD</color>
    <color name="comment_background_inactive">#5F5F5F</color>
    <color name="comment_background_active">#939393</color>
    <color name="comment_foreground">#FFFFFF</color>
    <color name="comment_foreground_important">#FF9D2F</color>
    ...
    <color name="comment_shadow">#323232</color>
```

You can easily start repeating RGBA values in this format, and that makes it complicated to change a basic color if needed. Also, those definitions are related to some context, like "button" or "comment", and should live in a button style, not in `colors.xml`.

Instead, do this:

```xml
<resources>

    <!-- grayscale -->
    <color name="white"     >#FFFFFF</color>
    <color name="gray_light">#DBDBDB</color>
    <color name="gray"      >#939393</color>
    <color name="gray_dark" >#5F5F5F</color>
    <color name="black"     >#323232</color>

    <!-- basic colors -->
    <color name="green">#27D34D</color>
    <color name="blue">#2A91BD</color>
    <color name="orange">#FF9D2F</color>
    <color name="red">#FF432F</color>

</resources>
```

Ask for this palette from the designer of the application. The names do not need to be color names as "green", "blue", etc. Names such as "brand_primary", "brand_secondary", "brand_negative" are totally acceptable as well. Formatting colors as such will make it easy to change or refactor colors, and also will make it explicit how many different colors are being used. Normally for a aesthetic UI, it is important to reduce the variety of colors being used.

**Treat dimens.xml like colors.xml.** You should also define a "palette" of typical spacing and font sizes, for basically the same purposes as for colors. A good example of a dimens file:

```xml
<resources>

    <!-- font sizes -->
    <dimen name="font_larger">22sp</dimen>
    <dimen name="font_large">18sp</dimen>
    <dimen name="font_normal">15sp</dimen>
    <dimen name="font_small">12sp</dimen>

    <!-- typical spacing between two views -->
    <dimen name="spacing_huge">40dp</dimen>
    <dimen name="spacing_large">24dp</dimen>
    <dimen name="spacing_normal">14dp</dimen>
    <dimen name="spacing_small">10dp</dimen>
    <dimen name="spacing_tiny">4dp</dimen>

    <!-- typical sizes of views -->
    <dimen name="button_height_tall">60dp</dimen>
    <dimen name="button_height_normal">40dp</dimen>
    <dimen name="button_height_short">32dp</dimen>

</resources>
```

You should use the `spacing_****` dimensions for layouting, in margins and paddings, instead of hard-coded values, much like strings are normally treated. This will give a consistent look-and-feel, while making it easier to organize and change styles and layouts.

**Avoid a deep hierarchy of views.** Sometimes you might be tempted to just add yet another LinearLayout, to be able to accomplish an arrangement of views. This kind of situation may occur:

```xml
<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    >

    <RelativeLayout
        ...
        >

        <LinearLayout
            ...
            >

            <LinearLayout
                ...
                >

                <LinearLayout
                    ...
                    >
                </LinearLayout>

            </LinearLayout>

        </LinearLayout>

    </RelativeLayout>

</LinearLayout>
```

Even if you don't witness this explicitly in a layout file, it might end up happening if you are inflating (in Java) views into other views.

A couple of problems may occur. You might experience performance problems, because there are is a complex UI tree that the processor needs to handle. Another more serious issue is a possibility of [StackOverflowError](http://stackoverflow.com/questions/2762924/java-lang-stackoverflow-error-suspected-too-many-views).

Therefore, try to keep your views hierarchy as flat as possible: learn how to use [RelativeLayout](https://developer.android.com/guide/topics/ui/layout/relative.html), how to [optimize your layouts](http://developer.android.com/training/improving-layouts/optimizing-layout.html) and to use the [`<merge>` tag](http://stackoverflow.com/questions/8834898/what-is-the-purpose-of-androids-merge-tag-in-xml-layouts).

**Beware of problems related to WebViews.** When you must display a web page, for instance for a news article, avoid doing client-side processing to clean the HTML, rather ask for a "*pure*" HTML from the backend programmers. [WebViews can also leak memory](http://stackoverflow.com/questions/3130654/memory-leak-in-webview) when they keep a reference to their Activity, instead of being bound to the ApplicationContext. Avoid using a WebView for simple texts or buttons, prefer TextViews or Buttons.


### Test frameworks

Android SDK's testing framework is still infant, specially regarding UI tests. Android Gradle currently implements a test task called [`connectedAndroidTest`](http://tools.android.com/tech-docs/new-build-system/user-guide#TOC-Testing) which runs JUnit tests that you created, using an [extension of JUnit with helpers for Android](http://developer.android.com/reference/android/test/package-summary.html). This means you will need to run tests connected to a device, or an emulator. Follow the official guide [[1]](http://developer.android.com/tools/testing/testing_android.html) [[2]](http://developer.android.com/tools/testing/activity_test.html) for testing.

**Use [Robolectric](http://robolectric.org/) only for unit tests, not for views.** It is a test framework seeking to provide tests "disconnected from device" for the sake of development speed, suitable specially for unit tests on models and view models. However, testing under Robolectric is inaccurate and incomplete regarding UI tests. You will have problems testing UI elements related to animations, dialogs, etc, and this will be complicated by the fact that you are "walking in the dark" (testing without seeing the screen being controlled).

**[Robotium](https://code.google.com/p/robotium/) makes writing UI tests easy.** You do not need Robotium for running connected tests for UI cases, but it will probably be beneficial to you because of its many helpers to get and analyse views, and control the screen. Test cases will look as simple as:

```java
solo.sendKey(Solo.MENU);
solo.clickOnText("More"); // searches for the first occurence of "More" and clicks on it
solo.clickOnText("Preferences");
solo.clickOnText("Edit File Extensions");
Assert.assertTrue(solo.searchText("rtf"));
```

### Proguard configuration

[ProGuard](http://proguard.sourceforge.net/) is normally used on Android projects to shrink and obfuscate the packaged code.

Whether you are using ProGuard or not depends on your project configuration. Usually you would configure gradle to use ProGuard when building a release apk.

```groovy
buildTypes {
    debug {
        runProguard false
    }
    release {
        signingConfig signingConfigs.release
        runProguard true
        proguardFiles 'proguard-rules.pro'
    }
}
```

In order to determine which code has to be preserved and which code can be discarded or obfuscated, you have to specify one or more entry points to your code. These entry points are typically classes with main methods, applets, midlets, activities, etc.
Android framework uses a default configuration which can be found from `SDK_HOME/tools/proguard/proguard-android.txt`. Custom project-specific proguard rules, as defined in `my-project/app/proguard-rules.pro`, will be appended to the default configuration.

A common problem related to ProGuard is to see the application crashing on startup with `ClassNotFoundException` or `NoSuchFieldException` or similar, even though the build command (i.e. `assembleRelease`) succeeded without warnings.
This means one out of two things:

1. ProGuard has removed the class, enum, method, field or annotation, considering it's not required.
2. ProGuard has obfuscated (renamed) the class, enum or field name, but it's being used indirectly by its original name, i.e. through Java reflection.

Check `app/build/outputs/proguard/release/usage.txt` to see if the object in question has been removed.
Check `app/build/outputs/proguard/release/mapping.txt` to see if the object in question has been obfuscated.

In order to prevent ProGuard from *stripping away* needed classes or class members, add a `keep` options to your proguard config:
```
-keep class com.futurice.project.MyClass { *; }
```

To prevent ProGuard from *obfuscating* classes or class members, add a `keepnames`:
```
-keepnames class com.futurice.project.MyClass { *; }
```

Check [this template's ProGuard config](https://github.com/futurice/android-best-practices/blob/master/templates/rx-architecture/app/proguard-rules.pro) for some examples.
Read more at [Proguard](http://proguard.sourceforge.net/#manual/examples.html) for examples.

**Tip.** Save the `mapping.txt` file for every release that you publish to your users. By retaining a copy of the `mapping.txt` file for each release build, you ensure that you can debug a problem if a user encounters a bug and submits an obfuscated stack trace.

### Thanks to

Antti Lammi, Joni Karppinen, Peter Tackage, Timo Tuominen, Vera Izrailit, Vihtori Mäntylä, Mark Voit, Andre Medeiros, Paul Houghton and other Futurice developers for sharing their knowledge on Android development.

### License

[Futurice Oy](www.futurice.com)
Creative Commons Attribution 4.0 International (CC BY 4.0)
