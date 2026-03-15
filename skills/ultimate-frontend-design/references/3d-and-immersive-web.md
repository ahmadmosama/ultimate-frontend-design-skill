# 3D & Immersive Web Design Reference

Complete reference for 3D web experiences, scroll-driven frame animations, Spline integration, and AI-assisted design pipelines for premium, Apple-style websites.

---

## 1. Scroll-Driven Frame Animation (Apple-Style 3D Scroll)

The "digital flipbook" technique: extract video frames, display them sequentially synced to scroll position. Produces Apple-quality product reveal effects without embedding video.

### How It Works

1. **Generate keyframe images** (AI or camera) — start state and end state of the product/object
2. **Generate transition video** between keyframes (Kling 3.0, Veo 3.1, or filmed)
3. **Extract ~180 frames** from the video as individual images
4. **Convert to WEBP** (25-35% smaller than JPEG, no quality loss)
5. **Render frames on HTML canvas** tied to scroll position

### Visual Transformation Patterns

| Pattern | Description | Best For |
|---------|-------------|----------|
| **Deconstruction** | Product breaks into component parts as user scrolls | Tech products, hardware |
| **X-ray Transparency** | Exterior fades to reveal internal components | Electronics, engineering |
| **Additive Construction** | Object builds up from nothing to finished state | Manufacturing, architecture |
| **Material Metamorphosis** | Product transitions between materials/finishes | Fashion, luxury goods |
| **Environment Shift** | Same product, background/lighting transforms | Lifestyle, automotive |
| **Scale Reveal** | Zoom from macro detail to full product view | Jewelry, watches, phones |

### Canvas-Based Frame Renderer

```js
class ScrollFrameAnimation {
  constructor(canvasId, frameCount, framePath) {
    this.canvas = document.getElementById(canvasId);
    this.ctx = this.canvas.getContext('2d');
    this.frameCount = frameCount;
    this.frames = [];
    this.currentFrame = 0;
  }

  // Preload all frames
  async preloadFrames(framePath, extension = 'webp') {
    const promises = [];
    for (let i = 0; i < this.frameCount; i++) {
      const img = new Image();
      const promise = new Promise((resolve) => {
        img.onload = resolve;
      });
      img.src = `${framePath}/frame-${String(i).padStart(4, '0')}.${extension}`;
      this.frames.push(img);
      promises.push(promise);
    }
    // Load first 30 frames with priority, rest progressively
    await Promise.all(promises.slice(0, 30));
    Promise.all(promises.slice(30)); // rest loads in background
  }

  // Draw specific frame to canvas
  drawFrame(index) {
    if (index === this.currentFrame) return;
    this.currentFrame = index;
    const img = this.frames[index];
    if (!img?.complete) return;

    const { width, height } = this.canvas;
    this.ctx.clearRect(0, 0, width, height);

    // Cover-fit the image (like object-fit: cover)
    const scale = Math.max(width / img.width, height / img.height);
    const x = (width - img.width * scale) / 2;
    const y = (height - img.height * scale) / 2;
    this.ctx.drawImage(img, x, y, img.width * scale, img.height * scale);
  }

  // Bind to scroll position
  bindToScroll(triggerElement) {
    const observer = () => {
      const rect = triggerElement.getBoundingClientRect();
      const scrollFraction = Math.max(0, Math.min(1,
        -rect.top / (rect.height - window.innerHeight)
      ));
      const frameIndex = Math.min(
        this.frameCount - 1,
        Math.floor(scrollFraction * this.frameCount)
      );
      requestAnimationFrame(() => this.drawFrame(frameIndex));
    };
    window.addEventListener('scroll', observer, { passive: true });
  }
}

// Usage
const anim = new ScrollFrameAnimation('product-canvas', 180, '/frames/product');
await anim.preloadFrames('/frames/product');
anim.bindToScroll(document.querySelector('.scroll-section'));
```

### HTML Structure

