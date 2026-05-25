# 🌟 Accordion Frames Spotlight Slider

An ultra-premium, interactive horizontal accordion slider with dynamic tracking lines, built using **Next.js 16/17+ (App Router)** and **React 19**. 

This component features hardware-accelerated panel expansion, fluid cursor-hover interactions, fully responsive touch support, and high-performance coordinate tracking indicators, creating an immersive showcase for photography or design portfolios.

<img width="1920" height="1080" alt="Screenshot (501)" src="https://github.com/user-attachments/assets/f397f6ef-d0a5-4318-b3c1-005a4488d490" />

---

## 📖 Table of Contents
- [✨ Key Features](#-key-features)
- [🛠️ Tech Stack](#️-tech-stack)
- [📁 Directory Structure](#-directory-structure)
- [🚀 Quick Start](#-quick-start)
- [🧩 How It Works](#-how-it-works)
- [🎨 Customization Guide](#-customization-guide)
  - [Adjusting Dimensions \& Panel Counts](#adjusting-dimensions--panel-counts)
  - [Using Your Own Images](#using-your-own-images)
  - [Tweaking Animations](#tweaking-animations)
- [⚡ Performance & Optimization](#-performance--optimization)
- [💡 Inspiration & Design System](#-inspiration--design-system)

---

## ✨ Key Features

* **Dynamic Expansion Accordion:** Hovering over a collapsed panel gracefully expands it while other panels smoothly shift to accommodate the new layout width.
* **Precision Crosshair Tracking:** A sleek focus border wraps the active panel, accompanied by vertical coordinate lines that stretch to the screen boundaries (`100svh`), creating a modern, agency-grade viewport-targeting aesthetic.
* **Perfect Cubic-Bezier Inertia:** Leverages `cubic-bezier(0.075, 0.82, 0.165, 1)` (easeOutCircular/Expo variant) for a premium, lightweight, high-friction kinetic response.
* **Intelligent Responsive Design:**
  * **Desktop:** Displays 20 panels with full-width expansion (400px) responding to high-accuracy `onMouseEnter` trigger events.
  * **Mobile:** Automatically scales down to 10 panels with optimized widths (100px) and switches interaction to touch-safe `onClick` events.
* **Auto-Centering Track:** Programmatically calculates overall track dimensions to ensure the group of panels is perfectly centered in the viewport at all times.
* **GPU-Accelerated:** Uses CSS `will-change: left, width` and absolute positioning to avoid page layout thrashing and maintain a locked 60fps rendering frame-rate.

---

## 🛠️ Tech Stack

* **Framework:** [Next.js](https://nextjs.org/) (App Router, featuring React Server Components layout wrapper)
* **Library:** [React 19](https://react.dev/) (Hooks used: `useState`, `useEffect`, `useRef`, `useCallback`)
* **Styling:** Vanilla CSS (Custom properties, responsive media queries, high-performance transitions)

---

## 📁 Directory Structure

Below is the directory tree highlighting the key architecture of this Next.js project:

```text
accordion-frames/
├── public/                 # Static assets folder
│   ├── images/             # 📂 User-defined images folder (Create & place custom pictures here!)
│   ├── spotlight/          # Placeholder spotlight images
│   ├── file.svg
│   ├── globe.svg
│   ├── next.svg
│   └── vercel.svg
├── src/
│   ├── app/                # Next.js App Router core files
│   │   ├── favicon.ico
│   │   ├── globals.css     # Global core styling & custom animations
│   │   ├── layout.js       # App layout container
│   │   ├── page.js         # Entry home page (Imports and mounts <Spotlight />)
│   │   └── page.module.css
│   └── components/         # Reusable React components
│       └── Spotlight.jsx   # Spotlight Track, panels state-management, & positioning logic
├── jsconfig.json           # Path aliasing configuration (@/* maps to src/*)
├── next.config.mjs         # Next.js configuration settings
├── package.json            # Dependencies and scripts (React 19 & Next.js)
└── README.md               # Extensive documentation
```

---

## 🚀 Quick Start

Get your development environment running locally in minutes:

### 1. Install Dependencies
Run the package manager setup command inside the project directory:
```bash
npm install
```

### 2. Start the Development Server
```bash
npm run dev
```
Open [http://localhost:3000](http://localhost:3000) in your browser to view the application.

### 3. Build for Production
To generate a highly optimized static/server-rendered bundle, run:
```bash
npm run build
npm run start
```

---

## 🧩 How It Works

### The Dynamic Grid Math
The layout coordinates are computed dynamically in JavaScript inside `Spotlight.jsx` rather than relying on standard flexbox or CSS Grid. This is necessary to achieve independent, absolute transition tracks and pixel-perfect center alignment.

```javascript
const getPanelPosition = useCallback(
    (panelIndex) => {
        // Compute total width based on collapsed panels, spacing gap, and the single active expanded panel
        const totalTrackWidth =
            (panelCount - 1) * (PANEL_WIDTH_COLLAPSED + PANEL_GAP) +
            expandedWidth;
        const offsetToCenter = (trackWidth - totalTrackWidth) / 2;

        // Calculate left position for the current panel
        let left = offsetToCenter;
        for (let i = 0; i < panelIndex; i++) {
            const w = i === focusedPanel ? expandedWidth : PANEL_WIDTH_COLLAPSED;
            left += w + PANEL_GAP;
        }

        const width = panelIndex === focusedPanel ? expandedWidth : PANEL_WIDTH_COLLAPSED;
        return { left, width };
    },
    [focusedPanel, panelCount, expandedWidth, trackWidth]
);
```

### Key Performance Ingredients:
1. **`ResizeObserver`**: Watches window and container width changes, seamlessly adjusting sizes on the fly to support dynamic screen resizing.
2. **`useCallback` Cache**: Keeps reference hooks stable to prevent redundant computations and unnecessary re-renders of panel structures during mouse movements.

---

## 🎨 Customization Guide

### Adjusting Dimensions & Panel Counts
At the top of `src/components/Spotlight.jsx`, you'll find a set of configurable constants to customize the behavior of the slider immediately:

```javascript
const PANEL_WIDTH_COLLAPSED = 20;       // Width (px) of panels when closed
const PANEL_WIDTH_EXPANDED = 400;       // Width (px) of panels on desktop hover
const PANEL_WIDTH_EXPANDED_MOBILE = 100; // Width (px) of panels on mobile tap
const PANEL_GAP = 5;                     // Space (px) between adjacent panels
const PANEL_COUNT_DESKTOP = 20;         // Total panels generated for screens > 1000px
const PANEL_COUNT_MOBILE = 10;          // Total panels generated for mobile displays
const BREAKPOINT_MOBILE = 1000;         // Viewport width threshold (px)
```

### Using Your Own Images
We have pre-created the `/images/` directory in the `public` folder for you! To display your own custom selections:

1. Copy your image files (JPG, PNG, WebP) directly into `public/images/`.
2. Update the dynamic file paths in `Spotlight.jsx` (near line 84). 
   For example, if you have images named `portfolio-1.jpg`, `portfolio-2.jpg`, etc., change the render line to:
   ```jsx
   <img src={`/images/portfolio-${i + 1}.jpg`} alt={`Portfolio item ${i + 1}`} />
   ```
3. Alternatively, create an array of custom filenames or URLs to map through dynamically:
   ```javascript
   const myImages = [
       "/images/my-image-1.webp",
       "/images/sunset-beach.jpg",
       // ... add your choices here
   ];
   ```

### Tweaking Animations
The transitions are styled globally. Open `src/app/globals.css` and locate the `.spotlight-panel` and `.spotlight-focus-indicator` styles:

```css
.spotlight-panel, .spotlight-focus-indicator {
    /* 1s is the duration; cubic-bezier provides the elastic, high-friction drag effect */
    transition: all 1.0s cubic-bezier(0.075, 0.82, 0.165, 1);
}
```
* **For a faster, snappy feel:** Change `1s` to `0.6s` or `0.5s`.
* **For a heavier, cinematic movement:** Try raising the duration or customizing the Bezier curve (e.g., `cubic-bezier(0.25, 1, 0.5, 1)`).

---

## ⚡ Performance & Optimization

* **Hardware Acceleration:** By applying `will-change: left, width`, browsers pre-allocate composite layers for the panels, avoiding heavy layout re-paint cycles during transitions.
* **SVG Optimization:** Pre-packaged SVGs are stored in the `public/` directory for fast standard rendering tests.
* **Modern CSS viewport units:** `100svh` (Small Viewport Height) prevents layouts from shifting or breaking when dynamic address bars slide in/out on mobile safari or chrome.

---

## 💡 Inspiration & Design System

The visual design language is inspired by minimalism, featuring high-contrast interactive elements, clean vertical coordinate borders, and precise modern movement patterns. This setup mirrors design agency layouts (like the work of Philipp Antoni) by placing emphasis on typography, high-quality images, and dynamic hover effects.

---

*Made with 🖤 and code. Customize the slider options to make this project uniquely yours!*
