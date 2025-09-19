# AI-Chat-app
AI Chat Application  Built an intelligent chat application using Spring AI integrated with the Google Gemini model for dynamic, natural language responses.  Implemented real-time messaging using WebSocket, enabling seamless user interactions without page refreshes.  Developed a clean and responsive UI with React, ensuring a smooth user experience.

import React, { useState, useEffect } from 'react';

const ChatApp = () => {
    const [messages, setMessages] = useState([]);
    const [input, setInput] = useState("");
    const [socket, setSocket] = useState(null);

    useEffect(() => {
        const ws = new WebSocket("ws://localhost:8080/chat");
        ws.onmessage = (event) => {
            setMessages(prev => [...prev, { sender: 'AI', text: event.data }]);
        };
        setSocket(ws);
        return () => ws.close();
    }, []);

    const sendMessage = () => {
        if (socket && input) {
            socket.send(input);
            setMessages(prev => [...prev, { sender: 'You', text: input }]);
            setInput("");
        }
    };

    return (
        <div style={{ padding: '2rem' }}>
            <h2>AI Chat</h2>
            <div style={{ maxHeight: '400px', overflowY: 'auto', border: '1px solid #ccc', padding: '1rem' }}>
                {messages.map((msg, i) => (
                    <div key={i}><strong>{msg.sender}:</strong> {msg.text}</div>
                ))}
            </div>
            <input
                value={input}
                onChange={(e) => setInput(e.target.value)}
                onKeyDown={(e) => e.key === 'Enter' && sendMessage()}
                style={{ width: '80%', marginRight: '1rem' }}
            />
            <button onClick={sendMessage}>Send</button>
        </div>
    );
};

export default ChatApp;

