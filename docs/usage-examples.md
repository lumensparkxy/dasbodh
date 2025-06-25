# Usage Examples

This document provides practical examples and use cases for integrating the Dasbodh API into various applications.

## 🚀 Quick Start Examples

### 1. Daily Inspiration App

Create a simple daily inspiration application that displays a random shloka.

#### Web Application (HTML + JavaScript)

```html
<!DOCTYPE html>
<html>
<head>
    <title>Daily Dasbodh Inspiration</title>
    <meta charset="UTF-8">
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f5f5f5;
        }
        .shloka-container {
            background: white;
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.1);
            text-align: center;
        }
        .shloka-text {
            font-size: 24px;
            line-height: 1.6;
            color: #333;
            margin: 20px 0;
            font-family: 'Times New Roman', serif;
        }
        .metadata {
            color: #666;
            font-style: italic;
            margin-top: 20px;
        }
        button {
            background-color: #007cba;
            color: white;
            padding: 10px 20px;
            border: none;
            border-radius: 5px;
            cursor: pointer;
            font-size: 16px;
        }
        button:hover {
            background-color: #005a87;
        }
    </style>
</head>
<body>
    <div class="shloka-container">
        <h1>Daily Dasbodh Inspiration</h1>
        <div id="shloka-content">
            <p>Click the button below to get your daily inspiration!</p>
        </div>
        <button onclick="getRandomShloka()">Get New Shloka</button>
    </div>

    <script>
        const API_BASE = 'https://dasbodh.azurewebsites.net';
        const FUNCTION_KEY = 'YOUR_FUNCTION_KEY'; // Replace with actual key

        async function getRandomShloka() {
            try {
                const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
                const shloka = await response.json();
                
                document.getElementById('shloka-content').innerHTML = `
                    <div class="shloka-text">${shloka.shlok}</div>
                    <div class="metadata">
                        <div><strong>Dashak:</strong> ${shloka.dashak}</div>
                        <div><strong>Samas:</strong> ${shloka.samas}</div>
                    </div>
                `;
            } catch (error) {
                console.error('Error fetching shloka:', error);
                document.getElementById('shloka-content').innerHTML = 
                    '<p>Sorry, there was an error loading the shloka. Please try again.</p>';
            }
        }

        // Load a shloka when page loads
        window.onload = getRandomShloka;
    </script>
</body>
</html>
```

### 2. Chapter Study Application

A more advanced application that allows users to study specific chapters.

#### React Component Example

```jsx
import React, { useState, useEffect } from 'react';

const DasbodhStudyApp = () => {
    const [currentChapter, setCurrentChapter] = useState(1);
    const [shlokas, setShlokas] = useState([]);
    const [loading, setLoading] = useState(false);
    const [error, setError] = useState(null);

    const API_BASE = 'https://dasbodh.azurewebsites.net';
    const FUNCTION_KEY = 'YOUR_FUNCTION_KEY';

    const fetchChapterShlokas = async (chapter) => {
        setLoading(true);
        setError(null);
        
        try {
            const response = await fetch(
                `${API_BASE}/api/getshlockbydashak?chapter=${chapter}&code=${FUNCTION_KEY}`
            );
            const data = await response.json();
            
            if (Array.isArray(data)) {
                setShlokas(data);
            } else {
                setError('No shlokas found for this chapter');
            }
        } catch (err) {
            setError('Failed to fetch shlokas');
            console.error(err);
        } finally {
            setLoading(false);
        }
    };

    useEffect(() => {
        fetchChapterShlokas(currentChapter);
    }, [currentChapter]);

    return (
        <div style={{ maxWidth: '800px', margin: '0 auto', padding: '20px' }}>
            <h1>Dasbodh Study Application</h1>
            
            <div style={{ marginBottom: '20px' }}>
                <label>Select Chapter: </label>
                <select 
                    value={currentChapter} 
                    onChange={(e) => setCurrentChapter(parseInt(e.target.value))}
                    style={{ padding: '5px', fontSize: '16px' }}
                >
                    {Array.from({length: 20}, (_, i) => (
                        <option key={i + 1} value={i + 1}>Chapter {i + 1}</option>
                    ))}
                </select>
            </div>

            {loading && <p>Loading shlokas...</p>}
            {error && <p style={{ color: 'red' }}>{error}</p>}
            
            <div>
                {shlokas.map((shloka, index) => (
                    <div key={index} style={{
                        backgroundColor: '#f9f9f9',
                        padding: '15px',
                        margin: '10px 0',
                        borderRadius: '5px',
                        borderLeft: '4px solid #007cba'
                    }}>
                        <div style={{ fontSize: '18px', lineHeight: '1.6' }}>
                            {shloka.shlok}
                        </div>
                        <div style={{ fontSize: '14px', color: '#666', marginTop: '10px' }}>
                            <div><strong>Dashak:</strong> {shloka.dashak}</div>
                            <div><strong>Samas:</strong> {shloka.samas}</div>
                            {shloka.paragraph && <div><strong>Paragraph:</strong> {shloka.paragraph}</div>}
                        </div>
                    </div>
                ))}
            </div>
        </div>
    );
};

export default DasbodhStudyApp;
```

