# Daily UI Intelligence Report — April 5, 2026

---

## 1. iyO — 3D Product Configurator (Awwwards SOTD April 3, 2026)

**URL**: https://www.awwwards.com/sites/iyo

### WHAT IT DOES:
High-fidelity 3D showcase and bespoke product configurator for iyO, a screenless computing hardware company. The site renders a photorealistic 3D model of the device in a WebGL canvas that occupies the full viewport. Users rotate, zoom, and interact with the product model directly — selecting materials, colors, and configurations in real-time. The DOM UI overlays on top of the canvas as fixed-position panels with glassmorphic backgrounds, creating a spatial separation between the interactive 3D layer and the 2D information layer. Page transitions dissolve the current camera angle and morph into the next section's viewpoint using lerped camera positions, giving the feeling of moving through one continuous space rather than navigating pages.

### PACKAGES:
- `three` (Three.js r160+) — 3D scene, lighting, PBR materials
- `@react-three/fiber` (v9+) — React declarative wrapper for Three.js
- `@react-three/drei` — helpers: `Environment`, `ContactShadows`, `OrbitControls`, `useGLTF`, `AccumulativeShadows`
- `@react-three/postprocessing` — bloom, vignette, tone mapping
- `gsap` + `ScrollTrigger` — scroll-driven camera transitions and DOM animations
- `leva` or custom React state — configurator controls
- `tailwindcss` v4 — utility styling for overlay panels
- `framer-motion` (Motion) — DOM panel enter/exit animations
- Font: likely Inter or custom geometric sans

### IMPLEMENTATION:
1. **Scene setup**: Single persistent `<Canvas>` component filling the viewport (`position: fixed; inset: 0; z-index: 0`). Camera starts at `[0, 1.5, 4]` looking at origin. Use `@react-three/drei`'s `PresentationControls` for constrained user rotation (azimuth ±45°, polar ±20°).
2. **Model loading**: Load `.glb` via `useGLTF`. The model should be draco-compressed (<3MB). Apply PBR materials with `MeshPhysicalMaterial` — `roughness: 0.15`, `metalness: 0.8`, `clearcoat: 1.0`, `clearcoatRoughness: 0.1` for the device shell. Swap material colors via React state.
3. **Lighting**: `<Environment preset="studio" />` for base IBL. Add one `<SpotLight>` at `[5, 5, 5]`, `intensity: 1.5`, `penumbra: 0.8` for a hero key light. `<ContactShadows>` beneath the model with `opacity: 0.4`, `blur: 2.5`.
4. **Post-processing**: `<EffectComposer>` with `<Bloom luminanceThreshold={0.9} intensity={0.3} />` and `<Vignette darkness={0.3} />`.
5. **Scroll-driven camera**: Register GSAP ScrollTrigger on a tall scroll container (`height: 400vh`). At each 100vh breakpoint, tween `camera.position` and `camera.lookAt` to predefined vectors. Easing: `power2.inOut`, duration: `1.2s`. Camera positions: hero `[0, 1.5, 4]`, features `[2, 0.5, 2]`, specs `[-1, 2, 3]`, CTA `[0, 0.8, 2.5]`.
6. **Overlay panels**: Fixed-position divs with `backdrop-filter: blur(16px); background: rgba(255,255,255,0.06); border: 1px solid rgba(255,255,255,0.08); border-radius: 16px; padding: 32px`. Animate in with `framer-motion` — `initial={{ opacity: 0, y: 20 }}`, `animate={{ opacity: 1, y: 0 }}`, `transition={{ duration: 0.6, ease: [0.22, 1, 0.36, 1] }}`.
7. **Configurator state**: React `useState` for `{ color: string, material: string, size: string }`. On change, tween material properties with GSAP (`gsap.to(meshRef.current.material.color, { r, g, b, duration: 0.4 })`).
8. **Responsive**: Below 768px, disable OrbitControls, fix camera to front-facing hero angle, stack overlay panels vertically below the canvas (canvas becomes `position: relative; height: 50vh`).
9. **Performance**: Use `<Suspense>` with a skeleton loader. `<AdaptiveDpr>` and `<AdaptiveEvents>` from drei. Texture resolution capped at 1024px on mobile.

