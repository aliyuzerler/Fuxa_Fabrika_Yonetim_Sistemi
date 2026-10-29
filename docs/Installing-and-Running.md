FUXA, NodeJS (backend) ve Angular (frontend) ile geliştirilmiştir.

**Ön Derlenmiş Electron Uygulaması** (ilk seçenek)

Electron Action Build'leri indirme düğmesine erişmek için GitHub'da giriş yapmış olmanız gerekecektir,
workflow'a tıklayın ve aşağıya doğru kaydırarak "Artifacts" bölümüne gidin ve sisteminiz için indirme simgesine tıklayın

[Electron Action Build'leri](https://github.com/frangoteam/FUXA/actions/workflows/electron_latest.yml)

<img width="2082" height="531" alt="image" src="https://github.com/user-attachments/assets/cf0e5282-f1ef-48b3-a122-de2a2754cf19" />

**Başsız Taşınabilir İkili Dosyalar** (Electron alternatifi)

Gömülü cihazlarda, sunucularda veya GUI'si olmayan sistemlerde başsız dağıtımlar için FUXA, tüm uygulamayı (sunucu, istemci) tek bir yürütülebilir dosyada paketleyen kendi kendine yeten taşınabilir ikili dosyalar sunar.

Bu ikili dosyalar, kullanıcı arayüzüne ihtiyaç duyulmadığı endüstriyel ortamlar, IoT cihazları ve otomatik sistemler için idealdir.

### Başsız İkili Dosyaları İndirme

Başsız ikili dosyalar GitHub Actions artifacts olarak mevcuttur. İndirmek için GitHub'da giriş yapmış olmanız gerekir.

[Başsız Taşınabilir Build'ler](https://github.com/frangoteam/FUXA/actions/workflows/headless_packaging.yml)

En son workflow çalıştırmasına tıklayın, "Artifacts" bölümüne kadar aşağıya kaydırın ve platformunuz için uygun dosyayı indirin:

- **Linux**: `fuxa-headless-linux-x64` (ARM cihazlar için `fuxa-headless-linux-arm64`)
- **Windows**: `fuxa-headless-win-x64.exe`
- **macOS**: `fuxa-headless-macos-x64` (Apple Silicon için `fuxa-headless-macos-arm64`)

### Başsız İkili Dosyaları Çalıştırma

#### Linux
1. Linux ikili dosyasını indirin (örneğin, `fuxa-headless-linux-x64`).
2. Yürütülebilir yapın: `chmod +x fuxa-headless-linux-x64`
3. Çalıştırın: `./fuxa-headless-linux-x64`
4. Web tarayıcısı üzerinden `http://localhost:1881` adresinden erişin (veya cihazın IP'si:1881)

#### Windows
1. Windows yürütülebilir dosyasını indirin (örneğin, `fuxa-headless-win-x64.exe`).
2. `.exe` dosyasına çift tıklayarak çalıştırın.
3. Web tarayıcısı üzerinden `http://localhost:1881` adresinden erişin.

#### macOS
1. macOS ikili dosyasını indirin (örneğin, `fuxa-headless-macos-x64`).
2. Yürütülebilir yapın: `chmod +x fuxa-headless-macos-x64`
3. Çalıştırın: `./fuxa-headless-macos-x64`
4. Web tarayıcısı üzerinden `http://localhost:1881` adresinden erişin.

### Başsız Özellikler
- **Kendi kendine yeten**: Harici bağımlılıklar veya kurulumlar gerekmez.
- **Veri kalıcılığı**: Kullanıcı verileri ana makinedeki `~/.fuxa-headless-data` dizininde depolanır.
- **Çapraz platform**: Windows, macOS ve Linux'ta (ARM mimariler dahil) çalışır.
- **Endüstriyel protokoller**: Modbus, OPC-UA, MQTT, Siemens S7 ve daha fazlasının tam desteği.
- **Web arayüzü**: Tam FUXA düzenleyicisine ve panolarına herhangi bir web tarayıcısı üzerinden erişin.

### Sorun Giderme
- 1881 portunun kullanılabilir olduğundan ve güvenlik duvarları tarafından engellenmediğinden emin olun.
- Gömülü cihazlar için FUXA'nın Node.js çalışma zamanı kullandığından sistem kaynaklarını (RAM, CPU) kontrol edin.
- Günlükler, kullanıcı veri dizininde mevcuttur (`~/.fuxa-headless-data/logs`).

**Electron olmayan kurulum tamamlandıktan sonra Web tarayıcısı ile erişim**

Kurulum işlemlerinden birini tamamladıktan sonra, FUXA arayüzüne varsayılan 1881 portu ve web sunucusu IP adresi üzerinden localhost veya ana makine IP'si ile erişebilirsiniz, ilk olarak localhost:1881 veya ana makine IP'si:1881'i deneyin

**Docker Compose** (ikinci seçenek)

Docker'ı kurun
```
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh ./get-docker.sh
```
```
cd
mkdir docker
mkdir fuxa
cd docker/fuxa
sudo nano docker-compose.yml
```

Compose dosyasına şunu girin:
```
version: '3.5'

services:
    fuxa:
        image: frangoteam/fuxa:latest
        network_mode: "host"
        volumes:
            - ./fuxa_appdata:/usr/src/app/FUXA/server/_appdata
            - ./fuxa_db:/usr/src/app/FUXA/server/_db
            - ./fuxa_logs:/usr/src/app/FUXA/server/_logs
        environment:
            - TZ=America/New_York
        restart: always
```
Not: Bu Compose dosyası ana makine ağ modunu kullanır, daha fazla detay için [Docker Ağları](https://docs.docker.com/engine/network/) sayfasına bakın, ana makine modu Docker konteynerinin ana makine ile aynı ağda olmasını sağlar. PLC'ler/Veritabanları vb. doğrudan erişim için en iyi şekilde çalışır ancak bazı sınırlamaları vardır, sadece Linux tabanlı sistemlerde çalışır, Windows'ta çalıştırmak isterseniz bridge ağ modunu kullanmanız gerekir.

`network_mode: "bridge"`

Docker Compose'ı Başlatın

`sudo docker compose up -d`

**Docker** (üçüncü seçenek)
```
docker pull frangoteam/fuxa:latest
docker run -d -p 1881:1881 frangoteam/fuxa:latest
```
Uygulama verilerinin (proje), daq (tag geçmişi), günlükler ve kaynak resimlerinin kalıcı depolanması
```
docker run -d -p 1881:1881 -v fuxa_appdata:/usr/src/app/FUXA/server/_appdata -v fuxa_db:/usr/src/app/FUXA/server/_db -v fuxa_logs:/usr/src/app/FUXA/server/_logs -v fuxa_shapes:/usr/src/app/FUXA/client/assets/lib/svgeditor/shapes -v fuxa_images:/usr/src/app/FUXA/server/_images frangoteam/fuxa:latest
```
**Kaynaktan Özel Docker İmajı Oluşturma** (dördüncü seçenek)

Bu, en son master dalından derleyecektir (dalı değiştirmek için docker dosyasını düzenleyebilirsiniz)
```
cd
mkdir docker
mkdir fuxa
cd docker/fuxa
mkdir fuxa-build
cd fuxa-build
```
`wget https://raw.githubusercontent.com/frangoteam/FUXA/master/Dockerfile`

`sudo docker build -t fuxa-custom-image-name --no-cache .`

Derleme tamamlandığında, özel imajı şöyle kullanabilirsiniz:

```
version: '3.5'

services:
    fuxa:
        image: fuxa-custom-image-name
        network_mode: "host"
        volumes:
            - ./fuxa_appdata:/usr/src/app/FUXA/server/_appdata
            - ./fuxa_db:/usr/src/app/FUXA/server/_db
            - ./fuxa_logs:/usr/src/app/FUXA/server/_logs
        environment:
            - TZ=America/New_York
        restart: always
```

**Node ve NPM ile Kurulum**

[Node](https://nodejs.org/en/about/previous-releases) Sürüm 18'in kurulu olması gerekir.

**UYARI** Linux'ta nodejs Sürüm 18 ile kurulum zor olabilir. Siemens PLC'leri ile S7 üzerinden iletişim kurmayı düşünmüyorsanız, [NPM @frangoteam/fuxa-min](https://www.npmjs.com/package/@frangoteam/fuxa-min) üzerinden kurulum yapabilirsiniz.

**NPM** (beşinci seçenek)

[NPM](https://www.npmjs.com/package/@frangoteam/fuxa) üzerinden kurun
```
npm install -g --unsafe-perm @frangoteam/fuxa
fuxa
```

**En Son Sürüm** (beşinci seçenek)

[En son sürümü indirin](https://github.com/frangoteam/FUXA/releases) ve açın

**UYARI** Linux'ta nodejs Sürüm 18 ile kurulum zor olabilir. Siemens PLC'leri ile S7 üzerinden iletişim kurmayı düşünmüyorsanız, server/package.json dosyasından node-snap7 kütüphanesini kaldırabilirsiniz

```
cd ./server
npm install
npm start
```

**Bir tarayıcıyı (Chrome daha iyi) açın ve http://localhost:1881 adresine gidin**

**HMI olarak kurulum** (Sadece Linux)

Bu sadece linux üzerinde test edilmiştir, ancak bazı adımlar Windows'ta da çalışabilir

Web tarayıcısı yerine electron kullanacağız, çünkü şişman değildir ve Dokunmatik Ekranlarla iyi çalışır (sağ tık menüsünü devredebilir vb.)

Uygulama zaten çalışan FUXA Web Sunucusuna erişiyor

`sudo apt-get update`

`curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -`

`sudo apt-get install -y nodejs`


Node.js ve npm sürümlerini kontrol ederek kurulumu doğrulayın:

`node -v`

`npm -v`

`sudo npm install -g electron --unsafe-perm=true --allow-root`

```
cd /opt 
sudo mkdir electron
cd electron
sudo mkdir fuxa-electron
cd fuxa-electron
```

`sudo npm init -y`

`sudo npm install electron --save-dev`

`sudo nano main.js`

```
const { app, BrowserWindow } = require('electron');

function createWindow() {
  const win = new BrowserWindow({
    //width: 800,
    //height: 600,
    fullscreen: true, // Tam ekran modunu etkinleştir
    webPreferences: {
      nodeIntegration: false,
      contextIsolation: true,
    }
  });

  win.loadURL('http://localhost:1881'); // Web sunucu URL'si ile değiştirin
}

app.whenReady().then(createWindow);
```

`sudo nano package.json`

```
{
  "name": "fuxa-electron",
  "version": "1.0.0",
  "main": "main.js",
  "scripts": {
    "start": "electron ."
  },
  "description": "",
  "devDependencies": {
    "electron": "^31.3.1"
  }
}
```

Test etmek için dizin içinde uygulamayı başlatın

`npm start`

Sistem servisi ve bir komut dosyası kullanarak otomatik başlangıcı ayarlayın

`sudo nano /opt/fuxa-electron-startup.sh`

```
#!/bin/bash
cd /opt/electron/fuxa-electron
npm start
```

`sudo chmod +x /opt/fuxa-electron-startup.sh`

`systemctl edit --user --force --full fuxa-electron-startup.service`

```
[Unit]
Description=Start Fuxa Electron Script
After=default.target
[Service]
ExecStart=/opt/fuxa-electron-startup.sh
[Install]
WantedBy=default.target
```

`systemctl enable --user fuxa-electron-startup.service` 

**Ters Proxy altında çalışırken**

Ters proxy altında çalışırken, sondan önce eğik çizgi içeren özel bir BASE_PATH belirtebilirsiniz

> Örneğin `foo.bar/fuxa` adresinde sunmak için ortam değişkeni olarak BASE_PATH=/fuxa olarak ayarlayabilirsiniz