```html
<section class="scroll-section" style="height: 500vh;">
  <div class="sticky-wrapper" style="position: sticky; top: 0; height: 100vh;">
    <canvas id="product-canvas" width="1920" height="1080"
      style="width: 100%; height: 100%; object-fit: cover;">
    </canvas>
  </div>
</section>
```

### GSAP + ScrollTrigger Version

```js
const canvas = document.getElementById('product-canvas');
const ctx = canvas.getContext('2d');
const frames = []; // preloaded Image objects

gsap.to({ frame: 0 }, {
  frame: frames.length - 1,
  snap: 'frame',
  ease: 'none',
  scrollTrigger: {
    trigger: '.scroll-section',
    start: 'top top',
    end: 'bottom bottom',
    scrub: 0.5,
    pin: true,
  },
  onUpdate: function () {
    const index = Math.round(this.targets()[0].frame);
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.drawImage(frames[index], 0, 0, canvas.width, canvas.height);
  },
});
```

### Frame Extraction with FFmpeg

```bash
# Extract frames from video
ffmpeg -i transition.mp4 -vf "fps=30,scale=1920:-1" frames/frame-%04d.png

# Convert to WEBP (25-35% smaller than JPEG)
for f in frames/*.png; do
  cwebp -q 85 "$f" -o "${f%.png}.webp"
done

# Or single command: extract + convert
ffmpeg -i transition.mp4 -vf "fps=30,scale=1920:-1" -c:v libwebp -quality 85 frames/frame-%04d.webp
```

### Performance Rules

- **Canvas over DOM** — draw frames to `<canvas>`, never swap `<img>` elements
- **WEBP format** — 25-35% smaller than JPEG at equivalent quality
- **Progressive preloading** — load first 30 frames immediately, rest in background
- **requestAnimationFrame** — never update canvas directly in scroll handler
- **Passive scroll listeners** — use `{ passive: true }` for scroll events
- **Resize handling** — update canvas dimensions on window resize, use ResizeObserver
- **Frame count** — 120-180 frames for smooth 4-6 second animations; more feels wasteful
- **Resolution** — 1920px wide max; mobile can use 960px variants

---

## 2. Spline — 3D Design for Web

Browser-based platform for interactive 3D experiences that export as production-ready code. Collaborative, real-time, no WebGL knowledge required.

### When to Use Spline

| Use Case | Spline Fit |
|----------|-----------|
| Interactive 3D product showcase | Excellent |
| Hero section with 3D element | Excellent |
| 3D icons/illustrations | Great |
| Animated logo/brand element | Great |
| Data visualization (3D) | Good |
| Full 3D game | Use Three.js/R3F instead |
| Complex particle systems | Use Three.js instead |
| AR/VR experiences | Use Three.js/A-Frame instead |

### React Integration

```bash
npm install @splinetool/react-spline @splinetool/runtime
```

```tsx
import Spline from '@splinetool/react-spline';

export default function Hero3D() {
  return (
    <Spline scene="https://prod.spline.design/xxx/scene.splinecode" />
  );
}
```

### Next.js with SSR (Blurred Placeholder)

```tsx
import Spline from '@splinetool/react-spline/next';

export default function Hero3D() {
  return (
    <Spline scene="https://prod.spline.design/xxx/scene.splinecode" />
  );
}
```

### Object Manipulation via Code API

```tsx
import { Application } from '@splinetool/runtime';
import Spline from '@splinetool/react-spline';

function Scene() {
  function onLoad(splineApp: Application) {
    const cube = splineApp.findObjectByName('Cube');
    if (cube) {
      cube.position.x += 10;
      cube.rotation.y = Math.PI / 4;
      cube.scale.set(1.5, 1.5, 1.5);
    }
  }

  return <Spline scene="..." onLoad={onLoad} />;
}
```

### Event Handling

