# Yapay zekâ metni tespiti Türkçe ve İngilizce'de: ölçtüklerimiz, ölçemediklerimiz ve Yazı Koçu'na dönüşümümüz

Taslak v0.1, Ekim 2026

**Okuma notu.** Bu taslaktaki ön bulgular, kaynakça künyeleri ve sürüm bilgileri kayıtlarla doğrulandı, açık işaret kalmadı. Sayısal sonuçların tamamı bu çalışmada ölçülmüş ve çıktısı elimizde olan değerlerdir.

## Özet

Ücretsiz, Türkçe ve İngilizce çalışan, olasılık gösteren bir yapay zekâ metni dedektörü kurmayı denedik. Birbirini düzelten beş deney hattı yürüttük. Üç bulgu öne çıkıyor.

1. **İlk başarılarımızın çoğu sahteydi.** %99'un üzerinde ve %100 gibi doğruluklar, modelin 'yapay zekâ mı insan mı' değil 'hangi veri kaynağı, hangi üslup' sorusunu çözmesinden geliyordu. Bir keresinde 'yapay zekâ' diye etiketlediğimiz veri aslında insan yazısı çıktı.
2. **Türkçe resmi ve akademik yazıda ölçtüğümüz hiçbir yöntem işlemedi.** Gerçek sohbet botu metinlerinden oluşan 75 AI ve 250 insan metninde, resmi türlerde (akademik ve ansiklopedi) hata oranı %10'a kadar çıkarılsa bile AI metinlerinin hiçbiri yakalanmadı. Aynı yöntemler gündelik metinde iyi görünüyor, ama bu sonuca da çekinceyle bakıyoruz.
3. **Aynı konuda eşleştirilmiş küçük bir pilotta** (30 çift) iki yöntem de şans düzeyinde kaldı, eğitimli sınıflandırıcı ters yönde çalıştı. Pilot ayrıca ilginç bir yan bulgu verdi: insan metinleri AI metinlerinden daha somut (özel isim, kısaltma, rakam yoğun) olabilir.

Önceden koyduğumuz karar kuralı karşılanmadı. Bu yüzden suçlayıcı bir dedektör yayımlamadık. Aynı bulgulardan, metni yazanı değerlendirmeyen, yazıyı somutlaştırmaya yardım eden kural tabanlı bir **Yazı Koçu** çıkardık.

## 1. Amaç, hipotez ve karar kuralı

**Başlangıç hedefi.** Türkçe ve İngilizce'de çalışan, ücretsiz erişilebilir, sonucu olasılık olarak veren bir dedektör. Ürünün hedef kitlesi öğrenci ve akademik yazı olduğu için resmi, düzgün yazılmış metin bizim için en kritik alandı. İlk planda detektör çıktısına göre metni dönüştüren bir bileşen de vardı. Detektör güvenilir olmadan bu bileşenin bir ölçütü olmayacağından ve amaç dedektör atlatmak olmadığından bu yönü sürdürmedik.

**Hipotez (H1).** Açık kaynak dil modelleri, ince ayarlı çok dilli bir sınıflandırıcı ve ücretsiz hesaplama kaynaklarıyla, Türkçe ve İngilizce'de kullanılabilir bir AI metni tespiti yapılabilir.

**Sonucu görmeden yazdığımız karar kuralı.** Resmi ya da öğrenci yazısında, hiç dokunulmamış bir test setinde, haksız 'AI' iddiası (yanlış pozitif oranı, FPR) %5'te kalırken AI metinlerin en az %40'ı yakalanıyorsa devam ederiz. Yakalanmıyorsa Türkçe için bu yaklaşım olmuyor, kapatırız. Neden FPR'ye bağlı bir ölçüt seçtiğimiz sonraki bölümde.

## 2. Ölçüm ilkeleri

Deneyler ilerledikçe ölçüm yöntemimiz sertleşti. Sonuçları okurken bu ilkeler geçerli.

