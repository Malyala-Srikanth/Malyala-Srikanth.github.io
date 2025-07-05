---
title: "Rate Limiting"
date: 2025-07-05
layout: post
tags: [api, backend, security, python]
author: Srikanth Malyala
---

## What is Rate Limiting?
Rate limiting is a technique used to control the number of requests a user, client, or system can make to a resource—such as an API, website, or service—within a specified period of time. By enforcing these limits, you can prevent abuse, protect your infrastructure from overload, and ensure a fair experience for all users. Rate limiting is a fundamental part of API design, web security, and scalable system architecture.

## Why is Rate Limiting Important?
- **Prevents Abuse & Attacks:** Stops malicious actors or bots from overwhelming your service with excessive requests (e.g., brute-force, scraping, or denial-of-service attacks).
- **Protects Resources:** Shields your backend, databases, and third-party services from being overloaded, which can lead to downtime or increased costs.
- **Ensures Fairness:** Guarantees that all users have equitable access to your service, preventing a single user from monopolizing resources.
- **Improves Reliability:** Helps maintain consistent performance and availability, even under heavy load or unexpected traffic spikes.

## Common Algorithms
- **Fixed Window:** Simple counter per window (e.g., 100 requests/minute)
- **Sliding Window:** Tracks requests in a rolling window for smoother limiting
- **Token Bucket:** Allows bursts; tokens refill at a set rate
- **Leaky Bucket:** Processes requests at a steady rate; excess is dropped or queued

## Rate Limiting Examples: Flask (`limits`) vs Django

{% raw %}
<div>
  <div style="margin-bottom: 1em;">
    <button onclick="showTab('flask-limits')" id="tab-flask-limits" class="tab-btn active">Flask + limits</button>
    <button onclick="showTab('django-custom')" id="tab-django-custom" class="tab-btn">Django (django-ratelimit)</button>
  </div>
  <div id="flask-limits" class="tab-content" style="display: block;">
    <pre><code class="language-python">
# Flask + limits (with Redis)
from flask import Flask, request, jsonify
from limits import parse
from limits.storage import RedisStorage
from limits.strategies import FixedWindowRateLimiter

app = Flask(__name__)
storage = RedisStorage("redis://localhost:6379")
limiter = FixedWindowRateLimiter(storage)
rate_limit = parse("5/minute")  # 5 requests per minute

@app.route('/api')
def my_api():
    client_ip = request.remote_addr
    if not limiter.hit(rate_limit, client_ip):
        return jsonify({'error': 'Rate limit exceeded.'}), 429
    return jsonify({'message': 'Request successful!'})

if __name__ == '__main__':
    app.run(debug=True)
    </code></pre>
  </div>
  <div id="django-custom" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# Django rate limiting using django-ratelimit
# pip install django-ratelimit
from django.http import JsonResponse
from django.views.decorators.http import require_GET
from ratelimit.decorators import ratelimit

@require_GET
@ratelimit(key='ip', rate='5/m', block=True)
def my_api(request):
    return JsonResponse({'message': 'Request successful!'})

# For class-based views:
from django.utils.decorators import method_decorator
from django.views import View

@method_decorator(ratelimit(key='ip', rate='5/m', block=True), name='dispatch')
class MyApiView(View):
    def get(self, request):
        return JsonResponse({'message': 'Request successful!'})
    </code></pre>
  </div>
</div>
<script>
function showTab(tabId) {
  document.getElementById('flask-limits').style.display = (tabId === 'flask-limits') ? 'block' : 'none';
  document.getElementById('django-custom').style.display = (tabId === 'django-custom') ? 'block' : 'none';
  document.getElementById('tab-flask-limits').classList.toggle('active', tabId === 'flask-limits');
  document.getElementById('tab-django-custom').classList.toggle('active', tabId === 'django-custom');
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

## Best Practices
- Return HTTP 429 for rate limit violations
- Inform users of their limit status (headers or body)
- Use Redis or similar for distributed setups
- Use libraries like [Flask-Limiter](https://flask-limiter.readthedocs.io/en/stable/), [limits](https://limits.readthedocs.io/en/stable/), or [django-ratelimit](https://django-ratelimit.readthedocs.io/en/stable/)

## Summary Table
<table>
  <thead>
    <tr>
      <th>Method</th>
      <th>Allows Bursts</th>
      <th>Smooth</th>
      <th>Memory Usage</th>
      <th>Complexity</th>
      <th>Use Case</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Fixed Window</td>
      <td>No</td>
      <td>No</td>
      <td>Low</td>
      <td>Low</td>
      <td>Simple APIs</td>
    </tr>
    <tr>
      <td>Sliding Window Log</td>
      <td>No</td>
      <td>Yes</td>
      <td>High</td>
      <td>Medium</td>
      <td>High-accuracy APIs</td>
    </tr>
    <tr>
      <td>Sliding Window Counter</td>
      <td>No</td>
      <td>Yes</td>
      <td>Medium</td>
      <td>Medium</td>
      <td>Smoother limits</td>
    </tr>
    <tr>
      <td>Token Bucket</td>
      <td>Yes</td>
      <td>Yes</td>
      <td>Low</td>
      <td>Medium</td>
      <td>Most APIs</td>
    </tr>
    <tr>
      <td>Leaky Bucket</td>
      <td>No</td>
      <td>Yes</td>
      <td>Low</td>
      <td>Medium</td>
      <td>Steady flow needed</td>
    </tr>
    <tr>
      <td>Concurrent Limiting</td>
      <td>N/A</td>
      <td>N/A</td>
      <td>Low</td>
      <td>Low</td>
      <td>Resource protection</td>
    </tr>
    <tr>
      <td>Dynamic/Adaptive</td>
      <td>Varies</td>
      <td>Varies</td>
      <td>Varies</td>
      <td>High</td>
      <td>Large-scale platforms</td>
    </tr>
  </tbody>
</table>

**References:**
- [limits (Python rate limiting library)](https://limits.readthedocs.io/en/stable/)
- [Flask-Limiter](https://flask-limiter.readthedocs.io/en/stable/)
- [django-ratelimit](https://django-ratelimit.readthedocs.io/en/stable/) 