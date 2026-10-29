# HowTo Tarifleri

Tarifler, talep üzerine cihazlara uygulanabilecek önceden tanımlanmış etiket değerleri kümesidir. Bir tarif, bir makinenin veya sürecin çalışma parametrelerini (sıcaklık ayar noktaları, süreler, miktarlar) yakalar ve operatörün bu değerleri tek bir eylemle (indirme) cihaza göndermesine veya daha sonra yeniden kullanılmak üzere mevcut cihaz değerlerini tarif geri yüklemesine olanak tanır.

## Genel Bakış

FUXA, iki tür reçite ayırır:

- **Reçite Türleri**: Hangi etiketlerin (Tag) katılacağını ve varsayılan değerlerinin ne olduğunu tanımlayan şablonlar. Türler, Reçite (Recipe) yönetimi sayfasında oluşturulur ve sürdürülür.
- **Reçite Örnekleri**: Bir türün somut kopyaları, her biri türün etiketleri için kendi değerlerine sahiptir. Reçite (Recipe) widget'ı bir türle ilişkilidir, ancak bu türün örneklerini görüntüler ve üzerinde çalışır.

Öne çıkan özellikler şunlardır:

- **İndirme**: Bir reçinin tüm etiket değerlerini cihazlarına tek tek yazma, canlı ilerleme raporlaması ile
- **Yükleme**: Mevcut cihaz değerlerini bir reçite geri okuma ve bunları kalıcı hale getirme
- **Reçite Türleri**: Etiket girişleri için tekrar kullanılabilen şablonlar
- **Reçite Örnekleri**: Bir türün bağımsız kopyaları, her biri kendi değerlerini tutar
- **Reçite Widget'ı**: Reçite türüyle ilişkili, operatör odaklı widget, oluşturma/düzenleme/silme/indirme/yükleme eylemleri ile
- **Dışa Aktarma/Dahili Aktarma**: Reçileri JSON veya CSV dosyaları olarak paylaşma
- **İptal**: Çalışan bir indirmeyi veya yüklemeyi herhangi bir anda durdurma
- **Yetkilendirme**: Kimin reçite yönetebileceği ve widget ile etkileşime geçebileceği konusunda rol tabanlı kontrol

## Tarif Türleri ve Örnekleri

Bir **tarif türü**, ana şablondur. Bir ad, isteğe bağlı bir açıklama ve liste girdilerinden (etiket kimliği, etiket adı, etiket türü ve varsayılan değer) oluşur. Türler, Tarifler yönetimi sayfasında ve widget'ın özellik panelinde görünen birimlerdir.

Bir **tarif örneği**, bir türden türetilmiş somut bir anlık görüntüdür. Örnekler, türün etiket kümesini paylaşır ancak her biri kendi değerlerini saklar, bu nedenle farklı ürünler veya partiler aynı türün ayrı örnekleri olarak kaydedilebilir. Bir örnek, bir `typeId` referansı aracılığıyla türüne bağlıdır; `typeId`'si olmayan tarifler türlerdir.

## Tarifleri Yönetme

Tarifler, Tarifler yönetimi sayfasından yönetilir. Tarifler, düzenleyicideki **Tarifler** kurulum kartından (fatura simgesi, menü etiketi "Tarifler") ulaşılır ve bu, `/tarifler` yolunu açar.

### Tarif Listesi

Sayfa, bir tabloda tüm tarif türlerini **Ad**, **Açıklama**, **Girdiler** ve **Eylemler** sütunlarıyla listeler. Tarifler adına göre (büyük/küçük harfe duyarlı olmayan) sıralanır. Listeden şunları yapabilirsiniz:

- **Bir tür tarif ekleyin**: Yeni bir tür için tarif düzenleyici iletişim kutusunu açar
- **Düzenle**: Seçili türle önceden doldurulmuş tarif düzenleyici iletişim kutusunu açar
- **Sil**: Onay istemi sonrasında türü kaldırır (konuk olmayan rol gerektirir, bkz. Yetkilendirme)
- **İndir**: Tarifin değerlerini cihazlarına iter
- **Yükle**: Mevcut cihaz değerlerini tarife geri okur
- **Dışa aktar**: Tarifi bir JSON veya CSV dosyası olarak indirir
- **İçe aktar**: Bir tarifi bir JSON veya CSV dosyasından yükler

### Tarif Düzenleyici İletişim Kutusu

Düzenleyici iletişim kutusu, bir tür tarif tanımlamanızı veya değiştirmenizi sağlar:

