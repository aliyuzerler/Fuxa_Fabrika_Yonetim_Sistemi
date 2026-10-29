Şekiller klasörü şu konumlarda bulunur: 'client/dist/assets/lib/svgeditor/shapes' (veya hata ayıklıyorsanız 'client/src/lib/svgeditor/shapes'), 'my-shapes.js' dosyasından kopyalayarak yeni bir JavaScript dosyası oluşturarak veya mevcut bir dosyayı düzenleyerek yeni bir şekil ekleyebilirsiniz.

JavaScript dosyasında aşağıdaki içerikleri değiştirebilirsiniz:
```
var shapesGroupName = 'Shapes'; // şekilleri düzenlemek ve gruplamak için kullanılır, düzenleyici menüsünde genişletme/daraltma etiketi olarak görüntülenir

var typeId = 'shapes';  // şekil türünü tanımlamak için kullanılır, 'shapes' 'ShapesComponent' adlı angular bileşeniyle bağlantılıdır
                        // yeni bir tür oluşturursanız, angular bileşenini de uygulamalısınız

```

bu dizide şekillerinizin verileri, şekil nesnesi aşağıdaki özniteliklere sahiptir:
```
var shapes = [{ name: 'diamond', 
                ico: 'assets/lib/svgeditor/shapes/img/shape-diamond.svg', 
                content:[{ id: '', 
                            type: 'path', 
                            attr: { d: 'M 20 0 L 40 20 L 20 40 L 0 20 Z' }
                        }]
		},...]

// 'name': şekil türü için benzersizdir
// 'ico': düzenleyici menüsünde görüntülenen yol simgesi
// 'content': svg elemanlarının dizisi
// 'id': angular bileşeninde yönetilen bir animasyon oluşturmak isterseniz kullanılan eleman kimliği
// 'type': svg eleman türü (path, text, ellipse,...) svg açıklamasına bakın
// 'attr': eleman özniteliği, türe bağlı olarak

```

Şekillerimi tasarlamak için Inkscape uygulamasını kullandım. 'type' ve 'attr' içine eklemek için elemanları ve düğüm özniteliklerini XML Düzenleyici ile görebilirsiniz.

**!Dosyanın geri kalan içeriğini değiştirmemelisiniz**

Yeni bir JavaScript şekiller dosyası oluşturduysanız, yükleyiciye de eklemeniz gerekir, 
daha sonra 'shapesLoader.js' dosyasına dinamik olarak yüklenecek dosya adınızı ekleyin ('shapesLoader.js' dosyasını kaldırmayın)
```
var shapesToLoad = ['my-shapes.js', 'your shape file name.js'];

```

Eğer kendi animasyonlarınızı tanımlamak için yeni bir şekil türü oluşturduysanız, ilgili angular bileşenini de uygulamalısınız. 'client/src/app/gauges/shapes/' içindeki dosya içeriğine bakmak daha iyidir