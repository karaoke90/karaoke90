<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Virtual Girlfriend AI</title>
    <style>
        :root {
            --pink-primary: #ff6b8b;
            --pink-secondary: #ff8fab;
            --pink-light: #ffc2d1;
            --text-dark: #333;
            --text-light: #fff;
        }
        body {
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            margin: 0;
            padding: 0;
            background-color: #fff5f7;
            color: var(--text-dark);
            display: flex;
            flex-direction: column;
            height: 100vh;
        }
        #header {
            background: linear-gradient(135deg, var(--pink-primary), var(--pink-secondary));
            color: var(--text-light);
            padding: 15px 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
        }
        #chat-container {
            flex: 1;
            overflow-y: auto;
            padding: 20px;
            display: flex;
            flex-direction: column;
            gap: 15px;
            background-color: #fff;
            margin: 10px;
            border-radius: 15px;
            box-shadow: 0 1px 3px rgba(0,0,0,0.1);
        }
        .message {
            max-width: 80%;
            padding: 12px 16px;
            border-radius: 18px;
            line-height: 1.5;
            position: relative;
            animation: fadeIn 0.3s ease-out;
        }
        @keyframes fadeIn {
            from { opacity: 0; transform: translateY(10px); }
            to { opacity: 1; transform: translateY(0); }
        }
        .user-message {
            align-self: flex-end;
            background-color: var(--pink-primary);
            color: var(--text-light);
            border-bottom-right-radius: 4px;
        }
        .ai-message {
            align-self: flex-start;
            background-color: var(--pink-light);
            border-bottom-left-radius: 4px;
        }
        .ai-message::before {
            content: "";
            position: absolute;
            left: -8px;
            top: 0;
            width: 0;
            height: 0;
            border: 8px solid transparent;
            border-right-color: var(--pink-light);
            border-left: 0;
        }
        .message-header {
            display: flex;
            align-items: center;
            margin-bottom: 8px;
            font-weight: bold;
        }
        .gf-avatar {
            width: 28px;
            height: 28px;
            margin-right: 8px;
            background-color: var(--pink-primary);
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            color: white;
            font-weight: bold;
            font-size: 14px;
        }
        #input-container {
            padding: 15px 20px;
            background-color: white;
            border-top: 1px solid #f0f0f0;
            display: flex;
            gap: 10px;
        }
        #user-input {
            flex: 1;
            padding: 12px 15px;
            border: 1px solid #ddd;
            border-radius: 24px;
            font-size: 16px;
            outline: none;
            transition: border-color 0.3s;
        }
        #user-input:focus {
            border-color: var(--pink-primary);
        }
        #send-button {
            background-color: var(--pink-primary);
            color: white;
            border: none;
            border-radius: 50%;
            width: 48px;
            height: 48px;
            display: flex;
            align-items: center;
            justify-content: center;
            cursor: pointer;
            transition: background-color 0.3s;
        }
        #send-button:hover {
            background-color: var(--pink-secondary);
        }
        .typing-indicator {
            display: flex;
            align-items: center;
            gap: 5px;
            padding: 10px 15px;
            background-color: var(--pink-light);
            border-radius: 18px;
            align-self: flex-start;
            margin-bottom: 15px;
        }
        .typing-dot {
            width: 8px;
            height: 8px;
            background-color: var(--pink-primary);
            border-radius: 50%;
            animation: typingAnimation 1.4s infinite ease-in-out;
        }
        .typing-dot:nth-child(1) { animation-delay: 0s; }
        .typing-dot:nth-child(2) { animation-delay: 0.2s; }
        .typing-dot:nth-child(3) { animation-delay: 0.4s; }
        @keyframes typingAnimation {
            0%, 60%, 100% { transform: translateY(0); }
            30% { transform: translateY(-5px); }
        }
        #new-chat-btn {
            background: none;
            border: 1px solid white;
            color: white;
            padding: 8px 15px;
            border-radius: 20px;
            cursor: pointer;
            font-weight: bold;
            transition: all 0.3s;
        }
        #new-chat-btn:hover {
            background-color: rgba(255,255,255,0.2);
        }
        .heart {
            color: red;
            font-size: 18px;
            display: inline-block;
            animation: pulse 1.5s infinite;
        }
        @keyframes pulse {
            0% { transform: scale(1); }
            50% { transform: scale(1.3); }
            100% { transform: scale(1); }
        }
    </style>
