<h2>A Simple Websocket Server written in Python</h2>

- RFC 6455 (All latest browsers)
- TLS/SSL out of the box
- Passes Autobahn Websocket Testsuite

<h4>Echo Server Example</h4>
`````python
class SimpleEcho(WebSocket):
    def handleMessage(self):
        # echo message back to client
        self.sendMessage(self.data)
    
    def handleConnected(self):
        print self.address, 'connected'
          
    def handleClose(self):
        print self.address, 'closed'

server = SimpleWebSocketServer('', 8000, SimpleEcho)
server.serveforever()
`````

Open <i>websocket.html</i> and connect to the server.

<h4>Chat Server Example</h4>
`````python
clients = []
class SimpleChat(WebSocket):
    def handleMessage(self):
       for client in clients:
          if client != self:
             client.sendMessage(self.address[0] + ' - ' + self.data)

    def handleConnected(self):
       print self.address, 'connected'
       for client in clients:
          client.sendMessage(self.address[0] + u' - connected')
       clients.append(self)

    def handleClose(self):
       clients.remove(self)
       print self.address, 'closed'
       for client in clients:
          client.sendMessage(self.address[0] + u' - disconnected')

server = SimpleWebSocketServer('', 8000, SimpleChat)
server.serveforever()
`````
Open multiple <i>websocket.html</i> and connect to the server.

<h4>Want to get up and running faster?</h4>

There is an example which provides a simple echo and chat server

Echo Server

    python SimpleExampleServer.py --example echo

Chat Server (open up multiple <i>websocket.html</i> files)
    
    python SimpleExampleServer.py --example chat


<h4>TLS/SSL Example</h4>

1) Generate a certificate with key

    openssl req -new -x509 -days 365 -nodes -out cert.pem -keyout cert.pem
    
2) Run the secure TSL/SSL server (in this case the cert.pem file is in the same directory)

    python SimpleExampleServer.py --example chat --ssl 1 --cert ./cert.pem
    
3) Offer the certificate to the browser by serving <i>websocket.html</i> through https. 
The HTTPS server will look for cert.pem in the local directory. 
Ensure the <i>websocket.html</i> is also in the same directory to where the server is run. 

    sudo python SimpleHTTPSServer.py

4) Open a web browser to: <i>https://localhost:443/websocket.html</i>

5) Change <i>ws://localhost:8000/</i> to <i>wss://localhost:8000</i> and click connect. 

Note: if you are having problems connecting, ensure that the certificate is added in your browser against the exception <i>https://localhost:8000</i> or whatever host:port pair you want to connect to. 

<h4>For the Programmers</h4>

handleConnected: called when handskake is complete
 - self.address: TCP address port tuple of the endpoint

handleClose: called when the endpoint is closed or there is an error

handleMessage: gets called when there is an incoming message from the client endpoint
 - self.opcode: the WebSocket frame type (STREAM, TEXT, BINARY)
 - self.data: bytearray (BINARY frame) or unicode payload (TEXT frame)  
 - self.request: HTTP details from the WebSocket handshake (refer to BaseHTTPRequestHandler)
 
sendMessage: send some text or binary data to the client endpoint
 - sending data as a unicode object will send a TEXT frame
 - sending data as a bytearray object will send a BINARY frame
 
sendClose: send close frame to endpoint


---------------------
The MIT License (MIT)