```tsx
<Spline
  scene="..."
  onSplineMouseDown={(e) => console.log('Clicked:', e.target.name)}
  onSplineMouseHover={(e) => console.log('Hovered:', e.target.name)}
  onSplineScroll={(e) => console.log('Scrolled')}
  onSplineKeyDown={(e) => console.log('Key:', e.target.name)}
/>
```

### Triggering Animations Programmatically

```tsx
function Scene() {
  const splineRef = useRef<Application>();

  function onLoad(splineApp: Application) {
    splineRef.current = splineApp;
  }

  function triggerHover() {
    splineRef.current?.emitEvent('mouseHover', 'ButtonObject');
  }

  function reverseHover() {
    splineRef.current?.emitEventReverse('mouseHover', 'ButtonObject');
  }

  return <Spline scene="..." onLoad={onLoad} />;
}
```

### Lazy Loading (Performance Critical)

```tsx
import { lazy, Suspense } from 'react';

const Spline = lazy(() => import('@splinetool/react-spline'));

function Hero3D() {
  return (
    <Suspense fallback={<div className="h-[600px] bg-muted animate-pulse rounded-xl" />}>
      <Spline scene="https://prod.spline.design/xxx/scene.splinecode" />
    </Suspense>
  );
}
```

### Self-Hosting `.splinecode` Files

```tsx
// Download .splinecode file and serve from your own domain
// Avoids CORS issues and CDN dependency
<Spline scene="/assets/3d/hero-scene.splinecode" />
```

### Spline Design Capabilities

**3D Modeling:**
- Primitives (box, sphere, cylinder, torus, plane)
- Extrude & loft (2D shapes to 3D with depth)
- Boolean operations (combine, subtract, intersect)
- Materials: Phong lighting, matcap, bump maps, glass, metal

**2D Design (Hana):**
- Infinite canvas with frames and auto-layout
- Vector networks for custom shapes
- Visual effects: shadows, blurs, glassmorphism, 3D projections

**AI Features:**
- Text-to-3D: describe an object, receive 4 variants optimized for web
- Image-to-3D: feed a 2D reference image
- Style transfer between objects

**Interactivity (No Code Required Inside Spline):**
- Events: click, hover, scroll, keydown/keyup, lookAt, follow cursor
- States: multiple visual states with automatic interpolation
- Physics engine for realistic interactions
- Variables and conditional logic
- Webhooks for real-time data

### Export Options

| Target | Method |
|--------|--------|
| React / Next.js | `@splinetool/react-spline` |
| Vanilla JS / HTML | `@splinetool/runtime` + embed code |
| Webflow | Embed component |
| Framer | Native integration |
| iOS (Swift) | Native export |
| Android (Kotlin) | Native export |
| glTF | Standard 3D format export |
| Video / GIF | For non-interactive use |

### Performance Optimization

- **Merge complex objects** into single meshes with single UV maps
- **Use `renderOnDemand`** prop (default: true) to avoid unnecessary re-renders
- **Lazy load** with React Suspense — Spline scenes can be 2-10MB
- **Self-host `.splinecode`** files to avoid CORS issues
- **Reduce polygon count** — web 3D should stay under 100k polygons for smooth performance
- **Compress textures** — use power-of-2 dimensions (512x512, 1024x1024)
- **Test on mid-range devices** — 3D is GPU-intensive, ensure graceful degradation

---

## 3. Three.js & React Three Fiber (R3F)

For cases where Spline's no-code approach isn't enough, Three.js via React Three Fiber provides full programmatic 3D control.

### When to Use R3F vs Spline

| Criteria | Spline | R3F (React Three Fiber) |
|----------|--------|------------------------|
| Learning curve | Low (visual editor) | High (code + 3D concepts) |
| Customization | Limited to Spline features | Unlimited |
| Performance tuning | Basic | Full shader/geometry control |
| File size | 2-10MB scenes | You control everything |
| Team workflow | Designers can contribute | Developer-only |
| Complex shaders | No | Yes (custom GLSL) |
| Physics | Basic built-in | Choose engine (Rapier, Cannon) |

### Basic R3F Setup

