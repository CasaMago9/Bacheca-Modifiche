# Bacheca-Modifiche
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <title>Bacheca Utente</title>
  <link href="https://fonts.googleapis.com/css2?family=Orbitron:wght@700&display=swap" rel="stylesheet">
  <style>
    body {
      font-family: 'Orbitron', sans-serif;
      max-width: 700px;
      margin: 40px auto;
      padding: 20px;
      background: linear-gradient(135deg, #0f0f0f, #1a1a1a);
      color: #f1c40f;
    }
    h2 {
      text-align: center;
      font-size: 2rem;
      color: #f39c12;
      margin-bottom: 30px;
    }
    textarea {
      width: 100%;
      height: 100px;
      margin-bottom: 10px;
      font-family: 'Orbitron', sans-serif;
      padding: 10px;
      font-size: 1rem;
      border: none;
      border-radius: 10px;
      background: #2c2c2c;
      color: white;
    }
    button {
      padding: 10px 20px;
      font-size: 1rem;
      font-family: 'Orbitron', sans-serif;
      background: #f1c40f;
      border: none;
      border-radius: 10px;
      cursor: pointer;
      margin-top: 10px;
    }
    button:hover {
      background: #d4ac0d;
    }
    .message {
      background: #1f1f1f;
      padding: 15px;
      margin: 15px 0;
      border-radius: 10px;
      color: #fff;
      position: relative;
      box-shadow: 0 2px 5px rgba(0,0,0,0.5);
    }
    .timestamp {
      font-size: 0.8em;
      color: #aaa;
      margin-top: 8px;
    }
    .actions {
      position: absolute;
      top: 10px;
      right: 10px;
    }
    .actions button {
      background: #e74c3c;
      color: white;
      font-size: 0.8rem;
      margin-left: 5px;
      border-radius: 5px;
    }
    .actions button:first-child {
      background: #3498db;
    }
  </style>
</head>
<body>
  <h2 id="userTitle">Bacheca</h2>
  <textarea id="messageInput" placeholder="Scrivi un messaggio..."></textarea>
  <br>
  <button onclick="postMessage()">Invia</button>
  <div id="messages"></div>

  <!-- Firebase -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>

  <script>
    const firebaseConfig = {
      apiKey: "API_KEY",
      authDomain: "PROJECT_ID.firebaseapp.com",
      databaseURL: "https://PROJECT_ID.firebaseio.com",
      projectId: "PROJECT_ID",
      storageBucket: "PROJECT_ID.appspot.com",
      messagingSenderId: "SENDER_ID",
      appId: "APP_ID"
    };

    firebase.initializeApp(firebaseConfig);
    const db = firebase.database();

    const params = new URLSearchParams(window.location.search);
    const userId = params.get("userId");
    const username = params.get("username") || "Bacheca";

    document.getElementById("userTitle").innerText = `Bacheca di ${username}`;

    function postMessage() {
      const message = document.getElementById("messageInput").value.trim();
      if (message !== "") {
        db.ref(`messages/${userId}`).push({
          text: message,
          timestamp: Date.now()
        });
        document.getElementById("messageInput").value = "";
      }
    }

    const messagesDiv = document.getElementById("messages");

    db.ref(`messages/${userId}`).orderByChild("timestamp").on("child_added", (data) => {
      renderMessage(data.key, data.val());
    });

    function renderMessage(key, msg) {
      const div = document.createElement("div");
      div.className = "message";
      div.id = key;

      const text = document.createElement("div");
      text.innerText = msg.text;
      text.className = "text";

      const timestamp = document.createElement("div");
      const date = new Date(msg.timestamp);
      timestamp.className = "timestamp";
      timestamp.innerText = date.toLocaleString();

      const actions = document.createElement("div");
      actions.className = "actions";

      const deleteBtn = document.createElement("button");
      deleteBtn.innerText = "Elimina";
      deleteBtn.onclick = () => deleteMessage(key);

      const editBtn = document.createElement("button");
      editBtn.innerText = "Modifica";
      editBtn.onclick = () => editMessage(key, msg.text);

      actions.appendChild(editBtn);
      actions.appendChild(deleteBtn);

      div.appendChild(text);
      div.appendChild(timestamp);
      div.appendChild(actions);

      messagesDiv.prepend(div);
    }

    function deleteMessage(key) {
      if (confirm("Sei sicuro di voler eliminare questo messaggio?")) {
        db.ref(`messages/${userId}/${key}`).remove();
        document.getElementById(key).remove();
      }
    }

    function editMessage(key, oldText) {
      const newText = prompt("Modifica il messaggio:", oldText);
      if (newText !== null && newText.trim() !== "") {
        db.ref(`messages/${userId}/${key}`).update({ text: newText });
        document.querySelector(`#${key} .text`).innerText = newText;
      }
    }
  </script>
</body>
</html>
