# WebSocket, Node-Red gibi diğer web uygulamalarıyla iletişim kurmak için harika ve basit bir yöntemdir. WebSocket'i doğrudan Fuxa Script'lerinde kullanabiliriz.

İşte çift yönlü veri aktarımının bir örneği, tüm Fuxa etiketlerini ve değerlerini bir Timestamp ve Payload içeren bir JSON Data objesine dönüştürüyoruz. Tüm etiket değerlerini almak ve Payload'a eklemek için bir for döngüsü kullanıyoruz, ayrıca WebSocket'ten gelen etiket değerlerini yazmak için de bir for döngüsü kullanıyoruz.

Script'in sunucu tarafında olması ve Başlangıç (on Startup) olarak ayarlanması gerekir. Aşağıdaki kod bir WebSocket İstemcisi olduğu için, çalışmakta olan bir WebSocket Sunucusuna sahip olmanız gerekmektedir.

İhtiyaç durumunda Fuxa Script'inde bir WebSocket Sunucusu da oluşturmak mümkündür. Aşağıdaki kod yalnızca sunucuya istemci bağlantısını kapsamaktadır.

```
const WebSocket = require('/usr/src/app/FUXA/server/node_modules/ws');
let ws;  // Bağlantıyı yönetmek için ws Web Socket'i global olarak tanımla

const WebSocketUrl = 'ws://127.0.0.1:1880'; // WebSocket Sunucusu URL'si

// PLC/FUXA Etiketleri Listesi
const tagNames = [
    'yourTagName1', 
    'yourTagName2', 
    'yourTagName3', 
    'yourTagName4', 
    'yourTagName5'
];

// Etiket değerlerini almak ve JSON payload oluşturmak için fonksiyon
async function createPayload() {
    let payload = {};

    for (const tagName of tagNames) {
        let tag = await $getTag($getTagId(tagName));
        payload[tagName] = tag;
    }

    return {
        data: {
            timestamp: new Date().toISOString(),
            payload: payload
        }
    };
}

// Veriyi WebSocket sunucusuna göndermek için fonksiyon
function sendData() {
    createPayload().then((payload) => {
        if (ws && ws.readyState === WebSocket.OPEN) {
            ws.send(JSON.stringify(payload));
        } else {
            //console.log('WebSocket açık değil. Veri gönderme atlanıyor.');
        }
    });
}

// WebSocket bağlantısını aç
function openWebSocketConnection() {
    ws = new WebSocket(WebSocketUrl);

    ws.on('open', () => {
        console.log('WebSocket bağlantısı kuruldu');
    });

    ws.on('message', (message) => {
        try {
            let receivedData = JSON.parse(message);
            if (receivedData.data && receivedData.data.payload) {
                for (const tagName in receivedData.data.payload) {
                    if (tagNames.includes(tagName)) {
                        $setTag($getTagId(tagName), receivedData.data.payload[tagName]);
                    }
                }
            }
        } catch (error) {
            console.error('WebSocket sunucusundan mesaj ayrıştırma hatası:', error);
        }
    });

    ws.on('error', (error) => {
        console.error('WebSocket hatası:', error);
    });

    ws.on('close', () => {
        console.log('WebSocket bağlantısı kapatıldı');
        // Bağlantı kapatılırsa yeniden bağlanmayı dene
        setTimeout(openWebSocketConnection, 5000); 
    });
}

// WebSocket bağlantısını başlat
openWebSocketConnection();

// Veriyi her 500ms'de bir göndermek için aralık ayarla
if (typeof globalThis.myTimer === 'undefined') globalThis.myTimer = null;

if (!globalThis.myTimer) globalThis.myTimer = setInterval(myTimerFunction, 500);

async function myTimerFunction() {
  sendData();
}
```