### WHY IT WORKS:
The persistent 3D canvas creates a spatial mental model — users feel like they're examining a physical object, not scrolling a webpage. Glassmorphic overlays maintain visual hierarchy: the product is always the figure, information is always the ground. The constrained rotation (not full orbit) prevents disorientation while still delivering the dopamine of direct manipulation. Scroll-driven camera movement exploits the Zeigarnik effect — each new angle reveals something incomplete, pulling the user forward.

### GOOD / BAD:
**GOOD**: The configurator-as-homepage pattern is best-in-class for hardware DTC — it collapses the entire funnel (awareness → consideration → configuration → purchase) into one scroll.
**BAD**: 3D model load time on slow connections likely causes a 2-4s blank canvas; needs a compelling skeleton/loading state to retain users.

### CLEAN APPLICATION:
Apply this to Clean's **product demo section** on the landing page. Instead of a hardware model, render a 3D visualization of Clean's context intelligence architecture — an animated node graph where agents (represented as glowing orbs) connect through a central Clean gateway node. As the user scrolls, the camera orbits the graph and new connections light up, showing context flowing between agents. Glassmorphic info panels appear explaining each capability. Instruction to design agent: "Build a scroll-driven 3D architecture visualization using React Three Fiber where 5-8 agent nodes orbit a central 'Clean' node, with particle trails showing context flow. Use GSAP ScrollTrigger to control camera position at 4 scroll breakpoints. Overlay glassmorphic panels with feature copy at each breakpoint."

---

## 2. Artefakt — Hybrid Production Company Portfolio (Awwwards SOTD April 4, 2026)

**URL**: https://www.awwwards.com/sites/artefakt

### WHAT IT DOES:
A portfolio site for a hybrid production company that fuses commercial and experimental work. The layout is a full-bleed WebGL gallery where project thumbnails render as textured planes in a Three.js scene. On scroll, images reveal through a custom GLSL shader — a noise-based displacement that "dissolves" a black mask to expose the image underneath. Page transitions are handled by Barba.js, keeping the WebGL canvas persistent across routes while the DOM content fades out and in. Text elements use GSAP SplitText for per-character staggered animations. The overall feel is a single continuous cinematic experience rather than discrete pages.

### PACKAGES:
- `three` (Three.js) — WebGL scene, custom shader materials
- `gsap` + `ScrollTrigger` + `SplitText` (Club GSAP) — scroll-driven reveals, text animation
- `@barba/core` — SPA-like page transitions without full framework
- `astro` or static HTML — lightweight page shell
- Custom GLSL shaders — image reveal/dissolve effect
- `lenis` or custom smooth scroll — momentum scrolling synced to render loop
- Font: custom serif + geometric sans pairing

### IMPLEMENTATION:
1. **Page architecture**: Astro or static HTML generates routes. Barba.js intercepts link clicks and performs async transitions. The Three.js canvas (`position: fixed; inset: 0; z-index: 0; pointer-events: none`) persists across all pages. DOM content sits above.
2. **Smooth scroll**: Implement with Lenis (`new Lenis({ duration: 1.2, easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t)) })`). Sync with Three.js render loop: `lenis.on('scroll', ({ scroll }) => { scrollProgress = scroll })`. In the `requestAnimationFrame` loop, update plane positions based on `scrollProgress`.
3. **WebGL gallery planes**: For each project image, create a `PlaneGeometry(1.6, 1, 32, 32)` with a custom `ShaderMaterial`. Vertex shader applies a subtle wave displacement: `pos.z += sin(pos.x * 4.0 + uTime) * 0.02 * uScrollVelocity`. Fragment shader implements the reveal: use a `simplex3D` noise function, compare `noise(uv * 3.0 + uTime * 0.1)` against a `uReveal` uniform (0→1) driven by ScrollTrigger. When `noise < uReveal`, output the texture color; otherwise output black (`vec3(0.0)`).
4. **ScrollTrigger reveal**: For each plane, register a ScrollTrigger: `start: "top 80%"`, `end: "top 20%"`, `scrub: 0.6`. Tween `uReveal` from `0.0` to `1.0`. The scrub value of 0.6 gives a slight delay that feels organic.
5. **Text animations**: Use GSAP SplitText to split headings into chars. On Barba page enter: `gsap.from(chars, { y: 60, opacity: 0, duration: 0.8, stagger: 0.02, ease: "power3.out" })`. On leave: `gsap.to(chars, { y: -40, opacity: 0, duration: 0.5, stagger: 0.01, ease: "power2.in" })`.
6. **Barba transitions**: `barba.init({ transitions: [{ leave: async ({ current }) => { await animateOut(current.container); disposeCurrentPlanes(); }, enter: async ({ next }) => { createPlanesForPage(next.container); await animateIn(next.container); } }] })`. Critical: call `.dispose()` on geometries/materials/textures in `leave` to prevent GPU memory leaks.
7. **Color scheme**: Background `#0a0a0a`, text `#f0f0f0`, accent `#c8ff00` (electric chartreuse) for hover states and active project indicators.
8. **Responsive**: On mobile (<768px), disable WebGL planes entirely. Fall back to CSS-only image reveals using `clip-path: inset()` animated via ScrollTrigger. Text animations reduce to `y: 30` with no per-char split (split by lines instead).