- **AUC tek başına yetmez.** AUC iki dağılımın ne kadar ayrıldığını söyler, ama bir öğrenciyi suçlamak için gereken düşük hata oranında neyin yakalandığını söylemez. Bu yüzden 'belirli bir FPR'de AI yakalama oranı' raporladık.
- **Güven aralığı zorunlu.** Küçük örneklemlerde (30 çift, tür başına 15 AI metni) bootstrap güven aralıkları çok genişti. Aralık iki yöntemi örtüşüyorsa fark kanıtlanmış sayılmadı.
- **Kısayol sensörleri.** Her test setinde kelime sayısı, büyük harf oranı ve noktalama oranı gibi yüzeysel özellikleri kontrol olarak skorladık. Bunlar 0,5'ten uzaksa veride yüzeysel bir ipucu var demektir. Karakter n-gram TF-IDF ve lojistik regresyon gibi basit bir model de eğitimden önce kısayol testi olarak çalıştırıldı.
- **Etiket kaynağını gözle örnekleme.** Bir veri kaynağını 'AI' ya da 'insan' diye kullanmadan önce örneklerini okuduk. Bu kural en pahalı hatamızı yakaladı (Deney B).
- **Tür × etiket matrisi.** Her türde hem insan hem AI metni olmalı, aksi halde model 'tür' öğrenir.
- **Eğitimde görülmeyen üretici.** Test setinde en az bir AI üreticisi eğitimden dışarıda tutuldu.
- **Dış test.** Asıl başarı ölçütü, eğitimde hiç kullanılmayan, elle üretilmiş gerçek sohbet botu metinleridir.

## 3. Deneyler

Sürüm adları iki ayrı çalışma hattında farklı kullanıldığı için bu raporda deneyleri içeriklerine göre A'dan E'ye adlandırıyoruz.

### Deney A: Tek bir üretici, tek bir insan kaynağı

**Ne yaptık.** Türkçe AI metinlerini yalnızca küçük bir açık modelden (Qwen), insan metinlerini yalnızca Vikipedi ve haber ajanslarından toplayıp çok dilli bir sınıflandırıcıyı (XLM-RoBERTa) eğittik.

**Ne gördük.** İlk deneyde kendi veri dağılımında %99'un üzerinde, yapay bir doğruluk elde ettik. Ancak model, doğrudan sohbet botuyla yazılmış belirgin AI metinlerinin neredeyse tamamına 'insan' dedi.

**Ne öğrendik.** Model 'yapay zekâ mı insan mı' yerine 'Qwen üslubu mu Vikipedi üslubu mu' öğrenmişti. Çok yüksek doğruluk, bir başarı değil bir alarm işaretidir.

### Deney B: 'AI' diye etiketlenen veri insan yazısı çıktı

**Ne yaptık.** Türkçe AI kaynağı olarak bir Hugging Face veri setini kullandık. İkinci bir eğitimde doğrulama doğruluğu %100'e ulaştı (kayıp değeri 0,000066).

**Ne gördük.** Veri setinin örneklerini okuyunca bunun AI çıktısı olmadığı ortaya çıktı: PDF kopyalamadan kalan satır sonu tireleri ('seçil - meşindeki'), 'Bu tez...' ve 'Bu araştırmada ... yöntemleri kullanılmıştır' gibi tez dili, Markdown işareti oranı yalnızca %1,3 ve 'Elbette/Tabii' ile başlayan sıfır metin. Veri seti Türkçe akademik tez ve makale derlemesiydi.

**Ne öğrendik.** Model 'resmi/akademik Türkçe = AI, günlük yorum = insan' kuralını öğrenmişti. Dış testte resmi bir köşe yazısına 1,000, günlük bir ürün yorumuna 0,000 AI skoru verdi. Bu kural, sonraki tüm deneylerde karşımıza çıkan temel hatanın ilk görünümüydü. Test setindeki küçüklük (3 insan, 3 AI) nedeniyle bunu sonuç değil kalıp olarak okuyoruz.

### Deney C: Çok üreticili İngilizce veri ve çeviriyle Türkçe

**Ne yaptık.** İngilizce için çok sayıda üreticiden ve farklı türlerden metin içeren açık bir veri seti kullandık (MAGE). Etiket yönünün bizimkinin tersi olduğunu (1 = insan) çapraz tabloyla doğruladık. Çok küçük ve eski üreticileri çıkardık, iki sınıfı aynı temizlikten geçirdik, insan metinlerini önce tür içinde böldük, bir üretici ailesini (text-davinci) tamamen teste ayırdık. Türkçe için aynı veriyi açık bir çeviri modeliyle (NLLB) Türkçeye çevirdik. Böylece iki sınıf da aynı çeviri izini taşıdı. Ardından XLM-RoBERTa'yı etiket yumuşatma ve düşük öğrenme oranıyla eğittik.

