---
title: "Streaming Responses"
date: 2025-07-09
layout: post
tags: [api, backend, performance, python, websockets, real-time, streaming]
author: Srikanth Malyala
---

With streaming responses becoming ubiquitous across applications today, I wanted to understand what they are and explore the various ways they can be implemented. This blog will explore the why, what, how, and when of streaming responses.

## What are Streaming Responses?
Streaming responses allow a server to send data to the client incrementally, as it becomes available, rather than waiting to generate the entire response before sending. This enables faster perceived response times, supports large payloads, and enables real-time data delivery.

## Why Use Streaming Responses?
- **Faster Time-to-First-Byte:** Clients start receiving data immediately.
- **Efficient for Large Data:** Avoids loading entire payloads into memory.
- **Real-Time Updates:** Enables server push, live feeds, and event streams.
- **Lower Latency:** Useful for APIs returning large files, logs, or progressive results.

## Common Use Cases
- Downloading large files (CSV, logs, media)
- Real-time dashboards and event feeds
- Server-Sent Events (SSE) and live notifications
- Progressive rendering of web pages or reports

## Streaming Response Examples: Flask vs Django vs FastAPI

{% raw %}
<div>
  <div style="margin-bottom: 1em;">
    <button onclick="showTab('flask-stream')" id="tab-flask-stream" class="tab-btn active">Flask</button>
    <button onclick="showTab('django-stream')" id="tab-django-stream" class="tab-btn">Django</button>
    <button onclick="showTab('fastapi-stream')" id="tab-fastapi-stream" class="tab-btn">FastAPI</button>
  </div>
  <div id="flask-stream" class="tab-content" style="display: block;">
    <pre><code class="language-python">
# Flask streaming response example
from flask import Flask, Response

def generate_large_csv():
    yield 'id,name\n'
    for i in range(1, 10001):
        yield f"{i},User {i}\n"

app = Flask(__name__)

@app.route('/download')
def download():
    return Response(generate_large_csv(), mimetype='text/csv')

if __name__ == '__main__':
    app.run(debug=True)
    </code></pre>
  </div>
  <div id="django-stream" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# Django streaming response example
from django.http import StreamingHttpResponse

def generate_large_csv():
    yield 'id,name\n'
    for i in range(1, 10001):
        yield f"{i},User {i}\n"

def download(request):
    response = StreamingHttpResponse(generate_large_csv(), content_type='text/csv')
    response['Content-Disposition'] = 'attachment; filename="users.csv"'
    return response
    </code></pre>
  </div>
  <div id="fastapi-stream" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# FastAPI streaming response example
from fastapi import FastAPI
from fastapi.responses import StreamingResponse
import io

def generate_large_csv():
    yield 'id,name\n'
    for i in range(1, 10001):
        yield f"{i},User {i}\n"

app = FastAPI()

@app.get("/download")
async def download():
    return StreamingResponse(
        generate_large_csv(),
        media_type="text/csv",
        headers={"Content-Disposition": "attachment; filename=users.csv"}
    )

# Alternative: Async generator for I/O bound operations
@app.get("/download-async")
async def download_async():
    async def generate_async_csv():
        yield 'id,name\n'
        for i in range(1, 10001):
            yield f"{i},User {i}\n"
    
    return StreamingResponse(
        generate_async_csv(),
        media_type="text/csv"
    )

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
    </code></pre>
  </div>
</div>
<script>
function showTab(tabId) {
  document.getElementById('flask-stream').style.display = (tabId === 'flask-stream') ? 'block' : 'none';
  document.getElementById('django-stream').style.display = (tabId === 'django-stream') ? 'block' : 'none';
  document.getElementById('fastapi-stream').style.display = (tabId === 'fastapi-stream') ? 'block' : 'none';
  document.getElementById('tab-flask-stream').classList.toggle('active', tabId === 'flask-stream');
  document.getElementById('tab-django-stream').classList.toggle('active', tabId === 'django-stream');
  document.getElementById('tab-fastapi-stream').classList.toggle('active', tabId === 'fastapi-stream');
}
</script>
<style>
.tab-btn {
  background: #eee;
  border: 1px solid #ccc;
  padding: 0.4em 1em;
  cursor: pointer;
  margin-right: 0.5em;
  border-radius: 4px 4px 0 0;
  font-weight: bold;
  color: #222;
  transition: background 0.2s, color 0.2s, box-shadow 0.2s, border 0.2s;
}
.tab-btn.active {
  background: #fff;
  border-bottom: 2.5px solid #4f8cff;
  border-top: 2.5px solid #4f8cff;
  border-left: 2.5px solid #4f8cff;
  border-right: 2.5px solid #4f8cff;
  color: #2563eb;
  box-shadow: 0 2px 8px rgba(79,140,255,0.08);
  z-index: 1;
}
.tab-content {
  border: 1px solid #ccc;
  border-top: none;
  padding: 1em;
  background: #fff;
  border-radius: 0 0 4px 4px;
  color: #222;
}
@media (prefers-color-scheme: dark) {
  .tab-btn {
    background: #23232b;
    border: 1px solid #444;
    color: #eee;
  }
  .tab-btn.active {
    background: #18181c;
    border-bottom: 2.5px solid #4f8cff;
    border-top: 2.5px solid #4f8cff;
    border-left: 2.5px solid #4f8cff;
    border-right: 2.5px solid #4f8cff;
    color: #60aaff;
    box-shadow: 0 2px 8px rgba(79,140,255,0.18);
    z-index: 1;
  }
  .tab-content {
    border: 1px solid #444;
    background: #18181c;
    color: #eee;
  }
}
</style>
{% endraw %}

