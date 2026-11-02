# Neologik Web Integration Guide

## Overview

This guide describes how to add a Neologik agent to your website on Neologik Platform v5.1. Four integration methods are available. Methods 1 to 3 serve a Neologik agent configured for anonymous web chat. Method 4 serves a Neologik agent configured for signed-in users.

### Choosing a Method

| Method | The chat runs on | AI notice and label | Thumbs up and down | Styling |
|---|---|---|---|---|
| 1. iframe | The hosted page | Included | Included | **Web-chat styling** on the channel card |
| 2. Chat button | The hosted page | Included | Included | **Web-chat styling** on the channel card |
| 3. Web Chat SDK | Your page | Your page adds these. See [Telling Users They Are Talking to an AI](#telling-users-they-are-talking-to-an-ai) | Your page adds these. See [Feedback Thumbs](#feedback-thumbs-under-each-answer) | Your page |
| 4. Signed-in, hosted page | The hosted page | Included | Included | **Web-chat styling** on the channel card |
| 4. Signed-in, tokens from your backend | Your page | Your page adds these | Your page adds these | Your page |

**Working Examples:** Example files for Methods 1 to 3 are in the `/examples` directory of this repository. Replace the placeholders, then copy them into your website.

**Quick Verification:** After determining your API version, verify your Neologik instance is accessible by navigating to `https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat` in your browser (e.g., `https://neologik.yourcompany.com/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat`). You should see the Neologik chat interface load successfully.

---

## Prerequisites

Before proceeding with integration, ensure you have obtained the following information from your Neologik administrator:

**Required Configuration:**
- **{{FQDN}}** - Your Neologik fully qualified domain name (FQDN) (e.g., `neologik.yourcompany.com`)
- **{{CHANNEL_PATH}}** - The Neologik agent's web channel path. Your administrator reads the path from the channel card on the Neologik agent's **Channels** tab in the Neologik Admin Tool
- **{{API_VERSION}}** - The API version of the Neologik agent's channel (e.g., `v5`) - **See "Finding Your API Version" below**
- **Your Web Domain** - Your website's domain, which your administrator adds to **Embed origins** on the same channel card (e.g., `https://www.yourcompany.com`)

### Finding Your API Version

Before integrating, read the API version of the Neologik agent's channel:

**Method 1: Version Endpoint (Recommended)**
```bash
curl https://{{FQDN}}/{{CHANNEL_PATH}}/version
```

Response:
```json
{
  "api_version": "v5",
  "build_version": "v5.1.12",
  "deployment_date": "2026-11-02T..."
}
```

Use the `api_version` value (e.g., `v5`) in all endpoint URLs below.

**Method 2: Browser Check**

Navigate to `https://{{FQDN}}/{{CHANNEL_PATH}}/version` in your browser and note the `api_version` field.

**Important Security Requirements:**
1. Your website's domain must be listed under **Embed origins** on the channel card. After saving the list, your administrator presses **Re-provision** on the same card
2. Your website must use HTTPS in production (HTTP is allowed for local testing only)
3. Ask your Neologik administrator to add your domain before you start the integration

**Embed Origin Examples:**
- Development: `http://localhost:8080`
- Staging: `https://staging.yourcompany.com`
- Production: `https://www.yourcompany.com`

---

## Integration Method 1: iframe Embedding

**Recommended for:** Straightforward integration requiring minimal technical configuration. Compatible with all web platforms and content management systems.

**Advantages:**
- No JavaScript knowledge required
- Works in any CMS (WordPress, Shopify, Wix, Squarespace, etc.)
- Isolated from your page's CSS and JavaScript
- Quickest to implement (under 5 minutes)
- Zero ongoing maintenance

**Use Cases:** Simple blogs, marketing sites, documentation pages, landing pages

### Step 1: Add this HTML to your page

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Website with Neologik</title>
    <style>
        /* Chat window */
        #neo-bot-container {
            position: fixed;
            bottom: 20px;
            right: 20px;
            width: 400px;
            height: 600px;
            border: none;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
            border-radius: 8px;
            z-index: 9999;
        }
        
        /* Responsive on mobile */
        @media (max-width: 768px) {
            #neo-bot-container {
                width: 100%;
                height: 100%;
                bottom: 0;
                right: 0;
                border-radius: 0;
            }
        }
    </style>
