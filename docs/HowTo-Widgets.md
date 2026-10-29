**Fuxa Widget'ları**

Fuxa widget'ları, script etiketlerini kullanarak saf SVG'ler ve Javascript ile oluşturulur
```
<svg>
  Buraya SVG İçeriği
  <script>
     Buraya JS Script İçeriği
  </script>
</svg>
```
Basit fonksiyonlar kullanarak ve değişkenleri global olarak tanımlayarak SVG scriptleri arasında veri aktarabiliriz
```
_pb_ = bool   parametresi ( true veya false )
_pn_ = sayı parametresi ( Int, Float, Real vb )
_ps_ = string parametresi ( string doğrudan girilebilir )
_pc_ = hex renk koduyla renk parametresi ( #00ff00ff )
```
Ayrıca, değişkenlerin `//!export-start` ve `//!export-end` yorumları içinde olması da çok önemlidir
```
//!export-start
let _pn_value = 50;
//!export-end
```
Değişkenler artık Fuxa'da SVG özellikler panelinde kullanılabilir olacak ve buradan Fuxa Tag'leri bağlayabileceksiniz

Widget/SVG arasında veri aktarmak için 2 adet fonksiyon kullanmamız gerekiyor:

SVG'den Fuxa'ya değer göndermek için
```
function postValue(id, value) {
  console.error('Tanımlanmadı!');
}
```
Bu fonksiyonu çağırmanız gerekiyor, ID'nin tam değişken adıyla eşleşmesi gerekir
```
postValue('_pn_value', someNewValue);
```

Fuxa'dan SVG'ye değer almak için
```
function putValue(id, value) {
  if (id === '_pn_value') {
    callFunction(value);
    newVar = value;
  }
}
```
Burada Fuxa'da fonksiyonun çağrılmasını bekliyoruz ve tam değişken adıyla tanımladığımız ID'yi kontrol ediyoruz

`setInterval` kullanıyorsanız sayfayı değiştirdiğinizde veya editöre girdiğinizde aralığı temizlemeniz de gerekir, bunu yapmazsanız widget başka bir `setInterval` çağrısı nedeniyle bozulur. Örneklerimizde nesnenin var olup olmadığını kontrol ediyoruz ve MutationObserver kullanıyoruz
```
const checkDestroy = document.getElementById('svgIdName'); // Önemli: SVG'nin adı olmalı!
if (!checkDestroy) {
  clearBlinking(); 
  return;
}
```
Ve Mutation Observer 
```
// SVG elementinin kaldırılmasını izlemek için MutationObserver'ı ayarlayın
const observer = new MutationObserver((mutations) => {
  mutations.forEach((mutation) => {
    mutation.removedNodes.forEach((node) => {
      if (node.id === 'svgIdName') { // Önemli: SVG'nin adı olmalı!
        clearBlinking(); 
        observer.disconnect(); 
      }
    });
  });
});
// SVG'nin body veya üst elementini gözlemeye başlayın
observer.observe(document.body, { childList: true, subtree: true }); 
```

SVG dosyasının en üstünde SVG Id'yi kullanmanız önemlidir: `<svg id=svgIdName>`

SVG'de CSS kullanabilir, tam JS kullanabilir ve standart `getElementById` ve `addEventListener` kullanarak elementlere/ID'lere erişebilirsiniz

Tam çalışan örnekler için widget bölümündeki örneklere göz atın https://github.com/frangoteam/FUXA/tree/master/server/_widgets