### 3. Mobile App Integration (React Native)

```javascript
import React, { useState } from 'react';
import { View, Text, TouchableOpacity, StyleSheet, ScrollView } from 'react-native';

const DasbodhMobileApp = () => {
    const [shloka, setShloka] = useState(null);
    const [loading, setLoading] = useState(false);

    const API_BASE = 'https://dasbodh.azurewebsites.net';
    const FUNCTION_KEY = 'YOUR_FUNCTION_KEY';

    const fetchRandomShloka = async () => {
        setLoading(true);
        try {
            const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
            const data = await response.json();
            setShloka(data);
        } catch (error) {
            console.error('Error:', error);
        } finally {
            setLoading(false);
        }
    };

    return (
        <ScrollView style={styles.container}>
            <Text style={styles.title}>Dasbodh Mobile</Text>
            
            <TouchableOpacity 
                style={styles.button} 
                onPress={fetchRandomShloka}
                disabled={loading}
            >
                <Text style={styles.buttonText}>
                    {loading ? 'Loading...' : 'Get Random Shloka'}
                </Text>
            </TouchableOpacity>

            {shloka && (
                <View style={styles.shlokaContainer}>
                    <Text style={styles.shlokaText}>{shloka.shlok}</Text>
                    <Text style={styles.metadata}>Dashak: {shloka.dashak}</Text>
                    <Text style={styles.metadata}>Samas: {shloka.samas}</Text>
                </View>
            )}
        </ScrollView>
    );
};

const styles = StyleSheet.create({
    container: {
        flex: 1,
        padding: 20,
        backgroundColor: '#f5f5f5',
    },
    title: {
        fontSize: 24,
        fontWeight: 'bold',
        textAlign: 'center',
        marginBottom: 30,
        color: '#333',
    },
    button: {
        backgroundColor: '#007cba',
        padding: 15,
        borderRadius: 8,
        alignItems: 'center',
        marginBottom: 20,
    },
    buttonText: {
        color: 'white',
        fontSize: 18,
        fontWeight: 'bold',
    },
    shlokaContainer: {
        backgroundColor: 'white',
        padding: 20,
        borderRadius: 10,
        shadowColor: '#000',
        shadowOffset: { width: 0, height: 2 },
        shadowOpacity: 0.1,
        shadowRadius: 4,
        elevation: 3,
    },
    shlokaText: {
        fontSize: 20,
        lineHeight: 28,
        textAlign: 'center',
        marginBottom: 15,
        color: '#333',
    },
    metadata: {
        fontSize: 14,
        color: '#666',
        fontStyle: 'italic',
        textAlign: 'center',
        marginBottom: 5,
    },
});

export default DasbodhMobileApp;
```

## 🔧 Backend Integration Examples

### 4. Node.js/Express Server Integration

