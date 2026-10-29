# Zamanlayıcı Kullanım Kılavuzu

Zamanlayıcı, FUXA'da belirli günlerde, aylar ve saatlere göre olayları tetiklemenize olanak tanıyan güçlü bir tabanlı otomasyon sistemidir. Tam cihaz kontrol yetenekleriyle hem zamanlayıcı modu (açık/kapalı dönemler) hem de olay modu (süre tabanlı tetikleyiciler) sunar.

## Genel Bakış

Zamanlayıcı, cihazları ve etiketleri zaman programlarına göre otomatik olarak kontrol etmenizi sağlar. Öne çıkan özellikler şunlardır:

- **Haftanın Günleri Programlaması**: Belirli günler için olayları planlayın (Pazartesi-Pazar)
- **Ay Modu**: Ay ve ayın günü kombinasyonlarıyla gelişmiş programlama
- **Zamanlayıcı Modu**: Açık/kapalı dönemler için başlangıç ve bitiş saatlerini tanımlayın
- **Olay Modu**: Belirli süreler için olayları tetikleyin
- **Cihaz Eylemleri**: Değer ayarlama veya betik çalıştırma gibi ek eylemleri gerçekleştirin
- **Ana Kontrol**: Zamanlayıcı, cihaz etiketleri için ana denetleyici olarak görev yapar

## Programlama Modları

### Haftanın Günleri Modu

Varsayılan programlama modu, olaylarınız için haftanın belirli günlerini seçmenize olanak tanır.

- **Günler**: Pazartesi, Salı, Çarşamba, Perşembe, Cuma, Cumartesi, Pazar
- **Zaman Aralığı**: Zamanlayıcı modu için başlangıç ve bitiş saatini, olay modu için başlangıç saati ve süresini tanımlayın
- **Görsel Göstergeler**: Seçili günler programlama ekranında vurgulanır

### Ay Modu

Ay ve ayın günü seçimlerini birleştiren hassas kontrol için gelişmiş programlama.

- **Aylar**: Ocak ile Aralık
- **Ayın Günleri**: 1 ile 31
- **Önemli Notlar**:
  - Bir ay 31 günden azsa (örneğin 28 günlüğünde Şubat), 31. gün için planlanan olaylar çalıştırılmaz
  - Node.js zamanlayıcısı "ayın son günü" işlevini desteklemez
  - Olaylar yalnızca geçerli tarih kombinasyonlarında tetiklenir

## Cihaz ve Etiket İlişkisi

### Cihaz Bağlama

Her zamanlayıcı, belirli bir cihaza bağlıdır ve ilişkili etiketini kontrol eder:

- **Cihaz Seçimi**: Projenizdeki mevcut cihazlardan seçim yapın
- **Etiket Kontrolü**: Zamanlayıcı doğrudan cihazın birincil etiketini kontrol eder
- **Ana Yetki**: Zamanlayıcı, manuel etiket değişikliklerine her zaman önceliklidir

### Cihaz Eylemleri

Birincil cihaz etiketini kontrollemenin yanı sıra, ek eylemleri yapılandırabilirsiniz:

- **Değer Ayarla**: Diğer cihaz etiketlerini belirli değerlere ayarlayın
- **Betik Çalıştır**: Özel JavaScript fonksiyonlarını çalıştırın
- **Tetik Noktaları**: Eylemler hem başlangıç hem de bitiş olaylarında çalışır

Cihaz eylemleri zamanlayıcı özelliklerinde yapılandırılır ve temel açık/kapalı kontrol ötesinde genişletilmiş otomasyon yetenekleri sağlar.

## Yetkilendirme

Zamanlayıcı, iki katmanlı bir yetkilendirme sistemi uygular:

### Ana Yetkilendirme
- **Otomatik Atama**: Zamanlanmış cihazlar otomatik olarak ana yetki seviyesini alır
- **Minimum Gereklilik**: Kullanıcıların zamanlanmış cihazlarla etkileşim kurabilmesi için en az ana yetkiye sahip olması gerekir
- **Geçiş Yeteneği**: Ana yetki, aktif olaylar sırasında manuel etiket değişikliklerini geçersiz kılmaya olanak tanır

### Cihaz Başına Yetkilendirme
- **Ek Güvenlik**: Bireysel cihazlar, ana gereksinim ötesinde daha yüksek yetki seviyelerine sahip olabilir
- **İnce Ayarlı Kontrol**: Farklı cihazlar farklı izin seviyeleri gerektirebilir (örneğin, kritik ekipman yönetici erişimi gerektirebilir)
- **Katmanlı Güvenlik**: Ana yetki temel erişim sağlarken, cihaz başına yetki cihaza özel kısıtlamalar ekler

## Zamanlama Ekleme/Düzenleme Formu

### Temel Ayarlar

- **Cihaz**: Kontrol edilecek hedef cihazı seçin
- **Başlangıç Saati**: Olayın ne zaman başlaması gerektiğini belirtin (SS:DD formatında)
- **Mod Geçişi**: Zamanlayıcı Modu ve Olay Modu arasında geçiş yapın

### Zamanlayıcı Modu vs Olay Modu