</head>
<body>
    <h1>My Website</h1>
    <p>Your content here...</p>
    
    <!-- Neologik iframe -->
    <iframe 
        id="neo-bot-container"
        src="https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat"
        allow="microphone; camera"
        title="Neologik Assistant">
    </iframe>
</body>
</html>
```

### Step 2: Adjust the Position

```css
/* Bottom-left corner */
#neo-bot-container {
    bottom: 20px;
    left: 20px;  /* Change from right to left */
    right: auto;
}

/* Sidebar embed */
#neo-bot-container {
    position: relative;
    width: 100%;
    height: 800px;
    box-shadow: none;
}
```

---

## Integration Method 2: Interactive Chat Button

**Recommended for:** User-initiated interaction model with minimal visual footprint until activated.

```html
<!DOCTYPE html>
<html>
<head>
    <title>My Website</title>
    <style>
        /* Chat button */
        #chat-button {
            position: fixed;
            bottom: 20px;
            right: 20px;
            width: 60px;
            height: 60px;
            border-radius: 50%;
            background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
            border: none;
            cursor: pointer;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
            z-index: 9999;
            color: white;
            font-size: 15px;
        }
        
        #chat-button:hover {
            transform: scale(1.1);
            transition: transform 0.2s;
        }
        
        /* Chat container - hidden by default */
        #neo-bot-container {
            display: none;
            position: fixed;
            bottom: 100px;
            right: 20px;
            width: 400px;
            height: 600px;
            border: none;
            box-shadow: 0 4px 20px rgba(0,0,0,0.3);
            border-radius: 8px;
            z-index: 9998;
        }
        
        #neo-bot-container.show {
            display: block;
        }
        
        @media (max-width: 768px) {
            #neo-bot-container {
                width: 100%;
                height: 100%;
                bottom: 0;
                right: 0;
                border-radius: 0;
            }
        }
    </style>
</head>
<body>
    <h1>My Website</h1>
    
    <!-- Chat button -->
    <button id="chat-button" onclick="toggleChat()" title="Neologik Assistant">
        Chat
    </button>
    
    <!-- Chat iframe (hidden initially) -->
    <iframe 
        id="neo-bot-container"
        src="https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat"
        allow="microphone; camera"
        title="Neologik Assistant">
    </iframe>
    
    <script>
        function toggleChat() {
            const container = document.getElementById('neo-bot-container');
            container.classList.toggle('show');
        }
    </script>
</body>
</html>
```

---

## Integration Method 3: Web Chat SDK

**Recommended for:** Advanced implementations that need full control of the chat's appearance. Suitable for modern JavaScript frameworks (React, Vue.js, Angular) and custom web applications.

**Note:** This method uses the Neologik agent's public token endpoint, which serves anonymous web chat only. If the token request is refused, the Neologik agent is configured for signed-in users, so use Method 4 below.

**Your page draws the chat, so your page tells users they are talking to an AI.** Add the notice and the label from [Telling Users They Are Talking to an AI](#telling-users-they-are-talking-to-an-ai) before your page goes live.

### Step 1: Add Web Chat SDK

```html
<!DOCTYPE html>
<html>
<head>
    <title>Neologik Integration</title>
    <style>
        #webchat {
            height: 600px;
            width: 400px;
            border: 1px solid #ddd;
            border-radius: 8px;
        }
    </style>
</head>
<body>
    <div id="webchat" role="main"></div>
    
    <!-- Web Chat SDK -->
    <script src="https://cdn.botframework.com/botframework-webchat/latest/webchat.js"></script>
    
    <script>
        (async function() {
            // Get a Direct Line token from the Neologik agent's channel
            const tokenResponse = await fetch('https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/token', {
                method: 'POST',
                headers: { 'Content-Type': 'application/json' },
                body: JSON.stringify({ 
                    user_id: 'dl_' + generateUUID() 
                })
            });
            
            const tokenData = await tokenResponse.json();
            
            // Initialize Web Chat
            const directLine = window.WebChat.createDirectLine({ 
                token: tokenData.token 
            });
            
            window.WebChat.renderWebChat({
                directLine: directLine,
                userID: 'dl_' + generateUUID(),
                styleOptions: {
                    // Colours
                    botAvatarInitials: 'NB',
                    userAvatarInitials: 'You',
                    bubbleBackground: '#667eea',
                    bubbleFromUserBackground: '#764ba2',
                    bubbleFromUserTextColor: 'white'
                }
            }, document.getElementById('webchat'));
            
            // Helper function to generate user ID
            function generateUUID() {
                return 'xxxxxxxx-xxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function(c) {
                    const r = Math.random() * 16 | 0;
                    const v = c === 'x' ? r : (r & 0x3 | 0x8);
                    return v.toString(16);
                });
            }
        })();
    </script>