```javascript
const express = require('express');
const fetch = require('node-fetch');
const app = express();

const API_BASE = 'https://dasbodh.azurewebsites.net';
const FUNCTION_KEY = process.env.DASBODH_API_KEY;

// Middleware for CORS
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Origin, X-Requested-With, Content-Type, Accept');
    next();
});

// Route to get daily shloka
app.get('/daily-shloka', async (req, res) => {
    try {
        const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
        const shloka = await response.json();
        
        res.json({
            success: true,
            data: shloka,
            timestamp: new Date().toISOString()
        });
    } catch (error) {
        res.status(500).json({
            success: false,
            error: 'Failed to fetch shloka'
        });
    }
});

// Route to get chapter shlokas with caching
const cache = new Map();
const CACHE_DURATION = 60 * 60 * 1000; // 1 hour

app.get('/chapter/:chapterNumber', async (req, res) => {
    const chapter = req.params.chapterNumber;
    const cacheKey = `chapter-${chapter}`;
    
    // Check cache first
    if (cache.has(cacheKey)) {
        const cached = cache.get(cacheKey);
        if (Date.now() - cached.timestamp < CACHE_DURATION) {
            return res.json(cached.data);
        }
    }
    
    try {
        const response = await fetch(
            `${API_BASE}/api/getshlockbydashak?chapter=${chapter}&code=${FUNCTION_KEY}`
        );
        const shlokas = await response.json();
        
        const result = {
            success: true,
            chapter: parseInt(chapter),
            totalShlokas: Array.isArray(shlokas) ? shlokas.length : 0,
            data: shlokas
        };
        
        // Cache the result
        cache.set(cacheKey, {
            data: result,
            timestamp: Date.now()
        });
        
        res.json(result);
    } catch (error) {
        res.status(500).json({
            success: false,
            error: 'Failed to fetch chapter shlokas'
        });
    }
});

app.listen(3000, () => {
    console.log('Server running on port 3000');
});
```

### 5. Python Flask Integration

```python
from flask import Flask, jsonify
import requests
import os
from datetime import datetime, timedelta
import json

app = Flask(__name__)

API_BASE = 'https://dasbodh.azurewebsites.net'
FUNCTION_KEY = os.environ.get('DASBODH_API_KEY')

# Simple in-memory cache
cache = {}
CACHE_DURATION = timedelta(hours=1)

def is_cache_valid(timestamp):
    return datetime.now() - timestamp < CACHE_DURATION

@app.route('/api/random-shloka')
def get_random_shloka():
    try:
        response = requests.get(f'{API_BASE}/api/getshlok?code={FUNCTION_KEY}')
        response.raise_for_status()
        
        shloka = response.json()
        return jsonify({
            'success': True,
            'data': shloka,
            'timestamp': datetime.now().isoformat()
        })
    except requests.RequestException as e:
        return jsonify({
            'success': False,
            'error': 'Failed to fetch shloka'
        }), 500

@app.route('/api/chapter/<int:chapter_num>')
def get_chapter_shlokas(chapter_num):
    cache_key = f'chapter_{chapter_num}'
    
    # Check cache
    if cache_key in cache:
        cached_data, timestamp = cache[cache_key]
        if is_cache_valid(timestamp):
            return jsonify(cached_data)
    
    try:
        response = requests.get(
            f'{API_BASE}/api/getshlockbydashak?chapter={chapter_num}&code={FUNCTION_KEY}'
        )
        response.raise_for_status()
        
        shlokas = response.json()
        result = {
            'success': True,
            'chapter': chapter_num,
            'total_shlokas': len(shlokas) if isinstance(shlokas, list) else 0,
            'data': shlokas
        }
        
        # Cache the result
        cache[cache_key] = (result, datetime.now())
        
        return jsonify(result)
    except requests.RequestException as e:
        return jsonify({
            'success': False,
            'error': 'Failed to fetch chapter shlokas'
        }), 500

@app.route('/api/search/<keyword>')
def search_shlokas(keyword):
    """
    Simple search implementation that fetches all data and filters locally
    Note: This is not efficient for large datasets
    """
    try:
        # This would need to be optimized for production use
        all_chapters_data = []
        
        for chapter in range(1, 21):  # Assuming 20 chapters
            response = requests.get(
                f'{API_BASE}/api/getshlockbydashak?chapter={chapter}&code={FUNCTION_KEY}'
            )
            if response.status_code == 200:
                chapter_data = response.json()
                if isinstance(chapter_data, list):
                    all_chapters_data.extend(chapter_data)
        
        # Filter by keyword
        filtered_shlokas = [
            shloka for shloka in all_chapters_data
            if keyword.lower() in shloka.get('shlok', '').lower()
        ]
        
        return jsonify({
            'success': True,
            'keyword': keyword,
            'total_results': len(filtered_shlokas),
            'data': filtered_shlokas[:50]  # Limit to 50 results
        })
    except Exception as e:
        return jsonify({
            'success': False,
            'error': 'Search failed'
        }), 500

if __name__ == '__main__':
    app.run(debug=True)
```