- **Ad**: Gerekli, 128 karaktere kadar
- **Açıklama**: İsteğe bağlı, 512 karaktere kadar
- **Girdiler**: Etiket adı, etiket kimliği ve etiket türü ile etiket girdilerinin bir tablosu; en az bir girdi gereklidir

**Girdi ekle** kullanarak etiket tarayıcısını açıp projenizin cihazlarından etiketleri seçebilir ve kaldırma düğmesini kullanarak bir girdiyi bırakabilirsiniz. Kaydetme için bir ad ve en az bir girdi gerekir.

### Etiket Tarayıcı

Etiket tarayıcı, bir tarif girdisinin referans alması gereken etiketleri seçmeye yardımcı olur. Mevcut projenin cihazlarını listeler; bir cihaz seçtikten sonra etiketlerini görürsünüz ve etiket adına veya kimliğine göre filtreleyebilirsiniz. Bir etiketi seçmek, onun adı ve türüyle tarife ekler.

### İndirme, Yükleme ve İlerleme

Bir tarif üzerinde **İndir** veya **Yükle**'ye tıklamak, bir iletişim kutusu açar (çalışma sırasında kapatılamaz) ve şunları gösterir:

- İşlenen geçerli girdi ve durumu (yazma/okuma, başarılı, hata)
- Yüzde tamamlanma ile bir ilerleme çubuğu
- Girdi başına hata mesajları, varsa
- Nihai başarılı ve hata sayıları

İletişim kutusunda, çalışan yürütmeni durduran **İptal** düğmesi ve yürütme bittikten sonra **Kapat** düğmesi bulunur.

## Recipe Widget (Tarayıcı Widget'ı)

Recipe widget'ı, bir görünümde doğrudan bir recipe türü ile çalışan operatör odaklı bileşendir.

### Widget'ı Ekleme

Editörün araç çubuğunda, **Recipe** (Tarayıcı) kontrolünü (fatura simgesi, ipucu "Recipe") seçin. Widget'ı görünümün üzerine bırakın ve özelliklerini açın.

### Widget Özellikleri

Widget'ın özellik paneli şunları sunar:

- **Recipe**: Widget'ın bağlı olduğu recipe türünü seçin. Bu türün örnekleri çalışma zamanında gösterilir. "No recipe selected" (Recipe seçilmedi) widget'ı boşta bekletme durumuna bırakır.
- **Behavior**: **Read-only mode** (Salt okunur mod) anahtarını kullanın. Salt okunur modda widget, New (Yeni), Delete (Sil) ve Save (Kaydet) eylemlerini gizler, değerleri salt okunur metin olarak gösterir ve sadece Download (İndir) ve Upload (Yükle) sunar.
- **Authorization**: Widget ile etkileşim kurabilecek kullanıcı rollerini yapılandırın. Standart FUXA izin iletişim kutusu kullanılır; hem eylem çubuğunun gösterilip gösterilmeyeceğini hem de etkinleştirilip etkinleştirilmeyeceğini kontrol eder.
- **Visible actions**: New (Yeni), Delete (Sil), Save (Kaydet), Download (İndir) ve Upload (Yükle) düğmelerini bağımsız olarak gösterin veya gizleyin (görünürlük ayar izniyle bağımsızdır).
- **Appearance**: Widget'ın arka plan, metin, kenarlık ve vurgu renkleri.

### Çalışma Zamanı İşlemi

Çalışma zamanında widget, bağlı recipe türünün tüm örneklerini yükler ve bunları tek tek gösterir:

- **Navigation**: Previous/next (Önceki/Sonraki) düğmeleri örnekler arasında ilerler, mevcut konumu gösterir (örneğin "2 / 5")
- **Entries**: Entries (Girdiler) tablosu, her tag'ı renkli bir tür rozetiyle ve düzenlenebilir bir değer alanıyla gösterir (sayısal alanlar sayı girişi, string değerler metin girişi kullanır)
- **New**: Yeni bir örnek oluşturur; türün varsayılan girdileri bu örneğe kopyalanır
- **Delete**: Mevcut örneği onay isteminden sonra kaldırır
- **Save**: Mevcut örneğin düzenlenmiş değerlerini kalıcı hale getirir
- **Download**: Önce düzenlenmiş tüm değerleri kaydeder, ardından örnek değerleri cihazlara gönderir ve bir ilerleme çubuğu gösterir ("Sending..." (Gönderiliyor...))
- **Upload**: Mevcut cihaz değerlerini mevcut örneğe okur, ilerlemeyi gösterir ve bittiğinde gösterilen girdileri yeniler

Editör modunda widget SVG tuvalin içinde işlenir ve izin kontrolleri atlanır, böylece tasarımcı onu serbestçe yapılandırabilir.

