ODBC ile başlamak için, ODBC sürücüleri önceden kurulu ve kullanıma hazır olduğu için Docker sürümünü kullanmanız önerilir.

**ODBC Sürücülerini Yükleme**

Kullanılabilecek mevcut sürücüler ve tanımlanmış adları burada bulunabilir: [ODBC Driver ini](https://github.com/frangoteam/FUXA/blob/master/odbc/odbcinst.ini)

Debian tabanlı bir Linux sistemine NPM ile kurulum yapıyorsanız, aşağıdaki adımlarla sistem için ODBC sürücülerini manuel olarak yüklemeyi deneyebilirsiniz:


**1:** `sudo apt-get update && sudo apt-get install -y unixodbc unixodbc-dev` 

**2:** Buradan her iki dosyayı da sisteminize kopyalayın [ODBC Driver Files](https://github.com/frangoteam/FUXA/tree/master/odbc)

**3:** İndirilen dosyaların dizinine gidin ve betiği çalıştırılabilir hale getirin ve odbcinst.ini dosyasını yükleyin
```
sudo chmod +x install_odbc_drivers.sh
sudo ./install_odbc_drivers.sh
sudo cp odbcinst.ini /etc/odbcinst.ini
```
**4:** Yüklenen sürücüleri test edin ve unixODBC kullanarak bağlanabilirsiniz, bağlantı dizesini DB'nize uygun şekilde değiştirin

`sudo myodbc-installer -s -a -c2 -n "test" -t "DRIVER=MySQL;SERVER=YourIP;PORT=3306;DATABASE=testDB;UID=User;PWD=MyPass"`

`sudo isql test`

**ODBC Nasıl Kullanılır**

Öncelikle Fuxa'da bir Device (Cihaz) ekleyerek ve ODBC'yi seçerek DB'nize bir bağlantı oluşturmanız gerekir

Tanımlanmış bir DSN kullanıyorsanız bunu girebilir veya aşağıda görüldüğü gibi tam bağlantı dizesini aynı alana ekleyebilirsiniz

![image](https://github.com/user-attachments/assets/aacaae07-eb2f-4e78-878e-eaa9fa66bb43)

Farklı DB'ler için bazı bağlantı dizeleri

`DRIVER=PostgreSQL;SERVER=Your_DB_IP;PORT=5432;DATABASE=testDB`

`DRIVER=MySQL;SERVER=Your_DB_IP;PORT=3306;DATABASE=testDB;SSLMODE=DISABLED`

MySQL, SSLMODE nedeniyle bağlantı sorunları yaratabilir, bu yüzden lütfen bağlantı dizesinde SSLMODE olmadan deneyin ve etkin/devre dışı bırakılmış olarak deneyin.
`SSLMODE=ENABLE`
`SSLMODE=DISABLED`

testDB'yi gerçek DB adınızla değiştirin

Test etmek için bir Server Side Script (Sunucu Taraflı Betik) oluşturun, sonucu görüntülemek için test sekmesini ve konsolunu kullanabilirsiniz

Öncelikle cihazlardan ODBC bağlantısını almanız gerekir, örnekte yukarıdaki resimde görüldüğü gibi adı postgreSQL olarak adlandırılmıştır
```
// Cihazı başlat, bağlantıyla aynı ad
let myDevice = await $getDevice('postgreSQL', true);
```
DB'den veri okuyun
```
let result = await myDevice.pool.query(`SELECT * FROM "testTable"`);
console.log(JSON.stringify(result));
```

**Önemli Notlar:** 

Veritabanınıza bağlı olarak tırnak işaretlerini kaldırmanız gerekebilir `"testTable"` -> `testTable` veya şemayı ekleyin `"DB_Schema"."testTable"`

Başka bir betikte ODBC kullanmanız gerekiyorsa, aynı bağlantı adını kullanamazsınız: 

Betiği 1 `let myDevice1 = await $getDevice('postgreSQL', true);`

Betiği 2 `let myDevice2 = await $getDevice('postgreSQL', true);`

**Tam Örnek**

Bu örnek ayrıca Tag'ları her 100ms'de bir sorgular ve SQL Sorgusunu yürütmek için bir tetikleyici kullanır, lütfen bu yöntemi kullanırken sisteminizi aşırı yüklemeye dikkat edin, gelecekte ideal çözüm Tag'lar için bir olay dinleyici oluşturmaktır ve basit bir addEventListener yöntemi kullanabiliriz, polling yapmamıza gerek kalmaz

Ayrıca DB'den değerleri alıp bunları Fuxa UI'sindeki Tablo'ya iten 1sn'lik bir döngü de vardır 

Query Manager adında bir fonksiyon var ve bu tetikleyiciyi ve yürütmenin tek seferlik işlemini yönetir

Not: Bunun doğru çalışması için FuxA'yı yeniden başlatmanız gerekebilir ve betiği her değiştirdiğinizde bazen betiği değiştirmek yeterli olurken bazen yeniden başlatma gerektirir, bu yüzden tuhaf bir sorunla karşılaşırsanız bir sorun oluşturmadan önce yeniden başlatmayı deneyin

Bir Server Side Script oluşturun ve başlangıçta çalışacak olarak ayarlayın
```
// Query Manager, tetikleyici olayına dayalı tek seferlik fonksiyon sağlamak için
async function createQueryManager(device) {
    let lastTriggerState = false;
    return async function(trigger, sqlQuery) {
        if (trigger && trigger !== lastTriggerState) {
            try {
                const result = await device.pool.query(sqlQuery);
                lastTriggerState = trigger;
                return result;
            } catch (error) {
                return 'Error executing query';
            }
        }
        lastTriggerState = trigger;
    };
}

// Cihazı başlat, bağlantıyla aynı ad
let myDevice = await $getDevice('postgreSQL', true);

// Her sorgu türü için Query Manager oluştur, sorgu verilerini tutar
let executeInsertQuery = await createQueryManager(myDevice); // Insert Örneği
let executeSelectQuery = await createQueryManager(myDevice); // Select Örneği

// Verileri tutmak için Global Değişkenler
let selResult;

// 100ms döngüsü Tag Olaylarını yakalar
let myLoop100ms = setInterval(loop100ms, 100);

async function loop100ms() {
  
    let customerName     = $getTag($getTagId('customerName'));
    let customerPhone    = $getTag($getTagId('customerPhone'));
    let customerEmail    = $getTag($getTagId('customerEmail'));
    let customerAge   	 = $getTag($getTagId('customerAge'));
    let execSaveCustomer = $getTag($getTagId('execSaveCustomer'));
  
	// Query Manager Örneği Fonksiyonunu Her 100ms'de Bir Çağır, ilk parametre sorguyu bir kez yürütmek için tetikleyicidir
    await executeInsertQuery(
        execSaveCustomer,
        `INSERT INTO "testData"."Customer" ("Name", "Phone", "Email", "Age") VALUES ('${customerName}', '${customerPhone}', '${customerEmail}', ${customerAge})`
    );
  
    // Aynı fonksiyon türünü çağıran ikinci örneği test etme
    //selResult = await executeSelectQuery(
    //    execSaveCustomer,
    //    'SELECT * FROM "testData"."Customer"'
    //);
	//
    //$setTag($getTagId('customerDataArray'), JSON.stringify(selResult));
}

// 1sn döngüsü DB'den verileri günceller
let myLoop1sec = setInterval(loop1sec, 1000);

async function loop1sec() {
  // Tablo için Verileri Güncellemek İçin Her 1 Saniyede Bir DB'den Okuduğumuz için Query Manager'a Burada İhtiyacımız Yok
  selResult = await myDevice.pool.query('SELECT * FROM "testData"."Customer"');
  $setTag($getTagId('customerDataArray'), JSON.stringify(selResult));
}
```

1sn aralığında bir Client Side Script (İstemci Taraflı Betik) oluşturun, bu betik verileri Tablo'ya ekleyecek

```
let customerData = JSON.parse($getTag($getTagId('customerDataArray'  ,)));

// Sütun ID'leri DB sütunlarıyla eşleşmeli
var tableData = {
  columns: [{
    id: 'Name',
    label: 'Name'
    }, {
    id: 'Phone',    
    label: 'Phone'
    }, {
    id: 'Email',    
    label: 'Email'
    }, {
    id: 'Age',    
    label: 'Age'
  }],
  rows: customerData
};

// FuxA'da kullanılan Tablo adı
$invokeObject('customerTable', 'setTableAndData', tableData);
```