# FUXA AR Modu

## Hedef
Tabletler için tarayıcı tabanlı AR modu.
Kamera, fiziksel tesis işaretlerini algılar ve işaret kimliklerine bağlı canlı FUXA kartlarını/ gösterge panelini gösterir.

## MVP Kararı
İlk geçiş, kamera akışı ve hafif canlı etiket kartları içeren özel bir `/ar` rotası kullanır.
Tam SVG Görünüm işleme yok.
Arka uç değişiklikleri yok.
Yerel uygulama yok.

## Önerilen MVP Yığını
- Angular tam ekran rotası
- Tarayıcı `getUserMedia`
- İlk QR/ barkod prototipi için tarayıcı `BarcodeDetector`
- FUXA `hmiService.viewsTagsSubscribe(tags, true)`
- FUXA `hmiService.onVariableChanged`

## Sonraki AR Takibi
BarcodeDetector'ı şunlarla değiştir/genişlet:
- Gördüğünüzü izlemek için MindAR
- veya işaret takibi için AR.js

## Dosyalar
- `client/src/app/_models/ar.ts`
- `client/src/app/_models/project.ts`
- `client/src/app/ar/ar.component.ts`
- `client/src/app/ar/ar.component.html`
- `client/src/app/ar/ar.component.scss`
- `client/src/app/app.routing.ts`
- `client/src/app/app.module.ts`
- `client/src/app/app.component.ts`

## kapsam MVP
- `/ar` rotası
- tam ekran kamera
- işaret algılama
- markerId → etiket kartı
- canlı değer güncellemeleri
- henüz düzenleyici UI yok

## Açık Sorular
- İşaret formatı
- Operatör yetkilendirme davranışı
- Çoklu işaret davranışı
- Safari/iPad desteği
- MVP sonrası MindAR entegrasyonu