# Neologik Integration Examples

This directory contains working examples of all three integration methods documented in [WEB_EMBED_GUIDE.md](../docs/WEB_EMBED_GUIDE.md).

## Quick Start

### Local Testing

1. Start a local web server in this directory:
   ```bash
   python -m http.server 8080
   ```

2. Open examples in your browser:
   - Method 1 (iframe): http://localhost:8080/method1-iframe-embed.html
   - Method 2 (Chat Button): http://localhost:8080/method2-chat-button.html
   - Method 3 (Web Chat SDK): http://localhost:8080/method3-webchat-sdk.html

## Examples

### Method 1: iframe Embedding
**File:** `method1-iframe-embed.html`

- Simplest integration method
- Fixed position in bottom-right corner
- Fully responsive (full-screen on mobile)
- No JavaScript required

### Method 2: Interactive Chat Button
**File:** `method2-chat-button.html`

- User-triggered popup
- Toggle button with animation
- Less intrusive than always-visible iframe
- Minimal JavaScript required

### Method 3: Web Chat SDK
**File:** `method3-webchat-sdk.html`

- Full Direct Line SDK implementation
- Custom styling and branding
- Token management
- Error handling
- Most flexible but requires more setup

## Configuration

All examples use:
- **FQDN:** `{{FQDN}}`
- **API Version:** `v4`
- **Webchat Endpoint:** `/{{BOT_SHORTNAME}}/{{API_VERSION}}/webchat`
- **Token Endpoint:** `/{{BOT_SHORTNAME}}/{{API_VERSION}}/token`

To use with your own Neologik instance, replace `{{FQDN}}` with your FQDN throughout the examples.

## Browser Compatibility

All examples tested and working in:
- Chrome 90+
- Firefox 88+
- Safari 14+
- Edge 90+
- Mobile Safari (iOS 14+)
- Chrome Mobile (Android 10+)

## Notes

- These are production-ready examples
- CORS must be configured to allow your domain
- HTTPS required for production deployment
- User IDs are persisted in localStorage for conversation continuity
