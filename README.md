from flask import Flask, request, jsonify
from groq import Groq

app = Flask(__name__)
client = Groq(api_key="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")


@app.route("/")
def home():
    return """
    <!DOCTYPE html>
    <html lang="it">
    <head>
        <meta charset="UTF-8">
        <title>EMI SUPER BOT</title>
        <style>
            body {
                font-family: Arial, sans-serif;
                background-color: #f4f4f4;
                margin: 0;
                padding: 0;
                display: flex;
                flex-direction: column;
                height: 100vh;
            }
            h1 {
                text-align: center;
                background-color: #333;
                color: white;
                margin: 0;
                padding: 15px;
            }
            #chat-box {
                flex: 1;
                padding: 15px;
                overflow-y: auto;
                display: flex;
                flex-direction: column;
            }
            .message {
                margin: 8px 0;
                padding: 10px 15px;
                border-radius: 10px;
                max-width: 70%;
            }
            .user {
                align-self: flex-end;
                background-color: #007bff;
                color: white;
                text-align: right;
            }
            .bot {
                align-self: flex-start;
                background-color: #e0e0e0;
                color: black;
                text-align: left;
            }
            #input-area {
                display: flex;
                padding: 10px;
                background-color: #fff;
                border-top: 1px solid #ccc;
            }
            #user-input {
                flex: 1;
                padding: 10px;
                font-size: 16px;
                border: 1px solid #ccc;
                border-radius: 5px;
            }
            #send-btn {
                padding: 10px 20px;
                font-size: 16px;
                margin-left: 10px;
                background-color: #007bff;
                color: white;
                border: none;
                border-radius: 5px;
                cursor: pointer;
            }
            #send-btn:hover {
                background-color: #0056b3;
            }
        </style>
    </head>
    <body>
        <h1> EMI SUPER BOT</h1>
        <div id="chat-box"></div>
        <div id="input-area">
            <input type="text" id="user-input" placeholder="Scrivi un messaggio..." />
            <button id="send-btn">Invia</button>
        </div>

        <script>
            const chatBox = document.getElementById('chat-box');
            const userInput = document.getElementById('user-input');
            const sendBtn = document.getElementById('send-btn');

            function addMessage(text, sender) {
                const msgDiv = document.createElement('div');
                msgDiv.classList.add('message', sender);
                msgDiv.textContent = text;
                chatBox.appendChild(msgDiv);
                chatBox.scrollTop = chatBox.scrollHeight;
            }

            async function sendMessage() {
                const message = userInput.value.trim();
                if (!message) return;
                addMessage(message, 'user');
                userInput.value = '';

                const response = await fetch('/chat', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ message })
                });
                const data = await response.json();
                addMessage(data.reply, 'bot');
            }

            sendBtn.addEventListener('click', sendMessage);
            userInput.addEventListener('keypress', (e) => {
                if (e.key === 'Enter') sendMessage();
            });
        </script>
    </body>
    </html>
    """

@app.route("/chat", methods=["POST"])
def chat():
    data = request.get_json()
    message = data.get("message", "")

    risposta = client.chat.completions.create(
        model="llama-3.1-8b-instant",
        messages=[
            {"role": "system", "content":"sei EMI SUPER BOT" "You are a multilingual assistant. Always reply in the SAME language the user writes to you. if user writes in English, reply in English. if user writes in Spanish, reply in Spanish. if user writes in Italian, reply in Italian. Never mix Languages "},
            {"role": "user", "content": message}
        ]
    )

    return jsonify({"reply": risposta.choices[0].message.content})

if __name__ == "__main__":
    import os
    app.run(host='0,0,0,0', port=int(os.environ.get('PORT', 5000)))
    
