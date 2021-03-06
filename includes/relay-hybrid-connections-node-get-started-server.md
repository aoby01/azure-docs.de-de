### <a name="create-a-nodejs-application"></a>Erstellen einer Node.js-Anwendung
* Erstellen Sie eine neue JavaScript-Datei namens `listener.js`.

### <a name="add-the-relay-npm-package"></a>Hinzufügen des Relay-NPM-Pakets
* Führen Sie `npm install hyco-ws` über eine Node-Eingabeaufforderung in Ihrem Projektordner aus.

### <a name="write-some-code-to-receive-messages"></a>Schreiben von Code zum Empfangen von Nachrichten
1. Fügen Sie die folgenden `constant` am Anfang der Datei `listener.js` hinzu:
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Fügen Sie der Datei `listener.js` die folgenden Relay-`constants` für die Hybridverbindungsdetails hinzu. Ersetzen Sie die Platzhalter in Klammern durch die entsprechenden Werte, die beim Erstellen der Hybridverbindung abgerufen wurden.
   
   1. `const ns` – der Relay-Namespace (Verwenden Sie den FQDN, z.B. `{namespace}.servicebus.windows.net`.)
   2. `const path` – der Name der Hybridverbindung
   3. `const keyrule` – der Name des SAS-Schlüssels
   4. `const key` – der Wert des SAS-Schlüssels
3. Fügen Sie der Datei `listener.js` den folgenden Code hinzu:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    „listener.js“ sollte nun wie folgt aussehen:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