## İndirme ve Yükleme Nasıl Çalışır

Her iki işlem de asenkrondur: istemci bunları tek bir API çağrısıyla başlatır ve WebSocket üzerinden canlı ilerlemeyi takip eder.

### İndirme (cihaza yazma)

1. İstemci, recipe kimliği ile `POST /api/recipes/download` çağrısı yapar
2. Sunucu, recipe'nin var olduğunu, en az bir girdisi olduğunu ve zaten çalışmadığını doğrular
3. Toplam girdi sayısıyla `202` ile hemen yanıt verir ve arka planda devam eder
4. Her girdi için sunucu, saklanan değeri tag türüne zorlar ve `setTagValue` ile cihaza yazar, durum (`writing`, `success`, `error`), girdi indeksi ve toplam ile `recipe:download-progress` olayları yayımlar
5. Yazılamayan girdiler tek tek bildirilir ve döngü devam eder; tamamlanmada `recipe:download-complete` olayı başarı/hata sayılarını ve hata listesini taşır

### Yükleme (cihazdan okuma)

1. İstemci, recipe kimliği ile `POST /api/recipes/upload` çağrısı yapar
2. Doğrulama, indirme ile aynıdır ve sunucu yine `202` ile ilk yanıtını verir
3. Her girdi için sunucu, `getTagValue` ile cihazdan mevcut değeri okur ve girdiyi günceller, `recipe:upload-progress` olayları (`reading`, `success`, `error`) yayımlar
4. En az bir girdi başarılı olursa, güncellenen değerler kalıcı hale getirilir; `recipe:upload-complete` olayı sonucu bildirir ve istemci örneği listesini yeniden yükler

### Değer Zorlaması

Değerler, yazılmadan önce doğru JavaScript türüne dönüşür, girdinin tag türüne göre:

- **bool/boolean**: booleans, `"true"`/`"1"` ve `"false"`/`"0"` kabul eder
- **int/dint/int16/int32**: tam sayı olarak ayrıştırılır
- **real/float/double/number**: ondalıklı sayı olarak ayrıştırılır
- **byte**: 0-255 aralığına sıkıştırılır
- **string/word**: değiştirilmeden iletilir

### İptal ve Eşzamanlılık

- Zaten çalışan bir recipe yeniden başlatılamaz; API `400 "Recipe execution already in progress"` ile yanıt verir. Bu koruma, aynı recipe'nin hem indirmesini hem de yüklemesini kapsar.
- İstemci, iptal için `recipe:cancel-execution` olayını WebSocket üzerinden yayımlayarak talep eder (yazma yetkisi gerektirir). Sunucu recipe'yi çalışma kümesinden kaldırır, döngü bir sonraki girdide durur ve `recipe:cancel-confirmed` olayı yayımlanır.

## Yetkilendirme

Tarif yetkilendirmesi iki yerde uygulanır:

- **Sunucu (REST API)**: Tarif şablonları proje verileridir ve özel uç noktaları kullanır: `GET/POST/DELETE /api/recipes/types`. Güvenli mod etkinleştirildiğinde, oluşturma, düzenleme, silme ve şablonları içe aktarma (`POST /api/recipes/types/import`) işlemleri, kullanıcının grupları ve rollerinden çözümlenen yönetici iznini gerektirir. Tarif örnekleri işletimsel verilerdir ve `GET/POST/DELETE /api/recipes/instances` uç noktasını kullanır; güvenli modda, kimliği doğrulanmış bir konuk olmayan kullanıcı gerektirir ve bağlı tarif şablonunun izni işleme için izin vermelidir. `POST /api/recipes/download` ve `POST /api/recipes/upload`, canlı etiket değerlerini yazdığı/okuduğu için aynı şablon izin kontrolünü kullanır.

- **Tarif widget'ı**: Widget'ın **Yetkilendirme** ayarında yapılandırılan izin, çalışma zamanında eylem çubuğunun gösterilip gösterilmeyeceğini ve etkinleştirilip etkinleştirilmeyeceğini kararlandırır. Editör modu her zaman kontrolü atlar. **Görünür eylemler** ayarı izinden bağımsızdır ve yalnızca hangi düğmelerin oluşturulduğunu kontrol eder.

## Veri Depolama

Tarif verileri proje yapılandırması ve işletimsel depolama arasında ayrılır:

- Tarif türleri/şablonlar, tanım etiketleri gibi proje içinde saklanır, çünkü tanım etiketleri etiket yapısını tanımlar ve proje etiketlerini referans olarak alır.
- Tarif örnekleri bir SQLite veritabanında (`recipes.db` sunucu çalışma dizininde) saklanır. Örnekler kendi değerlerini tutar ve şablonu `typeId` aracılığıyla referans olarak alır.