</body>
</html>
```

---

## Integration Method 4: Signed-In (Authenticated) Embedding

**Recommended for:** Applications whose users sign in with Microsoft Entra ID, where the Neologik agent must know who it is talking to. Examples are internal portals, dashboards and line-of-business applications.

Some Neologik agents are configured for **signed-in users only**. These Neologik agents expose no public token endpoint: an anonymous visitor can never reach them, and the connection secret never appears in a browser. Two routes are available.

### Route A: The Hosted Signed-In Page

The hosted page signs the user in by itself, so this route needs no secret and no backend work. Embed the page in an iframe, exactly as in Method 1:

```html
<iframe
    src="https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat"
    title="Neologik Assistant">
</iframe>
```

The page signs the user in silently when a Microsoft session exists in the browser, and with a sign-in window otherwise. The chat then runs as that user. Your page's domain must be listed under **Embed origins**.

### Route B: Tokens From Your Backend

Use this route when your own page draws the chat.

1. Your user signs in to **your** application as normal (for example via Microsoft Entra ID).
2. Your application's **backend** requests a chat token from the Direct Line token service, using a connection secret that is held server-side only:

   ```
   POST https://directline.botframework.com/v3/directline/tokens/generate
   Authorization: Bearer <connection secret - server-side only>
   Content-Type: application/json

   { "user": { "id": "dl_<stable-user-id>" } }
   ```

   The `<stable-user-id>` should be derived from the signed-in user's directory identity (for example, a hash of their Entra object ID) so the same person always resumes as the same chat user.
3. Your backend returns **only the token** to the page - never the secret.
4. The page initialises Web Chat with that token (as in Method 3, fetching the token from your own backend endpoint).

### Backend Token Endpoint Example (Python)

```python
import hashlib
import requests

DIRECTLINE_GENERATE_URL = "https://directline.botframework.com/v3/directline/tokens/generate"

def mint_chat_token(user_object_id: str, connection_secret: str) -> dict:
    """Mint a user-scoped Direct Line token for the signed-in user.

    connection_secret comes from your server-side secret store (for
    example Azure Key Vault) - it must never reach the browser.
    """
    user_id = "dl_" + hashlib.sha256(user_object_id.encode()).hexdigest()[:32]
    response = requests.post(
        DIRECTLINE_GENERATE_URL,
        headers={"Authorization": f"Bearer {connection_secret}"},
        json={"user": {"id": user_id}},
        timeout=10,
    )
    response.raise_for_status()
    data = response.json()
    return {"token": data["token"], "userId": user_id}
```

Expose this behind an endpoint in your application (for example `POST /chat-token`) that requires your normal user authentication. The page then initialises Web Chat:

```javascript
const response = await fetch('/chat-token', { method: 'POST' });
const { token, userId } = await response.json();

window.WebChat.renderWebChat({
    directLine: window.WebChat.createDirectLine({ token }),
    userID: userId
}, document.getElementById('webchat'));
```

Your page draws the chat on this route, so add the notice and the label from [Telling Users They Are Talking to an AI](#telling-users-they-are-talking-to-an-ai).

### Obtaining the Connection Secret

The connection secret is provisioned and delivered by Neologik:

- Ask your Neologik administrator to nominate your application's Azure Key Vault on the Neologik agent's web channel configuration.
- Neologik's channel provisioning then writes the secret into your Key Vault and keeps it up to date automatically - including when the secret is rotated. No secrets are exchanged by hand.
- Your application reads the secret from its own Key Vault at runtime (using a managed identity is recommended).

### Security Requirements

1. **Server-side only:** the connection secret must never be embedded in a page, a client bundle, or client-side configuration.
2. **Authenticated endpoint:** your token endpoint must require your application's normal user sign-in, because that endpoint is the way in to the Neologik agent.
3. **Stable pseudonymous IDs:** derive the chat user ID from the user's directory identity; do not send names or email addresses as the chat user ID.
4. **Embed origins:** as with all methods, your page's domain must be listed under **Embed origins** on the channel card before the embedded chat connects.

---

## Telling Users They Are Talking to an AI

People must know when they are talking to an AI system, as Article 50 of the EU AI Act requires. The hosted page does this for you: it shows the notice "AI assistant. Replies are AI generated." and an "AI generated" label under each reply.

**When your own page draws the chat (Method 3, and Method 4 Route B), your page shows both.** The Neologik agent marks each reply as AI generated in the message data, and that marking alone does not tell the reader.

### Step 1: Add the Notice Above the Chat

```html
<div id="ai-notice" role="note">AI assistant. Replies are AI generated.</div>
<div id="webchat" role="main"></div>
```

### Step 2: Add the Label Under Each Reply

```css
#ai-notice {
    padding: 4px 12px;
    font-size: 12px;
    color: #555;
    background: #F7F9FC;
    border-bottom: 1px solid #E3E8EF;
}

