<div align="right">

[English](README.md) &bull; **Türkçe**

</div>

<div align="center">

# ROVE

### Yüksek Performanslı Cihaz İçi Makine Algısı ve Nöral İndeksleme Motoru

[![Kararlı Sürüm](https://img.shields.io/badge/S%C3%9CR%C3%9CM-v1.0.11%20KARARLI-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![Platform](https://img.shields.io/badge/PLATFORM-WINDOWS%2010%20%2F%2011%20x64-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases/releases/tag/v1.0.11)
[![Mimari](https://img.shields.io/badge/M%C4%B0MAR%C4%B0-512--D%20CENTROID%20%2F%20FTS5-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)
[![Telemetri](https://img.shields.io/badge/TELEMETR%C4%B0-%250%20HAVA%20BO%C5%9ELUKLU%20(AIR--GAPPED)-09090b?style=for-the-badge&labelColor=18181b)](https://github.com/ardayesilgul/rove-releases)

<br/>

[**WINDOWS İÇİN ROVE v1.0.11 İNDİR (.EXE)**](https://github.com/ardayesilgul/rove-releases/releases/download/v1.0.11/Rove-Setup-1.0.11.exe)

</div>

---

## Teknik Genel Bakış

Rove; devasa yerel medya arşivlerini gerçek zamanlı olarak indekslemek, sınıflandırmak ve anında erişilebilir kılmak için Windows ortamında geliştirilmiş, dış ağlardan tamamen izole (air-gapped) bir makine algısı katmanıdır.

Bulut bağımlılıklarını kökten ortadan kaldıracak şekilde inşa edilen Rove; derin biyometrik vektörleştirmeyi, dinamik ağırlık merkezi (centroid) kimlik öğrenimini, milisaniye-altı tam metin indekslemeyi ve gerçek zamanlı ses telemetrisini sıfır harici API çağrısıyla doğrudan yerel donanımınız üzerinde çalıştırır.

---

## Çekirdek Mühendislik Sütunları

```
+-------------------------------------------------------------------------+
|                    ANA ARAYÜZ İŞ PARÇACIĞI (60 FPS VSYNC)               |
|   Donanım DWM Entegrasyonu | OutBack Yay Fiziği | Sıfır Bağımsız Pencere|
+-------------------------------------------------------------------------+
                                    ^
                                    | Qt Event Bus (İş Parçacığı Güvenli)
                                    v
+--------------------+--------------------+--------------------+----------+
|  NÖRAL BİYOMETRİ   | TAM METİN ARAMA    | DONANIM TELEMETRİSİ| GEÇİCİ   |
|  512-D Centroid    | SQLite FTS5 / BM25 | WASAPI Core Audio  | RAF      |
|  MTCNN + ResNet    | < 5ms Sorgulama    | 40Hz Gerçek Tepe   | WinRT    |
+--------------------+--------------------+--------------------+----------+
```

---

### `[SÜTUN_01: ÖZEL BİYOMETRİK MANİFOLD VE DİNAMİK AĞIRLIK MERKEZİ YAKINSAMASI]`

Rove, temel kütüphanelerin statik eşleştirme yaklaşımları yerine, onlarca yıllık dağınık fotoğraf arşivleri için özel olarak optimize edilmiş dinamik ve çok aşamalı bir biyometrik hat işletir:

* **Afin Kerteriz Normalizasyonu:** Çok katmanlı kaskad nöral tespit; yüz bölgelerini izole eder, açı ve eğim sapmalarını düzelterek $160 \times 160$ standart hizalanmış biyometrik kırpıntılar üretir.
* **512-Boyutlu Hiperküre Haritalaması:** Derin artık (residual) gömüleme modeli; her yüzü L2-normalize edilmiş 512 boyutlu sürekli bir vektör uzayına ($||v||_2 = 1.0$) yansıtır ve ışık/yaş değişimlerinden bağımsız değişmez yüz geometrisini kodlar.
* **Dinamik ve Uyarlanabilir Ağırlık Merkezi (Centroid) Takviyesi:** Kimlikler sabit fotoğraflar olarak değil, yaşayan küme merkezleri olarak modellenir. Farklı ışık, sakal, gözlük veya yaşlanma koşullarında onaylanan yeni fotoğraflar geldikçe, kişinin ağırlık merkezi matematiksel olarak gerçek geometrik merkezine yakınsar:
$$\mathbf{C}_{\text{yeni}} = \text{Normalize}\left( \frac{\mathbf{C}_{\text{eski}} \cdot N + \mathbf{V}_{\text{yeni}}}{N + 1} \right)$$
* **Üç Kademeli Karar Eşikleri:**
  * **Eşik 1 ($\ge 0.65$):** Doğrulanmış kimlik kümesine otonom doğrudan ekleme.
  * **Eşik 2 ($0.50 - 0.65$):** Şüpheli durumlar için kullanıcı onayına sunulan akıllı öneri havuzu.
  * **Eşik 3 ($< 0.50$):** Eşleşme bulunamayan yüzler için otomatik yeni küme dallanması.
* **Negatif İlişkilendirme ve Kara Liste İzolasyonu:** Hatalı eşleşme bildirimleri, ilgili vektörü anında kişinin ağırlık merkezinden budar ve hash imzasını `ignored_faces` tablosuna mühürleyerek aynı hatanın tekrarlanmasını kalıcı olarak engeller.
* **Dinamik Yığın Sanallaştırması:** 100.000'den fazla yüz içeren devasa arşivlerde bile arayüzü dondurmadan, dikey kaydırma geometrisine bağlı olarak 48'lik bloklar halinde akıcı yükleme sağlar.

---

### `[SÜTUN_02: 5 MS ALTINDA TAM METİN İNDEKSLEME VE BM25 SIRALAMASI]`

Doğrudan yerel disk üzerinde çalışan yüksek verimli belge indeksleme omurgası:

* **Yerel Belge Çözümleme:** Harici servis kullanmadan PDF, DOCX, XLSX ve TXT belgelerinden yapılandırılmış metinleri, ham fotoğraf formatlarından ise yüksek hassasiyetli EXIF metaverilerini ayıklar.
* **Deterministik FTS5 İndeksleme:** Tokenize edilen veri akışı, Porter stemmer ve unicode61 normalizasyonu ile SQLite FTS5 sanal tablolarında Write-Ahead Logging (WAL) modunda dizinlenir.
* **BM25 Alakalılık Puanlaması:** Terim doygunluğu ($k_1 = 1.2$) ve belge uzunluğu cezalandırması ($b = 0.75$) işletilir; dosya adı frekansına $3.5\times$ öncelik verilerek on binlerce belgede 5 milisaniyenin altında anlık sorgu sonucu üretilir.

---

### `[SÜTUN_03: ASENKRON İŞ PARÇACIĞI MİMARİSİ VE 60 FPS YALITIMI]`

* **Katı İş Parçacığı Ayrımı:** Ağır tensör hesaplamaları, disk fihristleme ve ses telemetrisi bağımsız QThread havuzlarında çalışır. Ana arayüz iş parçacığı (Main GUI Thread) I/O işlemlerinden tamamen izole edilerek yoğun CPU yükleri altında dahi "Program Yanıt Vermiyor" kilitlenmeleri %0'a indirilir.
* **Güvenli Olay Köprüsü (Qt Event Bus):** İş parçacıkları arası veri trafiği sıralı Qt sinyalleriyle yönetilir; paylaşılan bellek yarış durumları ve SQLite veritabanı kilitlenmeleri engellenir.

---

### `[SÜTUN_04: ÇEVRESEL DONANIM ENTEGRASYONU VE DİNAMİK ÇENTİK]`

Sistem durumunu bağımsız diyalog pencereleri açmadan zarifçe sunan donanım odaklı masaüstü yüzeyi:

* **WASAPI Core Audio Telemetrisi:** Hoparlörün fiziksel elektrik genliğini (`IAudioMeterInformation`) 40 Hz frekansla sorgular `[0.0, 1.0]`. Ses durduğunda 4-bant harmonik sinüs ekolayzırı yapay gürültü üretmeden doğrudan sıfır taban çizgisine oturur.
* **Çevresel Alan Farkındalığı:** Düşük seviyeli sistem kancalarıyla (`WH_MOUSE_LL`) tarayıcı sekmeleri ekran tepesine yaklaştığında 5px yukarı çekilerek sekme kapatmayı engellemez; tam ekran oyun ve videolarda anında gizlenir (`SW_HIDE`).
* **Donanım Seviyesi DWM Entegrasyonu:** Windows Masaüstü Pencere Yöneticisi (`DwmSetWindowAttribute`) ile pencere başlık çubuğunu donanım düzeyinde saf siyaha (`#000000`) boyayarak pürüzsüz ve dikişsiz bir koyu tema sunar.

---

## Performans ve Güvenlik Karşılaştırma Matrisi

| Kriter / Özellik | Geleneksel Etiketleme / Bulut İndeksleyiciler | Rove Yerel Algı Motoru |
| :--- | :--- | :--- |
| **Veri Gizliliği** | Bulut sunucularına yükleme / Harici API | **%100 Hava Boşluklu (Sadece Yerel Bilgisayar)** |
| **Biyometrik Kümeleme**| Statik birebir görsel karşılaştırması | **Dinamik ve Uyarlanabilir Centroid Öğrenimi** |
| **Arama Yanıt Süresi** | 250ms - 1500ms (İnternet bağlantısına bağlı) | **< 5ms (Yerel FTS5 + BM25)** |
| **Bellek Tüketimi** | 800 MB - 2.5 GB (Electron/Web tabanlı) | **~158 MB RSS (Derlenmiş Yerel Binary)** |
| **Arayüz Tepkiselliği** | Senkron işlem kilitlenmeleri | **Katı Asenkron 60 FPS VSync İzolasyonu** |
| **Telemetri & İzleme** | Sürekli arka plan analitiği ve veri toplama | **%0 Ağ Telemetrisi (Sıfır Dış Bağlantı)** |

---

## Sürüm Paketleri ve SHA-256 Doğrulama

Her kararlı sürüm Inno Setup ile paketlenir ve değişmez bir SHA-256 özetiyle mühürlenir.

### Güncel Kararlı Sürüm: v1.0.11
- **Kurulum Dosyası:** `Rove-Setup-1.0.11.exe`
- **Dosya Boyutu:** ~128 MB
- **SHA-256 Doğrulama Kodu:**
  ```text
  bcaa8cf34e0b4473addac48fda466dbf7ce6958697debd982d0fd64b535da702
  ```

#### Bütünlük Kontrolü (PowerShell)
```powershell
Get-FileHash -Path "Rove-Setup-1.0.11.exe" -Algorithm SHA256
```

---

## Hata Bildirimi ve Geri Bildirim

Tespit edilen hatalar, teknik öneriler veya özellik talepleri için [GitHub Issues](https://github.com/ardayesilgul/rove-releases/issues) üzerinden kayıt oluşturabilirsiniz.
