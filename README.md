---
description: Kurssimatskua
---

# Test

{% file src=".gitbook/assets/readme.md.pdf" caption="pdf" %}

## Mikä on Node.js

Node.js on sitä ja tätä. Alla esimerkki.

```javascript
var http = require('http');

//create a server object:
http.createServer(function (request, response) {

  response.writeHead(200, {'Content-Type':'text/html'});

  response.write('<h1>Hello World!<h1>'); //write a response to the client
  response.write("<h2>How are you today</h2>");
  response.write("<img src='https://software.intel.com/sites/default/files/managed/fa/a0/Runtime-logo-Node.jpg'>");

  var data ="<table border='1'><tr><td>Eka solu</td><td>Toka solu</td></tr><tr><td>Eka solu</td><td>Toka solu</td></tr></table>";
  response.write( data ); 

  response.end("<strong>This is the end</strong>"); //end the response
}).listen(8081); //the server object listens on port 8080
```





