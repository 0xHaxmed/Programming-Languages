# 04 - Media Elements: Images, Audio & Video

> 🌐 **Language / اللغة:** [English 🇬🇧](./04-media-images-and-audio-video.md) | [العربية 🇸🇦](../ar/04-media-images-and-audio-video.md)

---

## 📌 1. Embedding Images (`<img>`)

The `<img>` tag is a void (self-closing) element used to embed raster or vector graphics:

```html
<img src="./assets/avatar.png" alt="Profile picture of Alex Vance" width="300" height="300" loading="lazy">
```

### Critical Image Attributes:
- **`src` (Source):** Path to the image file (relative or absolute).
- **`alt` (Alternative Text):** **MANDATORY for accessibility!** Screen readers read this text to visually impaired users. If an image is purely decorative, use `alt=""` so screen readers ignore it.
- **`width` & `height`:** Always specify explicit dimension attributes. This allows the browser to reserve the aspect ratio box before the image downloads, eliminating **Cumulative Layout Shift (CLS)**!
- **`loading="lazy"`:** Defers loading of offscreen images until the user scrolls near them, dramatically speeding up initial page load.

---

## 🖼️ 2. Figures & Captions (`<figure>`, `<figcaption>`)

When an image represents an editorial figure, diagram, or illustration referenced by text, wrap it in `<figure>`:

```html
<figure>
    <img src="./diagram-xss-flow.png" alt="Data flow diagram from Source to Sink" width="600" height="350">
    <figcaption>Figure 1.1: Tracing untrusted taint flow into innerHTML sink.</figcaption>
</figure>
```

---

## 🎛️ 3. Audio & Video Elements

HTML5 native audio and video eliminate the need for third-party plugins:

### A. Video Player (`<video>`):
```html
<video controls width="640" height="360" poster="./assets/preview-poster.jpg" preload="metadata">
    <source src="./videos/intro.mp4" type="video/mp4">
    <source src="./videos/intro.webm" type="video/webm">
    <p>Your browser does not support HTML5 video. <a href="./videos/intro.mp4">Download it here</a>.</p>
</video>
```
- **`controls`:** Displays native browser playback controls (play, pause, volume, fullscreen).
- **`poster`:** URL of an image thumbnail shown while the video is downloading.
- **`<source>`:** Allows the browser to pick the first format it supports (WebM, MP4).

### B. Audio Player (`<audio>`):
```html
<audio controls>
    <source src="./audio/podcast-ep1.mp3" type="audio/mpeg">
    <source src="./audio/podcast-ep1.ogg" type="audio/ogg">
    Your browser does not support audio playback.
</audio>
```

---

## 🛡️ 4. Security Warning: Dangerous SVG Uploads (Stored XSS)

SVG (Scalable Vector Graphics) is an XML-based image format. Unlike JPEG or PNG, **SVG files can execute arbitrary JavaScript**:

```xml
<!-- Malicious SVG File -->
<svg xmlns="http://www.w3.org/2000/svg">
    <circle cx="50" cy="50" r="40" fill="red" />
    <script>alert(document.cookie)</script>
</svg>
```

### Defense Rules:
1. When embedded via `<img src="avatar.svg">`, modern browsers disable internal SVG scripts.
2. But if an application allows users to upload SVGs and directly renders them inline (`<svg>`) or serves them with `Content-Type: image/svg+xml` opened in a standalone tab, the script executes, causing **Stored Cross-Site Scripting (XSS)**!
3. Never allow untrusted user SVG uploads without strict server-side sanitization.

---

## 🎯 Summary & Lesson Challenge

- Always provide meaningful `alt` text for non-decorative images.
- Provide `width` and `height` to prevent page jump (CLS).
- Use `loading="lazy"` on below-the-fold images.
- Treat user-uploaded SVG files as executable code.

**Challenge:** Create a multimedia section featuring an image with a caption using `<figure>`, and a native HTML5 video player with fallback `<source>` tags.

---

**Next Lesson:**  
➡️ [Lesson 05: Lists & Tabular Data](./05-lists-and-tables.md) | ⬅️ [Lesson 03: Hyperlinks & Secure Anchors](./03-links-and-anchors.md)