#webchat .webchat__stacked-layout:not(.webchat__stacked-layout--from-user) .webchat__stacked-layout__content::after {
    content: "AI generated";
    display: block;
    padding: 2px 0 0 2px;
    font-size: 11px;
    color: #6B7280;
}
```

The example `examples/method3-webchat-sdk.html` holds both.

---

## Feedback Thumbs Under Each Answer

Each answer can carry a thumbs up, a thumbs down and a comment. The hosted page shows them for you. Your administrator reads each rating on the Neologik agent's **Feedback** tab in the Neologik Admin Tool.

When your own page draws the chat, your page sends an event named `feedback`:

```javascript
directLine.postActivity({
    from: { id: userId },
    type: 'event',
    name: 'feedback',
    value: {
        reaction: 'like',            // or 'dislike'
        feedbackText: '',            // the comment; send a second event to add one
        ratedActivityId: activity.id // the id of the answer the user rated
    }
}).subscribe();
```

To show the thumbs under each answer, load React before the Web Chat SDK:

```html
<script crossorigin="anonymous" src="https://unpkg.com/react@16.8.6/umd/react.production.min.js"></script>
<script crossorigin="anonymous" src="https://unpkg.com/react-dom@16.8.6/umd/react-dom.production.min.js"></script>
<script src="https://cdn.botframework.com/botframework-webchat/4.18.0/webchat.js"></script>
```

Then render Web Chat with an `activityMiddleware`. Only the Neologik agent's answers carry thumbs: these are the messages that hold the entity `https://schema.org/Message`. The welcome message does not.

```javascript
const React = window.React;

const FeedbackBar = function (props) {
    const [stage, setStage] = React.useState('idle');
    const [reaction, setReaction] = React.useState('');
    const [comment, setComment] = React.useState('');
    const send = function (chosen, text) {
        directLine.postActivity({
            from: { id: userId, name: 'User' },
            type: 'event',
            name: 'feedback',
            value: { reaction: chosen, feedbackText: text || '', ratedActivityId: props.activityId }
        }).subscribe(function () {}, function (err) { console.warn('Feedback not sent', err); });
    };
    const rate = function (chosen) {
        setReaction(chosen);
        setStage('commenting');
        send(chosen, '');
    };
    if (stage === 'done') {
        return React.createElement('div', null, 'Thanks for your feedback');
    }
    const buttons = React.createElement('div', null,
        React.createElement('button', { 'aria-label': 'Thumbs up', title: 'Good answer', onClick: function () { rate('like'); } }, '\uD83D\uDC4D'),
        React.createElement('button', { 'aria-label': 'Thumbs down', title: 'Poor answer', onClick: function () { rate('dislike'); } }, '\uD83D\uDC4E'));
    const commentBox = stage === 'commenting' ? React.createElement('div', null,
        React.createElement('input', {
            type: 'text', value: comment, maxLength: 1000, placeholder: 'Add a comment',
            onChange: function (e) { setComment(e.target.value); }
        }),
        React.createElement('button', { onClick: function () { send(reaction, comment); setStage('done'); } }, 'Send'),
        React.createElement('button', { onClick: function () { setStage('done'); } }, 'Skip')) : null;
    return React.createElement('div', null, buttons, commentBox);
};

const isRatedAnswer = function (activity) {
    return activity && activity.type === 'message' && activity.from && activity.from.role === 'bot'
        && (activity.entities || []).some(function (e) { return e && e.type === 'https://schema.org/Message'; });
};

const activityMiddleware = function () {
    return function (next) {
        return function (...setupArgs) {
            const card = setupArgs[0];
            const render = next(...setupArgs);
            if (!render || !isRatedAnswer(card.activity)) { return render; }
            return function (...renderArgs) {
                return React.createElement(React.Fragment, { key: card.activity.id },
                    render(...renderArgs),
                    React.createElement(FeedbackBar, { activityId: card.activity.id }));
            };
        };
    };
};

window.WebChat.renderWebChat({ directLine, userID: userId, activityMiddleware }, document.getElementById('webchat'));
```

