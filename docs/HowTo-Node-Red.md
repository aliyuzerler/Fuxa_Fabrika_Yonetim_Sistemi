# Node-RED Entegrasyonu Nasıl Yapılır

## Genel Bakış

FUXA, tam bir node-RED entegrasyonu içerir, SCADA sisteminizle etkileşim kuran güçlü otomasyon akışları oluştumanıza olanak tanır.

## Kurulum ve Yükleme

### FUXA'da Node-RED
Node-RED, FUXA ile birlikte otomatik olarak gelir ve ek bir yükleme gerektirmez. FUXA ayarlar menüsünden erişilebilir.

### Dashboard 2'yi Yükleme
Dashboard 2'nin gerekiyorsa ayrıca yüklenmesi gerekir:

1. Node-RED editörünü açın (FUXA ayarları aracılığıyla)
2. Menüyü (☰) tıklayın → **Paleti Yönet**
3. Aratın: `@flowfuse/node-red-dashboard`
4. **Yükle**'yi tıklayın
5. Node-RED/FUXA'yı yeniden başlatın

### Gerekli Bağımlılıklar
- **@flowfuse/node-red-dashboard**: Modern pan oluşturma için
- **node-red-contrib-fuxa**: Otomatik olarak dahil edilir (FUXA entegrasyon düğümleri sağlar)

### Docker'da isteğe bağlı ping istekleri

Varsayılan FUXA Docker imajı `ping` yüklemez. Bir Node-RED akışının sistem yürütülebilir dosyasına ihtiyacı varsa, isteğe bağlı özel bir imaj oluşturun. `ping` çağıran bir palet paketi yükleme, işletim sistemi yürütülebilir dosyasını kurmaz.

Aşağıdakileri `Dockerfile.ping` olarak kaydedin. Bu örnek, tabanlı FUXA imajını genişletir ve derleme sırasında paket yöneticisinin kullanılabilir olmasını gerektirir:

```dockerfile
ARG FUXA_IMAGE=frangoteam/fuxa:latest
FROM ${FUXA_IMAGE}

RUN apt-get update \
    && apt-get install -y --no-install-recommends iputils-ping \
    && rm -rf /var/lib/apt/lists/* \
    && ping -V
```

Özel imajı derleyin ve kontrol edin:

```bash
docker build --build-arg FUXA_IMAGE=frangoteam/fuxa:latest -f Dockerfile.ping -t fuxa-with-ping:local .
docker run --rm --entrypoint ping fuxa-with-ping:local -V
docker run --rm --entrypoint ping fuxa-with-ping:local -n -c 1 -W 2 127.0.0.1
```

Tekrarlanabilir bir derleme için, `FUXA_IMAGE` bağımsız değişkenini dağıtımınızda kullanılan sürüm etiketi veya özetle değiştirin. Mevcut veri hacimleri, ortam ve ağ ayarlarını korurken `fuxa-with-ping:local` imajını seçin. Yeni imajı kullanmak için konteyneri yeniden oluşturun; mevcut bir konteyneri yeniden başlatmak yürütülebilir dosyayı kurmaz. Varsayılan FUXA imajı değişmeden kalır.

Özel imajda, ek bir palet paketi yüklemeden sabit bir ana makineyi kontrol etmek için yerleşik Node-RED **exec** düğümünü kullanın:

1. Bir **inject** düğümünü bir **exec** düğümüne bağlayın.
2. Yürütülebilir komutu, başlangıç döngüsü kontrolü için `ping -n -c 1 -W 2 127.0.0.1` olarak ayarlayın.
3. **Msg.payload'ı ekle**'yi devre dışı bırakın ve spawn modunu devre dışı bırakın.
4. exec çıktılarını **debug** düğümlerine bağlayın. İlk çıktı stdout'u, ikinci stderr'ı ve üçüncü dönüş kodu nesnesini (`msg.payload.code`) içerir.
5. Dağıt ve enjekte et. Dönüş kodu `0` bir yanıt olduğunu gösterir. İzlemek istediğiniz cihazın sabit IP adresini veya ana bilgisayar adıyla `127.0.0.1`'i değiştirin.