#### Zamanlayıcı Modu
- **Bitiş Saati**: Olayın ne zaman durması gerektiğini tanımlayın
- **Etiket Davranışı**: Etiket, başlangıç saatinden bitiş saatine kadar AÇIK kalır
- **Kullanım Örneği**: Düzenli açık/kapalı dönemler (örneğin, 08:00 ile 18:00 arasında)

#### Olay Modu
- **Süre**: Olayın ne kadar sürmesi gerektiğini ayarlayın
- **Süre Birimleri**: Saat, dakika, saniye
- **Etiket Davranışı**: Etiket, belirtilen süre için AÇIK olur ve ardından otomatik olarak KAPANIR
- **Kullanım Örneği**: Zamanlı tetikleyiciler (örneğin, pompayı 30 saniye çalıştır)

### Tekrarlayan vs Tek Seferlik Olaylar

- **Tekrarlayan**: Olay programa göre tekrarlanır (varsayılan)
- **Tek Seferlik**: Olay bir kez çalıştırılır ve otomatik olarak kaldırılır
- **Otomatik Temizleme**: Tekrarlanmayan olaylar tamamlanma sonrası silinir

### Ay Modu Yapılandırması

Ay Modu etkinleştirildiğinde:

- **Ay Seçimi**: Olayın hangi aylarda çalışacağını seçin
- **Ayın Günleri Seçimi**: Seçilen aylar içinde belirli tarihleri seçin
- **Takvim Görünümü**: Programlamanızın görsel temsilini görmek için takvim düğmesine tıklayın

## Veri Depolama

### Veritabanı Yapısı

Zamanlamalar, aşağıdaki yapıyla proje veritabanında saklanır:

```json
{
  "schedules": {
    "Cihaz Adı": [
      {
        "id": "unique_schedule_id",
        "startTime": "08:00",
        "endTime": "18:00",
        "days": [false, true, true, true, true, true, false],
        "months": [false, false, false, false, false, false, false, false, false, true, false, false],
        "daysOfMonth": [false, false, false, false, true, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false, false],
        "monthMode": true,
        "recurring": true,
        "eventMode": false,
        "duration": 0,
        "deviceName": "Cihaz Adı",
        "variableId": "tag_id"
      }
    ]
  },
  "settings": {
    "deviceActions": [...]
  }
}
```

### Anahtar Alanlar

- **days**: Pazartesi-Pazar için boolean dizisi (7 eleman)
- **months**: Ocak-Aralık için boolean dizisi (12 eleman)
- **daysOfMonth**: 1-31 için boolean dizisi (31 eleman)
- **monthMode**: true olduğunda tabanlı programlamayı etkinleştirir
- **eventMode**: true olduğunda süre tabanlı olayları etkinleştirir
- **recurring**: Olayın tekrarlanıp tekrarlanmayacağını kontrol eder

## Kullanım Örnekleri

### Günlük Pompa Programı
- Cihaz: Su Pompası
- Günler: Pazartesi-Cuma
- Başlangıç: 08:00, Bitiş: 18:00
- Mod: Zamanlayıcı Modu
- Sonuç: Pompa, hafta içi iş saatleri boyunca çalışır

### Aylık Bakım
- Cihaz: Bakım Vanası
- Aylar: Ocak, Nisan, Temmuz, Ekim
- Günler: Her ayın 15. günü
- Başlangıç: 09:00, Süre: 2 saat
- Mod: Olay Modu, Tekrarlanmayan
- Sonuç: Vana, çeyreklik programda bakım için açılır

### Tatil Aydınlatması
- Cihaz: Tatil Işıkları
- Aylar: Aralık
- Günler: 1'den 31'e
- Başlangıç: 17:00, Bitiş: 23:00
- Mod: Zamanlayıcı Modu
- Sonuç: Işıklar, Aralık ayı her akşam çalışır

## En İyi Uygulamalar

1. **Zamanlamaları Test Edin**: Yeni zamanlamaları geliştirme sırasında her zaman test edin
2. **Ay Sınırlamaları**: Geçersiz tarih kombinasyonları için programlamadan kaçının
3. **Cihaz Eylemleri**: Karmaşık otomasyon mantığı için cihaz eylemlerini kullanın
4. **Zaman Dilimleri**: Programlama için sunucu zaman dilimine dikkat edin
5. **Kaynak Yönetimi**: Tekrarlanmayan olaylar birikimi önlemek için otomatik temizleme yapılır

## Sorun Giderme

### Yaygın Sorunlar

- **Olay Tetiklenmiyor**: Geçerlilik için ay/gün kombinasyonlarını kontrol edin
- **Etiket Değişmiyor**: Cihaz bağlantısını ve izinleri doğrulayın
- **Eylemler Çalışmıyor**: Cihaz eylemi yapılandırmasını kontrol edin
- **Zamanlama Kaydedilmiyor**: Uygun izinlere ve veritabanı erişimine sahip olduğunuzdan emin olun

### Hata Ayıklama Bilgisi

Zamanlayıcı günlükleri şunlar hakkında detaylı bilgi içerir:
- Zamanlama yükleme ve ayrıştırma
- Zaman damgası ile olay tetikleme
- Etiket yazma işlemleri
- Cihaz eylemi yürütme
- Ay modu tarih doğrulaması