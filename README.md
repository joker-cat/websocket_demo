# WebSocket
- 通常在實現 WebSocket 的伺服器端時，我們會搭配使用 Express 來簡化伺服器的構建和路由管理。
- Express 提供了易於使用的 API，並且可以輕鬆與 WebSocket 整合。

## 安裝
```npm install express socket.io```

## 創建一個伺服器檔案（例如 server.js）：
```js
const express = require('express');
const http = require('http');
const socketIO = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIO(server);

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', (socket) => {
  console.log('A user connected');

  socket.on('join', (username) => {
    io.emit('chat message', `${username} 加入了聊天室`);
  });

  socket.on('chat message', (msg) => {
    io.emit('chat message', msg);
  });

  socket.on('disconnect', () => {
    console.log('User disconnected');
  });
});

const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

```

## 創建一個 HTML 文件（例如 index.html），用於呈現聊天介面：
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>WebSocket 聊天室</title>
  <style>
    #messages {
      list-style-type: none;
      margin: 0;
      padding: 0;
    }

    #messages li {
      margin-bottom: 10px;
    }
  </style>
</head>
<body>
  <ul id="messages"></ul>
  <form id="form" action="">
    <input id="input" autocomplete="off" /><button>發送</button>
  </form>

  <script src="/socket.io/socket.io.js"></script>
  <script>
    document.addEventListener('DOMContentLoaded', () => {
      const socket = io();

      const form = document.querySelector('form');
      const input = document.querySelector('#input');
      const messages = document.querySelector('#messages');

      form.addEventListener('submit', (e) => {
        e.preventDefault();
        if (input.value) {
          socket.emit('chat message', input.value);
          input.value = '';
        }
      });

      socket.on('chat message', (msg) => {
        const item = document.createElement('li');
        item.textContent = msg;
        messages.appendChild(item);
        window.scrollTo(0, document.body.scrollHeight);
      });

      const username = prompt('請輸入你的名字：');
      socket.emit('join', username);
    });
  </script>
</body>
</html>
```
## 運行你的伺服器：
```node server.js```
-這樣，你就擁有一個使用 Express 和 Socket.IO 實現的多人聊天室伺服器。
- 這個伺服器將在 http://localhost:3000 上運行，用戶可以通過打開這個網址來加入聊天室。