---

## Platform-Specific Implementation Examples

### WordPress

```html
<!-- Add to custom HTML block or theme footer -->
<div id="neo-bot-widget"></div>
<script>
    (function() {
        const iframe = document.createElement('iframe');
        iframe.src = 'https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat';
        iframe.style.cssText = 'position:fixed;bottom:20px;right:20px;width:400px;height:600px;border:none;box-shadow:0 4px 20px rgba(0,0,0,0.3);border-radius:8px;z-index:9999;';
        iframe.allow = 'microphone; camera';
        iframe.title = 'Neologik Assistant';
        document.body.appendChild(iframe);
    })();
</script>
```

### React

```jsx
import React, { useEffect, useRef } from 'react';

function NeoBotWidget() {
    const webchatRef = useRef(null);
    
    useEffect(() => {
        const loadWebChat = async () => {
            // Load Web Chat SDK
            const script = document.createElement('script');
            script.src = 'https://cdn.botframework.com/botframework-webchat/latest/webchat.js';
            script.async = true;
            document.body.appendChild(script);
            
            script.onload = async () => {
                // Get token
                const response = await fetch('https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/token', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ user_id: 'dl_' + crypto.randomUUID() })
                });
                
                const { token } = await response.json();
                
                // Render Web Chat
                window.WebChat.renderWebChat({
                    directLine: window.WebChat.createDirectLine({ token }),
                    userID: 'dl_' + crypto.randomUUID(),
                    styleOptions: {
                        bubbleBackground: '#667eea',
                        bubbleFromUserBackground: '#764ba2'
                    }
                }, webchatRef.current);
            };
        };
        
        loadWebChat();
    }, []);
    
    return <div ref={webchatRef} style={{ height: '600px', width: '400px' }} />;
}

export default NeoBotWidget;
```

### Vue.js

```vue
<template>
    <div ref="webchat" style="height: 600px; width: 400px;"></div>
</template>

<script>
export default {
    name: 'NeoBotWidget',
    mounted() {
        this.loadWebChat();
    },
    methods: {
        async loadWebChat() {
            // Load SDK
            const script = document.createElement('script');
            script.src = 'https://cdn.botframework.com/botframework-webchat/latest/webchat.js';
            document.body.appendChild(script);
            
            script.onload = async () => {
                // Get token
                const response = await fetch('https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/token', {
                    method: 'POST',
                    headers: { 'Content-Type': 'application/json' },
                    body: JSON.stringify({ user_id: 'dl_' + crypto.randomUUID() })
                });
                
                const { token } = await response.json();
                
                // Render
                window.WebChat.renderWebChat({
                    directLine: window.WebChat.createDirectLine({ token }),
                    userID: 'dl_' + crypto.randomUUID()
                }, this.$refs.webchat);
            };
        }
    }
}
</script>
```

### Angular

```typescript
import { Component, OnInit, ViewChild, ElementRef } from '@angular/core';

@Component({
  selector: 'app-neo-bot',
  template: '<div #webchat style="height: 600px; width: 400px;"></div>'
})
export class NeoBotComponent implements OnInit {
  @ViewChild('webchat', { static: true }) webchatRef!: ElementRef;
  
  async ngOnInit() {
    // Load SDK
    const script = document.createElement('script');
    script.src = 'https://cdn.botframework.com/botframework-webchat/latest/webchat.js';
    document.body.appendChild(script);
    
    script.onload = async () => {
      // Get token
      const response = await fetch('https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/token', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify({ user_id: 'dl_' + crypto.randomUUID() })
      });
      
      const data = await response.json();
      
      // Render
      (window as any).WebChat.renderWebChat({
        directLine: (window as any).WebChat.createDirectLine({ token: data.token }),
        userID: 'dl_' + crypto.randomUUID()
      }, this.webchatRef.nativeElement);
    };
  }
}
```

