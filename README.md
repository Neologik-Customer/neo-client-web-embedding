# Neologik Web Integration Guide

## Overview

This guide provides comprehensive instructions for integrating Neologik into your website. Three integration methods are available to accommodate different technical requirements and use cases.

**Working Examples:** Production-ready example files are available in the `/examples` directory of this repository. All examples have been tested and are ready to copy into your website.

**Quick Verification:** After determining your API version, verify your Neologik instance is accessible by navigating to `https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat` in your browser (e.g., `https://neologik.yourcompany.com/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat`). You should see the Neologik chat interface load successfully.

---

## Prerequisites

Before proceeding with integration, ensure you have obtained the following information from your Neologik administrator:

**Required Configuration:**
- **{{FQDN}}** - Your Neologik fully qualified domain name (FQDN) (e.g., `https://neologik.yourcompany.com`)
- **{{BOT_SHORTNAME}}** - Your Neologik bot name, as agreed with Neologik
- **{{API_VERSION}}** - Your bot's API version (e.g., `v4`) - **See "Finding Your API Version" below**
- **Your Web Domain** - (if different from the Neologik FQDN) Your website domain that must be whitelisted to access Neologik (e.g., `https://yourcompany.com`)

### Finding Your API Version

Before integrating, you need to determine your bot's API version:

**Method 1: Version Endpoint (Recommended)**
```bash
curl https://{{FQDN}}/{{BOT_SHORTNAME}}/version
```

Response:
```json
{
  "api_version": "v4",
  "build_version": "4.1.0",
  "deployment_date": "2026-01-17T..."
}
```

Use the `api_version` value (e.g., `v4`) in all endpoint URLs below.

**Method 2: Browser Check**

Navigate to `https://{{FQDN}}/{{BOT_SHORTNAME}}/version` in your browser and note the `api_version` field.

**Important Security Requirements:**
1. Your website's domain must be registered in the Neologik allowed origins configuration
2. Your website must use HTTPS in production (HTTP only allowed for local testing)
3. Contact your Neologik administrator to authorize your domain before attempting integration

**Domain Whitelisting Examples:**
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
        /* Full-screen bot */
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
        src="https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat"
        allow="microphone; camera"
        title="Neologik Assistant">
    </iframe>
</body>
</html>
```

### Step 2: Customize Position (Optional)

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

## Integration Method 2: Interactive Chat Button\n\n**Recommended for:** User-initiated interaction model with minimal visual footprint until activated.

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
            font-size: 24px;
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
        💬
    </button>
    
    <!-- Chat iframe (hidden initially) -->
    <iframe 
        id="neo-bot-container"
        src="https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat"
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

## Integration Method 3: Web Chat SDK\n\n**Recommended for:** Advanced implementations requiring comprehensive customization capabilities. Suitable for modern JavaScript frameworks (React, Vue.js, Angular) and custom web applications.

### Step 1: Add Web Chat SDK

```html
<!DOCTYPE html>
<html>
<head>
    <title>Custom Bot Integration</title>
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
            // Get Direct Line token from your bot
            const tokenResponse = await fetch('https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/token', {
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
                    // Customize colors
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

## Platform-Specific Implementation Examples

### WordPress

```html
<!-- Add to custom HTML block or theme footer -->
<div id="neo-bot-widget"></div>
<script>
    (function() {
        const iframe = document.createElement('iframe');
        iframe.src = 'https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat';
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
                const response = await fetch('https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/token', {
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
                const response = await fetch('https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/token', {
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
      const response = await fetch('https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/token', {
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

## Customization Options

### Color Themes

```javascript
styleOptions: {
    // Brand colors
    accent: '#667eea',
    backgroundColor: 'white',
    
    // Bot appearance
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

### CORS Configuration

Your domain **must be whitelisted**. Contact your administrator to add:
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
- **No PII Storage**: No personal data is stored without consent
- **Session Persistence**: User ID saved in localStorage for continuity
- **Secure Communication**: All traffic over HTTPS

---

## Browser and Device Compatibility

### Supported Browsers
- ✅ Chrome 90+
- ✅ Firefox 88+
- ✅ Safari 14+
- ✅ Edge 90+
- ✅ Mobile Safari (iOS 14+)
- ✅ Chrome Mobile (Android 10+)

### Responsive Design
The bot automatically adapts to:
- Desktop (full widget)
- Tablet (optimized layout)
- Mobile (full-screen or compact)

---

## Analytics and Monitoring Integration

### Track Bot Usage (Google Analytics Example)

```javascript
// Track when bot is opened
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
<!-- CORRECT (replace {{API_VERSION}} with your version, e.g., v4) -->
<iframe src="https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat"></iframe>

<!-- INCORRECT - Do not use test endpoint -->
<iframe src="https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/test-chat.html"></iframe>
```

#### CORS Errors in Browser Console

**Cause:** Domain not whitelisted in Neologik configuration

**Solution:** Contact your Neologik administrator to whitelist your domain. Provide:
- Development domain: `http://localhost:8080` (if testing locally)
- Staging domain: `https://staging.yourcompany.com`
- Production domain: `https://www.yourcompany.com`

#### Chat Loads But Doesn't Connect

**Cause:** Token endpoint not accessible or returning errors

**Solution:** Test the token endpoint manually (replace {{API_VERSION}} with your version)
```javascript
fetch('https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/token', {
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
| 404 error | Wrong endpoint | Use `/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat` (check version at `/{{BOT_SHORTNAME}}/version`) |
| CORS error | Domain not whitelisted | Contact Neologik administrator |
| Blank iframe | HTTPS/SSL issue | Check SSL certificate validity |
| No bot response | Token expired | Tokens valid for 1 hour, refresh page |
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

1. **Lazy Loading:** Load the bot only when needed (e.g., on button click) to improve initial page load time
2. **Asynchronous Loading:** Don't block page rendering while bot initializes
3. **CDN Usage:** Web Chat SDK is served from Microsoft's CDN for optimal performance
4. **Caching:** Browser will cache SDK and assets after first load (~500ms subsequent loads)

**Performance Metrics:**
- Initial load: 2-3 seconds
- Cached load: ~500ms
- SDK size: ~200KB (gzipped)

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
3. **Connection Issues:** Provide clear feedback when bot is unavailable
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
<!-- Replace {{API_VERSION}} with your actual API version (e.g., v4) -->
<script>
(function() {
    const iframe = document.createElement('iframe');
    iframe.src = 'https://{{FQDN}}/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat';
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
**Recommended:** Method 3 (Web Chat SDK)
- Full control over styling and behavior
- Custom event handling
- Analytics integration
- Can integrate with existing authentication

### Internal Tool or Admin Panel
**Recommended:** Method 3 (Web Chat SDK)
- Customize UX to match application
- Integrate with existing auth system
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

4. **Important:** Request `http://localhost:8080` to be whitelisted in Neologik CORS configuration

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

## Pre-Deployment Checklist

---

## Technical Support

**Questions?** Contact your Neologik administrator for:
- Domain whitelisting
- Custom bot URL
- Advanced configuration
- Technical support

---

## Pre-Deployment Checklist

- [ ] Domain whitelisted in bot configuration
- [ ] HTTPS enabled on your website
- [ ] Code snippet added to website
- [ ] Tested on desktop and mobile
- [ ] CSP headers configured (if applicable)
- [ ] Analytics tracking added (optional)
- [ ] Accessibility tested
- [ ] Error handling implemented

**Integration setup complete.**