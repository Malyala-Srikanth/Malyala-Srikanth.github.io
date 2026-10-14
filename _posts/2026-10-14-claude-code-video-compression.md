---
title: "Exploring Video Compression: A Badminton Break Conversation and Deep Dive"
date: 2026-10-14
layout: post
tags: [video, compression, algorithms, python, image-processing, exploration]
author: Srikanth Malyala
---

Over the weekend, I was playing badminton with **[Lemon Reddy](https://www.linkedin.com/in/lemonreddy/)**, who works on video compression algorithms at Amazon Prime Video. During a break between sets, he was telling me about how he uses Claude Code to research and optimize compression algorithms—diving deep into papers, experimenting with different approaches, and working through complex constraints. His work fascinated me, and it got me curious: *How does video compression actually work? What are the mathematical principles behind it? Can I understand and implement these concepts myself?*

This blog is my exploration of that curiosity. I started wondering about the fundamentals and dove into understanding video compression from the ground up using Python. Here's what I discovered along the way.

## Why Video Compression Matters

Video is data-intensive. A single minute of 1080p video at 30fps can be several gigabytes without compression. Video compression tackles this challenge by:

1. **Reducing Storage**: Enabling practical storage and archiving of video content
2. **Enabling Streaming**: Making real-time video delivery over networks feasible
3. **Saving Bandwidth**: Critical for mobile and internet-constrained environments
4. **Preserving Quality**: Modern compression algorithms maintain visual quality while drastically reducing file size

Before diving into implementation, it's important to understand the fundamental concepts:
- The difference between **lossy** and **lossless** compression
- **Temporal redundancy**: Similarity between consecutive frames
- **Spatial redundancy**: Similarity within a single frame
- **Psychovisual redundancy**: Limitations of human vision perception

## Using Python Tools and Claude Code for Learning

When learning video compression, Python is an excellent starting language because:
- It has rich libraries for image and video processing (OpenCV, Pillow, NumPy)
- Code is readable and focuses on algorithms rather than low-level details
- You can quickly prototype and experiment with different approaches
- You can visualize results easily

**Claude Code can accelerate your learning**:

1. **Understanding Concepts**: Ask Claude to explain compression papers, algorithms, or techniques in simple terms
2. **Code Generation**: Get Python implementations of compression algorithms with explanations
3. **Debugging**: When your implementation doesn't work, Claude can help identify issues
4. **Optimization**: Once basics work, ask Claude for optimization suggestions
5. **Visualization**: Get help creating visualizations to understand compression effects

## Core Compression Concepts

Let's start with the fundamental concepts you need to understand video compression:

### 1. Lossy vs Lossless Compression

**Lossless Compression**: Perfect reconstruction of original data
- Used when every bit matters (medical imaging, archives)
- Lower compression ratios (typically 2-5x)
- Examples: PNG, FLAC audio

**Lossy Compression**: Accepts some data loss that's imperceptible to humans
- Used for streaming video and audio
- Higher compression ratios (10-100x or more)
- Examples: JPEG, MP4, H.265

Video streaming almost always uses lossy compression because:
- Perfect reconstruction is unnecessary for visual content
- Humans can't perceive certain frequency ranges and color information
- It achieves much better compression ratios

### 2. Temporal Redundancy

Most video frames are very similar to the previous frame. By exploiting this:

```
Key Frame: Store full frame (I-frame)
Predicted Frame: Store only differences from previous frame (P-frame)
```

This is why a scene with minimal motion compresses much better than one with lots of movement.

### 3. Spatial Redundancy

Within a single frame, nearby pixels have similar colors. Instead of storing each pixel individually:

- **Chroma Subsampling**: Store color at lower resolution than brightness
  - 4:4:4 = No subsampling (high quality, less compression)
  - 4:2:0 = Color at 1/4 resolution (standard for streaming)
  
- **Transform Coding**: Convert pixel blocks into frequency components where you can discard less important information

### 4. Chroma Subsampling
Human eyes are much more sensitive to brightness changes than color changes:
- **4:4:4**: Full resolution for luminance (Y) and chrominance (Cb, Cr)
- **4:2:2**: Half horizontal color resolution
- **4:2:0**: Half resolution in both dimensions (standard for most streaming)

### 3. Chroma Subsampling
Human eyes are more sensitive to brightness than color variations:
- **4:4:4**: Full resolution for luminance and chrominance (no subsampling)
- **4:2:2**: Half horizontal color resolution
- **4:2:0**: Half resolution in both directions (most common)

## Python Implementation: From Basics to Practice

Now let's implement these concepts in Python to solidify your understanding:

### Transform Coding Example
{% raw %}
<div>
  <div style="margin-bottom: 1em;">
    <button onclick="showCompressionTab('dct')" id="tab-dct" class="tab-btn active">DCT Transform</button>
    <button onclick="showCompressionTab('rle')" id="tab-rle" class="tab-btn">Run-Length Encoding</button>
    <button onclick="showCompressionTab('huffman')" id="tab-huffman" class="tab-btn">Huffman Coding</button>
  </div>
  
  <div id="dct" class="tab-content" style="display: block;">
    <pre><code class="language-python">
import numpy as np
from scipy.fftpack import dct

# Example 8x8 DCT block (typical in JPEG/video codecs)
pixel_block = np.array([
    [139, 144, 149, 153, 155, 155, 155, 155],
    [144, 151, 153, 156, 159, 156, 156, 156],
    [149, 153, 156, 159, 164, 160, 162, 162],
    [153, 156, 159, 162, 168, 169, 176, 175],
    [155, 159, 164, 168, 176, 190, 194, 191],
    [155, 156, 160, 169, 190, 205, 214, 215],
    [155, 156, 162, 176, 194, 214, 220, 205],
    [155, 156, 162, 175, 191, 215, 205, 196]
])

# Apply 2D DCT
dct_block = dct(dct(pixel_block.astype(float), axis=0), axis=1)

# Quantization (dividing by quality matrix)
# Higher values = more aggressive compression
quality_matrix = np.array([
    [16, 11, 10, 16, 24, 40, 51, 61],
    [12, 12, 14, 19, 26, 58, 60, 55],
    [14, 13, 16, 24, 40, 57, 69, 56],
    [14, 17, 22, 29, 51, 87, 80, 62],
    [18, 22, 37, 56, 68, 109, 103, 77],
    [24, 35, 55, 64, 81, 104, 113, 92],
    [49, 64, 78, 87, 103, 121, 120, 101],
    [72, 92, 95, 98, 112, 100, 103, 99]
])

quantized = np.round(dct_block / quality_matrix).astype(int)

print("Original block shape:", pixel_block.shape)
print("DCT coefficients (top-left 4x4):")
print(dct_block[:4, :4])
print("\nQuantized coefficients (top-left 4x4):")
print(quantized[:4, :4])

# High-frequency components are heavily quantized to zeros
# This achieves compression while maintaining visual quality
zero_count = np.sum(quantized == 0)
print(f"\nZeros after quantization: {zero_count}/64 ({100*zero_count/64:.1f}%)")
    </code></pre>
  </div>
  
  <div id="rle" class="tab-content" style="display: none;">
    <pre><code class="language-python">
# Run-Length Encoding (RLE) - Simple compression for repeated values
# Used for storing quantized DCT coefficients

def rle_encode(data):
    """Encode data using run-length encoding"""
    if not data:
        return []
    
    encoded = []
    count = 1
    prev = data[0]
    
    for i in range(1, len(data)):
        if data[i] == prev and count < 255:
            count += 1
        else:
            encoded.append((count, prev))
            prev = data[i]
            count = 1
    encoded.append((count, prev))
    return encoded

def rle_decode(encoded):
    """Decode run-length encoded data"""
    decoded = []
    for count, value in encoded:
        decoded.extend([value] * count)
    return decoded

# Example: Quantized DCT output often has many zeros
data = [5, 3, 3, 3, 0, 0, 0, 0, 0, 0, 0, 1, 2, 0, 0, 0, 0]

encoded = rle_encode(data)
print("Original data:", data)
print("Encoded (count, value):", encoded)
print(f"Compression ratio: {len(data)} → {len(encoded)*2} bytes")

decoded = rle_decode(encoded)
print("Decoded data:", decoded)
print("Match:", data == decoded)
    </code></pre>
  </div>
  
  <div id="huffman" class="tab-content" style="display: none;">
    <pre><code class="language-python">
import heapq
from collections import defaultdict, Counter

class HuffmanNode:
    def __init__(self, char, freq):
        self.char = char
        self.freq = freq
        self.left = None
        self.right = None
    
    def __lt__(self, other):
        return self.freq < other.freq

def build_huffman_tree(data):
    """Build Huffman tree from frequency data"""
    frequency = Counter(data)
    
    if len(frequency) == 1:
        return HuffmanNode(list(frequency.keys())[0], 1)
    
    heap = [HuffmanNode(char, freq) for char, freq in frequency.items()]
    heapq.heapify(heap)
    
    while len(heap) > 1:
        left = heapq.heappop(heap)
        right = heapq.heappop(heap)
        
        parent = HuffmanNode(None, left.freq + right.freq)
        parent.left = left
        parent.right = right
        heapq.heappush(heap, parent)
    
    return heap[0]

def build_codes(node, prefix="", codes=None):
    """Generate Huffman codes from tree"""
    if codes is None:
        codes = {}
    
    if node is None:
        return codes
    
    if node.char is not None:
        codes[node.char] = prefix if prefix else "0"
        return codes
    
    build_codes(node.left, prefix + "0", codes)
    build_codes(node.right, prefix + "1", codes)
    return codes

# Example: Compression of quantized DCT coefficients
data = [0, 0, 0, 1, 2, 0, 3, 0, 0, 1, 5, 0, 0, 0, 2]

tree = build_huffman_tree(data)
codes = build_codes(tree)

print("Symbol frequencies:", Counter(data))
print("\nHuffman codes:")
for symbol, code in sorted(codes.items(), key=lambda x: len(x[1])):
    print(f"  {symbol}: {code} (length: {len(code)})")

# Encode
encoded_bits = "".join(codes[symbol] for symbol in data)
print(f"\nOriginal size: {len(data) * 8} bits (8 bits per symbol)")
print(f"Encoded size: {len(encoded_bits)} bits")
print(f"Compression ratio: {len(data)*8 / len(encoded_bits):.2f}x")
    </code></pre>
  </div>
</div>

<script>
function showCompressionTab(tabId) {
  document.getElementById('dct').style.display = (tabId === 'dct') ? 'block' : 'none';
  document.getElementById('rle').style.display = (tabId === 'rle') ? 'block' : 'none';
  document.getElementById('huffman').style.display = (tabId === 'huffman') ? 'block' : 'none';
  document.getElementById('tab-dct').classList.toggle('active', tabId === 'dct');
  document.getElementById('tab-rle').classList.toggle('active', tabId === 'rle');
  document.getElementById('tab-huffman').classList.toggle('active', tabId === 'huffman');
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

### Working with OpenCV and NumPy

Here's a practical example using OpenCV to work with video frames:

```python
import cv2
import numpy as np

# Load a video file
cap = cv2.VideoCapture('video.mp4')

# Read first frame
ret, frame = cap.read()

if ret:
    # Get frame dimensions
    height, width, channels = frame.shape
    print(f"Frame size: {width}x{height}, Channels: {channels}")
    
    # Convert BGR to RGB for processing
    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
    
    # Convert to YUV (better for compression than RGB)
    frame_yuv = cv2.cvtColor(frame, cv2.COLOR_BGR2YUV)
    
    # Resize frame (reduces data, useful for streaming)
    small_frame = cv2.resize(frame, (width//2, height//2))
    
    print(f"Original size: {frame.nbytes} bytes")
    print(f"Resized size: {small_frame.nbytes} bytes")
    print(f"Compression ratio: {frame.nbytes / small_frame.nbytes:.2f}x")

cap.release()
```

This demonstrates basic concepts:
- Video frames are matrices of pixel values
- Color space conversion (RGB → YUV helps with chroma subsampling)
- Resizing trades quality for file size

## Building Your First Video Processor

Here's a practical project to apply what you've learned:

```python
import cv2
import numpy as np

class SimpleVideoProcessor:
    def __init__(self, input_path, output_path):
        self.input_path = input_path
        self.output_path = output_path
    
    def apply_compression(self, quality=80, resize_factor=1.0):
        """Apply basic compression techniques"""
        cap = cv2.VideoCapture(self.input_path)
        
        # Get video properties
        fps = int(cap.get(cv2.CAP_PROP_FPS))
        width = int(cap.get(cv2.CAP_PROP_FRAME_WIDTH))
        height = int(cap.get(cv2.CAP_PROP_FRAME_HEIGHT))
        
        # Create video writer
        fourcc = cv2.VideoWriter_fourcc(*'mp4v')
        new_width = int(width * resize_factor)
        new_height = int(height * resize_factor)
        out = cv2.VideoWriter(self.output_path, fourcc, fps, 
                             (new_width, new_height))
        
        frame_count = 0
        while True:
            ret, frame = cap.read()
            if not ret:
                break
            
            # Apply resize (spatial redundancy reduction)
            if resize_factor < 1.0:
                frame = cv2.resize(frame, (new_width, new_height))
            
            # Convert to YUV for better compression
            frame_yuv = cv2.cvtColor(frame, cv2.COLOR_BGR2YUV)
            
            # Reduce color channels (chroma subsampling simulation)
            frame_yuv[:,:,1:] = cv2.resize(frame_yuv[:,:,1:], 
                                          (new_width//2, new_height//2))
            
            out.write(frame)
            frame_count += 1
        
        cap.release()
        out.release()
        
        return frame_count

# Usage
processor = SimpleVideoProcessor('input.mp4', 'output.mp4')
frames = processor.apply_compression(resize_factor=0.5)
print(f"Processed {frames} frames with 50% size reduction")
```

This demonstrates key concepts:
- Reading and writing video
- Spatial reduction through resizing
- Chroma subsampling simulation
- Practical trade-offs between quality and file size


## Learning Resources

**Python Libraries:**
- [OpenCV Documentation](https://docs.opencv.org/) - Video processing and manipulation
- [NumPy Documentation](https://numpy.org/doc/) - Efficient array operations
- [Pillow (PIL)](https://pillow.readthedocs.io/) - Image processing
- [PyAV](https://pyav.org/) - Pythonic binding for FFmpeg

**Video Codec Standards:**
- [H.265/HEVC Codec Standard](https://www.itu.int/rec/T-REC-H.265/en) - Modern standard
- [AV1 Codec Specification](https://aomediacodec.org/) - Next-generation open codec
- [VP9 Codec Documentation](https://developers.google.com/media/vp9) - Open-source codec
- [H.264 Standard](https://www.itu.int/rec/T-REC-H.264/en) - Widely used baseline

**Educational Resources:**
- [Video Compression Fundamentals](https://en.wikipedia.org/wiki/Video_compression) - Good overview
- [DCT and Quantization](https://en.wikipedia.org/wiki/JPEG) - Understand transform coding
- [Huffman Coding Tutorial](https://en.wikipedia.org/wiki/Huffman_coding) - Entropy encoding
- [3Blue1Brown on DCT](https://www.youtube.com/watch?v=v7ScGV5128A) - Visual explanation of transforms

**Tools to Experiment With:**
- FFmpeg - Industry-standard video processing tool
- MediaInfo - Inspect video file properties and compression
- VLC - Playback and analysis of videos