**Veri mühendisliği dersleri.** İlk bölme kodu, insan örneği az olan türlerde tüm insanları teste harcayıp eğitime 56 satır bırakmıştı. Düzeltme: insanları önce böl, AI sayısını sonra ayarla. Karakter n-gram kısayol testi düzeltilmiş veride 0,70 verdi, tür dışarıda bırakma skorları 0,41 ile 0,71 arasındaydı. Bu, aşırı bir sızıntı olmadığını ama yüzeysel ipuçlarının tamamen yok olmadığını gösterdi.

**Sonuçlar.**

- İngilizce held-out üretici testi (text-davinci, eski bir model): AUC 0,908. 0,5 eşiğinde insan metinlerinin %36,6'sına 'AI' dedi.
- Eşik doğrulama setinde ayarlandığında İngilizce'de AI yakalama: FPR %1'de %18,5, %5'te %44,1, %10'da %64,1.
- Türkçe çeviri testi: AUC 0,767. Doğrulama setinde %5 FPR hedefi test setinde %9,2 FPR'ye kaydı (yalnızca 185 insan örneğine dayandığı için gürültülü). Yakalama %32,8. %10 hedefi için gerçek FPR %16,8, yakalama %49,1.

**Ne öğrendik.** Doğrulamada eşik seçmek test FPR'sini iyi tutuyor, ama güvenli bir FPR'de eski bir modelin metninin bile yarısından azı yakalandı. Çeviri Türkçesi gerçek Türkçeyi temsil etmiyor (çevirilerde 'layık kullanıcı' gibi bozulmalar vardı).

### Deney D: Eğitimsiz yöntem (Binoculars) ve gerçek Türkçe sohbet botu testi

**Yöntem.** Binoculars, iki dil modelinin (gözlemci ve performer) aynı metin üzerindeki beklenmedikliklerini oranlayan eğitimsiz bir yöntemdir. Biz Qwen2.5-1.5B ve Qwen2.5-1.5B-Instruct çiftini kullandık. Formülün iki varyantını da hesapladık, orijinaline karşılık gelen 0,789 AUC verdi, ters varyant 0,746.

**Ön ölçüm (çeviri ve eski üretici).** İngilizce text-davinci testinde AUC 0,942 (FPR %1'de %66,7, %5'te %80,7 yakalama). Ancak bu rakamlarda eşik test içinden seçildi, yani iyimser. Türkçe çeviri testinde AUC yalnızca 0,635. Çeviri hem insan hem AI metnini aynı akıcılığa getirdiği için perplexity temelli sinyal silinmiş olabilir. Bu yüzden çeviri verisi Türkçe hakkında az şey söyler.

**Gerçek Türkçe test seti.** ChatGPT, Claude ve Gemini'den elle, tarayıcı üzerinden alınan 75 AI metni (beş tür, her tür ve her üreticiden beşer) ve 250 insan metni (Vikipedi, akademik tez özetleri, haber, yorum). Uzunluklar tür içinde eşitlendi (ortalama 114 kelime). Güven aralıkları tabakalı bootstrap ile.

| Yöntem | AUC (%95 güven aralığı) |
| --- | --- |
| Binoculars 1,5B | 0,789 (0,739 – 0,836) |
| XLM-R (Deney C modeli) | 0,710 (0,655 – 0,763) |
| İkisinin sıra ortalaması | 0,843 (0,803 – 0,885) |
| Yalnızca 3 yüzeysel özellik (çapraz doğrulama) | 0,666 |

Kontrol özellikleri: kelime sayısı 0,572, büyük harf oranı 0,516, noktalama oranı 0,396. Lojistik regresyonla birleştirilen Binoculars ve XLM-R skorları da 0,844 verdi, yani basit sıra ortalamasına bir şey katmadı. Eğitimde görmediği bir sohbet botuyla (üç botun her biri sırayla dışarıda bırakıldı) AUC 0,83 – 0,87 civarında kaldı. Yüzeysel özellikler eklenince 0,881'e çıktı ama bu artışa güvenmiyoruz: veri kaynaklarının izlerini (küçük harfli haber metinleri, Vikipedi biçimlendirme artıkları) kullanıyor olabilir.