Sayı ve yanıt zaman aşımı ICMP sorgusunu sınırlar. Döngü içi (`127.0.0.1`) konteynerin kendisini, Docker ana makinesini veya bir PLC'yi kontrol eder. Başarısız bir ping, cihazın çevrimdışı olduğu anlamına gelmez: yönlendirme veya bir güvenlik duvarı ICMP yanıtlarını engelleyebilir.

Yürütülebilir dosyayı ve döngü içi bağlantıyı Node-RED dışından doğrulamak için, `<container-name>`'i çalıştıran özel FUXA konteyner adıyla değiştirin:

```bash
docker exec <container-name> ping -V
docker exec <container-name> ping -n -c 1 -W 2 127.0.0.1
```

Sistem `ping` komutunu çağıran palet düğümleri aynı yürütülebilir dosyayı kullanabilir, ancak Node-RED paketleri hala ayrıca yüklenmelidir. Ping `Operation not permitted` (İzin Verilmedi) bildiriyorsa, konteynerin ICMP soket izinlerini ve çalışma zamanı ilkesini kontrol edin. Yürütülebilir dosyayı kurmak konteyner yeteneklerini değiştirmez.

### Yapılandırma
Ek yapılandırma gerekmez. Node-RED otomatik olarak FUXA'nın çalışma zamanı ortamına bağlanır.

### Güvenlik ve Erişim Modu
FUXA güvenliği etkinleştirildiğinde Node-RED erişimi yapılandırılabilir.

**Ayar**: `nodeRedAuthMode`

- `secure` (varsayılan): Node-RED editörü ve admin API'leri kimlik doğrulama (JWT veya API anahtarı) gerektirir.
- `legacy-open`: Node-RED açık kalır (kimlik doğrulama yok) ve eski davranışı korur.

**Notlar**:
- Değişiklikler sunucu yeniden başlatıldıktan sonra uygulanır.
- `secureEnabled=true` ve `nodeRedAuthMode=secure` ise, dış sistemler bir API anahtarı başlığı kullanarak Node-RED HTTP uç noktalarına erişebilir:
  `x-api-key: <api-key>`
  API anahtarları FUXA altında **Editör → Kurulum → API Anahtarları** yönetilir.

## Node-RED'e Erişim

### Node-RED Düzenleyici
Node-RED akış düzenleyicisine FUXA ayarları üzerinden erişin:

1. FUXA'da **Ayarlar** düğmesine tıklayın
2. **Node-RED** bölümüne gidin
3. **Node-RED Düzenleyici Aç** düğmesine tıklayın

Bu işlem, Node-RED akış düzenleyicisini yeni bir pencere/sekmede açar.

### Dashboard 2
**Not**: Dashboard 2 varsayılan olarak yüklü değildir ve ayrı olarak yüklenmelidir.

#### Kurulum
1. Node-RED'de, **Menü → Palet Yönet** seçeneğine gidin
2. Arama yapın ve kurun: `@flowfuse/node-red-dashboard`
3. Node-RED/FUXA'yı yeniden başlatın

#### Dashboard 2'ye Erişim
Kurulduktan sonra, Node-RED panolarınızı görüntüleyebilirsiniz:

**FUXA Görünümünde Gömülü**: Herhangi bir FUXA görünümüne bir bileşen ekleyin

#### FUXA Görünümünde Dashboard Gömme

1. Projenizi FUXA düzenleyicide açın
2. Görünümünüze bir iframe bileşeni ekleyin
3. URL'yi şuna ayarlayın: `/dashboard`
4. İframe boyutunu ve özelliklerini ihtiyaca göre yapılandırın
5. Sadece tek bir sayfa göstermek istiyorsanız, ayarları düzenleyebilir ve `/dashboard/page1` veya sayfanızın adını kullanabilirsiniz

