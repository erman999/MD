# **HTML and Manifest Skeleton**

This document provides a clean and modern HTML template alongside a PWA (Progressive Web App) manifest file. Both are designed to meet the latest web standards and ensure compatibility across browsers and devices.

---

## **HTML Skeleton**

This is a modern, best-practice HTML template suitable for most web projects.

```html
<!DOCTYPE html>
<html lang="en" dir="ltr">
<head>
  <!-- Character Encoding -->
  <meta charset="utf-8">

  <!-- Viewport for Responsive Design -->
  <meta name="viewport" content="width=device-width, initial-scale=1">

  <!-- Browser Compatibility -->
  <meta http-equiv="X-UA-Compatible" content="IE=edge">

  <!-- Meta Information -->
  <meta name="author" content="Your Name">
  <meta name="description" content="A brief description of the page for SEO purposes">
  <meta name="keywords" content="HTML, Template, Best Practices">

  <!-- Theme Color -->
  <meta name="theme-color" content="#00FFF0">

  <!-- Icons -->
  <link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
  <link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
  <link rel="apple-touch-icon" href="/apple-touch-icon.png">

  <!-- Manifest -->
  <link rel="manifest" href="/manifest.json">

  <!-- CSS -->
  <link rel="stylesheet" href="/style.css">

  <!-- Title -->
  <title>Your Page Title</title>
</head>
<body>
  <header>
    <h1>Welcome to Your Web Page!</h1>
  </header>

  <main>
    <p>Your content goes here...</p>
  </main>

  <footer>
    <p>&copy; <span id="year"></span> Your Name</p>
  </footer>

  <!-- JavaScript -->
  <script src="/script.js" defer></script>
  <script>
    // Update the copyright year dynamically
    document.getElementById('year').textContent = new Date().getFullYear();
  </script>
</body>
</html>
```

### **Key Updates and Notes**
1. **Modern Compatibility**:
   - Replaced `IE=11` with `IE=edge` as Internet Explorer is no longer actively supported.
   - Included `<meta name="keywords">` for better SEO.

2. **Improved Icons**:
   - Added `apple-touch-icon` for better compatibility on iOS devices.
   - Included multiple favicon sizes for modern browser compatibility.

3. **Deferred JavaScript**:
   - Added `defer` to the `<script>` tag to improve page loading performance.

4. **Dynamic Footer Year**:
   - Automatically updates the copyright year to keep your site current.

---

## **Web App Manifest**

The manifest file ensures that your web app can be installed as a Progressive Web App (PWA) and provides metadata for its appearance and behavior.

```json
{
  "name": "Your App Name",
  "short_name": "App Short Name",
  "description": "A brief description of your web app's purpose",
  "display": "standalone",
  "start_url": "/",
  "scope": "/",
  "orientation": "portrait-primary",
  "prefer_related_applications": false,
  "background_color": "#ffffff",
  "theme_color": "#00FFF0",
  "icons": [
    {
      "src": "/img/logo-192x192.png",
      "sizes": "192x192",
      "type": "image/png"
    },
    {
      "src": "/img/logo-512x512.png",
      "sizes": "512x512",
      "type": "image/png",
      "purpose": "any maskable"
    }
  ]
}
```

### **Key Updates and Notes**
1. **Improved Display Mode**:
   - Changed `display` from `fullscreen` to `standalone` for better UX unless fullscreen is absolutely required.

2. **Orientation Support**:
   - Added `orientation: "portrait-primary"` for apps optimized for portrait mode.

3. **Description Field**:
   - Ensures the app description is clear and concise for better app discovery.

4. **Icon Purposes**:
   - Included `maskable` purpose for icons to ensure compatibility with devices that require adaptive icons.

---

## **Best Practices**

1. **Accessibility**:
   - Use semantic HTML elements like `<header>`, `<main>`, and `<footer>` for better structure and accessibility.

2. **SEO Optimization**:
   - Ensure `<meta name="description">` and `<meta name="keywords">` are meaningful and concise.

3. **Performance**:
   - Minify and optimize CSS and JavaScript files for faster page load times.
   - Use a CDN for static assets when possible.

4. **PWA Standards**:
   - Test your manifest file using [Lighthouse](https://developers.google.com/web/tools/lighthouse) to ensure PWA compatibility.

5. **Dynamic Content**:
   - Use JavaScript to enhance interactivity while maintaining server-side rendering for static content.

---

## **Conclusion**

This template and manifest skeleton are tailored for modern web development and meet current standards for performance, compatibility, and accessibility. Use these as a starting point for your next web project!