**Asıl bulgu: resmi ve gündelik yazı arasındaki uçurum.** Birleştirilmiş modelin çapraz doğrulamalı tahminleriyle:

| Alt küme | İnsan / AI | AUC | AI yakalama, FPR %10 | FPR %20 | FPR %30 |
| --- | --- | --- | --- | --- | --- |
| Resmi (akademik + ansiklopedi) | 100 / 30 | 0,712 | %0 | %0 | %80 |
| Gündelik (forum, yorum, haber) | 150 / 45 | 0,935 | %93 | %98 | %100 |

Resmi türlerde FPR %30'a gelene kadar neredeyse hiçbir AI metni yakalanmıyor. Akademik tür AUC'si 0,691, ansiklopedi 0,736. AI metinlerinin skoru her türde birbirine çok yakın (medyan ≈ 0,69), insan metinlerinin skoru türe göre değişiyor: akademik ve ansiklopedik insan metinlerinin üst %10'u 0,89, AI medyanının çok üstünde. En yüksek skor alan insan metinleri gerçek akademik özetler ve ansiklopedi maddeleriydi.

**Gündelik sonuçlara çekince.** Forum ve yorum aynı insan kaynağından (ürün yorumu) geliyor, yani aslında tek tür. 'Haber' insan metinleri küçük harfli ve noktalamasız bir haber derlemesinden geldi, bu haber sonucunu veri kusuruna bağlı yapıyor. Tür başına yalnızca 15 AI metni var. Tüm analiz aynı 325 metin üzerinde çapraz doğrulamayla yapıldı, hiç dokunulmamış taze bir test setimiz yoktu.

**Başka bir ölçüm seti.** Daha kısa metinlerle (ortalama 48 kelime, 82 AI, 250 insan) yapılan ikinci bir çalıştırmada sıralama tersine döndü: Binoculars AUC 0,575, XLM-R 0,814. Bu iki set arasındaki fark tek bir nedene bağlanamaz (AI sayısı ve kaynaklar da farklıydı). Ama yönü tutarlı: Binoculars kısa metinde zayıflıyor, ve sonuç test setinin yapısına çok bağlı.

### Deney E: Aynı konuda eşleştirilmiş pilot

**Tasarım.** 30 çift (60 metin). İnsan tarafı 2022 öncesi Hugging Face verilerinden: 15 akademik tez özeti ve 15 tarihsel Vikipedi maddesi. AI tarafı aynı konularda: akademikte ChatGPT, ansiklopedide Claude 3.5 Sonnet. Öğrenci ödevlerini AI kirliliği riski nedeniyle dışarıda bıraktık. Ölçüm CPU'da yapıldığı için Binoculars'ın 0,5B çifti kullanıldı, yani Deney D ile doğrudan karşılaştırılamaz.

| Yöntem | AUC (%95 GA, çift bootstrap) | Çift kazanma oranı |
| --- | --- | --- |
| Binoculars 0,5B | 0,601 (0,449 – 0,751) | 0,57 |
| XLM-R | 0,387 (0,248 – 0,518) | 0,30 |
| Sıra ortalaması | 0,478 (0,347 – 0,614) | 0,53 |

'Çift kazanma', aynı konudaki AI metninin insan metninden yüksek skor almasıdır. 0,50 yazı turadır.

Kontrol özelliklerinin AUC'si: kelime sayısı 0,368, noktalama oranı 0,302 ve **büyük harf oranı 0,196**. Büyük harf oranı tek başına, ters okunduğunda yaklaşık 0,80 AUC verir, yani bu setteki hiçbir modelimizden iyidir. Çiftlerin yaklaşık %87'sinde insan metni AI metninden daha yüksek büyük harf oranına sahip.

**Yorum.** XLM-R'nin ters yönde (0,387) çalışması, Deney B ve D'de gördüğümüz 'resmi yazı = AI' kuralının eşleştirilmiş çiftlerde doğrudan görünüşüdür: insan tarafı resmi (tez, ansiklopedi), AI tarafı aynı konuyu daha düz yazıyor. Büyük harf oranı sonucu ise şu **hipotezi** üretiyor: insan metni özel isim, kısaltma ve rakam gibi somut bilgiyle dolu, konu verilen AI metni daha genel kalıyor. Bunu kanıtlamadık, pilot küçük (30 çift, güven aralıkları yaklaşık ±0,15) ve tür ile üretici birebir örtüşüyor (akademik tamamı ChatGPT, ansiklopedi tamamı Claude), yani bir etkiyi diğerinden ayıramıyoruz.