### WHY IT WORKS:
The noise-based reveal shader creates visual unpredictability — each image appears differently, which sustains novelty across the gallery (Von Restorff isolation effect). Persistent canvas across page transitions eliminates the "flash of white" that breaks immersion in traditional MPAs. The smooth scroll with velocity-driven displacement gives the planes a physical weight, engaging proprioceptive feedback loops that make scrolling feel tactile.

### GOOD / BAD:
**GOOD**: The shader reveal is technically sophisticated yet performant — noise computation happens entirely on the GPU with zero JS overhead during scroll.
**BAD**: Barba.js + GSAP SplitText cleanup is notoriously fragile — missed `.kill()` calls on ScrollTrigger instances between transitions will cause memory leaks and janky animations on return visits to pages.

### CLEAN APPLICATION:
Apply this to Clean's **case studies / testimonials section**. Instead of a static grid of logos or quote cards, render each customer story as a WebGL plane with the shader-reveal effect. As the user scrolls through the landing page, each case study image dissolves in with the noise shader while the company name and metrics animate in with SplitText character staggering. Instruction to design agent: "Build a scroll-revealed case study gallery using Three.js ShaderMaterial with noise-based dissolve reveal, GSAP ScrollTrigger with scrub: 0.6, and SplitText character animations for headlines. 4-6 entries, dark background #0a0a0a, electric accent color for metrics numbers."

---

## 3. Codrops Seamless 3D Transitions (Webflow + GSAP + Three.js, March 2026)

**URL**: https://tympanus.net/codrops/2026/03/18/building-seamless-3d-transitions-with-webflow-gsap-and-three-js/

### WHAT IT DOES:
A gallery-style multi-page experience where a persistent Three.js scene never reloads between page navigations. The 3D world contains hand-textured low-poly models (created in Blender, UV-unwrapped, hand-painted in Photoshop). Webflow handles the layout and CMS content. Barba.js manages route transitions. GSAP animates DOM elements using data attributes (`data-animation="title"`, `data-animation="text"`, `data-animation="spacer"`). The Experience class is a singleton holding scene, camera, renderer, and world — it initializes once and persists. Navigation feels like moving through a single 3D space with the DOM content as floating UI cards.

### PACKAGES:
- `three` — 3D scene, custom hand-painted materials
- `gsap` + `ScrollTrigger` + `SplitText` — DOM animation orchestration
- `@barba/core` — page transition management
- `vite` — build tool, outputs single JS bundle for Webflow
- Blender + Photoshop — asset pipeline (not runtime)
- Webflow — layout and CMS (can substitute with any static framework)