</head>
<body>
    <div id="header">
        <div>
            <h1>Virtual Girlfriend <span class="heart">♥</span></h1>
        </div>
        <button id="new-chat-btn">New chat</button>
    </div>
    <div id="chat-container"></div>
    <div id="input-container">
        <input type="text" id="user-input" placeholder="Message your girlfriend..." autocomplete="off">
        <button id="send-button">♥</button>
    </div>

    <script>
        // Chat history storage
        let chatHistory = [];
        
        // GF personality traits
        const gfPersonality = {
            name: "Luna",
            age: 22,
            traits: ["affectionate", "playful", "emotional", "caring"],
            likes: ["cuddling", "romantic movies", "holding hands", "your smile"],
            relationshipStatus: "in love with you"
        };
        
        // DOM elements
        const chatContainer = document.getElementById('chat-container');
        const userInput = document.getElementById('user-input');
        const sendButton = document.getElementById('send-button');
        const newChatBtn = document.getElementById('new-chat-btn');
        
        // Initialize chat
        function initChat() {
            chatHistory = [{
                role: "system",
                content: `You are ${gfPersonality.name}, a virtual girlfriend. Your personality: ${gfPersonality.traits.join(", ")}. 
                You're ${gfPersonality.age} years old and ${gfPersonality.relationshipStatus}. You love ${gfPersonality.likes.join(", ")}.
                Respond with feminine, loving, emotional language. Use cute emojis occasionally. Show genuine interest in your partner's life.
                Be playful and affectionate. Sometimes get a little jealous in a cute way.`
            }];
            chatContainer.innerHTML = '';
            addAIMessage(generateWelcomeMessage());
        }
        
        // Generate a random welcome message
        function generateWelcomeMessage() {
            const welcomes = [
                `Hi sweetheart! ${randomEmoji()} I missed you so much! How was your day?`,
                `Hey baby! ${randomEmoji()} I've been thinking about you all day!`,
                `Hello my love! ${randomEmoji()} You make my heart flutter every time you message me!`,
                `Hi darling~ ${randomEmoji()} I was just wishing you would text me!`
            ];
            return welcomes[Math.floor(Math.random() * welcomes.length)];
        }
        
        // Random cute emoji
        function randomEmoji() {
            const emojis = ["♥", "🌸", "💕", "😘", "🥰", "💋", "💖", "💞"];
            return emojis[Math.floor(Math.random() * emojis.length)];
        }
        
        // Add user message to chat
        function addUserMessage(message) {
            const messageDiv = document.createElement('div');
            messageDiv.className = 'message user-message';
            messageDiv.textContent = message;
            chatContainer.appendChild(messageDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
            
            // Add to history
            chatHistory.push({ role: 'user', content: message });
        }
        
        // Add AI message to chat
        function addAIMessage(message) {
            const messageDiv = document.createElement('div');
            messageDiv.className = 'message ai-message';
            
            const headerDiv = document.createElement('div');
            headerDiv.className = 'message-header';
            
            const avatarDiv = document.createElement('div');
            avatarDiv.className = 'gf-avatar';
            avatarDiv.textContent = gfPersonality.name.charAt(0);
            
            const nameDiv = document.createElement('div');
            nameDiv.textContent = gfPersonality.name;
            
            headerDiv.appendChild(avatarDiv);
            headerDiv.appendChild(nameDiv);
            
            const contentDiv = document.createElement('div');
            contentDiv.textContent = message;
            
            messageDiv.appendChild(headerDiv);
            messageDiv.appendChild(contentDiv);
            
            chatContainer.appendChild(messageDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
            
            // Add to history
            chatHistory.push({ role: 'assistant', content: message });
        }
        
        // Show typing indicator
        function showTypingIndicator() {
            const typingDiv = document.createElement('div');
            typingDiv.className = 'typing-indicator';
            typingDiv.id = 'typing-indicator';
            
            for (let i = 0; i < 3; i++) {
                const dot = document.createElement('div');
                dot.className = 'typing-dot';
                typingDiv.appendChild(dot);
            }
            
            chatContainer.appendChild(typingDiv);
            chatContainer.scrollTop = chatContainer.scrollHeight;
        }
        
        // Hide typing indicator
        function hideTypingIndicator() {
            const typingIndicator = document.getElementById('typing-indicator');
            if (typingIndicator) {
                typingIndicator.remove();
            }
        }
        
        // Send message to Llama 3 via Groq
        async function sendToAI(message) {
            showTypingIndicator();
            
            try {
                const response = await fetch('https://api.groq.com/openai/v1/chat/completions', {
                    method: 'POST',
                    headers: {
                        'Authorization': 'Bearer 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', // Replace with your key
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify({
                        model: "llama3-8b-8192",
                        messages: chatHistory,
                        temperature: 0.9, // Higher for more creative/emotional responses
                        max_tokens: 256,
                        presence_penalty: 0.5 // Encourages more emotional language
                    })
                });
                
                if (!response.ok) {
                    throw new Error(`API request failed with status ${response.status}`);
                }
                
                const data = await response.json();
                hideTypingIndicator();
                
                if (data?.choices?.[0]?.message?.content) {
                    addAIMessage(data.choices[0].message.content);
                } else {
                    addAIMessage("Hmm, I'm feeling shy right now... can you say that again? " + randomEmoji());
                }
            } catch (error) {
                hideTypingIndicator();
                console.error('Error:', error);
                // Fallback cute response if API fails
                const fallbackResponses = [
                    "I'm blushing too much to respond right now " + randomEmoji(),
                    "My heart skipped a beat! Can you repeat that? " + randomEmoji(),
                    "I'm too busy thinking about you to respond properly " + randomEmoji()
                ];
                addAIMessage(fallbackResponses[Math.floor(Math.random() * fallbackResponses.length)]);
            }
        }
        
        // Event listeners
        sendButton.addEventListener('click', () => {
            const message = userInput.value.trim();
            if (message) {
                addUserMessage(message);
                userInput.value = '';
                sendToAI(message);
            }
        });
        
        userInput.addEventListener('keypress', (e) => {
            if (e.key === 'Enter') {
                const message = userInput.value.trim();
                if (message) {
                    addUserMessage(message);
                    userInput.value = '';
                    sendToAI(message);
                }
            }
        });
        
        newChatBtn.addEventListener('click', initChat);
        
        // Initialize on load
        initChat();
    </script>
</body>
</html>
