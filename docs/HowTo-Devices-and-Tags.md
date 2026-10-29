Cihazınızı ve Tag'larınızı eklemek için editörde **Bağlantılar** bölümüne gidin.

![](images/setup-connections.png)

Bir **OPCUA** cihazı ekleyin ve bağlayın.
![](images/fuxa-device.gif)

OPCUA Tag'ları eklemek için cihazın bağlı olması gerekir.
![](images/fuxa-opcuatag.gif)

Bir **Modbus** bağlantısı eklemek için **Eklentiler** bölümünde sürücüyü kurmanız gerekir.
![](images/fuxa-modbus.gif)

Bir **MQTT** bağlantısı ve bir konu aboneliği ekleyin
![](images/fuxa-mqtt.gif)

Bir **WebAPI** bağlantısı ve JSON sonucunun bir Tag'ı ekleyin
![](images/fuxa-webapi.gif)

**Tag Seçenekleri**

Tanımlanmış veri tipine sahip olmayan herhangi bir Tag tipiniz varsa bunu ayarlayabilirsiniz. Örneğin, TIME veri tipi tanımlanmış bir tip değildir, ancak temel veri tipi genellikle int64 veya uint64'tür ve zaman milisaniye cinsindendir.

![image](https://github.com/user-attachments/assets/6fbbd718-2ab0-4bac-85dd-8f0339540ca5)

Tag seçenekleri altında ayrıca ölçek betiğini kullanarak herhangi bir veri tipi dönüşümü vb. yapabilirsiniz. Örneğin, TIME'dan gelen veri LOW ve HIGH dizisidir, bu dizinin elemanını alıp sayı tipi olarak döndüren basit bir betik oluşturabiliriz. Gerekirse yazma için gerçek değeri geri döndürebiliriz.

![image](https://github.com/user-attachments/assets/3acb42db-bfee-4eed-a385-e8fe264e7ddd)

**Önemli!** Betik parametresi value olarak adlandırılmalıdır, çünkü seçim için ilgili betikleri gösteren bir filtre vardır. Bu betikte yorum satırları kullanmayın, çünkü desteklenmez.

İşte dizinin elemanını döndüren okuma betiği

![image](https://github.com/user-attachments/assets/60b5ce2b-435b-4b18-bc4f-eb73e9fc5bd0)

İşte gerçek Tag değerini döndüren yazma betiği

![image](https://github.com/user-attachments/assets/51cfad3f-9354-45e5-a0a5-206507d1ca93)