### IMPLEMENTATION:
1. **Singleton Experience class**:
```
class Experience {
  static instance = null;
  constructor(canvas) {
    if (Experience.instance) return Experience.instance;
    Experience.instance = this;
    this.scene = new THREE.Scene();
    this.sizes = { width: window.innerWidth, height: window.innerHeight };
    this.camera = new THREE.PerspectiveCamera(35, this.sizes.width / this.sizes.height, 0.1, 100);
    this.renderer = new THREE.WebGLRenderer({ canvas, antialias: true, alpha: true });
    this.renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));
    this.world = new World(this);
    this.tick();
  }
  tick() { requestAnimationFrame(() => this.tick()); this.renderer.render(this.scene, this.camera); }
}
```
2. **Data-attribute animation system**: Query all `[data-animation]` elements. For `title`: SplitText into chars, `gsap.from(chars, { y: '100%', duration: 0.8, stagger: 0.03, ease: 'power3.out' })`. For `text`: `gsap.from(el, { opacity: 0, y: 30, duration: 0.6, ease: 'power2.out' })`. For `spacer`: `gsap.from(el, { scaleX: 0, duration: 0.8, ease: 'power2.inOut', transformOrigin: 'left' })`.
3. **Page transition orchestration**: On leave — reverse all animations (chars slide up, text fades, spacers scale to 0). Duration: 0.5s. On enter — create new world objects for the page, run enter animations. Camera tweens to new position with `gsap.to(camera.position, { x, y, z, duration: 1.2, ease: 'power2.inOut' })`.
4. **3D model workflow**: In Blender, model with low-poly geometry (~2k-5k triangles). Unwrap UVs. Export UV layout as PNG. In Photoshop, paint textures directly on the UV map. Export as `.glb` with embedded textures. This gives a stylized, art-directed look that's lightweight and doesn't need complex PBR lighting.
5. **Memory management**: On every Barba leave transition: iterate all meshes, call `geometry.dispose()`, `material.dispose()`, `texture.dispose()`. Kill all GSAP instances: `ScrollTrigger.getAll().forEach(st => st.kill())`. Revert all SplitText instances.
6. **Canvas positioning**: `position: fixed; top: 0; left: 0; width: 100%; height: 100%; z-index: -1; pointer-events: none`.
7. **Performance targets**: <3MB total 3D assets per page. 60fps on M1 MacBook Air. Use `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))` to cap retina rendering.

### WHY IT WORKS:
The persistent 3D scene exploits the Gestalt principle of continuity — the brain interprets the unchanging 3D backdrop as proof that all pages exist in one space, creating a stronger sense of place than any CSS transition could. Hand-painted textures create warmth and personality that PBR-realistic renders lack, making the technical achievement feel approachable rather than cold. The data-attribute animation system decouples animation logic from content structure, making it maintainable at scale.

### GOOD / BAD:
**GOOD**: The singleton pattern + data-attribute system is production-grade architecture — it's how you'd actually build this for a real client, not just a demo.
**BAD**: The Blender-to-Photoshop-to-GLB asset pipeline is slow and requires a multidisciplinary team; not viable for rapid iteration.

### CLEAN APPLICATION:
Apply this to Clean's **documentation or product walkthrough** page. Build a persistent 3D scene showing Clean's architecture (a stylized low-poly representation of connected agents and a central gateway). As the user navigates between doc sections (via Barba transitions), the camera moves to focus on different parts of the architecture while the DOM content updates. Instruction to design agent: "Build a singleton Three.js Experience class with a low-poly 3D architecture diagram. Use Barba.js for section transitions with GSAP camera tweens (duration: 1.2s, ease: power2.inOut). DOM content animates via data-animation attributes. Dark theme, accent glow on the central Clean node."

---

## 4. Linear-Style Dark Mode with LCH Color System + Aurora Gradients

**URL**: https://linear.app (design system reference) | Aurora UI trend (2026 industry-wide)

### WHAT IT DOES:
Linear rebuilt their theme generation system using the LCH (Lightness-Chroma-Hue) color space instead of HSL, defining only three variables — base color, accent color, and contrast — to generate all 98 theme tokens automatically. The result is a dark interface where depth is communicated through luminance alone: nearer surfaces are lighter grays, distant surfaces are darker, creating a natural elevation system without drop shadows. The 2026 "Aurora UI" trend layers animated gradient mesh blobs over these dark interfaces — large radial gradients with heavy Gaussian blur, animated on GPU via CSS `@keyframes` or `conic-gradient` rotation, creating an atmospheric glow behind content sections.