## WebSocket Streaming Examples

WebSockets provide full-duplex communication channels, enabling real-time bidirectional data streaming between client and server.

{% raw %}
<div>
  <div style="margin-bottom: 1em;">
    <button onclick="showWSTab('flask-ws')" id="tab-flask-ws" class="tab-btn active">Flask</button>
    <button onclick="showWSTab('django-ws')" id="tab-django-ws" class="tab-btn">Django</button>
    <button onclick="showWSTab('fastapi-ws')" id="tab-fastapi-ws" class="tab-btn">FastAPI</button>
  </div>
  <div id="flask-ws" class="tab-content" style="display: block;">
    <pre><code class="language-python">
# Flask WebSocket with Flask-SocketIO
from flask import Flask
from flask_socketio import SocketIO, emit
import time
import threading

app = Flask(__name__)
app.config['SECRET_KEY'] = 'secret!'
socketio = SocketIO(app, cors_allowed_origins="*")

def background_task():
    """Background task that sends periodic updates"""
    count = 0
    while True:
        time.sleep(1)
        count += 1
        socketio.emit('status_update', 
                     {'data': f'Server time: {time.time()}, Count: {count}'},
                     namespace='/live')

@socketio.on('connect', namespace='/live')
def handle_connect():
    print('Client connected')
    emit('response', {'data': 'Connected to live stream'})

@socketio.on('request_data', namespace='/live')
def handle_request(json):
    print(f'Received request: {json}')
    emit('data_response', {'data': f'Response to: {json["message"]}'})

if __name__ == '__main__':
    # Start background task
    thread = threading.Thread(target=background_task)
    thread.daemon = True
    thread.start()
    
    socketio.run(app, debug=True, port=5000)
    </code></pre>
  </div>
  <div id="django-ws" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# Django WebSocket with Django Channels
# consumers.py
import json
import asyncio
from channels.generic.websocket import AsyncWebsocketConsumer
from datetime import datetime

class LiveStreamConsumer(AsyncWebsocketConsumer):
    async def connect(self):
        await self.accept()
        # Start background task for periodic updates
        self.background_task = asyncio.create_task(self.send_periodic_updates())
        
    async def disconnect(self, close_code):
        if hasattr(self, 'background_task'):
            self.background_task.cancel()
    
    async def receive(self, text_data):
        data = json.loads(text_data)
        message = data.get('message', '')
        
        # Echo back the message
        await self.send(text_data=json.dumps({
            'type': 'response',
            'message': f'Server received: {message}'
        }))
    
    async def send_periodic_updates(self):
        count = 0
        while True:
            try:
                await asyncio.sleep(1)
                count += 1
                await self.send(text_data=json.dumps({
                    'type': 'update',
                    'data': f'Server time: {datetime.now().isoformat()}, Count: {count}'
                }))
            except asyncio.CancelledError:
                break

# routing.py
from django.urls import re_path
from . import consumers

websocket_urlpatterns = [
    re_path(r'ws/live/$', consumers.LiveStreamConsumer.as_asgi()),
]

# settings.py additions
INSTALLED_APPS = [
    # ...
    'channels',
]

ASGI_APPLICATION = 'myproject.asgi.application'
CHANNEL_LAYERS = {
    'default': {
        'BACKEND': 'channels.layers.InMemoryChannelLayer'
    }
}
    </code></pre>
  </div>
  <div id="fastapi-ws" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# FastAPI WebSocket example
from fastapi import FastAPI, WebSocket, WebSocketDisconnect
import asyncio
import json
from datetime import datetime
from typing import List

app = FastAPI()

class ConnectionManager:
    def __init__(self):
        self.active_connections: List[WebSocket] = []
    
    async def connect(self, websocket: WebSocket):
        await websocket.accept()
        self.active_connections.append(websocket)
    
    def disconnect(self, websocket: WebSocket):
        self.active_connections.remove(websocket)
    
    async def send_personal_message(self, message: str, websocket: WebSocket):
        await websocket.send_text(message)
    
    async def broadcast(self, message: str):
        for connection in self.active_connections:
            try:
                await connection.send_text(message)
            except:
                # Remove failed connections
                self.active_connections.remove(connection)