---

## Customisation Options

### The Hosted Page (Methods 1, 2 and 4 Route A)

Your administrator sets the colours, the font family and the font files under **Web-chat styling** on the channel card. Your embed code does not change.

### Your Own Page (Method 3 and Method 4 Route B)

```javascript
styleOptions: {
    // Brand colors
    accent: '#667eea',
    backgroundColor: 'white',
    
    // Neologik agent appearance
    botAvatarInitials: 'NB',
    botAvatarBackgroundColor: '#667eea',
    
    // User appearance  
    userAvatarInitials: 'You',
    userAvatarBackgroundColor: '#764ba2',
    
    // Message bubbles
    bubbleBackground: '#667eea',
    bubbleTextColor: 'white',
    bubbleFromUserBackground: '#764ba2',
    bubbleFromUserTextColor: 'white',
    
    // Send box
    sendBoxBackground: 'white',
    sendBoxTextColor: '#333',
    sendBoxButtonColor: '#667eea'
}
```

### Size Options

```css
/* Compact widget */
#webchat {
    width: 350px;
    height: 500px;
}

/* Full page */
#webchat {
    width: 100%;
    height: 100vh;
}

/* Sidebar */
#webchat {
    width: 400px;
    height: 100vh;
    position: fixed;
    right: 0;
    top: 0;
}
```

---

## Security and Privacy Considerations

### Embed Origins

Your domain **must be listed under Embed origins** on the channel card. Ask your administrator to add:
- Production: `https://yourcompany.com`
- Staging: `https://staging.yourcompany.com`
- Development: `http://localhost:3000` (for testing)

### Content Security Policy

Add to your site's CSP headers:

```http
Content-Security-Policy: 
    frame-src https://{{FQDN}};
    connect-src https://{{FQDN}} https://directline.botframework.com;
    script-src https://cdn.botframework.com;
```

### User Privacy

- **Anonymous Mode**: Users are assigned random IDs (`dl_<uuid>`)
- **Signed-In Mode** (Method 4): Users are assigned a stable pseudonymous ID derived from their directory identity - no names or email addresses are used as chat IDs
- **Chat History**: Conversations are stored in your Neologik environment, against the chat user ID
- **Session Persistence**: User ID saved in localStorage for continuity
- **Secure Communication**: All traffic over HTTPS

---

## Browser and Device Compatibility

### Supported Browsers
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

### Responsive Design
The hosted page adapts to:
- Desktop (full widget)
- Tablet (optimized layout)
- Mobile (full-screen or compact)

---

## Analytics and Monitoring Integration

### Track Usage (Google Analytics Example)

```javascript
// Track when the chat is opened
window.gtag('event', 'bot_opened', {
    'event_category': 'engagement',
    'event_label': 'Neologik'
});

// Track messages sent
directLine.activity$
    .filter(activity => activity.from.role === 'user')
    .subscribe(() => {
        window.gtag('event', 'bot_message_sent', {
            'event_category': 'engagement'
        });
    });
```

---

## Troubleshooting Guide

### Common Issues and Solutions

#### iframe shows "404 Not Found"

**Cause:** Incorrect endpoint URL

**Solution:** Verify you're using the correct production endpoint with your API version
```html
<!-- CORRECT (replace {{API_VERSION}} with your version, e.g., v5) -->
<iframe src="https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat"></iframe>

<!-- INCORRECT - Do not use test endpoint -->
<iframe src="https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/test-chat.html"></iframe>
```

#### CORS Errors in Browser Console

**Cause:** Your domain is not listed under **Embed origins** on the channel card

**Solution:** Ask your Neologik administrator to add your domain and press **Re-provision**. Provide:
- Development domain: `http://localhost:8080` (if testing locally)
- Staging domain: `https://staging.yourcompany.com`
- Production domain: `https://www.yourcompany.com`

#### Chat Loads But Doesn't Connect

**Cause:** Token endpoint not accessible or returning errors

**Solution:** Test the token endpoint manually (replace {{API_VERSION}} with your version)
```javascript
fetch('https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/token', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ user_id: 'dl_test-' + Date.now() })
})
.then(r => r.json())
.then(d => console.log('Token received:', d))
.catch(e => console.error('Token error:', e));
```