```bash
npm install three @react-three/fiber @react-three/drei
```

```tsx
import { Canvas } from '@react-three/fiber';
import { OrbitControls, Environment, Float } from '@react-three/drei';

function Scene() {
  return (
    <Canvas camera={{ position: [0, 0, 5], fov: 45 }}>
      <ambientLight intensity={0.5} />
      <directionalLight position={[5, 5, 5]} />
      <Float speed={2} rotationIntensity={0.5} floatIntensity={1}>
        <mesh>
          <boxGeometry args={[1, 1, 1]} />
          <meshStandardMaterial color="#6366f1" />
        </mesh>
      </Float>
      <OrbitControls enableZoom={false} />
      <Environment preset="studio" />
    </Canvas>
  );
}
```

### Scroll-Linked 3D (R3F + GSAP)

```tsx
import { useFrame } from '@react-three/fiber';
import { useRef } from 'react';

function ProductModel() {
  const meshRef = useRef();

  useFrame(() => {
    const scrollY = window.scrollY;
    const maxScroll = document.body.scrollHeight - window.innerHeight;
    const progress = scrollY / maxScroll;

    if (meshRef.current) {
      meshRef.current.rotation.y = progress * Math.PI * 2;
      meshRef.current.position.y = Math.sin(progress * Math.PI) * 2;
    }
  });

  return (
    <mesh ref={meshRef}>
      <torusKnotGeometry args={[1, 0.3, 128, 32]} />
      <meshStandardMaterial color="#8b5cf6" metalness={0.8} roughness={0.2} />
    </mesh>
  );
}
```

### Loading GLTF Models

```tsx
import { useGLTF } from '@react-three/drei';

function ProductModel({ url }: { url: string }) {
  const { scene } = useGLTF(url);
  return <primitive object={scene} scale={1.5} />;
}

// Preload for instant display
useGLTF.preload('/models/product.glb');
```

---

## 4. AI-Assisted Design Pipeline

A modern workflow combining AI image generation, video interpolation, and code generation for premium web experiences.

### The 3-Tool Pipeline

```
Step 1: AI Image Generation (Nano Banana 2 / Gemini)
  ↓ Generate start + end keyframe images
Step 2: AI Video Interpolation (Kling 3.0 / Veo 3.1)
  ↓ Generate smooth transition video between keyframes
Step 3: Frame Extraction + Code (FFmpeg + Claude Code)
  ↓ Extract frames, convert to WEBP, build scroll animation
Result: Apple-style 3D scroll website
```

### Keyframe Generation Rules

- **Background colors must match** the website section they'll sit in
- **Consistent lighting** between start and end frames
- **Same camera angle/perspective** — only the subject should change
- **Clean backgrounds** — solid colors or simple gradients work best
- **High resolution** — generate at 2x the display size for retina

### Video Transition Rules

- **Keep transitions simple** — avoid spinning, distortion, or extreme camera moves
- **2-4 second transitions** are ideal (120-180 frames at 30fps)
- **Linear motion** between states — the scroll speed handles the pacing
- **Consistent object scale** — don't let the AI dramatically resize the subject

### Reference-Driven Design

Instead of describing layouts in text, use visual references from design galleries:

| Gallery | Best For |
|---------|----------|
| Godly.website | Premium web design, dark themes, motion-heavy |
| Awwwards.com | Award-winning creative sites, experimental |
| Dribbble.com | UI components, mobile screens, illustrations |
| Mobbin.com | Mobile app patterns, real-world screenshots |
| Lapa.ninja | Landing page inspiration, SaaS |
| Land-book.com | Product landing pages |
| One Page Love | Single-page site designs |

**Workflow:** Screenshot a reference → feed to AI as visual context → generate design that matches the quality bar while being original.

---

## 5. Immersive Web Design Patterns

### Cursor-Aware 3D Elements

