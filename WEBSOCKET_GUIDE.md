# WebSocket Integration Guide

## Overview

The `client/index.html` file now includes WebSocket functionality that allows external applications (like BetaAlphaMonitor) to trigger YouTube video opening remotely.

## How It Works

### Client Side (Browser)
The web application automatically connects to a WebSocket server at `ws://localhost:8080` when the page loads. The connection status is displayed in the WebSocket Status panel.

### Server Side Integration
Your WebSocket server should send JSON messages in this format:

```javascript
{
  "type": "run_function",
  "videoType": "PMR"  // or "BMR" for different videos
}
```

### Example Server Code
```javascript
const WebSocket = require('ws');
const wss = new WebSocket.Server({ port: 8080 });

function yourFunction() {
  console.log('Function has been triggered by BetaAlphaMonitor!');
  // Place your logic here
}

wss.on('connection', function connection(ws) {
  ws.on('message', function incoming(message) {
    try {
      const data = JSON.parse(message);
      if (data.type === 'run_function') {
        yourFunction();
      }
    } catch (err) {
      console.error('Invalid message:', message);
    }
  });
  
  console.log('Client connected');
  
  // Example: Send message to trigger YouTube video
  ws.send(JSON.stringify({
    type: 'run_function',
    videoType: 'PMR'
  }));
});
```

## Features

- **Automatic Connection**: Connects to WebSocket server on page load
- **Auto-Reconnection**: Attempts to reconnect up to 5 times if connection is lost
- **Status Indicator**: Shows current connection status (Connected/Disconnected/Reconnecting)
- **Manual Controls**: Reconnect button and test functionality
- **Configurable URL**: Change WebSocket server URL via the UI
- **Video Type Support**: Specify 'PMR' or 'BMR' to open different YouTube videos

## Configuration

You can change the WebSocket server URL by:
1. Modifying the input field in the WebSocket Status panel
2. Clicking "Update URL" button
3. Or editing the `wsUrl` variable in the JavaScript code

## Testing

Use the "Test YouTube Trigger" button to simulate receiving a WebSocket message without needing an actual server connection.

## Error Handling

The implementation includes robust error handling for:
- Connection failures
- Invalid JSON messages
- Network timeouts
- Server disconnections

All errors are logged to the browser console for debugging.