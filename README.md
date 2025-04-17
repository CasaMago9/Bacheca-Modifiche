# Bacheca-Modifiche
<!DOCTYPE html>
<html lang="it">
<head>
  <meta charset="UTF-8">
  <title>Bacheca Condivisa</title>
  <style>
    body { font-family: sans-serif; max-width: 600px; margin: 40px auto; padding: 20px; background: #f5f5f5; }
    textarea { width: 100%; height: 80px; margin-bottom: 10px; }
    button { padding: 10px 20px; }
    .message { background: white; padding: 10px; margin: 10px 0; border-radius: 5px; }
  </style>
</head>
<body>
  <h2>Bacheca Pubblica</h2>
  <textarea id="messageInput" placeholder="Scrivi un messaggio..."></textarea>
  <br>
  <button onclick="postMessage()">Invia</button>
  <div id="messages"></div>

  <!-- Firebase -->
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/9.22.1/firebase-database-compat.js"></script>
  
  <script>
    // ⬇️ INCOLLA QUI la tua configurazione Firebase
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

    function postMessage() {
      const message = document.getElementById("messageInput").value.trim();
      if (message !== "") {
        db.ref("messages").push({
          text: message,
          timestamp: Date.now()
        });
        document.getElementById("messageInput").value = "";
      }
    }

    const messagesDiv = document.getElementById("messages");

    db.ref("messages").on("child_added", (data) => {
      const msg = data.val();
      const div = document.createElement("div");
      div.className = "message";
      div.innerText = msg.text;
      messagesDiv.prepend(div);
    });
  </script>
</body>
</html>