```tsx
import { useRef } from 'react';
import { motion, useMotionValue, useTransform } from 'motion/react';

function Immersive3DCard({ children }: { children: React.ReactNode }) {
  const ref = useRef<HTMLDivElement>(null);
  const x = useMotionValue(0);
  const y = useMotionValue(0);

  const rotateX = useTransform(y, [-0.5, 0.5], [15, -15]);
  const rotateY = useTransform(x, [-0.5, 0.5], [-15, 15]);

  function handleMouseMove(e: React.MouseEvent) {
    if (!ref.current) return;
    const rect = ref.current.getBoundingClientRect();
    x.set((e.clientX - rect.left) / rect.width - 0.5);
    y.set((e.clientY - rect.top) / rect.height - 0.5);
  }

  function handleMouseLeave() {
    x.set(0);
    y.set(0);
  }

  return (
    <motion.div
      ref={ref}
      onMouseMove={handleMouseMove}
      onMouseLeave={handleMouseLeave}
      style={{
        rotateX,
        rotateY,
        transformStyle: 'preserve-3d',
        perspective: 1000,
      }}
      transition={{ type: 'spring', stiffness: 200, damping: 20 }}
    >
      {children}
    </motion.div>
  );
}
```

### Scroll-Synced Background Transitions

```css
.immersive-section {
  background: linear-gradient(to bottom, #000 0%, #0a0a2e 50%, #000 100%);
  animation: bg-shift linear;
  animation-timeline: scroll();
  animation-range: entry 0% exit 100%;
}

@keyframes bg-shift {
  0%   { background-color: #000; }
  25%  { background-color: #0a0a2e; }
  50%  { background-color: #1a0a2e; }
  75%  { background-color: #0a1a2e; }
  100% { background-color: #000; }
}
```

### Depth Layering with Parallax

```tsx
function ParallaxHero() {
  const { scrollYProgress } = useScroll();

  const bgY = useTransform(scrollYProgress, [0, 1], ['0%', '30%']);
  const midY = useTransform(scrollYProgress, [0, 1], ['0%', '15%']);
  const fgY = useTransform(scrollYProgress, [0, 1], ['0%', '5%']);

  return (
    <div className="relative h-[200vh]">
      <motion.div className="fixed inset-0 z-0" style={{ y: bgY }}>
        {/* Far background layer */}
      </motion.div>
      <motion.div className="fixed inset-0 z-10" style={{ y: midY }}>
        {/* Mid layer (3D element or product) */}
      </motion.div>
      <motion.div className="fixed inset-0 z-20" style={{ y: fgY }}>
        {/* Foreground text/UI */}
      </motion.div>
    </div>
  );
}
```

---

## 6. Decision Matrix: 3D Approach Selection

| Project Type | Recommended Approach | Why |
|-------------|---------------------|-----|
| Product landing with scroll animation | Frame animation (canvas) | Apple-quality, no 3D expertise needed |
| Interactive 3D hero/showcase | Spline | Visual editor, fast iteration, designer-friendly |
| Complex 3D with custom shaders | React Three Fiber | Full programmatic control |
| 3D data visualization | R3F + Drei | Declarative, composable, React ecosystem |
| Simple 3D icon/illustration | Spline | Quickest path to production |
| VR/AR experience | Three.js + WebXR | Only option for immersive |
| Portfolio/agency site | Spline + GSAP scroll | Best balance of quality vs. effort |
| E-commerce product viewer | Spline or `<model-viewer>` | Built-in AR on mobile |

### `<model-viewer>` for E-Commerce

```html
<!-- Google's web component for simple 3D product viewing -->
<script type="module" src="https://ajax.googleapis.com/ajax/libs/model-viewer/4.0/model-viewer.min.js"></script>

<model-viewer
  src="/models/shoe.glb"
  alt="Running shoe 3D model"
  auto-rotate
  camera-controls
  ar
  shadow-intensity="1"
  style="width: 100%; height: 500px;"
>
  <button slot="ar-button" style="...">View in AR</button>
</model-viewer>
```