- Tarif id'leri `r_` sonrasında 16 karakterlik hex değerlerden oluşur; giriş id'leri `e_` sonrasında 8 karakterlik hex değerlerden oluşur
- Doğrulama sınırları: ad zorunludur (maks 128 karakter), açıkl isteğe bağlıdır (maks 512), 1 ile 1000 giriş arasında, her giriş boş olmayan bir etiket id'si ve geçerli bir etiket türü içermelidir

## Kullanım Örnekleri

### CIP (Temizleme-Yerinde) Tarif Türü

- Tür: "CIP Döngüsü"
- Girdiler: sıcaklık ayar değeri (real), alkalilik konsantrasyonu (real), yıkama süresi (int), durulama süresi (int), pompa hızı (int)
- Örnekler: ürün başına bir tane — "Ürün A", "Ürün B", "Ürün C" — her biri farklı ayar değerleriyle

Bir üretim çalışması öncesinde operatör, Tarif widget'ında ürün örneğini seçer ve **İndir** butonuna basar. Sunucu, tank PLC'sine sırayla her değeri yazar; widget ilerlemeyi gösterir. Hat başka bir ürüne değiştiğinde, operatör farklı bir örnek seçer ve tekrar indirir.

### Mevcut Çalışma Değerlerini Yakala

Bir operatör süreci manuel olarak ayarlar ve sonucu kaydetmek ister. Widget'ta bir örnek seçer ve **Yükle** butonuna basar. Sunucu, canlı değerleri aygıttan örneğe okur, kalıcı hale getirir ve widget yeni değerleri gösterir. Örnek daha sonra aynı koşulları yeniden üretmek tekrar indirilebilir.

## En İyi Uygulamalar

1. **Önce tasarım türlerini tanımlayın**: her makine veya işlem için bir reçete türü tanımlayın, ardından somut parametre setleri için örnekler oluşturun
2. **Girdileri yönetilebilir tutun**: bir reçete en fazla 1000 girdi destekler; netlik için alakasız Tag'leri ayrı türlerde bölün
3. **Cihaz bağlantısını doğrulayın**: giriş başına başarısızlık indirme/yükleme raporu oluşturur, ancak bağlantısı kesilen cihaz her girişte başarısız olur — önce cihaz durumunu kontrol edin
4. **Operatör ekranları için salt okunur modu kullanın**: operatörlerin sadece değerleri uygulaması gerekiyorsa, salt okunur modu etkinleştirin böylece değerler görünümde düzenlenemez
5. **Görünen eylemleri sınırlayın**: widget'ın görünüm ayarlarını kullanarak operatörlerin aslında ihtiyaç duyduğu eylemleri sadece görünür yapın
6. **Tek bir girdiyle test edin**: büyük reçetelerle çalışmadan önce akışı doğrulamak için küçük bir reçete ile başlayın

## Sorun Giderme

### Yaygın Sorunlar

- **"Tarif yürütme zaten devam ediyor"**: tarif hala çalışıyor (veya önceki yürütme tamamlanmadı). Tamamlanması için bekleyin veya ilerleme iletişim kutusundan iptal edin.
- **"Etikete yazma başarısız oldu" / "Etiketten okuma başarısız oldu"**: cihaza ulaşılamıyor veya etiket kimliği projede artık geçerli değil.
- **İndirme hatalarla tamamlanıyor**: ilerleme iletişim kutusundaki girdi başına hata listesi başarısız olan etiketleri adlandırır; değerlerini ve türlerini kontrol edin.
- **Konuk tarifleri kaydedemez veya silemez**: güvenli mod etkinleştirildiğinde, yazma işlemleri konuk olmayan bir rol gerektirir.
- **Widget "Yapılandırılmış tarif türü yok" gösteriyor**: widget özelliklerini açın ve bir tarif türü seçin.
- **Widget eylem çubuğu gizli**: yapılandırılmış yetkilendirme veya görünür-eylemler ayarları düğmeleri gizliyor; düzenleyici modda izin kontrolü atlanır.

### Hata Ayıklama Bilgisi

Tarif işlemleri sunucu konsoluna ve log dosyalarına şunlarla ilgili ayrıntılarla günlük yazar:

- Tarif veri doğrulama hataları (geçersiz ad, girdiler, etiket türleri veya değerler)
- İndirme/yükleme başlangıcı, girdi başına yazma/okuma sonuçları ve tamamlama özetleri
- Yürütme iptalleri ve yetkisiz istekler
- Depolama hataları (veritabanı erişimi, JSON ayrıştırma)