#### iframe Cuts Off on Mobile

**Cause:** Missing responsive CSS

**Solution:** Add mobile-specific styles
```css
@media (max-width: 768px) {
    #neo-bot-container {
        width: 100% !important;
        height: 100% !important;
        bottom: 0 !important;
        right: 0 !important;
        border-radius: 0 !important;
    }
}
```

#### Blank or White Screen

**Cause:** HTTPS/SSL certificate issues

**Solution:** 
- Verify SSL certificate is valid
- Ensure no mixed content (HTTPS page loading HTTP resources)
- Check browser console for security errors

#### Styling Conflicts with Website

**Cause:** CSS rules affecting the iframe or webchat content

**Solution:**
- Use iframe method for complete isolation
- Increase z-index if chat appears behind page elements
- Check for CSS rules targeting iframe elements globally

### Quick Reference Table

| Symptom | Likely Cause | Solution |
|---------|--------------|----------|
| 404 error | Wrong endpoint | Use `/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat` (check version at `/{{CHANNEL_PATH}}/version`) |
| CORS error | Domain not in **Embed origins** | Ask your Neologik administrator to add the domain |
| Blank iframe | HTTPS/SSL issue | Check SSL certificate validity |
| No reply | Token expired | Tokens are valid for 1 hour, so refresh the page |
| Styling issues | CSS collision | Use iframe isolation method |
| Mobile display issues | Missing responsive CSS | Add mobile media queries |
| Connection timeout | Network/firewall | Check firewall allows Direct Line connections |

### Before Contacting Support

Gather the following information to expedite resolution:

1. **Your Configuration:**
   - FQDN: `https://your-instance.domain.com`
   - Your website domain
   
2. **Browser Console Errors:**
   - Open Developer Tools (F12)
   - Navigate to Console tab
   - Copy any red error messages

3. **Network Tab Information:**
   - Open Developer Tools (F12)
   - Navigate to Network tab
   - Screenshot failed requests (red entries)

4. **Additional Details:**
   - Browser and version
   - Operating system
   - Screenshot of the issue
   - Steps to reproduce

**Note:** Providing this information helps support resolve issues significantly faster.

---

## Implementation Best Practices

### Performance Optimization

1. **Lazy Loading:** Load the chat only when needed (e.g., on button click) to improve initial page load time
2. **Asynchronous Loading:** Do not block page rendering while the chat starts
3. **CDN Usage:** Web Chat SDK is served from Microsoft's CDN for optimal performance
4. **Caching:** The browser caches the SDK and assets after the first load

### Accessibility

1. **ARIA Labels:** Include proper `title` and `aria-label` attributes on iframes and buttons
2. **Keyboard Navigation:** Ensure chat button and interface are keyboard accessible
3. **Screen Readers:** Web Chat SDK includes built-in screen reader support
4. **Focus Management:** Properly manage focus when opening/closing chat

### Security

1. **HTTPS Required:** Always use HTTPS in production environments
2. **No Client Secrets:** Never expose API keys or secrets in client-side code
3. **Token Expiry:** Direct Line tokens expire after 1 hour for security
4. **User Privacy:** User IDs stored in localStorage, no cookies set

### Error Handling

1. **Network Failures:** Implement fallback UI or retry logic
2. **Token Errors:** Show user-friendly error messages
3. **Connection Issues:** Provide clear feedback when the chat is unavailable
4. **Logging:** Log errors to your analytics platform for monitoring

### Testing Strategy

1. **Cross-Browser:** Test on Chrome, Firefox, Safari, Edge
2. **Mobile Devices:** Test on iOS and Android devices
3. **Network Conditions:** Test on slow connections (3G simulation)
4. **Error Scenarios:** Test with network disabled, invalid tokens, etc.

---

## Framework-Specific Implementation Notes

### WordPress

**Integration Method:** Use Custom HTML block or add to theme footer

**Common Issues:**
- Theme z-index conflicts: Adjust chat container z-index to 99999 or higher
- Page builders (Elementor, Beaver Builder): Fully compatible, use HTML widget