### Başka çalışma hattından aktarılan ön bulgular

Bu iki bulgu diğer çalışma hattında elde edildi ve kayıtlarımızla doğrulandı:

- **Kelime dağarcığı önyargısı.** Aynı fikir daha basit kelimelerle yazıldığında AI skoru %78'den %14'e düştü ('bakkal testi'). Bu tek bir örnek olduğu için istatistik değil, gösterge olarak kullanılmalı.
- **Konu önyargısı.** Konu çeşitliliğinin kontrolsüz olduğu bir eğitimde saniyeler içinde %100 AUC'ye ulaşıldı ve model 'kurumsal metin = AI, haber = insan' kuralını ezberledi.

İkisi de Deney A ve B'de gördüğümüz kısayol öğrenme örüntüsüyle uyumlu.

## 4. Dedektörler resmi Türkçede neden çöküyor?

Bu bölümde gözlemlediğimiz şeylerle yalnızca hipotez olanları ayırıyoruz.

**Gözlemlenenler.**

- Resmi insan metni, AI metniyle aynı skor aralığında ya da üstünde kalıyor (Deney D, FPR %10'da yakalama %0).
- Eğitimli sınıflandırıcılarımız tekrar tekrar 'resmi/düzgün yazı = AI' kuralını öğrendi (Deney B, C, E).
- AI skorları türler arasında neredeyse sabit, insan skorları türe göre kayıyor. Sistem büyük ölçüde 'yazı ne kadar resmi' sorusunu ölçüyor.
- Doğrudan sohbet botuyla yazılmış metinde, bu botları hiç görmemiş modelde bile sinyal (AUC ≈ 0,8) var, ama o sinyal resmi insan yazısıyla örtüşüyor.

**Hipotezler (kanıtlanmadı).**

1. **Kayıt örtüşmesi.** Hem sohbet botları hem akademik yazarlar resmi, kalıplı, edilgen bir dil kullanıyor. Biçimsel özellikler iki sınıfı ayıramıyor.
2. **Öngörülebilirlik.** Perplexity temelli yöntemler 'bir dil modeline kolay tahmin edilen metni' AI sayar. Liang ve diğerleri (2023), İngilizceyi ikinci dil olarak yazanların metinlerinin düşük perplexity nedeniyle sık sık yapay zekâ metni diye sınıflandırıldığını gösterdi. Bu çalışma İngilizce metinlere dayanıyor. Aynı mekanizmanın resmi Türkçe metinde de işlediği bizim hipotezimizdir, bulgularımız bunu düşündürüyor ama kanıtlamıyor.
3. **Özgüllük farkı.** Pilot, insan metninin daha çok isim, kısaltma ve rakam içerdiğini düşündürüyor. Konu verilen AI metni daha genel kalıyor. Bu fark, bir 'AI imzası' değil, istem biçiminin yan etkisi olabilir.
4. **Küçük modelin Türkçe sınırı.** 0,5B–1,5B parametreli Qwen modellerinin Türkçe olasılık kalitesi bir tavan olabilir. Daha büyük modelleri denemedik.
5. **Veri ve ölçek.** Birkaç yüz metinle öğrenilebilecek şeyin sınırı var. Kaliteli ve geniş eşleştirilmiş Türkçe veri, Türkçe'de bizim erişebildiğimiz kaynaklarda yok.

Bu bulguların bir ortak noktası var: mevcut yöntemlerin 'yazarı tahmin etme' sinyali, yazının **özelliklerinden** (resmiyet, kalıplılık, özgüllük) ayrışmıyor.

## 5. Karar

Önceden koyduğumuz kural, resmi ve öğrenci yazısında %5 FPR'de en az %40 yakalama şartı koyuyordu. Ölçtüğümüz resmi alt kümede FPR %10'da bile yakalama %0'dı. Eşleştirilmiş pilot da yöntemlerin şans düzeyinde olduğunu (hatta birinin ters çalıştığını) gösterdi. Kuralı sonucu gördükten sonra gevşetmedik.

Kararımız: **Türkçe resmi yazı için bu yaklaşımla güvenilir bir 'AI mı insan mı' dedektörü yayımlamıyoruz.** Bu kesin bir imkânsızlık kanıtı değil. Daha büyük modellerle, binlerce eşleştirilmiş ve özgüllüğü kontrol edilmiş çiftle sonuç farklı çıkabilir. Ama bu, bizim erişebildiğimiz kaynakların çok üstünde bir yatırım ve sonuç olumsuz kalabilir.

Bu karara yol açan asıl gerekçe doğruluk oranı değil, **hata türlerinin asimetrisi**. Bir dedektörün en zararlı hatası, resmi ve düzgün yazan gerçek bir öğrenciye 'AI yazmış' demektir. Bizim ölçümümüzde bu hata tam olarak resmi yazıda yoğunlaşıyor. Ayrıca bir skorun kanıt gibi okunma riski, skorun gerçek olasılığı yansıtıp yansıtmadığı sorusundan bağımsız olarak vardır. Ve AI modelleri değiştikçe her dedektör eskir.

## 6. Yazı Koçu: neden ve nasıl

Bulgularımızdan çıkan en kullanışlı şey, 'AI imzası' değil, **somutluk farkıydı**. Bir paragrafta isim, tarih, rakam, kaynak ya da alıntı yoksa o paragraf, kim yazmış olursa olsun, daha az ikna edici ve daha az bilgilendirici olur. Bunu yazara geri bildirim olarak vermek kimseyi suçlamaz ve işe yarar.

**İlkeler.**

1. **Yazarı değerlendirmez.** Araç metnin yapay zekâ ya da insan tarafından yazıldığı hakkında hiçbir iddiada bulunmaz. Çıktıda bir 'AI skoru' yoktur.
2. **Her uyarı açıklanabilir.** Metnin hangi bölümünün hangi kuralla işaretlendiğini ve ne yapılabileceğini gösterir.
3. **Yerel çalışır.** Çözümleme tarayıcıda yapılır, metin sunucuya gitmez. Kayıt, hesap ya da GPU gerekmez.
4. **Otomatik yeniden yazım yok.** Araç öneri verir, metni değiştirmez. Yazar kendi sesini korur.

**Kapsam (ilk sürüm).**

- **Somutluk.** Her paragrafta isim, yıl, tarih, rakam, atıf, alıntı ve kısaltma sayar. Dayanak bulunmayan paragrafları ve 'birçok', 'uzmanlara göre', 'son yıllarda' gibi belirsiz ifadeleri işaretler.
- **Kalıp ifadeler.** 'Önemli bir rol oynamaktadır' gibi klişeleri her geçtiğinde, 'sonuç olarak' gibi geçişleri tekrar ettiklerinde ya da yoğunlaştıklarında gösterir.
- **Cümle ritmi.** Üst üste benzer uzunlukta cümleleri, aynı sözcükle başlayan cümleleri ve çok uzun cümleleri işaretler.
- Türkçe ve İngilizce destekler. Kural sözlükleri açık, küçük ve herkesin okuyabileceği biçimde tutulur.

**Doğrulama planı.** Eşikleri veriden ayarlamadık, kendi seçimlerimiz. Bu yüzden iyi yazılmış gerçek metinlerin (tez özeti, düzgün köşe yazısı) çoğunun az uyarı aldığını ve 5–10 kullanıcının kendi metinleriyle 'hangi uyarı işe yaradı, hangisi saçmaydı' geri bildirimi verdiğini doğrulayacağız. Gereksiz bulunan kural varsayılan olarak kapatılır. Bu doğrulama henüz yapılmadı.

**Yapmayacağımız şeyler.** Toplam bir puan göstermek, 'dedektörleri geçmek' vaadi vermek, tek tıkla metni 'düzelten' bir düğme eklemek.

## 7. Sınırlamalar

- **Örneklem küçük.** En iyi test setimizde 75 AI ve 250 insan metni var, tür başına 15 AI. Eşleştirilmiş pilot 30 çiftti. Güven aralıkları geniş, tür bazlı sonuçlar yalnızca yön göstergesi.
- **Tür ile üretici örtüşüyor.** Pilotta akademik metinler tamamen ChatGPT, ansiklopedik metinler tamamen Claude. Üretici etkisini tür etkisinden ayıramadık.
- **Taze test seti yok.** Analizlerin çoğu aynı metinler üzerinde yapıldı. Çapraz doğrulama bu sorunu kısmen hafifletir, tamamen çözmez.
- **Veri kaynağı kusurları.** Haber kaynağı küçük harfli ve noktalamasız, ürün yorumları hem 'yorum' hem 'forum' diye kullanıldı, Vikipedi dökümünde biçimlendirme artıkları var. Bunların bir kısmı sonradan fark edildi.
- **Öğrenci metni yok.** Hedef kitlemizin yazısı olan öğrenci ödevi, AI kirliliği riski nedeniyle ölçüme alınmadı. Dolayısıyla elimizdeki sonuçlar tez özeti ve ansiklopedi maddesi için geçerli, öğrenci ödevi için geçerli olduğunu bilmiyoruz.
- **Modeller küçük ve eski olabilir.** İngilizce test üreticileri eski (text-davinci). Türkçe testlerde GPT-4o, Claude 3.5 Sonnet ve Gemini 1.5 Pro kullanıldı. Bunlar artık en yeni sürümler olmayabilir, yeni modellerde sonuç farklı çıkabilir.
- **Ticari araçlarla karşılaştırma yok.** GPTZero, Turnitin gibi araçlarla kendi testimizi yapmadık. Onların Türkçe'deki performansı hakkında bir iddiamız yok.
- **Yazı Koçu doğrulanmadı.** Aracın uyarılarının yazarlara gerçekten yardımcı olup olmadığı henüz ölçülmedi.

## 8. Tekrarlanabilirlik ve lisans notları

Ölçüm hattı (veri hazırlama, kısayol testleri, Binoculars ve XLM-R skorlama, güven aralıkları, eşleştirilmiş çift analizi) betiklerle tekrarlanabilir. Yayımlarken şunlara dikkat edilmeli: Çeviri için kullanılan NLLB model ağırlıkları CC-BY-NC lisanslıdır, yani ticari kullanıma kapalıdır. Bu, araştırma ve ücretsiz yayın için sorun değil, ama çıktısıyla eğitilmiş modellerin ticari kullanımı ayrıca değerlendirilmelidir. MAGE, tez derlemesi ve Vikipedi için lisans koşulları da kontrol edilmelidir. İnsan metinleri başkalarının yazısı olduğundan, ham veri paylaşılmadan yalnızca skorlar ve betikler yayımlanabilir.

## 9. Kaynakça

- Hans ve diğerleri. Spotting LLMs With Binoculars: Zero-Shot Detection of Machine-Generated Text. 2024.
- Li, Yafu ve diğerleri. MAGE: Machine-generated Text Detection in the Wild. ACL 2024 (arXiv:2305.13242).
- Liang ve diğerleri. GPT detectors are biased against non-native English writers. 2023.
- Guo ve diğerleri. How Close is ChatGPT to Human Experts? (HC3). 2023.

## 10. 'Hakkında' sayfası için kısa metin

> **Yazı Koçu neden dedektör değil?**
>
> Önce Türkçe ve İngilizce'de yapay zekâ metni tespit eden bir araç yapmayı denedik. Gerçek sohbet botu metinleri üzerinde ölçtüğümüzde, resmi ve akademik Türkçe yazıda yöntemlerin gerçek insan yazısını yapay zekâ metninden güvenilir biçimde ayıramadığını gördük. Düzgün yazan bir öğrenciyi haksız yere işaretleme riski, böyle bir aracı yayımlamamız için fazla büyüktü.
>
> Bu çalışmadan çıkan faydalı bulguyu aracımıza çevirdik: somut, isim, tarih ve rakamla desteklenmiş yazı daha ikna edicidir. Yazı Koçu metnini kimin yazdığına bakmaz. Paragraflarının somutluğuna, kalıplaşmış ifadelere ve cümle ritmine bakar, nerede iyileştirebileceğini gösterir. Metnin tarayıcından dışarı çıkmaz. Sonuçlar öneridir, hüküm değildir.
>
> Ölçümlerimizin ayrıntıları ve sınırlamaları araştırma raporunda yer alıyor.