**Önemli**: `/dashboard` adresine erişmeye çalışmadan önce Dashboard 2'nin yüklenmiş olması gerekir.

## FUXA Katkı Düğümleri

FUXA katkı paketi, SCADA entegrasyonu için özel düğümler sağlar. Bu paket yalnızca FUXA Node-Red Entegrasyonu ile çalışacak şekilde tasarlanmıştır ve bağımsız bir Node-Red kurulumunda çalışmayacaktır.

### Tags Nodes

#### get-tag
**Amaç**: FUXA etiketinin mevcut değerini almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)

**Giriş**: Herhangi bir mesaj (etiket okumayı tetikler)
**Çıkış**: `msg.payload` etiket değerini içerir

**Örnek Çıkış**:
```json
{
  "payload": 25.3,
  "_msgid": "abc123"
}
```

#### set-tag
**Amaç**: Bir değeri FUXA etiketine yazmak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)

**Giriş**: `msg.payload` yazılacak değeri içerir
**Çıkış**: `msg.payload` yazılan değeri içerir

**Örnek Giriş**:
```json
{
  "payload": 50.0
}
```

#### get-tag-change
**Amaç**: Cihaz anketleme olaylarına dayalı olarak FUXA etiketindeki değer değişikliklerini izlemek

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)

**Giriş**: Yok (olayla yönlendirilen)
**Çıkış**: `msg.payload`, değer değiştiğinde yeni etiket değerini içerir

**Çalışma prensibi**: Bu düğüm, FUXA'nın cihaz anketleme olaylarına abone olur. Cihazlar anketlendiğinde ve bir etiket değeri son anketten beri değiştiğinde, düğüm yeni değerle bir mesaj çıktısı verir. Zamanlama, cihazın anketleme aralığına bağlıdır (genellikle 1-5 saniye).

**Örnek Çıkış**:
```json
{
  "payload": 25.3,
  "topic": "temperature",
  "tagId": "device1.temperature",
  "tagName": "temperature",
  "timestamp": "2025-01-01T10:30:00.000Z",
  "previousValue": 24.8
}
```

**Not**: Bu düğüm, etiket değerleri gerçekten değiştiğinde, her anket döngüsünde değil mesaj çıktısı verir. Manuel anketleme gerektirmeden değişimleri verimli bir şekilde izler.

#### get-tag-id
**Amaç**: Bir etiketin adına göre iç ID'sini almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)

**Giriş**: Herhangi bir mesaj
**Çıkış**: `msg.payload` etiket ID'sini içerir

#### get-historical-tags
**Amaç**: Birden fazla etiket için geçmiş verilerini aynı anda alma

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tags**: Virgülle ayrılmış etiket adları listesi (örneğin, "temp1,temp2,pressure")
- **From Time**: Geçmiş dönemin başlangıcı (datetime-local seçici)
- **To Time**: Geçmiş dönemin sonu (datetime-local seçici)

**Giriş**: Herhangi bir mesaj veya `msg.tags`, `msg.from`, `msg.to` ile geçersiz kılma
**Çıkış**: `msg.payload` geçmiş veri dizisini içerir

**Çoklu Etiket Desteği**:
- Etiketleri virgülle ayrılmış değerler olarak girin: `temp1,temp2,pressure`
- Her etiket için geçmiş verileri aynı zaman diliminde alınır
- Sonuçlar tek bir yanıpta birleştirilir

**Örnek Giriş** (virgülle ayrılmış):
```json
{
  "tags": "temp1,temp2,pressure",
  "from": "2025-01-01T08:00",
  "to": "2025-01-01T17:00"
}
```

**Örnek Giriş** (dizi):
```json
{
  "tags": ["temp1", "temp2", "pressure"],
  "from": "2025-01-01T08:00",
  "to": "2025-01-01T17:00"
}
```

