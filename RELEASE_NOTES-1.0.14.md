# Rove 1.0.14

- Dinamik Ada'nın Windows medya oturumu sorguları tek, tekrar kullanılan bağlantıya alındı. Zaman aşımındaki yerel işlemler iptal edilip kapatılır.
- Windows medya ve ses erişimi Rove arayüzünden ayrı, yerel yardımcı süreçlerde çalışır. Takılan yardımcı süreçler sınırlı bekleme ve yeniden deneme politikasıyla sonlandırılır; kullanıcı programları kapatılmaz.
- Yardımcı süreçlere 256 MiB özel bellek sınırı ve ana süreç kapanınca sonlandırma kuralı uygulandı. Ana arayüzün belleğini yapay olarak düşük gösteren zorunlu çalışma kümesi temizleme kaldırıldı.
- Dinamik Ada ayarlardan kapatıldığında görünüm, zamanlayıcılar, fare kancası ve izleyiciler durur. Otomatik görünürlük, bildirimler ve kısayollar kapalı adayı geri açmaz. Tercih uygulama yeniden başlatıldığında korunur.
- Medya yoklamasında her seferinde açılıp kapatılmayan Windows masaüstü tanıtıcıları kaldırıldı. Kapak önbelleği ve medya komut kuyruğu sınırlandırıldı.
- Ana ekrandaki son dosya görselleri gerçek önizleme alanına, kaynak en-boy oranı korunarak yerleşir. Görselin tamamı gösterilir; sıkıştırma, esnetme veya merkezden kırpma yapılmaz.
- İsim bekleyen ve onay bekleyen yüz kartlarında fotoğrafın altında "Önizle" ve "Dosyayı aç" kontrolleri bulunur. Açık/koyu temaya uygun okunaklı yüzeyler, anlık basılma ve hafif hover tepkisi kullanılır. Dar kartlarda kontroller alt alta yerleşir. Dosya açma, diğer sayfalarla aynı akışı kullanır; yüz seçimini veya onay kararını değiştirmez.
- Yüz tanıma modelleri, benzetme eşikleri ve arama sıralaması değiştirilmedi.

Paket kişisel kişi/fotoğraf veritabanı içermez. Bu sürümün performans ölçümleri sınırlı yerel testlerdir; tüm donanımlarda sıfır gecikme garantisi değildir.
