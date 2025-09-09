<!DOCTYPE html>
<html>
<head>
  <title>Realtime Chat</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0; padding: 0;
      display: flex; flex-direction: column; height: 100vh;
      background: linear-gradient(to right, #74ebd5, #ACB6E5);
    }
    #chat {
      flex: 1;
      overflow-y: auto;
      padding: 10px;
      display: flex;
      flex-direction: column;
    }
    .message {
      max-width: 70%;
      padding: 10px;
      margin: 5px 0;
      border-radius: 20px;
      word-wrap: break-word;
    }
    .self {
      background-color: #dcf8c6;
      align-self: flex-end;
    }
    .other {
      background-color: #fff;
      align-self: flex-start;
    }
    #inputArea {
      display: flex;
      padding: 10px;
      border-top: 1px solid #ccc;
      background: #fff;
    }
    #msg {
      flex: 1;
      padding: 10px;
      border-radius: 20px;
      border: 1px solid #ccc;
      outline: none;
    }
    button {
      margin-left: 10px;
      padding: 10px 15px;
      border: none;
      border-radius: 20px;
      background-color: #0084ff;
      color: white;
      cursor: pointer;
    }
  </style>
</head>
<body>

<div id="chat"></div>

<div id="inputArea">
  <input id="msg" placeholder="Type a message..." />
  <button onclick="sendMessage()">Send</button>
</div>

<script type="module">
  import { initializeApp } from "https://www.gstatic.com/firebasejs/12.2.1/firebase-app.js";
  import { getDatabase, ref, push, onChildAdded, set } 
    from "https://www.gstatic.com/firebasejs/12.2.1/firebase-database.js";

  // ✅ Your Firebase Config (already added)
  const firebaseConfig = {
    apiKey: "AIzaSyDbTP56I1WcVZ4PPj2ooPEWOWEm7goGaGU",
    authDomain: "online-chat-d27e4.firebaseapp.com",
    projectId: "online-chat-d27e4",
    storageBucket: "online-chat-d27e4.appspot.com",
    messagingSenderId: "561438282767",
    appId: "1:561438282767:web:4e2cb90f03d55067c1ff7a",
    measurementId: "G-X6E87JQRWV"
  };

  // Initialize Firebase
  const app = initializeApp(firebaseConfig);
  const db = getDatabase(app);

  const chatDiv = document.getElementById("chat");
  const userId = prompt("Enter your name:");

  // Send message
  window.sendMessage = function() {
    const msg = document.getElementById("msg").value;
    if (msg.trim() === "") return;

    const messagesRef = ref(db, "messages");
    const newMsg = push(messagesRef);

    set(newMsg, {
      text: msg,
      user: userId,
      time: Date.now()
    });

    document.getElementById("msg").value = "";
  }

  // Listen for new messages
  const messagesRef = ref(db, "messages");
  onChildAdded(messagesRef, (snapshot) => {
    const msgData = snapshot.val();
    const p = document.createElement("div");
    p.classList.add("message");
    p.classList.add(msgData.user === userId ? "self" : "other");
    p.textContent = msgData.user + ": " + msgData.text;
    chatDiv.appendChild(p);
    chatDiv.scrollTop = chatDiv.scrollHeight;
  });
</script>

</body>
</html>
