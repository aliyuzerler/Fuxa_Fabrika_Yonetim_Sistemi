# FUXA'ya Katkıda Bulunmak

FUXA'ya katkıda bulunma ilginiz için teşekkür ederiz!

Hem kod tabanına hem de belgelere yönelik iyileştirmeleri memnuniyetle karşılıyoruz.

---

# 💻 Kod Katkıları

## Gereksinimler

- Node.js 18 LTS (önerilir)
- npm

## Geliştirme Ortamını Kurma

```bash
git clone https://github.com/<your-username>/FUXA.git
cd FUXA
```

### Backend
```bash
cd server
npm install
npm start
```

### Frontend
```bash
cd client
npm install
npm start
```

### Kodlama standartları
Lütfen mevcut kod tabanında kullanılan kodlama standartlarını izlediğinizden emin olun. Bazı temel kurallar şunlardır:
- Girintiyi 4 boşlukla yapın, sekme kullanmayın.
- Açma parantezi if/for/function gibi ifadelerle aynı satırda, kapatma parantezi ise kendi satırında olsun.

## 🚫 Derlenmiş Dosyaları YÜKLEMEYİN

Lütfen Pull Request'lere oluşturulmuş veya derlenmiş dosyaları dahil etmeyin.

Özellikle:

- Frontend üretim derlemelerini (`/client/dist`) yüklemeyin
- Oluşturulmuş Angular derleme çıktılarını yüklemeyin
- Derlenmiş veya geçici dosyaları yüklemeyin
- node_modules'ı yüklemeyin

Pull Request'ler yalnızca kaynak kodu ve ilgili değişiklikler içermelidir.

---

# 📚 Belgelere Katkıda Bulunma

Belge kaynak dosyaları şurada bulunur:

```markdown
/docs
```
Belge sitesi MkDocs ile Material teması kullanılarak oluşturulmuştur.

## Belgeleri Yerel Olarak Çalıştırma

Bağımlılıkları kurun:

```bash
pip install "mkdocs<2.0"
pip install mkdocs-material
```

Yerel sunucuyu başlatın:

```bash
mkdocs serve
```

Açın:
```markdown
http://127.0.0.1:8000/FUXA/
```

# 🔁 Pull Request İş Akışı
1. Depoyu forklayın
2. Bir dal oluşturun (git checkout -b feature/my-feature)
3. Değişikliklerinizi yapın
4. Açıklayıcı bir commit mesajı yazın (git commit -m "X için belgeleri iyileştir")
5. Dalınızı itin
6. Bir Pull Request açın

Büyük değişiklikler için, lütfen öneriyi tartışmak için önce bir issue açın.

---

# ✍ Yazım Kuralları (Belgeler)
- Açık ve teknik bir dil kullanın
- Bölümleri iyi yapılandırılmış tutun
- Görseller için göreli yollar kullanın (örneğin images/example.png)
- Eski Wiki sayfalarına link vermekten kaçının

Onaylandıktan sonra, belge değişiklikleri GitHub Actions aracılığıyla otomatik olarak dağıtılır.