**Örnek Çıkış**:
```json
{
  "payload": [
    {
      "tag": "temp1",
      "data": [
        {"timestamp": "2025-01-01T08:00:00Z", "value": 25.3},
        {"timestamp": "2025-01-01T09:00:00Z", "value": 26.1}
      ]
    },
    {
      "tag": "temp2",
      "data": [
        {"timestamp": "2025-01-01T08:00:00Z", "value": 24.8},
        {"timestamp": "2025-01-01T09:00:00Z", "value": 25.2}
      ]
    }
  ]
}
```

#### get-tag-daq-settings
**Amaç**: Bir etiket için DAQ (Veri Toplama) ayarlarını almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)

**Giriş**: Herhangi bir mesaj
**Çıkış**: `msg.payload`, DAQ ayarları nesnesini içerir

#### set-tag-daq-settings
**Amaç**: Bir etiket için DAQ (Veri Toplama) ayarlarını yapılandırma

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Tag**: Etiket adı (FUXA cihazlarından doldurulan açılır liste)
- **Enabled**: Bu etiket için DAQ'ı etkinleştirmek/devre dışı bırakmak için onay kutusu
- **Interval**: Milisaniye cinsinden örnekleme aralığı
- **Deadband**: Veri güncellemeleri için minimum değişiklik eşiği

**Giriş**: Herhangi bir mesaj veya `msg.tag`, `msg.enabled`, `msg.interval`, `msg.deadband` ile geçersiz kılma
**Çıkış**: `msg.payload` güncellenmiş ayarları içerir

**DAQ Ayarları**:
- **Enabled**: Bu etiket için veri toplamayı aç/kapat
- **Interval**: Etiketi ne sıklıkta örneklemek (milisaniye cinsinden)
- **Deadband**: Yalnızca bu eşiğe büyük değişiklikleri raporla (gürültüyü önler)

### Devices Nodes

#### cihaz-etkinleştir
**Amaç**: Bir cihaz bağlantısını etkinleştirme veya devre dışı bırakma

**Parametreler**:
- **Ad**: Tanımlama için isteğe bağlı düğüm adı
- **Cihaz Adı**: Cihaz adı (FUXA cihazlarından doldurulan açılır liste)
- **Etkin**: Cihazı etkinleştirmek/devre dışı bırakmak için onay kutusu

**Giriş**: Herhangi bir mesaj veya `msg.deviceName`, `msg.enabled` ile geçersiz kılma
**Çıkış**: `msg.payload` işlem sonucunu içerir

**Etkinleştir/Devre Dışı Bırak**: Cihazı etkinleştirmek için kutuyu işaretleyin, devre dışı bırakmak için işaretini kaldırın

#### get-device
**Amaç**: FUXA aygıtı hakkında bilgi almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Device Name**: Aygıt adı (FUXA aygıtlarından doldurulan açılır liste)
- **Include Tags**: Yanıtta etiket bilgilerini içeren onay kutusu

**Giriş**: Herhangi bir mesaj veya `msg.deviceName`, `msg.includeTags` ile geçersiz kılma
**Çıkış**: `msg.payload` aygıt bilgisi nesnesi içerir

**Include Tags**: İşaretlendiğinde, yanıt aygıtla ilişkili tüm etiketleri içerir

#### enable-device
**Amaç**: Cihaz bağlantısını etkinleştirme veya devre dışı bırakma

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Device**: Cihaz adı (FUXA cihazlarından doldurulan açılır liste)
- **Enable**: Etkinleştirme/devre dışı bırakma için onay kutusu

**Giriş**: Herhangi bir mesaj veya `msg.enable` ile geçersiz kılma
**Çıkış**: `msg.payload` işlem sonucunu içerir

#### get-device-property
**Amaç**: Bir cihazdan bir özellik değerini almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Device Name**: Cihaz adı (FUXA cihazlarından doldurulan açılır liste)
- **Property**: Alınacak özellik adı

**Giriş**: Herhangi bir mesaj veya `msg.deviceName`, `msg.property` ile geçersiz kılma
**Çıkış**: `msg.payload` özellik değerini içerir

