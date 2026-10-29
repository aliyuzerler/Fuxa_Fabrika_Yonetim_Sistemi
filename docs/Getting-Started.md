## FUXA ile başlangıç yapma

Bu öğreticinin amacı, FUXA'nın en popüler özelliklerinin temel kullanımını göstermektir. Şu nasıl yapılacağını öğreneceksiniz:
- Cihazları FUXA'ya bağlamak
- Verileri cihazlardan FUXA'ya göndermek
- Gerçek zamanlı bir GUI SCADA/HMI/Dashboard oluşturmak
- Eşikleri tanımlamak ve alarm tetiklemek

## Ön gereksinimler

FUXA sunucusunun çalışır durumda olması gerekecek. FUXA'yı [**Kurulum Kılavuzu**](https://github.com/frangoteam/FUXA/wiki/Installing-and-Running) kullanarak kurabilirsiniz.
FUXA kullanıcı arayüzü şu URL ile kullanılabilir olacaktır: http://localhost:1881.

Temel olarak FUXA web sunucusu iki sayfa gerçekleştirir:
- Son kullanıcı için görselleştirme http://localhost:1881/home
- Proje oluşturmak ve tasarlamak için Editör http://localhost:1881/editor

## Adım 1. Cihazı bağlama ve etiketleri yapılandırma
Cihazı bağlamak için önce cihaz kimlik bilgilerini almanız gerekir. FUXA çeşitli cihazları destekler: Modbus RTU/TCP, Siemens S7 Protokolü, OPC-UA, BACnet IP, MQTT, Ethernet/IP (Allen Bradley). Bir cihaza bağlandıktan sonra, canlı değerlerin, Etiketlerin, Sensör vb. aboneliklerini yapılandırabilirsiniz.
Yapılandırma kılavuzunu buradan bulabilirsiniz [**Cihazlar ve Etiketler Nasıl Yapılır**](https://github.com/frangoteam/FUXA/wiki/HowTo-Devices-and-Tags)

## Adım 2. Görselleştirme oluşturma
Bir Görünüm oluşturacak ve en popüler widget'ları ekleyeceğiz. Aşağıdaki talimatları görün.

   ### Adım 2.1 Boş Görünüm Oluşturma
   [**Görünüm Nasıl Oluşturulur**](https://github.com/frangoteam/FUXA/wiki/HowTo-View)
   ### Adım 2.2 Denetimleri Görünümle Bağlama
   [**Denetimler Nasıl Bağlanır**](https://github.com/frangoteam/FUXA/wiki/HowTo-bind-Controls)
   ### Adım 2.3 Şekilleri Görünümle Bağlama
   [**Şekiller Nasıl Bağlanır**](https://github.com/frangoteam/FUXA/wiki/HowTo-bind-Shapes)
   ### Adım 2.4 Grafik Denetimini Görünümle Bağlama
   [**Grafik Denetimi Nasıl Bağlanır**](https://github.com/frangoteam/FUXA/wiki/HowTo-Chart-Control)

## Adım 3. UI Yerleşimini Yapılandırma
[**UI Yerleşimi Nasıl Yapılır**](https://github.com/frangoteam/FUXA/wiki/HowTo-UI-Layout)

## Adım 4. Alarmları Yapılandırma
[**Alarmlar Nasıl Ayarlanır**](https://github.com/frangoteam/FUXA/wiki/HowTo-setup-Alarms)

## Adım 5. Etkinleştirme ve müşteri kullanıcısı oluşturma

## İpuçları ve püf noktaları