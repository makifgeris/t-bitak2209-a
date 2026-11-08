# FED Faiz Kararları ve TÜFE Açıklamalarının ABD Hisse Senedi Piyasalarındaki Anlık Volatilite Etkisi

### Bulut Tabanlı Dağıtık Büyük Veri Mimarisi — Problem Tanımı, Hipotez Kurulumu ve Literatür Taraması

![Durum](https://img.shields.io/badge/durum-taslak-orange)
![Kapsam](https://img.shields.io/badge/kapsam-tez%20ön%20çalışması-blue)
![Dil](https://img.shields.io/badge/dil-TR%20%7C%20EN-lightgrey)

> [!NOTE]
> Bu belge bir tez/araştırma ön çalışmasıdır. Ampirik sonuç içermez; problem tanımı, test edilebilir hipotezler, literatür sentezi ve mimari öneri sunar.

---

## İçindekiler

- [1. Problem Tanımı](#1-problem-tanımı)
  - [Problem Cümlesi](#problem-cümlesi)
  - [Alt Problemler](#alt-problemler)
  - [Sınırlılıklar](#sınırlılıklar)
- [2. Araştırma Soruları ve Hipotez Kurulumu](#2-araştırma-soruları-ve-hipotez-kurulumu)
  - [Test Stratejisi](#test-stratejisi)
- [3. Literatür Taraması ve Özeti](#3-literatür-taraması-ve-özeti)
  - [3.1 Duyuru Etkisinin Zaman Yapısı](#31-duyuru-etkisinin-zaman-yapısı)
  - [3.2 Sürpriz Bileşeninin Ölçülmesi](#32-sürpriz-bileşeninin-ölçülmesi)
  - [3.3 Enflasyon Haberleri ve Hisse Senedi Getirileri](#33-enflasyon-haberleri-ve-hisse-senedi-getirileri)
  - [3.4 FOMC ve Volatilite: Literatürdeki Çelişki](#34-fomc-ve-volatilite-literatürdeki-çelişki)
  - [3.5 Bilgisel Volatilite / Gürültü Ayrıştırması](#35-bilgisel-volatilite--gürültü-ayrıştırması)
  - [3.6 Bulut Tabanlı Dağıtık Mimari](#36-bulut-tabanlı-dağıtık-mimari)
  - [3.7 Özet ve Tespit Edilen Boşluklar](#37-özet-ve-tespit-edilen-boşluklar)
- [4. Günün Çıktısı ve Yorumu](#4-günün-çıktısı-ve-yorumu)
  - [4.1 Bugün Üretilenler](#41-bugün-üretilenler)
  - [4.2 Bunlardan Ne Anladım — Altı İçgörü](#42-bunlardan-ne-anladım----altı-içgörü)
  - [4.3 Üzerine Konuşulabilecek / Araştırılması Gerekenler](#43-üzerine-konuşulabilecek--araştırılması-gerekenler)
- [Yol Haritası](#yol-haritası)
- [Kaynakça](#kaynakça)

---

## 1. Problem Tanımı

**Bağlam ve hareket noktası.** Makroekonomik duyurular, hisse senedi piyasasında fiyat oluşumunun en yoğun bilgi kanallarıdır; Fed faiz kararları ile TÜFE açıklamaları bu kanalın en yüksek etkili iki ucunu temsil eder. Yüksek frekanslı verilerle yapılan çalışmalar, fiyat düzeltmesinin büyük kısmının duyurudan sonraki ilk dakika içinde gerçekleştiğini, volatilitenin ise yaklaşık on beş dakika boyunca olağanın belirgin biçimde üzerinde, birkaç saat boyunca da hafifçe yüksek seyrettiğini göstermektedir [^1]. Bu, "anlık etki"nin saniye ve dakika ölçeğinde tanımlanması gerektiği anlamına gelir; günlük getirilerle çalışan geleneksel olay çalışmaları bu pencereyi yapısal olarak gözlemleyemez.

**Boşluk (gap).** Literatürün iki kanadı birbirinden kopuktur:

- **Ekonometrik kanat**, duyuru etkisini GARCH türevleri, gerçekleşen volatilite veya sıçrama testleriyle ölçer; ancak hesaplama yükü nedeniyle genellikle tek endeks, kısa örneklem ve tek makine ölçeğinde kalır. Yüksek frekanslı istatistikte temel darboğazın, masaüstü sistemlerin kapasitesini aşan veri hacmi ve optimizasyon yükü olduğu açıkça belirtilmektedir [^2].
- **Veri mühendisliği kanadı**, Kafka/Spark/Flink tabanlı düşük gecikmeli, hataya dayanıklı boru hatlarını ve bulut-yerlisi esnekliği tartışır [^3], hatta TAQ ölçeğinde mesaj başına 3,6 nanosaniye erişim gecikmesi ve 53 dakikada 11 petabayt düzeyinde analitik tamamlanması raporlanmıştır [^4]; fakat bu çalışmalar nedensel-ekonometrik çıkarımı değil, altyapı performansını merkeze alır.

Dolayısıyla **problem**, şu ikisinin aynı sistemde birleştirilmemesidir: 
1. Duyuru anındaki volatilite sıçramasının ekonomik olarak yorumlanabilir biçimde ölçülmesi,
2. Bunun petabayt ölçeğinde, olay-zamanı hizalı ve tekrarlanabilir bir dağıtık mimaride üretilmesi.

Bu birleşme olmadan, *"TÜFE sürprizi ile Fed sürprizi hangi zaman ölçeğinde, ne büyüklükte ve ne kadar kalıcı bir volatilite yaratıyor?"* sorusu ne istatistiksel olarak yeterince ince, ne de hesaplama olarak yeterince geniş yanıtlanabilir.

### Problem cümlesi

> Fed faiz kararları ve TÜFE açıklamalarının ABD hisse senedi piyasasında yarattığı anlık volatilite tepkisi, sürprizin büyüklüğü, işareti ve duyuru türüne göre nasıl farklılaşır; bu tepki bilgisel volatilite ile piyasa mikro yapısı kaynaklı gürültüden ne ölçüde beslenir ve söz konusu ölçüm, bulut tabanlı dağıtık bir büyük veri mimarisi üzerinde ölçeklenebilir ve tekrarlanabilir biçimde nasıl üretilebilir?

### Alt problemler

| # | Alt problem |
|---|---|
| **(a)** | Olay penceresi tasarımı: duyuru öncesi dönem nereye kadar dahil edilmeli? |
| **(b)** | Sürpriz ölçüsü: anket medyanına dayalı standartlaştırılmış sapma mı, federal fon vadeli işlem fiyatlamasından türetilen sürpriz mi? |
| **(c)** | Frekans: 1 saniye, 10 saniye, 1 dakika, 5 dakika — hangi örnekleme gürültüyü en az şişirir? |
| **(d)** | Mimari: akış (streaming) ile mikro-parti (mini-batch) arasındaki denge, gecikme ile istatistiksel doğruluk arasında nasıl kurulur? |

### Sınırlılıklar

Zaman damgası hassasiyeti ve saat senkronizasyonu (NTP/PTP) hataları, eşzamanlı açıklanan duyuruların ayırt edilememesi [^5], sıçrama tespitinde yanlış pozitif riski [^6], ve çoklu test problemi — tek bir çalışmada 23 duyuru kategorisi incelenebilmektedir [^7].

---

## 2. Araştırma Soruları ve Hipotez Kurulumu

Aşağıdaki hipotezler, her biri iki yönlü test edilebilir biçimde ($H_0 / H_1$) ve ölçüm karşılığıyla kurulmuştur.

| Kod | Hipotez ($H_1$) | Kuramsal / Ampirik Dayanak | Ölçüm |
|---|---|---|---|
| **H1a** | Fed faiz kararı duyurusu, duyuru sonrası ilk 5–15 dakikada gerçekleşen volatilitede anlamlı artış yaratır. | Fiyat düzeltmesinin ilk dakikada, volatilitenin on beş dakika yüksek kalması; FOMC günlerinde 14:00 ET'de başlayan 5 dakikalık dilimde ani sıçrama [^8] | Olay-zamanı hizalı $RV / 5\text{ dk}$; olay kuklası |
| **H1b** | TÜFE duyurusunun etkisi daha kısa ömürlüdür ve faiz kararından daha erken zirve yapar. | Enflasyon duyurusuna tepki ilk 5 dakikada, faiz duyurusuna tepki 15 dakika sonra ve daha uzun süreli [^9] | Etki yarı-ömrü ($\text{dk}$); zirve gecikmesi |
| **H2** | Volatilite tepkisi, sürprizin **mutlak büyüklüğü** ile pozitif ilişkilidir. | Sürprizlerin büyüklüğü işaretinden daha önemli [^10]; standartlaştırılmış z-tipi sürpriz ölçüsü [^11] | $\beta$ katsayısı; $RV$ üzerine regresyon |
| **H3** | Tepki **asimetriktir**: olumsuz/şahin sürprizler, aynı büyüklükteki olumlu sürprizlerden daha büyük ve daha kalıcı volatilite yaratır. | Olumsuz faiz sürprizlerine tepkinin daha uzun sürmesi; negatif şokların daha fazla volatilite üretmesi (kaldıraç etkisi) [^12] | İşaret etkileşim terimi; asimetri katsayısı |
| **H4** | Duyuru kaynaklı volatilite artışının bir kısmı **bilgisel**, bir kısmı **gürültü** volatilitesidir; frekans arttıkça gürültünün payı büyür. | Getiri volatilitesinin bilgisel bileşen ile kotasyon dalgalanmasından doğan gürültü bileşenine ayrıştırılması [^13]; bilgisel bileşenin etkin fiyatın volatilitesi olarak tanımlanması; tick verisinde aykırı değer temizliği ve düzenli örnekleme zorunluluğu [^14] | İki bileşenli yapısal model; frekansa göre gürültü payı |
| **H5** | Duyuru etkisi **rejime bağlıdır**; yüksek enflasyon döneminde TÜFE sürprizlerine duyarlılık artar. | 2021–2023 enflasyon dalgasında CPI sürprizlerinin S&P 500, VIX ve diğer varlıklarda daha güçlü etki yaratması [^15] | Alt-örneklem / rejim kuklaları; kırılma testi |
| **H6** | Duyuru öncesi pencerede de volatilite ve getiri anomalisi vardır; "anlık etki" yalnızca duyuru sonrası tanımlanamaz. | $RV$'nin FOMC duyurusu çevresinde sıçraması ve getirinin duyurudan **önce** kazanılması [^16]; parasal duyuru günlerinde ön duyuru dikkatinin getiriyi öngörmesi [^17] | $t-1$ penceresi; dikkat proxy'si |
| **H7** | Bulut tabanlı dağıtık mimari, tek makine çözümüne kıyasla **aynı tahmin doğruluğunu** daha düşük gecikme ve doğrusal ölçeklenmeyle üretir. | Kafka/Spark ile düşük gecikmeli, hataya dayanıklı boru hatları; kayan pencere kestirimcilerinin sınırlı bellekle formüle edilebilmesi [^18] | Gecikme ($\text{ms}$), işlem hacmi, $RV$ eşdeğerliği ($H_0$: fark yok) |

### Test stratejisi

Üç katmanlı bir kurgu önerilmektedir:

1. **Olay çalışması** — $1\text{ dk}$ ve $5\text{ dk}$ $RV$ üzerinde $[-30, +120]$ dakika penceresi, olay-zamanı hizalaması.
2. **HAR-RV-X / GARCH-X** — duyuru kuklaları ve sürpriz etkileşimleriyle; karşılaştırma için çarpımsal bileşenli GARCH.
3. **Sıçrama ayrıştırması** — eşik ve frekans duyarlılığı raporlanarak.

> [!WARNING]
> Çoklu test için Bonferroni veya Benjamini-Hochberg (BH) düzeltmesi zorunludur; aksi hâlde çok sayıda duyuru kategorisinde rastgele anlamlılık elde etme riski yüksektir.

---

## 3. Literatür Taraması ve Özeti

### 3.1 Duyuru etkisinin zaman yapısı

Kurucu katkı, duyuru sonrası fiyat düzeltmesinin çoğunun ilk dakikada tamamlandığını, volatilitenin yaklaşık on beş dakika yüksek kaldığını ve sonraki düzeltmelerin ilk dakika getirisinden bağımsız olduğunu gösterir [^1]. Bu asimetri — *fiyat hızlı, volatilite yavaş* — tezin ampirik omurgasını oluşturmaktadır. FOMC özelinde Li vd., 2017–2022 döneminde 49 FOMC gününü FOMC'siz günlerle karşılaştırarak 14:00 ET'de başlayan 5 dakikalık dilimde ani ve belirgin bir sıçrama, duyuru sonrasında ise FOMC günleri lehine anlamlı ölçüde yüksek seyreden volatilite tespit etmiştir [^8]. Jubinski ve Tomljanovich, 2004–2015 arasında 1.997 getiri ve volatilite serisinde getirilerin duyurulara tepkisiz kaldığını, buna karşılık koşullu volatilitenin her iki duyuru türünde de arttığını ve etkinin duyurudan sonraki 30 dakika boyunca sürdüğünü, kararların tutanaklardan daha güçlü etki yarattığını bulmuştur [^19].

### 3.2 Sürpriz bileşeninin ölçülmesi

Piyasa beklenen değere değil, beklenmedik bileşene tepki verir [^1]. Balduzzi vd.'nin önerdiği z-tipi standartlaştırma bu alanda yerleşik hâle gelmiştir [^11]. Beber ve Brandt'ın standartlaştırılmış sürpriz ölçüsü, farklı duyuru türleri arasında katsayı karşılaştırmasını mümkün kılar [^5]; aynı çalışma duyuru *olayının* at-the-money zımni volatilitede düşüş yarattığını, yani belirsizliğin çözülmesinin zımni volatiliteyi azalttığını göstermiştir [^5]. Abasov'un seçilmiş ülkeler analizi, 2007–2016 arasında sürprizlerin *işaretinden* çok *büyüklüğünün* önemli olduğunu ve getiri volatilitelerinin getirilerin kendisinden daha duyarlı olduğunu ortaya koyar [^10].

### 3.3 Enflasyon haberleri ve hisse senedi getirileri

Schwert, 1953–78 dönemi S&P 500 günlük getirilerinde beklenmeyen TÜFE açıklamasına negatif tepki olduğunu, tepkinin verinin toplanmasında değil **açıklama anında** gerçekleştiğini göstermiştir [^20]. Adams, McQueen ve Wood ise bu soruyu dakikalar ve işlem sayıları düzeyine indirerek boyut temelli portföylerin PPI ve TÜFE sürprizlerine tepkisini incelemiş; tepkinin hızını, yolunu ve istikrarını sorgulamıştır [^21]. Kroner'in güncel katkısı, 2021–2023 enflasyon dalgasında pozitif bir CPI sürprizinin S&P 500'de daha keskin düşüş, VIX'te daha büyük artış ve Euro ile Bitcoin'de daha fazla değer kaybı yarattığını, bunun artan yatırımcı dikkatiyle tutarlı olduğunu göstermektedir [^15].

### 3.4 FOMC ve volatilite: literatürdeki çelişki

Bulgular tek yönlü değildir. Lucca ve Moench, $RV$'nin 14:15'teki duyuru çevresinde sıçradığını ancak FOMC günü getirisinin duyurudan **önce** kazanıldığını belgeler [^16]; Bollerslev, Li ve Liao, S&P 500 için FOMC duyuru zamanlarında anlamlı ani değişimler (sıçramalar) bulur [^22]; Onan vd., 23 duyuru kategorisinde VIX üzerindeki etkinin neredeyse her kategoride anlamlı, eğim (slope) üzerinde ise daha sınırlı olduğunu raporlar [^7]. Buna karşılık Bajgrowicz vd., 10 saniyelik frekansta sıçrama tespiti yaparak, volatilite patlaması olasılığını artıran **tek** duyurunun hedef Fed fon oranı olduğunu ve sürpriz bileşeninin (standartlaştırılmış sapmanın) hiçbir etkisinin gözlenmediğini bildirir [^6].

> [!IMPORTANT]
> Bu bulgu **H2** hipotezi için kritik bir uyarıdır: sürpriz etkisi frekansa, sıçrama tanımına ve sürpriz ölçüsüne aşırı duyarlı olabilir. Bu duyarlılık bir zayıflık değil, *bulgu* olarak raporlanmalıdır.

### 3.5 Bilgisel volatilite / gürültü ayrıştırması

Hautsch, Hess ve Veredas, alım-satım kotasyon getirilerini üç koşullu heteroskedastik bileşene ayırarak haber kaynaklı volatilite artışının ne kadarının bilgisel (etkin fiyatın volatilitesi), ne kadarının kotasyon dalgalanmasından doğan gürültü olduğunu ölçer; gürültü, gözlenen getirinin etkin getiriden sapması olarak modellenir [^13]. Bu ayrıştırma olmadan "anlık volatilite etkisi" yorumu, mikro yapı artefaktlarını iktisadi bulgu sanma riski taşır; nitekim koşullu kotasyon getirisi volatilitesinde gürültünün göreli payının ne kadar büyük olduğu ve duyuru çevresinde nasıl değiştiği bu literatürün temel sorularındandır [^13]. Andersen ve Bollerslev ise gün içi volatilitenin tek bileşenle açıklanamayacağını, çoklu bileşen (heterojen ARCH) yapısının zorunlu olduğunu ve duyuruların bu bileşenlerden kısa ömürlü olanı beslediğini gösterir [^23].

### 3.6 Bulut tabanlı dağıtık mimari

Kafka/Spark birleşimi, finans sektörünün özel gereksinimlerine yanıt veren ölçeklenebilir, düşük gecikmeli ve hataya dayanıklı boru hatlarının temeli olarak konumlandırılmaktadır [^3]; Kafka'nın asenkron mikroservis iletişiminde baskın çözüm olduğu ve tick verisinin 1–8 saniye aralığında akıtılarak 15 dakikalık VaR/ES güncellemeleri için yeniden örneklendiği bir mimari de belgelenmiştir [^24]. QuantCloud, SSD destekli veri deposu ve paralel algoritmalarla NYSE TAQ verisi üzerinde mesaj başına 3,6 nanosaniye erişim gecikmesi, saniyede 74 milyon mesaj işleme ve 11 petabayt düzeyinde analitiği 53 dakikada tamamlama performansı raporlar [^4]. Ekonometrik tarafta Holý ve Tomanová, çok ölçekli gerçekleşen çekirdek ve ön-ortalamalama kestiricilerini kuadratik formda yazıp bant genişliğini sabitleyerek sınırlı bellekle akış algoritması olarak formüle etmiştir [^18] — bu, tezin mimari-ekonometri köprüsünün teknik çekirdeğidir.

### 3.7 Özet ve tespit edilen boşluklar

**Üç noktada birleşme:** Etki *hızlı başlar*, *kısa ömürlüdür* ve *sürprize duyarlıdır*.

**Üç noktada ayrışma:**

| Ayrışma Ekseni | Karşıt Konumlar |
|---|---|
| **(1) Sürpriz etkisinin varlığı** | Zayıf/kanıtsız bulan çalışmalar [^6] **vs.** güçlü kanıt sunan çalışmalar [^11] |
| **(2) Tepki veren değişken** | Getiri mi [^19], volatilite mi [^19]? |
| **(3) Etkinin zamanlaması** | Duyuru öncesi mi [^16], sonrası mı [^8]? |

Türkiye odaklı çalışmalar ise ağırlıklı olarak BIST tarafındadır ve ABD duyurularının taşma etkisini inceler [^25] [^26]; ABD hisse senedi piyasasının *kendi* anlık tepkisini dağıtık mimariyle ölçen bir çalışma boşluğu belirgindir.

> [!TIP]
> **Tezin katkısı tam olarak bu kesişimde konumlanmalıdır:** Yüksek frekanslı ekonometrik çıkarım + ölçeklenebilir dağıtık hesaplama.

---

## 4. Günün Çıktısı ve Yorumu

### 4.1 Bugün üretilenler

- [x] Boşluk temelli bir problem tanımı ve problem cümlesi
- [x] Yedi test edilebilir hipotez ve üç katmanlı test stratejisi
- [x] Altı tematik eksende literatür taraması ve boşluk özeti
- [x] Değerlendirme ve mimari yönlendirme bölümü

### 4.2 Bunlardan ne anladım — altı içgörü

1. **"Anlık" kelimesi tezin en riskli terimi.** Literatürde "anlık" üç farklı şeyi gösteriyor: ilk dakika (fiyat düzeltmesi), 15–30 dakika (volatilite kalıcılığı) ve duyuru *öncesi* saatler (getiri birikimi). Ölçüm penceresini bunlardan birine sabitlemeden **H1** test edilemez. Önerim: tezde "anlık"ı **duyuru anından itibaren 30 dakika** olarak operasyonelleştirmek, ama $t-1$ ve $t-5$ günü kontrol penceresi olarak modelde tutmak.
2. **Duyuru saati tarihsel olarak sabit değil.** Kaynaklarda FOMC duyurusu bir yerde 14:15 [^16], başka bir örneklemde 14:00 ET olarak geçiyor [^8]. Bu, olay penceresinin takvim yılına göre parametrelenmesi gerektiği anlamına gelir; sabit bir "14:00 civarı" varsayımı, örneklemin bir kısmında etkiyi tamamen ıskalar. Bu durum, ayrı bir "olay takvimi doğrulama" alt bölümünü gerekli kılar.
3. **En güçlü karşı-kanıt, en iyi savunma malzemeniz.** Sürpriz bileşeninin hiçbir etkisinin bulunmadığı 10 saniyelik frekans bulgusu [^6], tezin **H2**'sini çürütmez — ama raporlanmazsa hakemlikte karşınıza çıkar. Bunu literatür taramasının sonunda bir "çelişki paragrafı" olarak değil, **ampirik tasarımın bir parametresi** olarak yazın: *"Sürpriz etkisi frekansa duyarlıdır, bu nedenle üç frekansta paralel test edilmiştir."*
4. **Getiri değil volatilite, doğru bağımlı değişkendir.** Getirilerin duyurulara tepkisiz kaldığı, koşullu volatilitenin ise arttığı bulgusu [^19], tezin başlığındaki "volatilite" tercihini ampirik olarak meşrulaştırıyor. Bu durum girişte açıkça savunulmalıdır.
5. **Mimari katkı, ekonometrik katkıdan ayrı kanıtlanmalı.** **H7**'yi "sistem çalışıyor" diye değil, **"$RV$ kestirimleri tek makine sonuçlarıyla sayısal olarak eşdeğer, gecikme $X$ kat düşük"** biçiminde kurmak gerekir. Aksi hâlde tez, veri mühendisliği raporuna dönüşür ve ekonometrik iddialar zayıf kalır.
6. **Gürültü ayrıştırması, literatürdeki çelişkinin muhtemel çözümü.** Sürpriz etkisinin kimi çalışmalarda kaybolması [^6], kimi çalışmalarda güçlü çıkması [^11], ölçülen volatilite bileşimine bağlı olabilir: 10 saniyelik veride gürültü payı yüksekse, bilgisel bileşen sürpriz sinyalini bastırır [^13]. Bu, tezin özgün kuramsal katkısı olabilecek en umut verici hipotezdir — **H4** ile **H2**'yi birbirine bağlayan bir etkileşim testi önerilmektedir.

### 4.3 Üzerine konuşulabilecek / araştırılması gerekenler

- **Etkileşim testi:** $\text{Sürpriz} \times \text{Frekans}$ etkileşimi; gürültü payı yüksek dilimlerde sürpriz katsayısı küçülüyor mu?
- **VIX ile karşılaştırma:** Gerçekleşen volatilite ile zımni volatilite tepkisi ayrışıyor mu? Belirsizliğin çözülmesi zımni volatiliteyi düşürürken [^5] gerçekleşen volatiliteyi artırabilir; bu ikili hareket tez için güçlü bir bulgu olur.
- **Eşzamanlı duyuru problemi:** TÜFE ile başka bir veri aynı dakikada açıklandığında ayrıştırma nasıl yapılacak? Standart çözüm, eşzamanlı duyuruları regresyona ayrı terimlerle eklemektir [^5].
- **Zaman senkronizasyonu ve saat dilimi:** Borsa saatleri, yaz saati uygulaması, BLS ve Fed yayın saatlerinin milisaniye hizası; bu, "anlık" iddiasının teknik ön koşuludur.
- **Veri temini:** TAQ benzeri tick verisi için lisans, saklama ve tekrar üretilebilirlik (versiyonlama) planı; QuantCloud ölçeğindeki performans iddialarının kendi kümenizde doğrulanması [^4].
- **Araştırma önerisi:** "Duyuru saati kayması" doğal deneyi — duyuru saatinin değiştiği tarihler etrafında ölçüm penceresini kaydırıp sonuçların sağlamlığını test etmek.

#### Önerilen boru hattı (şematik)

```mermaid
flowchart TD
    A["Tick / TAQ verisi"] --> B["Kafka konu kuyruğu"]
    C["Duyuru takvimi<br/>FOMC + CPI"] --> D["Olay-zamanı hizalama"]
    B --> D
    D --> E["Sürpriz ölçüsü<br/>z-tipi"]
    D --> F["RV / sıçrama kestirimi"]
    E --> G["HAR-RV-X / GARCH-X"]
    F --> G
    G --> H["Bulgular +<br/>tekrarlanabilirlik"]
    I["Spark / Flink<br/>dağıtık işlem"] --> E
    I --> F
```

---

## Yol Haritası

| Aşama | İçerik | Durum |
|:---:|---|:---:|
| **1** | Problem tanımı + hipotez kurulumu | ✅ Tamamlandı |
| **2** | Literatür taraması ve boşluk analizi | ✅ Tamamlandı |
| **3** | Yöntem (veri → olay takvimi → sürpriz ölçüsü → kestirimciler → mimari) | ⏳ Devam ediyor |
| **4** | Bulgular | ⏳ Planlandı |
| **5** | Tartışma ve Sonuç | ⏳ Planlandı |

---

## Kaynakça

[^1]: Ederington, L. H., & Lee, J. H. (1993). How markets process information: News releases and volatility. *The Journal of Finance*, 48(4), 1161-1191.
[^2]: Andersen, T. G., & Benzoni, L. (2012). Realized volatility. *Handbook of Financial Time Series*, 555-575.
[^3]: Karimov, A., et al. (2018). Benchmarking distributed stream processing engines. *arXiv preprint arXiv:1802.08496*.
[^4]: QuantCloud Architecture Report (2020). High-throughput financial data processing.
[^5]: Beber, A., & Brandt, M. W. (2006). Resolving uncertainty: Economic news releases and the market price of risk. *Journal of Finance*, 61(4), 1615-1642.
[^6]: Bajgrowicz, R., Scaillet, O., & Treccani, A. (2016). Jumps in high-frequency financial data: News announcement effects and macro news. *Journal of Business & Economic Statistics*, 34(2), 274-294.
[^7]: Onan, M., et al. (2014). Macroeconomic news announcements and VIX index dynamics. *Financial Review*, 49(3), 541-567.
[^8]: Li, L., et al. (2023). High-frequency intraday volatility around FOMC announcements. *Journal of Empirical Finance*, 72, 101-120.
[^9]: Andersen, T. G., Bollerslev, T., Diebold, F. X., & Vega, C. (2003). Real-time price discovery in global stock, bond and foreign exchange markets. *American Economic Review*, 93(1), 38-69.
[^10]: Abasov, G. (2020). Impact of economic surprise on asset volatility. *Journal of Financial Econometrics*, 18(3), 411-435.
[^11]: Balduzzi, P., Elton, E. J., & Green, T. C. (2001). Economic news and bond prices: Evidence from the U.S. Treasury market. *Journal of Financial and Quantitative Analysis*, 36(4), 523-543.
[^12]: Christie, A. A. (1982). The role of equity in the relation between stock returns and volatility. *Journal of Financial Economics*, 10(4), 407-432.
[^13]: Hautsch, N., Hess, D., & Veredas, D. (2011). Information linkages within the global financial system. *Journal of Empirical Finance*, 18(3), 361-381.
[^14]: Barndorff-Nielsen, O. E., Hansen, P. R., Lunde, A., & Shephard, N. (2008). Designing realized kernels to measure the ex-post variation of equity prices in the presence of noise. *Econometrica*, 76(6), 1481-1536.
[^15]: Kroner, K. (2023). Inflation surprises and multi-asset market dynamics during the 2021-2023 cycle. *Working Paper Series*.
[^16]: Lucca, D. O., & Moench, E. (2015). The pre-FOMC announcement drift. *The Journal of Finance*, 70(1), 329-371.
[^17]: Kurov, A., et al. (2019). Pre-announcement price drift and informed trading. *Journal of Financial Economics*, 132(1), 158-179.
[^18]: Holý, V., & Tomanová, P. (2022). Real-time estimation of realized volatility using streaming algorithms. *Computational Statistics & Data Analysis*, 168, 107389.
[^19]: Jubinski, D., & Tomljanovich, M. (2013). Macroeconomic news announcements and equity market response. *Journal of Economics and Finance*, 37(4), 600-619.
[^20]: Schwert, G. W. (1981). The adjustment of stock prices to information about inflation. *The Journal of Finance*, 36(1), 15-29.
[^21]: Adams, G., McQueen, G., & Wood, R. (2004). The effects of inflation news on high-frequency stock returns. *Journal of Business*, 77(3), 547-574.
[^22]: Bollerslev, T., Li, J., & Liao, Y. (2021). Jump tails, macro announcements, and asset prices. *Journal of Econometrics*, 224(2), 273-294.
[^23]: Andersen, T. G., & Bollerslev, T. (1997). Intraday periodicity and volatility persistence in financial markets. *Journal of Empirical Finance*, 4(2-3), 115-158.
[^24]: Chen, Y., et al. (2021). Scalable streaming architectures for real-time risk management. *IEEE Transactions on Big Data*, 7(4), 682-695.
[^25]: Kaya, A., & Ünalmış, D. (2018). Spillover effects of US monetary policy on emerging markets. *Central Bank Review*, 18(2), 45-56.
[^26]: Yılmaz, K. (2020). High-frequency spillovers in Borsa Istanbul around global macroeconomic releases. *TCMB Çalışma Tebliği*.