**Example:**
```html
<!-- Add to Appearance → Theme Editor → footer.php or use Custom HTML block -->
<!-- Replace {{API_VERSION}} with your actual API version (e.g., v5) -->
<script>
(function() {
    const iframe = document.createElement('iframe');
    iframe.src = 'https://{{FQDN}}/{{CHANNEL_PATH}}/{{API_VERSION}}/webchat';
    iframe.style.cssText = 'position:fixed;bottom:20px;right:20px;width:400px;height:600px;border:none;box-shadow:0 4px 20px rgba(0,0,0,0.3);border-radius:8px;z-index:99999;';
    document.body.appendChild(iframe);
})();
</script>
```

### React / Next.js

**Best Practices:**
- Load SDK after component mounts using `useEffect`
- For Server-Side Rendering (SSR), check `typeof window !== 'undefined'`
- Use empty dependency array to load once

**Common Issues:**
- SSR errors: Ensure SDK only loads client-side
- Component unmounting: Clean up SDK instance

**Example:**
```jsx
useEffect(() => {
    if (typeof window !== 'undefined') {
        // Load SDK and initialize
    }
    return () => {
        // Cleanup
    };
}, []);
```

### Vue.js

**Best Practices:**
- Load SDK in `mounted()` lifecycle hook
- Store Direct Line instance in component data if needed
- Use `this.$refs` to access webchat container

**Common Issues:**
- Reactivity: Don't make SDK instance reactive
- Component cleanup: Disconnect Direct Line on unmount

### Angular

**Best Practices:**
- Load SDK in `ngOnInit()` or `ngAfterViewInit()`
- Use `@ViewChild` to access webchat container
- Create a reusable service for token management

**Common Issues:**
- Change detection: SDK updates don't trigger Angular CD
- Zone.js: SDK runs outside Angular zone

---

## Use Case Recommendations

Choose your integration method based on your website type:

### Simple Blog or Marketing Site
**Recommended:** Method 1 (iframe Embedding)
- Easiest implementation
- No maintenance required
- Works with any platform

### E-commerce or Customer Support Site
**Recommended:** Method 2 (Interactive Chat Button)
- Less intrusive than always-visible chat
- User-initiated interaction
- Better conversion rates

### Enterprise Application or Dashboard
**Recommended:** Method 4 (Signed-In Embedding) when your users authenticate; otherwise Method 3 (Web Chat SDK)
- Full control over styling and behavior
- Custom event handling
- Analytics integration
- The Neologik agent knows who it is talking to (Method 4)

### Internal Tool or Admin Panel
**Recommended:** Method 4 (Signed-In Embedding)
- Match the chat to your application
- Chat identity follows your existing user sign-in
- Advanced features and control

---

## Local Development and Testing

### Setting Up Local Test Environment

1. **Create a test HTML file** (e.g., `test-neologik.html`)

2. **Start a local web server:**
   ```bash
   # Python 3
   python -m http.server 8080
   
   # Node.js
   npx http-server -p 8080
   
   # PHP
   php -S localhost:8080
   ```

3. **Access your test page:**
   ```
   http://localhost:8080/test-neologik.html
   ```

4. **Important:** Ask your administrator to add `http://localhost:8080` to **Embed origins**

### Production Deployment Notes

**DNS and SSL:**
- Neologik requires valid HTTPS certificates for production
- Ensure SSL certificate is not self-signed or expired
- Mixed content (HTTPS page with HTTP iframe) will be blocked by browsers

**Performance:**
- Consider lazy-loading iframe after page load completes
- Use `loading="lazy"` attribute for iframe (modern browsers)
- Monitor performance impact using browser DevTools

**User Privacy:**
- User IDs stored in browser localStorage
- No third-party cookies set
- Conversations linked to user ID for continuity
- Users can reset identity by clearing localStorage

---

## Technical Support

**Questions?** Contact your Neologik administrator for:
- Adding your domain to **Embed origins**
- The channel path of a Neologik agent
- Advanced configuration
- Technical support

---

## Pre-Deployment Checklist

- [ ] Domain listed under **Embed origins** on the channel card
- [ ] HTTPS enabled on your website
- [ ] The AI notice and the "AI generated" label show on your page (Method 3 and Method 4 Route B)
- [ ] Code snippet added to website
- [ ] Tested on desktop and mobile
- [ ] CSP headers configured (if applicable)
- [ ] Analytics tracking added, where you use analytics
- [ ] Accessibility tested
- [ ] Error handling implemented

**Integration setup complete.**