## 📱 Simple Use Cases

### 6. Daily Notification Bot (Telegram)

```javascript
const TelegramBot = require('node-telegram-bot-api');
const fetch = require('node-fetch');

const bot = new TelegramBot(process.env.TELEGRAM_BOT_TOKEN, { polling: true });
const API_BASE = 'https://dasbodh.azurewebsites.net';
const FUNCTION_KEY = process.env.DASBODH_API_KEY;

// Command to get random shloka
bot.onText(/\/shloka/, async (msg) => {
    const chatId = msg.chat.id;
    
    try {
        const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
        const shloka = await response.json();
        
        const message = `
🕉️ *Daily Dasbodh Shloka*

${shloka.shlok}

_${shloka.dashak}_
_${shloka.samas}_

Use /shloka for another verse
Use /chapter [number] for specific chapter
        `;
        
        bot.sendMessage(chatId, message, { parse_mode: 'Markdown' });
    } catch (error) {
        bot.sendMessage(chatId, 'Sorry, I could not fetch a shloka right now. Please try again later.');
    }
});

// Command to get chapter shlokas
bot.onText(/\/chapter (\d+)/, async (msg, match) => {
    const chatId = msg.chat.id;
    const chapter = match[1];
    
    if (chapter < 1 || chapter > 20) {
        bot.sendMessage(chatId, 'Please provide a chapter number between 1 and 20.');
        return;
    }
    
    try {
        const response = await fetch(
            `${API_BASE}/api/getshlockbydashak?chapter=${chapter}&code=${FUNCTION_KEY}`
        );
        const shlokas = await response.json();
        
        if (Array.isArray(shlokas) && shlokas.length > 0) {
            const randomShloka = shlokas[Math.floor(Math.random() * shlokas.length)];
            
            const message = `
📖 *Chapter ${chapter} - Random Shloka*

${randomShloka.shlok}

_${randomShloka.dashak}_
_${randomShloka.samas}_

Total shlokas in this chapter: ${shlokas.length}
            `;
            
            bot.sendMessage(chatId, message, { parse_mode: 'Markdown' });
        } else {
            bot.sendMessage(chatId, `No shlokas found for chapter ${chapter}.`);
        }
    } catch (error) {
        bot.sendMessage(chatId, 'Sorry, I could not fetch chapter shlokas right now.');
    }
});

// Welcome message
bot.onText(/\/start/, (msg) => {
    const chatId = msg.chat.id;
    const message = `
🙏 Welcome to Dasbodh Bot!

Available commands:
/shloka - Get a random shloka
/chapter [1-20] - Get random shloka from specific chapter

Example: /chapter 5
    `;
    
    bot.sendMessage(chatId, message);
});

console.log('Dasbodh Telegram bot is running...');
```

### 7. Email Newsletter Service

