# Güvenlik Politikası

## Güvenlik Açığı Bildirimi

FUXA'da bir güvenlik açığı keşfedersek, lütfen bunu özel olarak e-posta yoluyla bildirin:

dev@frangoteam.org

Lütfen **güvenlik açıkları için genel bir GitHub sorunu açmayın**.

Bildiriminizi mümkün olan en kısa sürede alındığını teyit edecek ve sorunu inceleyeceğiz.

Sorumlu açıklama, açıklamalar kamuya açıklanmadan önce güvenlik açıklarını düzeltmemize yardımcı olur.

---

## Kapsam

FUXA, sistem yöneticileri tarafından yönetilen **güvenilir ortamlarda** çalışmak üzere tasarlanmış endüstriyel bir SCADA/HMI platformudur.

Sunucu tarafı komut dosyası, otomasyon mantığı veya cihaz entegrasyonları gibi belirli özellikler, platformun **kasıtlı yetenekleridir** ve tasarılandığı gibi kullanıldığında bir güvenlik açığı olarak kabul edilmez.

Güvenlik raporları, **istemediğimiz erişim, ayrıcalık yükseltme, kimlik doğrulama atlama veya veri sızıntısı** gibi konulara odaklanmalıdır.

## Kapsam Dışı

Aşağıdaki yapılandırmalar, tasarım gereği güvensiz kabul edilir ve güvenlik açığı raporlarının kapsamı dışındadır:

- `secureEnabled=false` ile yapılan dağıtımlar
- Kimlik doğrulama ve yetkilendirme kasıtlı olarak devre dışı bırakılan dağıtımlar
- Yöneticinin güvenlik özelliklerini kasıtlı olarak devre dışı bırakmasını gerektiren senaryolar
- Güvenlik devre dışı bırakıldığında kasıtlı olarak ortaya çıkan işlevselliklere erişim gösteren raporlar

`secureEnabled=false` olduğunda, FUXA yalnızca güvenilir ortamlar için tasarlanmış, güvenli olmayan bir modda çalışır. Bu modda kimlik doğrulama veya yetkilendirmenin olmamasından kaynaklanan davranışlar bir güvenlik açığı olarak kabul edilmez.

---

## Desteklenen Sürümler

FUXA'nın **en son kararlı sürümü**, güvenlik güncellemelerini alan tek sürümdür.

Bir güvenlik açığı bildirmeden önce, sorunun en son sürümde hala var olduğunu doğrulayın.

---

## Güvenlik Güncellemeleri

Güvenlik düzeltmeleri, normal sürüm yayınlarının bir parçası olarak yayınlanır.

Uygun olduğunda, güvenlikle ilgili düzeltmeler, sürüm notlarında belgelenir.

---

FUXA'nın güvenliğini iyileştirmeye yardımcı olduğunuz için teşekkür ederiz.