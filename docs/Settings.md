Sunucu ayarları 'server\\_appdata\settings.js' dosyasında bulunur. Değişiklikten sonra sunucuyu yeniden başlatmanız gerekir.

### Kimlik Doğrulama
Kimlik doğrulamayı etkinleştirmek ve yapılandırmak için:

```
secureEnabled: true,            // etkinleştir veya devre dışı bırak
secureOnlyEditor: false,        // doğruysa, yalnızca Editör ve yönetici sayfaları için oturum açma gerekir, değerler herkes tarafından değiştirilebilir
secretCode: '<strong-random-secret>', // belirteçleri kodlamak için gizli kod
tokenExpiresIn: '1h'            // belirteç sona erme gecikmesi '1h'=1 saat, 60=60 saniye, '1d'=1 gün
```

Varsayılan kullanıcı 'admin' şifresi '123456'dır, elbette bunu değiştirebilirsiniz.