**Özellik Adları**: Yaygın özellikler arasında "durum", "bağlı", "son hata" vb. bulunur

#### set-device-property
**Amaç**: Bir cihazın özelliğine değer atama

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Device Name**: Cihaz adı (FUXA cihazlarından doldurulan açılır liste)
- **Property**: Ayarlanacak özellik adı
- **Value**: Özellik için ayarlanacak değer

**Giriş**: `msg.payload` değeri içerir veya `msg.deviceName`, `msg.property`, `msg.value` ile geçersiz kılınır
**Çıkış**: `msg.payload` ayarlanan değeri içerir

### Alarms Nodes

#### get-alarms
**Amaç**: Aktif mevcut alarmalırı almak

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı

**Giriş**: Herhangi bir mesaj
**Çıkış**: `msg.payload` aktif alarmaların dizisini içerir

**Örnek Çıkış**:
```json
{
  "payload": [
    {
      "id": "alarm1",
      "name": "Yüksek Sıcaklık",
      "status": "aktif",
      "timestamp": "2025-01-01T10:30:00Z",
      "value": 85.5
    }
  ]
}
```

#### get-history-alarms
**Amaç**: Geçmiş alarm verilerini almak

**Parametreler**:
- **Ad**: Tanımlama için isteğe bağlı düğüm adı
- **Başlangıç Zamanı**: Geçmiş dönemin başlangıcı (datetime-local seçici)
- **Bitiş Zamanı**: Geçmiş dönemin sonu (datetime-local seçici)

**Giriş**: Herhangi bir mesaj veya `msg.startTime` ve `msg.endTime` ile geçersiz kılma
**Çıkış**: `msg.payload` geçmiş alarm dizisini içerir

#### ack-alarm
**Amaç**: FUXA'da alarm onaylama

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Alarm Adı**: Alarm adı (FUXA alarmlarından doldurulan açılır liste)
- **Türler**: Virgülle ayrılmış alarm türleri (isteğe bağlı)

**Giriş**: Herhangi bir mesaj veya `msg.alarmName`, `msg.types` ile geçersiz kılma
**Çıkış**: `msg.payload`, onaylama sonucunu içerir

**Alarm Türleri**: Belirli alarm kategorilerini onaylamak için alarm türlerini belirtin (virgülle ayrılmış: "warning,error,critical")

### Views Nodes

#### set-view
**Amaç**: FUXA'daki mevcut görünümü değiştirme

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **View Name**: Görünüm adı (FUXA görünümlerinden doldurulan açılır liste)

**Giriş**: Herhangi bir mesaj veya `msg.viewName` ile geçersiz kılma
**Çıkış**: `msg.payload` işlem sonucunu içerir

#### open-card
**Amaç**: FUXA'da belirli bir kart/diyaloğu açar

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Card**: Kart adı (FUXA kartlarından dolu açılır liste)

**Giriş**: Herhangi bir mesaj veya `msg.cardName` ile geçersiz kılma
**Çıkış**: `msg.payload` işlem sonucunu içerir

### Scripts Nodes

#### execute-script
**Amaç**: FUXA betiğini çalıştırma

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Script**: Betik adı (FUXA betiklerinden doldurulan açılır liste)

**Giriş**: Herhangi bir mesaj veya `msg.scriptName` ile geçersiz kılma
**Çıkış**: `msg.payload`, yürütme sonucunu içerir

**Örnek Çıkış**:
```json
{
  "payload": {
    "success": true,
    "script": "myScript",
    "result": "Script completed successfully",
    "timestamp": "2025-01-01T10:45:00Z"
  }
}
```

### DAQ Nodes

#### get-daq
**Amaç**: Tek bir etiket için Veri Toplama (DAQ) verilerini almak

**Parametreler**:
- **Name**: Kimlik doğrulama için isteğe bağlı düğme adı
- **Tag**: Tek etiket adı (FUXA cihazlarından doldurulan açılır liste)
- **From Time**: DAQ döneminin başlangıcı (datetime-local seçici)
- **To Time**: DAQ döneminin sonu (datetime-local seçici)