```javascript
const nodemailer = require('nodemailer');
const cron = require('node-cron');
const fetch = require('node-fetch');

const API_BASE = 'https://dasbodh.azurewebsites.net';
const FUNCTION_KEY = process.env.DASBODH_API_KEY;

// Email configuration
const transporter = nodemailer.createTransporter({
    service: 'gmail',
    auth: {
        user: process.env.EMAIL_USER,
        pass: process.env.EMAIL_PASS
    }
});

const subscribers = [
    'subscriber1@example.com',
    'subscriber2@example.com'
    // Add more subscribers
];

async function sendDailyShloka() {
    try {
        // Fetch random shloka
        const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
        const shloka = await response.json();
        
        const htmlContent = `
            <html>
            <body style="font-family: Arial, sans-serif; max-width: 600px; margin: 0 auto;">
                <h2 style="color: #007cba; text-align: center;">Daily Dasbodh Inspiration</h2>
                
                <div style="background-color: #f9f9f9; padding: 20px; border-radius: 10px; margin: 20px 0;">
                    <p style="font-size: 18px; line-height: 1.6; text-align: center; color: #333;">
                        ${shloka.shlok}
                    </p>
                </div>
                
                <div style="text-align: center; color: #666; font-style: italic;">
                    <p><strong>Dashak:</strong> ${shloka.dashak}</p>
                    <p><strong>Samas:</strong> ${shloka.samas}</p>
                </div>
                
                <hr style="margin: 30px 0;">
                <p style="text-align: center; color: #999; font-size: 12px;">
                    This is your daily Dasbodh shloka. 
                    <a href="#">Unsubscribe</a> | 
                    <a href="#">View in browser</a>
                </p>
            </body>
            </html>
        `;
        
        // Send to all subscribers
        for (const email of subscribers) {
            await transporter.sendMail({
                from: process.env.EMAIL_USER,
                to: email,
                subject: `Daily Dasbodh Shloka - ${new Date().toLocaleDateString()}`,
                html: htmlContent
            });
        }
        
        console.log(`Daily shloka sent to ${subscribers.length} subscribers`);
        
    } catch (error) {
        console.error('Error sending daily shloka:', error);
    }
}

// Schedule to run every day at 7 AM
cron.schedule('0 7 * * *', sendDailyShloka);

console.log('Daily shloka email service is running...');
```

## 📊 Analytics and Tracking

### 8. Usage Analytics Integration

```javascript
// Google Analytics tracking for API usage
function trackAPIUsage(endpoint, parameters = {}) {
    if (typeof gtag !== 'undefined') {
        gtag('event', 'api_call', {
            'custom_parameter': endpoint,
            'chapter': parameters.chapter || null,
            'paragraph': parameters.paragraph || null
        });
    }
}

// Usage in your application
async function getRandomShlokaWithTracking() {
    trackAPIUsage('getshlok');
    
    const response = await fetch(`${API_BASE}/api/getshlok?code=${FUNCTION_KEY}`);
    const shloka = await response.json();
    
    return shloka;
}
```

## 🔒 Security Best Practices

### 9. API Key Management

```javascript
// Environment-based configuration
const config = {
    apiBase: process.env.NODE_ENV === 'production' 
        ? 'https://dasbodh.azurewebsites.net'
        : 'http://localhost:7071',
    functionKey: process.env.DASBODH_API_KEY,
    timeout: 10000
};

// Secure API client
class DasbodhClient {
    constructor(config) {
        this.apiBase = config.apiBase;
        this.functionKey = config.functionKey;
        this.timeout = config.timeout;
    }
    
    async makeRequest(endpoint, params = {}) {
        const url = new URL(`${this.apiBase}${endpoint}`);
        url.searchParams.append('code', this.functionKey);
        
        Object.keys(params).forEach(key => {
            url.searchParams.append(key, params[key]);
        });
        
        const controller = new AbortController();
        const timeoutId = setTimeout(() => controller.abort(), this.timeout);
        
        try {
            const response = await fetch(url.toString(), {
                signal: controller.signal
            });
            
            if (!response.ok) {
                throw new Error(`HTTP error! status: ${response.status}`);
            }
            
            return await response.json();
        } finally {
            clearTimeout(timeoutId);
        }
    }
    
    async getRandomShloka() {
        return this.makeRequest('/api/getshlok');
    }
    
    async getChapterShlokas(chapter) {
        return this.makeRequest('/api/getshlockbydashak', { chapter });
    }
    
    async getSpecificShloka(chapter, paragraph) {
        return this.makeRequest('/api/getshlokbydashak', { chapter, paragraph });
    }
}

// Usage
const client = new DasbodhClient(config);
const shloka = await client.getRandomShloka();
```

These examples demonstrate various ways to integrate the Dasbodh API into different types of applications, from simple web pages to complex backend services. Each example includes error handling, best practices, and can be adapted to specific requirements.