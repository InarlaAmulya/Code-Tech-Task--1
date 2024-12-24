# Code-Tech-Task--1
# Initialize the project
npm init -y

# Install dependencies
npm install express socket.io
npm install --save-dev nodemon
// server.js
const express = require('express');
const http = require('http');
const socketIo = require('socket.io');

// Initialize the app and server
const app = express();
const server = http.createServer(app);
const io = socketIo(server);

// Serve static files (like HTML, CSS, JS)
app.use(express.static('public'));

// When a user connects to the server
io.on('connection', (socket) => {
  console.log('A user connected');
  
  // Listen for a message from the client
  socket.on('chat message', (msg) => {
    console.log('Message: ' + msg);
    // Broadcast the message to all clients
    io.emit('chat message', msg);
  });

  // Handle disconnection
  socket.on('disconnect', () => {
    console.log('A user disconnected');
  });
});

// Start the server
const PORT = process.env.PORT || 3000;
server.listen(PORT, () => {
  console.log(Server running on port ${PORT});
});
<!-- public/index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Real-time Chat App</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #f7f7f7;
      padding: 20px;
    }
    .chat-container {
      max-width: 600px;
      margin: 0 auto;
      background: white;
      padding: 20px;
      border-radius: 8px;
      box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
    }
    .messages {
      max-height: 300px;
      overflow-y: auto;
      margin-bottom: 20px;
    }
    .message {
      padding: 8px;
      margin-bottom: 5px;
      background: #e1e1e1;
      border-radius: 4px;
    }
    input[type="text"] {
      width: 80%;
      padding: 8px;
      margin-right: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
    }
    button {
      padding: 8px 16px;
      background-color: #007bff;
      color: white;
      border: none;
      border-radius: 4px;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>
  <div class="chat-container">
    <h2>Real-time Chat</h2>
    <div class="messages" id="messages"></div>
    <form id="chat-form">
      <input type="text" id="message" placeholder="Type a message" required />
      <button type="submit">Send</button>
    </form>
  </div>

  <!-- Include Socket.io client -->
  <script src="/socket.io/socket.io.js"></script>
  <script>
    const socket = io();

    const form = document.getElementById('chat-form');
    const input = document.getElementById('message');
    const messagesContainer = document.getElementById('messages');

    // Listen for incoming messages from the server
    socket.on('chat message', (msg) => {
      const messageElement = document.createElement('div');
      messageElement.classList.add('message');
      messageElement.textContent = msg;
      messagesContainer.appendChild(messageElement);
      messagesContainer.scrollTop = messagesContainer.scrollHeight; // Auto-scroll to bottom
    });

    // Send message when the form is submitted
    form.addEventListener('submit', (e) => {
      e.preventDefault();
      const message = input.value;
      socket.emit('chat message', message); // Send the message to the server
      input.value = ''; // Clear input field
    });
  </script>
</body>
</html>
{
  "scripts": {
    "start": "nodemon server.js"
  }
}
npm start