**Giriş**: Herhangi bir mesaj veya `msg.from`, `msg.to` ile geçersiz kılma
**Çıkış**: `msg.payload` tek etiket için DAQ verilerini içerir

**Sadece Tek Etiket**: get-historical-tags düğmesine aksine, bu düğme aynı anda yalnızca bir etiketi işler. Birden fazla etiket için ayrı get-daq düğümleri veya get-historical-tags kullanın.

**Örnek Çıkış**:
```json
{
  "payload": [
    {"timestamp": "2025-01-01T10:00:00Z", "value": 25.3, "quality": "good"},
    {"timestamp": "2025-01-01T10:01:00Z", "value": 25.8, "quality": "good"}
  ]
}
```

### Events Nodes

#### olay-yayınla
**Amaç**: Sistemler arası iletişim ve otomasyon tetiklemesi için FUXA'nın olay sistemi üzerinden özel olaylar yayınlamak

**Parametreler**:
- **Ad**: Tanımlama için isteğe bağlı düğüm adı
- **Olay Türü**: Özel olay adı/türü (örneğin, "machine-fault", "production-complete", "maintenance-required")

**Giriş**: `msg.payload` olay verilerini içerir (herhangi bir format: metin, sayı, nesne, dizi)
**Çıkış**: `msg.payload` değişmeden (geçiş yapar)

**Olay Türleri ve Kullanım**:

