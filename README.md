Task 1: Project Setup
✅ Node.js Server with Express
Initialize project:

bash
mkdir server && cd server  
npm init -y  
Install dependencies:

bash
npm install express socket.io cors  
Create server entry file (index.js):

js
const express = require('express');
const http = require('http');
const cors = require('cors');
const socketIO = require('socket.io');

const app = express();
const server = http.createServer(app);
const io = socketIO(server, { cors: { origin: '*' } });

app.use(cors());

io.on('connection', (socket) => {
  console.log('User connected:', socket.id);
});

server.listen(4000, () => console.log('Server running on port 4000'));
✅ React Front-End Setup
Initialize client with Vite:

bash
npm create vite@latest client --template react  
cd client  
npm install  
npm install socket.io-client  
Set up Socket.io Client (socket.js):

js
import { io } from 'socket.io-client';
export const socket = io('http://localhost:4000');
Establish Basic Connection in App Component:

js
import { useEffect } from 'react';
import { socket } from './socket';

useEffect(() => {
  socket.on('connect', () => {
    console.log('Connected:', socket.id);
  });
}, []);
💬 Task 2: Core Chat Functionality
✅ User Authentication
Simple Username-based: Prompt for username and store it in context or localStorage.

JWT Approach (optional): Implement login API on server and verify tokens using jsonwebtoken.

✅ Global Chat Room
Server:

js
io.on('connection', socket => {
  socket.on('chatMessage', ({ user, message }) => {
    io.emit('chatMessage', { user, message, timestamp: Date.now() });
  });
});
Client: Listen to 'chatMessage' and update messages in state.

✅ Display Messages
Render each message with name and timestamp using:

js
<span>{msg.user}:</span> <span>{msg.text}</span> <span>{new Date(msg.timestamp).toLocaleTimeString()}</span>
✅ Typing Indicators
Server:

js
socket.on('typing', (user) => {
  socket.broadcast.emit('typing', user);
});
Client: Emit 'typing' when user types and show indicator if received.

✅ Online/Offline Status
Track users by storing socket IDs and broadcast 'userOnline' and 'userOffline' events when they connect/disconnect.

🚀 Task 3: Advanced Chat Features
✅ Private Messaging
Use socket.to(targetSocketId).emit('privateMessage', data)

✅ Multiple Chat Rooms
Use socket.join('roomName') Emit messages to specific room: io.to('roomName').emit(...)

✅ "User is Typing" Indicator
Already covered in Task 2, but extended to specific rooms or private messages.

✅ File/Image Sharing
Use socket.emit('fileMessage', { name, data }) after base64-encoding files or use APIs for upload/download.

✅ Read Receipts
Track when messages are rendered in the client and emit a 'messageRead' event to notify sender.

✅ Message Reactions
Maintain reaction state per message and emit updates via socket.emit('reaction', { msgId, reaction })

🔔 Task 4: Real-Time Notifications
✅ New Message Notification
Listen for 'chatMessage' and use Toast/Sound/Badge.

✅ User Join/Leave
Emit 'userJoined' and 'userLeft' events and display messages like “Joshua joined the room.”

✅ Unread Message Count
Track unread messages per conversation and display count badge.

✅ Sound Notifications
Use:

js
const audio = new Audio('/notify.mp3');
audio.play();
✅ Browser Notifications
Use:

js
Notification.requestPermission().then(() => {
  new Notification("New Message", { body: "Joshua sent a message!" });
});
🔧 Task 5: Performance and UX Optimization
✅ Message Pagination
Server: Support query params like /messages?before=timestamp

Client: Load on scroll or "Load older" button

✅ Reconnection Logic
Socket.io supports auto-reconnect. Add listeners for connect_error and reconnect_attempt.

✅ Namespaces and Rooms
Use namespaces like /chat and rooms for topic-based chats.

✅ Message Delivery Acknowledgment
Use callback on socket.emit('message', data, ack => ...) to confirm delivery.

✅ Message Search
Filter messages on client or implement keyword-based search on server.

✅ Mobile Responsiveness
Use Tailwind CSS for layout:

html
<div className="w-full sm:w-3/4 md:w-2/3 lg:w-1/2 mx-auto p-4">