manager = ConnectionManager()

@app.websocket("/ws/live")
async def websocket_endpoint(websocket: WebSocket):
    await manager.connect(websocket)
    
    # Start background task for this connection
    async def send_updates():
        count = 0
        while True:
            try:
                await asyncio.sleep(1)
                count += 1
                update = {
                    "type": "update",
                    "data": f"Server time: {datetime.now().isoformat()}, Count: {count}"
                }
                await manager.send_personal_message(json.dumps(update), websocket)
            except WebSocketDisconnect:
                break
    
    # Start the background task
    background_task = asyncio.create_task(send_updates())
    
    try:
        while True:
            # Listen for messages from client
            data = await websocket.receive_text()
            message_data = json.loads(data)
            
            # Echo response
            response = {
                "type": "response",
                "message": f"Server received: {message_data.get('message', '')}"
            }
            await manager.send_personal_message(json.dumps(response), websocket)
            
    except WebSocketDisconnect:
        background_task.cancel()
        manager.disconnect(websocket)
        print("Client disconnected")

# Optional: Broadcast endpoint
@app.post("/broadcast/{message}")
async def broadcast_message(message: str):
    await manager.broadcast(f"Broadcast: {message}")
    return {"message": "Message broadcast sent"}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="0.0.0.0", port=8000)
    </code></pre>
  </div>
</div>
<script>
function showWSTab(tabId) {
  document.getElementById('flask-ws').style.display = (tabId === 'flask-ws') ? 'block' : 'none';
  document.getElementById('django-ws').style.display = (tabId === 'django-ws') ? 'block' : 'none';
  document.getElementById('fastapi-ws').style.display = (tabId === 'fastapi-ws') ? 'block' : 'none';
  document.getElementById('tab-flask-ws').classList.toggle('active', tabId === 'flask-ws');
  document.getElementById('tab-django-ws').classList.toggle('active', tabId === 'django-ws');
  document.getElementById('tab-fastapi-ws').classList.toggle('active', tabId === 'fastapi-ws');
}
</script>
{% endraw %}

### WebSocket vs HTTP Streaming

| Feature | HTTP Streaming | WebSockets |
|---------|----------------|------------|
| **Direction** | Unidirectional (server → client) | Bidirectional |
| **Connection** | Request-response based | Persistent connection |
| **Use Cases** | File downloads, logs, SSE | Chat, real-time dashboards, gaming |
| **Overhead** | Lower (HTTP headers per request) | Higher initial, lower per message |
| **Browser Support** | Universal | Modern browsers |

## Best Practices
- Always set appropriate `Content-Type` and `Content-Disposition` headers
- Use generators to minimize memory usage
- Handle client disconnects gracefully
- Avoid long blocking operations in generators
- For real-time, consider chunked encoding or SSE
- **WebSocket specific:**
  - Implement proper connection management
  - Handle reconnections on client side
  - Use heartbeat/ping-pong for connection health
  - Implement authentication and authorization
  - Consider message queuing for reliability

## Summary Table
<table>
  <thead>
    <tr>
      <th>Framework</th>
      <th>HTTP Streaming</th>
      <th>WebSocket Support</th>
      <th>Memory Efficient</th>
      <th>Real-Time Capable</th>
      <th>Notes</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Flask</td>
      <td>Response (with generator)</td>
      <td>Flask-SocketIO</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Simple, flexible; requires extension for WebSockets</td>
    </tr>
    <tr>
      <td>Django</td>
      <td>StreamingHttpResponse</td>
      <td>Django Channels</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Supports file-like objects; ASGI for WebSockets</td>
    </tr>
    <tr>
      <td>FastAPI</td>
      <td>StreamingResponse</td>
      <td>Native WebSocket</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Built-in async support, native WebSocket support</td>
    </tr>
  </tbody>
</table>

**References:**
- [Flask: Streaming](https://flask.palletsprojects.com/en/latest/patterns/streaming/)
- [Flask-SocketIO: WebSocket Support](https://flask-socketio.readthedocs.io/en/latest/)
- [Django: StreamingHttpResponse](https://docs.djangoproject.com/en/stable/ref/request-response/#streaminghttpresponse)
- [Django Channels: WebSocket Support](https://channels.readthedocs.io/en/latest/)
- [FastAPI: StreamingResponse](https://fastapi.tiangolo.com/advanced/custom-response/#streamingresponse)
- [FastAPI: WebSockets](https://fastapi.tiangolo.com/advanced/websockets/)
- [Server-Sent Events (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events)
- [WebSocket API (MDN)](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket) 