**Sistem Olayları** (FUXA'nın yerleşik olayları):
- `device-status` - Cihaz bağlantı durumu değişiklikleri
- `device-property` - Cihaz özellik güncellemeleri
- `device-values` - Etiket değer değişiklikleri
- `alarms-status` - Alarm durumu değişiklikleri
- `script-console` - Betik konsolu çıktısı
- `heartbeat` - Sistem atışı/aktif sinyalleri

**Özel Olaylar** (kullanıcı tanımlı):
- `production-start` - Üretim hattı başlatıldı
- `production-stop` - Üretim hattı durduruldu
- `quality-alert` - Kalite kontrol uyarısı
- `maintenance-due` - Ekipman bakım gerekiyor
- `operator-login` - Operatör kimlik doğrulaması
- `batch-complete` - Üretim partisi tamamlandı

**Olay Veri Örnekleri**:

**Basit metin olayı**:
```json
{
  "payload": "Makine arızası tespit edildi"
}
```

**Yapılandırılmış olay verisi**:
```json
{
  "payload": {
    "eventType": "production-alert",
    "machine": "Line-A",
    "severity": "high",
    "timestamp": "2025-01-01T10:30:00Z",
    "details": {
      "temperature": 85.5,
      "pressure": 2.1,
      "errorCode": "E-202"
    }
  }
}
```

**Dizi verili olay**:
```json
{
  "payload": [
    {"sensor": "temp1", "value": 75.2},
    {"sensor": "temp2", "value": 76.8},
    {"sensor": "temp3", "value": 74.9}
  ]
}
```

**Olayların Nasıl Çalıştığı**:
1. Olaylar FUXA'nın dahili olay sistemine gönderilir
2. Diğer FUXA bileşenleri bu olayları dinleyebilir
3. Olaylar betikleri tetikleyebilir, ekranları güncelleyebilir veya otomasyon dizilerini başlatabilir
4. Olaylar tüketilene veya sistem yeniden başlatılana kadar kalıcıdır

**Yaygın Kullanım Senaryoları**:
- **Makine İzleme**: Ekipman arıza durumuna girildiğinde olaylar yayınlamak
- **Üretim Takibi**: Üretim kilomet taşlarını veya kalite sorunlarını sinyallemek
- **Bakım Uyarıları**: Ekipmanın bakım gerektiğinde bildirmek
- **Operatör Bildirimleri**: Operatörleri sistem koşulları hakkında uyarmak
- **Veri Entegrasyonu**: Yapılandırılmış verileri dış sistemlere göndermek

**Betiklerle Entegrasyon**:
Olaylar, FUXA betikleri tarafından olay sistemi kullanılarak tüketilebilir. Betikler belirli olay türlerini dinleyebilir ve otomatik yanıtlar çalıştırabilir.

#### send-message
**Amaç**: FUXA'nın bildirim sistemi üzerinden e-posta bildirimleri veya mesajları göndermek

**Parametreler**:
- **Name**: Tanımlama için isteğe bağlı düğüm adı
- **Address**: Alıcı e-posta adresi
- **Subject**: E-posta konu satırı
- **Message**: Varsayılan mesaj içeriği (geçersiz kılınabilir)

**Giriş**: `msg.payload` mesaj verisi içerebilir veya `msg.address`, `msg.subject`, `msg.message` ile geçersiz kılınabilir
**Çıkış**: `msg.payload` gönderme sonucunu içerir

**Mesaj Formatı Seçenekleri**:

**Basit metin mesajı** (yapılandırılan adres/konu kullanır):
```json
{
  "payload": "Uyarı: Sıcaklık eşiği aşıldı"
}
```

**Tam mesaj geçersiz kılma**:
```json
{
  "address": "operator@sirket.com",
  "subject": "Üretim Uyarısı",
  "message": "A hattı, bakım gereksinimi nedeniyle durdu"
}
```

**Birden çok alıcı**:
```json
{
  "address": ["operator1@sirket.com", "yonetic@sirket.com"],
  "subject": "Kalite Kontrol Uyarısı",
  "message": "#1234 numaralı parti kalite kontrolünden geçemedi"
}
```

## Veri Formatları

### Etiket Değerleri
Etiket değerleri şunlar olabilir:
- **Sayılar**: `25.3`, `100`, `0`
- **Dizeler**: `"Çalışıyor"`, `"Durmuş"`
- **Boole değerleri**: `true`, `false`
- **Nesneler**: Karmaşık veri yapıları

### Zaman Damgaları
Tüm zaman damgaları ISO 8601 formatını kullanır:
```json
"2025-01-01T10:30:00.000Z"
```

### Hata İşleme
Tüm düğümler hataları sekme sekmesine yazar. Yaygın hata desenleri:
```json
{
  "payload": null,
  "_msgid": "abc123",
  "error": "Etiket bulunamadı: myTag"
}
```

## Sorun Giderme

### Pan Yüklenmiyor
**Sorun**: `/dashboard` hata gösteriyor veya boş sayfa

**Çözüm**: Dashboard 2'yi kurun
1. Node-RED editörünü açın
2. **Yönetici Paleti**'ne gidin
3. `@flowfuse/node-red-dashboard` kurun
4. Node-RED/FUXA'yı yeniden başlatın

### FUXA Düğümleri Görünmüyor
**Sorun**: FUXA contrib düğümleri palet görünür değil

**Çözüm**: Node-RED günlüklerinde hataları kontrol edin. Düğümler FUXA Node-RED'i başlattığında otomatik olarak kaydedilir.

### Açılır Listeler Doldurulmuyor
**Sorun**: Tag/cihaz açılır listeleri boş

**Çözüm**: FUXA'da cihaz ve tag yapılandırıldığından emin olun. Açılır listeler FUXA proje verilerinden doldurulur.

### Olaylar Çalışmıyor
**Sorun**: Yayılan olaylar beklenen davranışı tetiklemiyor

**Çözüm**: Diğer FUXA bileşenlerinin (script'ler, görünümler) doğru olay türlerini dinlediğinden emin olun. Olaylar büyük/küçük harfe duyarlıdır.

### E-posta Bildirimleri Gönderilmiyor
**Sorun**: send-message düğümü e-postaları teslim etmiyor

**Çözüm**: FUXA sunucu yapılandırmasında SMTP ayarlarını yapılandırın. SMTP bağlantı hataları için FUXA günlüklerini kontrol edin.
