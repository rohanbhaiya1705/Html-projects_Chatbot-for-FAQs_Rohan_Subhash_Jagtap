# Html-projects_Chatbot-for-FAQs_Rohan_Subhash_Jagtap


<!DOCTYPE html>
<!-- Declare HTML5 document type -->
<html lang="en">
<!-- Set document language to English -->
<head>
    <!-- Specify character encoding -->
    <meta charset="UTF-8">
    <!-- Configure responsive viewport -->
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <!-- Set page title -->
    <title>Chat Interface</title>
    <!-- Internal CSS styles -->
    <style>
        /* Style for chat messages container */
        .chat-messages {
            height: 400px;
            overflow-y: auto;
            border: 1px solid #ccc;
            padding: 10px;
            background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
            border-radius: 10px;
            margin-bottom: 10px;
        }
        /* Base style for messages */
        .message {
            margin: 10px 0;
            display: flex;
            align-items: center;
        }
        /* Style for user messages */
        .user-message {
            justify-content: flex-end;
        }
        /* Style for bot messages */
        .bot-message {
            justify-content: flex-start;
        }
        /* Style for message content */
        .message-content {
            max-width: 70%;
            padding: 10px 15px;
            border-radius: 15px;
            line-height: 1.4;
        }
        /* Style for user message content */
        .user-message .message-content {
            background-color: #007bff;
            color: white;
            border-bottom-right-radius: 0;
        }
        /* Style for bot message content */
        .bot-message .message-content {
            background-color: #e9ecef;
            color: #333;
            border-bottom-left-radius: 0;
        }
        /* Style for buttons within messages */
        .message button {
            margin-left: 10px;
            padding: 5px 10px;
            font-size: 12px;
        }
        /* Style for chat input container */
        .chat-input-container {
            display: flex;
            gap: 10px;
        }
        /* Style for user input field */
        #userInput {
            flex: 1;
            padding: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
            font-size: 16px;
        }
        /* Style for buttons */
        button {
            padding: 10px 20px;
            background-color: #007bff;
            color: white;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }
        /* Hover effect for buttons */
        button:hover {
            background-color: #0056b3;
        }
        /* Responsive design for smaller screens */
        @media (max-width: 600px) {
            .chat-messages {
                height: 300px;
            }
            .message-content {
                max-width: 85%;
            }
            #userInput {
                font-size: 14px;
            }
            button {
                padding: 8px 15px;
            }
        }
    </style>
</head>
<body>
    <!-- Chat messages container -->
    <div class="chat-messages" id="chatMessages">
        <!-- Initial bot message -->
        <div class="message bot-message">
            <div class="message-content">Hello! I'm here to help answer your questions. What would you like to know?</div>
        </div>
    </div>
    <!-- Input container for user input and send button -->
    <div class="chat-input-container">
        <input type="text" id="userInput" placeholder="Type your question here...">
        <button onclick="sendMessage()">Send</button>
    </div>
    <!-- JavaScript for chat functionality -->
    <script>
        // Add event listener for Enter key to send message
        document.getElementById('userInput').addEventListener('keypress', (event) => {
            if (event.key === 'Enter') sendMessage();
        });

        // Function to send user message and fetch bot response
        function sendMessage() {
            const input = document.getElementById('userInput');
            const message = input.value.trim();
            if (!message) return; // Exit if message is empty
            addMessage(message, 'user'); // Add user message to chat
            input.value = ''; // Clear input field
            const loadingMessage = addMessage('Processing...', 'bot'); // Show loading message
            // Send message to server
            fetch('/chat', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ message: message })
            })
            .then(response => {
                if (!response.ok) throw new Error('Network response was not ok');
                return response.json();
            })
            .then(data => {
                loadingMessage.remove(); // Remove loading message
                if (!data.answer) throw new Error('No answer provided by server');
                let botResponse = data.answer;
                // Append confidence score if available
                if (data.confidence > 0) {
                    const confidencePercent = Math.round(data.confidence * 100);
                    botResponse += `\n\nConfidence: ${confidencePercent}%`;
                }
                addMessage(botResponse, 'bot'); // Add bot response to chat
            })
            .catch(error => {
                loadingMessage.remove(); // Remove loading message
                addMessage('Sorry, I encountered an error. Please try again.', 'bot'); // Show error message
                console.error('Error:', error); // Log error to console
            });
        }

        // Function to add a message to the chat
        function addMessage(message, sender) {
            const messagesDiv = document.getElementById('chatMessages');
            const messageDiv = document.createElement('div');
            messageDiv.className = `message ${sender}-message`;
            const contentDiv = document.createElement('div');
            contentDiv.className = 'message-content';
            contentDiv.textContent = message.replace(/<[^>]+>/g, ''); // Strip HTML tags
            const copyButton = document.createElement('button');
            copyButton.textContent = 'Copy';
            // Add click event to copy message text
            copyButton.onclick = () => {
                navigator.clipboard.writeText(message);
                showNotification('Message copied!');
            };
            messageDiv.appendChild(contentDiv);
            messageDiv.appendChild(copyButton);
            messagesDiv.appendChild(messageDiv);
            messagesDiv.scrollTop = messagesDiv.scrollHeight; // Scroll to bottom
            return messageDiv;
        }

        // Function to show temporary notification
        function showNotification(text) {
            const notification = document.createElement('div');
            notification.textContent = text;
            notification.style.position = 'fixed';
            notification.style.bottom = '20px';
            notification.style.right = '20px';
            notification.style.padding = '10px';
            notification.style.background = '#28a745';
            notification.style.color = 'white';
            notification.style.borderRadius = '5px';
            document.body.appendChild(notification);
            setTimeout(() => notification.remove(), 3000); // Remove after 3 seconds
        }
    </script>
</body>
</html>