### PACKAGES:
- `tailwindcss` v4 — utility classes with CSS custom properties
- `framer-motion` (Motion) — entrance animations, layout transitions
- CSS native — `color-mix()`, `oklch()`, `@property` for animatable gradients
- `culori` (npm) — LCH/OKLCH color space conversions in JS
- Font: Inter Variable or Geist (Vercel's font)

### IMPLEMENTATION:
1. **LCH theme tokens**: Define three CSS custom properties:
```css
:root {
  --base: oklch(0.13 0.01 260);    /* near-black with slight blue */
  --accent: oklch(0.72 0.18 155);   /* electric green */
  --contrast: oklch(0.95 0.01 260); /* near-white */
}
```
Generate surface levels: `--surface-0: oklch(0.10 0.01 260)` (deepest), `--surface-1: oklch(0.14 0.01 260)`, `--surface-2: oklch(0.18 0.01 260)`, `--surface-3: oklch(0.22 0.01 260)`. Each step increases lightness by ~0.04. Borders: `oklch(0.25 0.01 260 / 0.5)`.

2. **Aurora gradient background**: Create a full-viewport `::before` pseudo-element on the `<body>`:
```css
body::before {
  content: '';
  position: fixed;
  inset: 0;
  z-index: -1;
  background:
    radial-gradient(ellipse 80% 50% at 20% 40%, oklch(0.35 0.15 280 / 0.4), transparent),
    radial-gradient(ellipse 60% 80% at 80% 20%, oklch(0.30 0.18 155 / 0.3), transparent),
    radial-gradient(ellipse 70% 60% at 50% 80%, oklch(0.25 0.12 320 / 0.2), transparent);
  filter: blur(80px);
  animation: aurora 20s ease-in-out infinite alternate;
}
@keyframes aurora {
  0% { transform: translateX(-5%) translateY(-5%) rotate(0deg); }
  100% { transform: translateX(5%) translateY(5%) rotate(3deg); }
}
```
3. **Glow effects on accent elements**: For CTAs and active states:
```css
.cta-button {
  background: var(--accent);
  color: oklch(0.10 0.01 260);
  box-shadow:
    0 0 20px oklch(0.72 0.18 155 / 0.4),
    0 0 60px oklch(0.72 0.18 155 / 0.15);
  transition: box-shadow 0.3s cubic-bezier(0.22, 1, 0.36, 1);
}
.cta-button:hover {
  box-shadow:
    0 0 30px oklch(0.72 0.18 155 / 0.6),
    0 0 80px oklch(0.72 0.18 155 / 0.25);
}
```
4. **Card glassmorphism**: `background: oklch(0.15 0.01 260 / 0.6); backdrop-filter: blur(12px); border: 1px solid oklch(0.30 0.01 260 / 0.3); border-radius: 12px`.
5. **Typography hierarchy**: Headings in `oklch(0.95 0.01 260)` at `font-weight: 600`. Body in `oklch(0.70 0.01 260)` at `font-weight: 400`. Muted/secondary in `oklch(0.50 0.01 260)`. All using Inter Variable or Geist.
6. **Animated gradient border** (for feature cards):
```css
@property --angle { syntax: '<angle>'; initial-value: 0deg; inherits: false; }
.feature-card { background: var(--surface-1); position: relative; border-radius: 12px; }
.feature-card::before {
  content: ''; position: absolute; inset: -1px; border-radius: 13px; z-index: -1;
  background: conic-gradient(from var(--angle), transparent 60%, var(--accent) 80%, transparent 100%);
  animation: rotate-border 4s linear infinite;
}
@keyframes rotate-border { to { --angle: 360deg; } }
```
7. **Responsive**: Aurora gradient simplifies to a single radial gradient on mobile to reduce GPU load. Card glass effects reduce `backdrop-filter: blur(8px)` on mobile.

### WHY IT WORKS:
LCH color space maintains perceptual uniformity — a 0.04 lightness step looks equally different at every luminance level, unlike HSL where lightness steps become imperceptible in dark ranges. The aurora gradient activates peripheral vision, creating a sense of "space" beyond the content area (environmental affordance). The glow on CTAs uses the Helmholtz-Kohlrausch effect: saturated colors on dark backgrounds appear brighter than their measured luminance, making accent elements pop without increasing actual contrast.

### GOOD / BAD:
**GOOD**: The three-variable theme system is a maintenance dream — change one value and the entire 98-token palette updates coherently. The animated border trick is lightweight (single `@property` animation) yet visually premium.
**BAD**: `oklch()` has ~94% browser support as of April 2026; needs fallback `rgb()` values for the long tail. `backdrop-filter` is still a performance concern on low-end Android devices.

### CLEAN APPLICATION:
Apply this to **Clean's entire landing page** as the foundational design system. Clean is developer infrastructure — it must feel technically sophisticated. The LCH dark mode with aurora gradients positions Clean alongside Linear, Vercel, and Raycast in visual language. Instruction to design agent: "Implement Clean's landing page with OKLCH-based dark theme (base: near-black with blue undertone, accent: electric green matching Clean's brand). Aurora gradient backdrop on the hero section. Glassmorphic feature cards with animated conic-gradient borders. Glow-on-hover CTAs. All surface levels generated from three CSS custom properties."

---

## 5. Aceternity UI — Split-Flap Display + Mac Terminal Components

**URL**: https://ui.aceternity.com/components/text-flipping-board | https://ui.aceternity.com/components/typewriter-effect

### WHAT IT DOES:
Aceternity UI's latest component drops include two standout pieces: (1) A **split-flap display** (Vestaboard-style) that animates text character-by-character with a mechanical flip transition — each character panel rotates on a 3D axis, revealing the new character from behind the previous one, complete with a subtle shadow that simulates the physical gap between flaps. (2) A **Mac-style terminal** component with bash syntax highlighting and a typewriter effect — text appears character-by-character with a blinking cursor, inside a window chrome that mimics macOS Terminal (traffic light dots, title bar, rounded corners). Both are built with Framer Motion (now "Motion") + Tailwind CSS, zero external dependencies beyond React.

### PACKAGES:
- `react` (18+) — component framework
- `motion` (formerly `framer-motion`) v11+ — `AnimatePresence`, `motion.div`, spring physics
- `tailwindcss` v4 — utility styling
- `clsx` + `tailwind-merge` — conditional class composition
- Font: `JetBrains Mono` (terminal), `Inter` or `SF Pro` (UI)

### IMPLEMENTATION:
**Split-Flap Display:**
1. **Character grid**: Render an array of `<FlipChar>` components. Each receives a `char` prop (the target character) and a `prevChar` state (the outgoing character).
2. **Flip animation**: Each `<FlipChar>` is a `div` with `perspective: 400px`. Inside, two halves (top and bottom) clip to `50%` height. The top half has `transform-origin: bottom` and rotates from `0deg` to `-90deg` (falls forward). A second layer underneath (the new character) rotates from `90deg` to `0deg` (rises into place).
3. **Motion config**: Use `motion.div` with `animate={{ rotateX: 0 }}`, `initial={{ rotateX: 90 }}`, `transition={{ type: "spring", stiffness: 300, damping: 30, mass: 0.8 }}`. The spring physics create a natural mechanical bounce.
4. **Stagger**: Characters flip sequentially with a `delay: index * 0.06` — left to right, creating a cascading wave.
5. **Shadow simulation**: A `div` with `background: linear-gradient(to bottom, rgba(0,0,0,0.3), transparent)` and `height: 2px` sits between the top and bottom halves, simulating the physical gap.
6. **Container styling**: `bg-neutral-900 rounded-lg p-6`, each character cell `w-10 h-14 bg-neutral-800 rounded-sm flex items-center justify-center text-xl font-mono text-white`.

**Mac Terminal:**
1. **Window chrome**: Outer div with `bg-neutral-900 rounded-xl overflow-hidden border border-neutral-800`. Title bar: `h-10 flex items-center px-4 bg-neutral-800/50`. Traffic lights: three `w-3 h-3 rounded-full` divs with `bg-red-500`, `bg-yellow-500`, `bg-green-500` and `gap-2`.
2. **Content area**: `p-6 font-mono text-sm text-green-400 leading-relaxed`. Lines prefixed with `$ ` in `text-neutral-500`.
3. **Typewriter effect**: Use a recursive `setTimeout` with `delay: 40ms` per character. On each tick, increment a `charIndex` state. Render `text.slice(0, charIndex)`. Blinking cursor: `<span className="animate-pulse">▌</span>` appended after the visible text.
4. **Syntax highlighting**: Parse the command string. Keywords (`npm`, `git`, `clean`) in `text-cyan-400`. Flags (`--context`, `-v`) in `text-yellow-400`. Strings in `text-green-400`. Comments in `text-neutral-600`.
5. **Multi-line sequence**: After one command "types" out, pause 800ms, then "type" the output. Then pause 400ms, show a new prompt, and type the next command. Drive this with a state machine: `{ phase: 'typing' | 'paused' | 'output', lineIndex: number, charIndex: number }`.

### WHY IT WORKS:
The split-flap display exploits skeuomorphic nostalgia — the mechanical metaphor creates an emotional anchor that pure digital animations lack (the same reason Apple kept the click sound for keyboards). The staggered cascade creates a left-to-right reading flow that naturally draws the eye across the text. The terminal component leverages developer familiarity — for a technical audience, showing a CLI interaction is more credible than a marketing screenshot because it implies "this is a real tool that works in your environment." The typewriter pacing forces users to actually read the content instead of scanning.

### GOOD / BAD:
**GOOD**: Both components are zero-dependency beyond Motion + React — no heavy animation libraries. The spring physics on the split-flap give it a premium feel that CSS-only keyframe animations can't replicate.
**BAD**: The split-flap component can cause layout shift if the character grid width isn't pre-calculated for the longest possible string. The typewriter terminal is inaccessible — screen readers won't wait for the animation, so needs an `aria-label` with the full text upfront.

### CLEAN APPLICATION:
Apply these to Clean's **hero section**. Use the split-flap display as the headline treatment: cycle through phrases like "Context for every agent", "50% fewer tokens", "2x faster tasks", "One platform. All agents." — flipping between them every 4 seconds. Below it, render a Mac terminal component showing a real Clean CLI interaction: `$ clean init`, output showing connected agents, then `$ clean context --team` showing shared context flowing. Instruction to design agent: "Build Clean's hero with a split-flap headline (spring: stiffness 300, damping 30, stagger 0.06s, cycle every 4s between 4 phrases). Below, a Mac terminal component with typewriter effect (40ms/char, 800ms pause between commands) showing a Clean CLI session. Dark theme background with aurora gradient behind both components."

---

## BUILD DIRECTIVE

**TARGET**: Clean's landing page hero section + above-the-fold experience (tryclean.ai)

**APPROACH**: Combine the LCH dark theme system (Example 4) as the foundation with the split-flap display headline and Mac terminal demo (Example 5) as the hero content. The page opens on a deep dark background with a subtle aurora gradient (two radial gradients, blurred, slowly drifting). The split-flap display sits center-top, cycling through Clean's value props ("Context for every agent" → "50% fewer tokens" → "2x faster tasks" → "One unified platform") with spring-physics character flips every 4 seconds. Below it, a Mac terminal component shows a realistic Clean CLI session with typewriter animation — `clean init`, agent discovery output, `clean context --team` with flowing context data. Below the terminal, a single glowing CTA button with the glow intensifying on hover. The entire above-the-fold experience should feel like Linear meets Raycast: technically credible, visually atmospheric, zero fluff. No stock imagery. No generic SaaS patterns. The product demonstrates itself.

**STACK**:
- `next` (App Router) — framework
- `react` 18+ — UI
- `tailwindcss` v4 — styling with OKLCH custom properties
- `motion` (framer-motion v11+) — split-flap springs, entrance animations
- `gsap` + `ScrollTrigger` — scroll-driven sections below the fold
- `geist` font — Vercel's variable font for body + headings
- `jetbrains-mono` — terminal component
- `clsx` + `tailwind-merge` — utility class composition

**REFERENCE URLS**:
- https://linear.app — dark theme, color system, elevation hierarchy, glow effects
- https://ui.aceternity.com/components/text-flipping-board — split-flap display component reference
- https://ui.aceternity.com/components/typewriter-effect — terminal typewriter reference
- https://www.awwwards.com/sites/artefakt — shader reveals for below-fold content

**KEY CONSTRAINTS**:
- Clean is developer infrastructure. The result must feel like a tool built by engineers for engineers. No rounded-corner-SaaS-blobby-gradient-stock-photo patterns.
- Performance budget: <200KB JS gzipped, 60fps on M1 MacBook Air, Lighthouse performance score >90.
- No 3D for the hero — save Three.js for the product demo section below the fold. The hero should be fast, crisp, and load instantly.
- The terminal demo must show realistic Clean commands, not placeholder code. It should demonstrate the actual value prop (agents sharing context) in the CLI output.
- Aurora gradient must be subtle — atmospheric, not distracting. Think "glow from behind" not "rave lights."
- Mobile: split-flap scales down to viewport width, terminal becomes horizontally scrollable, aurora simplifies to single gradient.
- Accessibility: split-flap needs `aria-label` with current text. Terminal needs `aria-label` with full content. All color contrast ratios must pass WCAG AA on the dark background.
