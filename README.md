# ✍️ Yazı Koçu (Writing Coach) & Türkçe AI Tespiti Araştırması

> **Özet:** Türkçe resmi ve akademik metinlerde yapay zekâ dedektörlerinin neden başarısız olduğunu gösteren deneysel bir araştırma ve bunun yerine geliştirilen, gizlilik odaklı, kural tabanlı bir **Yazı Koçu**.

---

## 🌟 Canlı Demo
Bu projeyi doğrudan tarayıcınızda çalıştırmak için:
1. `index.html` dosyasını indirin ve herhangi bir tarayıcıda (Chrome, Safari, Edge) çift tıklayarak açın.
2. Sıfır sunucu, sıfır API ücreti, tamamen cihazınızda (istemci tarafında) çalışır.

---

## 📖 Proje Hakkında: Neden Dedektör Değil de Yazı Koçu?

Bu proje başlangıçta bir **"Türkçe Yapay Zekâ Metin Dedektörü"** geliştirmek amacıyla başlatıldı. Ancak yürüttüğümüz 5 aşamalı deneysel süreçte kritik bilimsel bulgular elde ettik:

1. **Clever Hans Etkisi & Konu Önyargısı:** Eğitilen derin öğrenme modelleri "yapay zekâ dilini" değil, "resmi ve akademik üslubu" yapay zekâ olarak ezberledi.
2. **Yüksek Yanlış Pozitif (False Positive) Riski:** Gerçek insan tez özetleri ve ansiklopedi maddeleri, yapay zekâ modellerinden (ChatGPT-4o, Claude 3.5 Sonnet) daha yüksek "yapay zekâ" puanı aldı. Yani masum bir öğrencinin düzgün yazdığı ödev haksız yere suçlanıyordu.
3. **Özgüllük & Somutluk Farkı:** Modellerin asıl ayırt edemediği şeyin yazarın kimliği değil, metindeki somut bilgi (özel isim, tarih, sayı, atıf) yoğunluğu olduğu anlaşıldı.

Bu sonuçların ardından, **öğrencileri ve yazarları haksız yere suçlayan şüpheli bir dedektör yayınlamak yerine**, metnin kim tarafından yazıldığını sorgulamayan ve yazıyı daha somut, ritimli ve güçlü kılmaya yardımcı olan bir **Yazı Koçu** geliştirdik.

---

## 🚀 Yazı Koçu Neler Yapar?

- **🔍 Somutluk Analizi:** Paragraftaki özel isim, tarih, sayı, atıf ve alıntıları sayar. Fazla soyut kalan paragraflara somut dayanak ekleme önerisi verir.
- **✂️ Kalıp & Klişe Tespiti:** *"Önemli bir rol oynamaktadır"*, *"günümüzde"*, *"bu bağlamda"* gibi dili sıradanlaştıran dolgu kalıplarını işaretler.
- **🎵 Cümle Ritmi:** Üst üste aynı uzunlukta yazılan monoton cümleleri veya aynı kelimeyle başlayan ardışık yapıları gösterir.
- **🔒 %100 Gizlilik:** Metin asla bir sunucuya gitmez; tüm analiz tarayıcınızda (JavaScript ile) 0 milisaniyede gerçekleşir.

---

## 📄 Detaylı Araştırma Raporu
Tüm deney adımları, veri setleri, matematiksel formüller ve istatistiksel bootstrap analizleri için depodaki [`arastirma-raporu.md`](arastirma-raporu.md) dosyasını inceleyebilirsiniz.

---

## ⚖️ Lisans
MIT License. Araştırma ve kişisel kullanım için tamamen açıktır.
