# UI Intelligence Report — March 26, 2026

> Design agent creative brief. Every detail below is implementation-ready. No fluff.

---

## 1. Darknode — https://www.awwwards.com/sites/darknode

**WHAT IT DOES:**
A dark-themed product site built on Webflow with GSAP-driven scroll animations and Blender-rendered 3D models composited into the DOM. The layout is a vertical scroll narrative: full-viewport sections pin and transition via GSAP ScrollTrigger, with 3D assets fading or rotating into view as the user scrolls. The color system is brutally minimal — two colors only (#F90000 red accent, #14181E dark background) — creating extreme contrast that directs the eye to CTAs and key content. Microinteractions on hover states and a custom-animated footer round out the experience. Awwwards SOTD March 21, 2026, scored 7.31/10.

**PACKAGES:**
- Webflow (no-code layout + CMS)
- GSAP 3.12+ (ScrollTrigger, SplitText plugins)
- Blender 4.x (3D asset pipeline, exported as .glb/.gltf or pre-rendered PNGs)
- Three.js or Spline (if 3D is real-time; otherwise static renders from Blender)
- Custom CSS (no Tailwind — Webflow native styles)

**IMPLEMENTATION:**
1. **Layout:** Full-viewport sections (`height: 100vh; overflow: hidden`) stacked vertically. Each section is a ScrollTrigger pin target.
2. **Color system:** CSS custom properties `--bg: #14181E; --accent: #F90000; --text: #FFFFFF`. No gradients. Pure flat contrast.
3. **Scroll animations:** GSAP ScrollTrigger with `scrub: 0.8` for smooth scroll-linked motion. Pin each section with `pin: true, anticipatePin: 1`. Stagger child elements with `stagger: 0.12, ease: "power2.out"`.
4. **3D integration:** Blender models exported as .glb, loaded via Three.js GLTFLoader. Position models in a persistent canvas overlay (`position: fixed; top: 0; left: 0; pointer-events: none; z-index: 1`). Animate model rotation/position with GSAP tied to scroll progress.
5. **Microinteractions:** Hover states on buttons use `scale: 1.05, duration: 0.3, ease: "power1.out"`. Red accent elements pulse subtly on hover with `opacity` oscillation.
6. **Typography:** Large sans-serif headings (likely Geist or Inter), 80-120px on desktop, `font-weight: 700`. Body at 16-18px, `font-weight: 400`, `line-height: 1.6`.
7. **Footer:** Custom animated footer with staggered link reveals on scroll-into-view.

**WHY IT WORKS:**
Maximum contrast ratio between two colors creates an unmistakable visual hierarchy — the red literally screams against the dark field (contrast ratio ~8:1). The restraint of only two colors eliminates decision fatigue and makes every red element feel intentional. Scroll-pinned sections enforce linear storytelling, preventing cognitive overload. 3D models provide depth cues that break the flatness of 2D web without sacrificing performance.

**GOOD / BAD:**
- GOOD: The two-color system is genuinely best-in-class restraint — proves you don't need gradients or 47 shades to make impact.
- BAD: Heavy reliance on scroll-pinning can feel sluggish on trackpads; 3D assets increase initial load time significantly.

**CLEAN APPLICATION:**
Apply this to Clean's landing page hero and feature sections. Use Clean's brand dark background with a single vivid accent color (electric blue or green — whatever maps to "developer infrastructure"). Pin each feature section (MCP proxy, token compression, shared context) as its own full-viewport scroll-triggered panel. Load a single lightweight .glb model (abstract node graph or data flow visualization) in a fixed canvas overlay that transforms as the user scrolls through features. Instruction to design agent: "Build Clean's landing page as a scroll-pinned dark narrative with one accent color, full-viewport feature sections, and a single persistent 3D element that morphs per section."

---

## 2. Codrops Scroll-Reactive 3D Gallery — https://tympanus.net/codrops/2026/03/09/building-a-scroll-reactive-3d-gallery-with-three-js-velocity-and-mood-based-backgrounds/

**WHAT IT DOES:**
A depth-based WebGL image gallery built in vanilla Three.js where images are stacked along the Z-axis (not a flat carousel). Each image defines its own color palette that drives the page background via smooth gradient interpolation. The scroll velocity itself becomes a design signal — faster scrolling increases motion intensity, lifts elements, and adds "wind-like" trail effects. The result feels less like a slideshow and more like walking through a mood. Built by Houmahani Kane, published March 9, 2026.

**PACKAGES:**
- three (Three.js core, latest stable)
- vite (build tool)
- No GSAP — pure requestAnimationFrame loop with custom easing
- Custom GLSL shaders (vertex + fragment for image planes)
- No UI framework — vanilla JS + HTML/CSS

**IMPLEMENTATION:**
1. **Scene setup:** `THREE.PerspectiveCamera` with `fov: 45`, positioned at `z: 10`. Images are `THREE.PlaneGeometry` meshes distributed along Z-axis with ~2-3 unit spacing.
2. **Scroll mapping:** Listen to `wheel` event, accumulate `deltaY` into a `targetScroll` value. Lerp current scroll toward target: `currentScroll += (targetScroll - currentScroll) * 0.08` per frame. This gives buttery deceleration.
3. **Velocity calculation:** `velocity = currentScroll - previousScroll` computed per frame. Normalize to 0-1 range. Feed into uniforms for shaders and into JS animation params.
4. **Z-depth navigation:** Camera `position.z` is driven by scroll value. As user scrolls, camera moves forward through the Z-stacked images. Each image has `position.z = index * -spacing`.
5. **Mood-based backgrounds:** Each image defines a `palette: [color1, color2]`. As camera Z approaches an image's Z, interpolate `document.body.style.background` between current and next palette using `THREE.Color.lerp()` mapped to scroll position.
6. **Velocity-driven motion:** When velocity is high, apply subtle `rotation.z` to image planes (±0.02 rad), add `position.y` oscillation (sine wave, amplitude scaled by velocity), and increase a "trail" particle system opacity.
7. **Editorial text:** HTML overlays positioned absolutely, fading in/out based on camera Z proximity to each image. Use CSS `opacity` transition with `transition: opacity 0.4s ease`.
8. **Performance:** Use `THREE.TextureLoader` with `texture.minFilter = THREE.LinearFilter` to avoid mipmap overhead. Limit to 8-12 images max. Use `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`.

**WHY IT WORKS:**
The Z-axis stacking exploits the depth perception cue of linear perspective — objects receding into depth create a natural sense of journey and discovery (Gestalt principle of continuity). The velocity-as-signal pattern turns passive scrolling into expressive interaction — the page literally responds to *how* you scroll, not just *where*. Mood-based palette shifts create subconscious emotional transitions between content zones.

**GOOD / BAD:**
- GOOD: The velocity-as-design-signal pattern is genuinely novel and reusable in any scroll-heavy context. The mood palette system is elegant.
- BAD: Vanilla Three.js without R3F means no React integration — you'd need to port the architecture. Performance degrades with more than ~15 images.

**CLEAN APPLICATION:**
Apply this to Clean's product demo or "How It Works" section. Stack Clean's core concepts along the Z-axis: (1) Raw codebase → (2) Tree Sitter parsing → (3) TOON compression → (4) Shared context delivery. Each "depth layer" gets its own mood palette (cold blue for raw code, warm green for parsed, electric purple for compressed, bright white for delivered). Scroll velocity controls particle density in a data-flow visualization. Instruction to design agent: "Build a Z-depth scrolling product explainer for Clean where each processing stage is a depth layer with its own color mood, and scroll velocity drives particle/data-flow animation intensity."

---

## 3. Seamless 3D Page Transitions (Mael Ruffini) — https://tympanus.net/codrops/2026/03/18/building-seamless-3d-transitions-with-webflow-gsap-and-three-js/

**WHAT IT DOES:**
A multi-page site where a persistent Three.js canvas stays alive across page navigations (powered by Barba.js). When the user navigates, Barba intercepts the browser, fades the DOM content, then GSAP animates the Three.js camera to a new position and swaps the visible 3D model — all without reloading the canvas. The 3D models are hand-textured in Blender with a hand-drawn UV painting style (not procedural). The result feels like one continuous 3D world with HTML content floating inside it. Published March 18, 2026.

**PACKAGES:**
- three (Three.js core)
- gsap (GSAP 3.12+ with SplitText)
- @barba/core (Barba.js for SPA-like page transitions)
- vite (bundler — single JS bundle injected into Webflow)
- Blender 4.x (3D modeling + hand-drawn UV textures)

**IMPLEMENTATION:**
1. **Architecture:** Three.js renderer is created ONCE in a `<canvas>` element that sits OUTSIDE Barba.js's content wrapper. Barba only swaps `.barba-container` children. The canvas persists.
2. **Camera rig:** GSAP animates `camera.position` and `camera.lookAt` target between predefined waypoints. Each "page" has a camera position: `{ x, y, z, lookAt: { x, y, z } }`. Transition uses `gsap.to(camera.position, { ...targetPos, duration: 1.2, ease: "power2.inOut" })`.
3. **Model swapping:** All models are pre-loaded at init. On navigation, GSAP fades current model out (`gsap.to(currentModel.material, { opacity: 0, duration: 0.4 })`), then fades new model in after camera arrives.
4. **Hand-drawn textures:** In Blender: model with rough geometry → UV unwrap → export UV layout as PNG → draw on UV in Photoshop/Procreate → apply as `map` in `THREE.MeshBasicMaterial`. This gives a hand-crafted feel that's distinctive.
5. **DOM text animations:** GSAP SplitText splits headings into chars. On page enter: `gsap.from(chars, { y: 40, opacity: 0, stagger: 0.03, duration: 0.8, ease: "power3.out" })`. On page leave: reverse with `duration: 0.4`.
6. **Barba lifecycle:** `barba.init({ transitions: [{ leave: async (data) => { /* GSAP out animation */ }, enter: async (data) => { /* GSAP in animation + camera move */ } }] })`.
7. **Performance:** Pre-load all .glb models on initial page load. Use `THREE.DRACOLoader` for compressed geometry. Keep polygon counts under 10k per model.

**WHY IT WORKS:**
The persistent 3D canvas eliminates the jarring "page reload" break that destroys spatial memory. Users build a mental model of a continuous space, which increases engagement and reduces bounce (spatial continuity principle). The hand-drawn textures create warmth and personality that counteracts the "cold" feel of most 3D web. GSAP's easing profiles (power2.inOut) match natural human motion expectations.

**GOOD / BAD:**
- GOOD: The persistent-canvas-across-navigation pattern is technically elegant and creates a truly unique browsing feel. The hand-drawn texture approach is refreshingly anti-corporate.
- BAD: Requires Barba.js which adds routing complexity. Not easily portable to Next.js/React Router without significant refactoring. Pre-loading all models increases initial bundle.

**CLEAN APPLICATION:**
Apply this to Clean's documentation or product tour. Instead of flat docs pages, create a persistent 3D "space station" or "control room" scene. As users navigate between docs sections (Setup → Configuration → API Reference → Team Context), the camera moves through the 3D space and different "stations" light up. The 3D models should be technical/schematic — wireframe nodes, circuit-board aesthetics — not hand-drawn (wrong vibe for dev infra). Instruction to design agent: "Build Clean's product tour as a persistent Three.js scene with camera-driven navigation between sections. Use technical/schematic 3D aesthetics (wireframes, node graphs, circuit traces). Barba.js or React Router for SPA transitions. Each section = a camera waypoint in the 3D space."

---

## 4. Aceternity UI — Dither Shader + ASCII Art Components — https://ui.aceternity.com/components/dither-shader / https://ui.aceternity.com/components/ascii-art

**WHAT IT DOES:**
Two new Aceternity UI components that bring retro-computational aesthetics to modern React apps. The **Dither Shader** applies real-time ordered dithering to any image using WebGL fragment shaders, creating pixel-art/retro aesthetics. The **ASCII Art** component transforms images into animated ASCII art rendered on `<canvas>`, with 15+ charset presets (standard, blocks, binary, braille, dots, circles, stars), multiple animation modes (fade, typewriter, matrix rain), and color support using the original image palette or custom Tailwind variables. Both ship as copy-paste React components with Tailwind CSS + Framer Motion.

**PACKAGES:**
- react, react-dom (18+)
- tailwindcss (v4)
- framer-motion (latest, now called "motion")
- Next.js 15 (optional, works with any React framework)
- TypeScript
- For Dither Shader: WebGL 2.0 context, custom fragment shader (inline GLSL string)
- For ASCII Art: Canvas 2D API, no WebGL required

**IMPLEMENTATION:**
1. **ASCII Art component:**
   - Props: `src: string` (image URL), `charset?: string` (preset name or custom chars), `animation?: "fade" | "typewriter" | "matrix"`, `colorMode?: "original" | "custom"`, `fontSize?: number` (controls grid resolution), `className?: string`.
   - Rendering: Load image → draw to offscreen canvas → sample pixel grid at `fontSize` intervals → map brightness (`0.299*R + 0.587*G + 0.114*B`) to charset index → render chars to visible canvas using `ctx.fillText()`.
   - Animation: Typewriter reveals chars left-to-right, top-to-bottom with `requestAnimationFrame`. Matrix rain randomly reveals/hides chars. Fade uses opacity interpolation.
   - Responsive: Canvas resizes via `ResizeObserver`. Recalculates grid on resize.
   - Visibility: Uses `IntersectionObserver` to start animation only when in viewport.

2. **Dither Shader component:**
   - Props: `src: string`, `threshold?: number` (0-1), `patternSize?: number` (2, 4, or 8 for Bayer matrix size), `colorA?: string`, `colorB?: string`.
   - Rendering: WebGL 2.0 canvas. Vertex shader is passthrough. Fragment shader implements ordered dithering: sample texture color → convert to luminance → compare against Bayer matrix value at `gl_FragCoord.xy % patternSize` → output colorA or colorB.
   - Bayer matrix (4x4): `[0,8,2,10, 12,4,14,6, 3,11,1,9, 15,7,13,5] / 16.0` passed as uniform.
   - Animation: `threshold` can be animated with Framer Motion's `useMotionValue` + `useTransform` for scroll-linked dithering effects.

3. **Integration:** Both components install via Aceternity CLI: `npx aceternity-ui add ascii-art dither-shader`. ShadCN CLI 3.0 compatible.

**WHY IT WORKS:**
The retro-computational aesthetic taps into developer nostalgia and signals "we understand the craft" — dithering and ASCII art are deeply embedded in computing history. These effects create visual distinctiveness in a sea of gradient-mesh, glassmorphism sameness. The Bayer matrix dithering specifically creates ordered, grid-aligned patterns that feel intentional rather than noisy, satisfying the human preference for structured complexity (Gestalt principle of Prägnanz).

**GOOD / BAD:**
- GOOD: The ASCII Art component's visibility-aware animation is smart — no wasted GPU cycles. Both components are genuinely copy-paste with zero config overhead. The dither shader is lightweight WebGL.
- BAD: ASCII art can be unreadable at small sizes or on mobile. Dither shader requires WebGL 2.0 which excludes some older devices. Both are style-over-substance if overused.

**CLEAN APPLICATION:**
Apply the Dither Shader to Clean's hero section background — take a screenshot of a codebase or terminal output, apply real-time dithering that resolves to full clarity as the user scrolls down (animate `threshold` from 1.0 to 0.0 on scroll). This creates a "decoding" metaphor that maps perfectly to Clean's context-resolution product story. Apply ASCII Art to Clean's loading states or 404 page — render the Clean logo as animated ASCII with the "matrix rain" preset. Instruction to design agent: "Use Aceternity's Dither Shader on Clean's hero: dithered code screenshot that resolves on scroll. Use ASCII Art for loading/404 states with matrix rain animation on the Clean logo."

---

## 5. Spline Omma AI Canvas — https://omma.build/

**WHAT IT DOES:**
Spline's new AI canvas (launched March 24, 2026) generates fully interactive 3D web experiences from natural language prompts. Under the hood, multiple AI agents run in parallel — one generates code via LLM, a second creates 3D meshes, a third generates images — all from a single text prompt. The output is production-ready interactive content combining 3D scenes, motion design, animation, and functional UI. This isn't a static mockup generator; it produces shippable interactive experiences. Pricing starts at $29/month. Used by Google, Datadog, Robinhood, UPS, Scale AI, Resend.

**PACKAGES:**
- Spline Runtime (@splinetool/runtime) for embedding output
- React integration: @splinetool/react-spline
- Omma-generated experiences export as embeddable iframes or Spline scenes
- For custom integration: Spline's Code API
- SwiftUI integration available for native iOS

**IMPLEMENTATION:**
1. **Generation workflow:** Prompt → Omma AI → parallel agent execution (code LLM + 3D mesh gen + image gen) → interactive scene output.
2. **Embedding in React:**
   ```jsx
   import Spline from '@splinetool/react-spline';
   <Spline scene="https://prod.spline.design/[scene-id]/scene.splinecode" />
   ```
3. **Event handling:** Spline scenes expose event hooks: `onMouseDown`, `onMouseHover`, `onKeyDown`. Map these to React state for interactive product demos.
4. **Performance considerations:** Spline scenes are WebGL-based. Use `loading="lazy"` on the Spline component. Preload critical scenes. Keep polygon counts reasonable (Omma tends to generate complex scenes — may need manual simplification).
5. **Custom code layer:** Use Spline's Code API to programmatically control scene state, camera positions, material properties, and animations from your React app.
6. **Responsive:** Spline scenes auto-resize but need explicit `width: 100%; height: 100%` on the container. Test on mobile — complex scenes may need simplified mobile variants.

**WHY IT WORKS:**
Omma democratizes 3D web experiences that previously required a Three.js expert + 3D artist + designer. The parallel-agent architecture means generation is fast (minutes, not weeks). The output is interactive by default, not static — this matches the "show, don't tell" principle that drives engagement. The Spline ecosystem (3M+ users, Fortune 500 adoption) means the runtime is battle-tested.

**GOOD / BAD:**
- GOOD: Genuinely production-ready output from prompts. The parallel agent architecture is smart engineering. React/SwiftUI integrations make embedding trivial.
- BAD: Vendor lock-in to Spline's runtime. Generated scenes can be bloated and hard to optimize. $29/month adds up. You lose fine-grained control compared to hand-coded Three.js.

**CLEAN APPLICATION:**
Use Omma to rapidly prototype Clean's interactive product demo — prompt: "A 3D visualization of data flowing from multiple code files through a compression pipeline into a glowing central node, with particles representing tokens being filtered and compressed. Dark background, electric blue and green accents, technical aesthetic." Embed the resulting Spline scene in Clean's landing page "How It Works" section. The 3D interactivity lets visitors mouse around the data flow, creating engagement. Instruction to design agent: "Generate a Spline/Omma scene showing Clean's data compression pipeline as an interactive 3D node graph. Embed it in the How It Works section. Use @splinetool/react-spline for integration. Dark bg, blue/green accents."

---

## BUILD DIRECTIVE

**TARGET:** Clean's landing page hero + "How It Works" product explainer section (tryclean.ai)

**APPROACH:** Build a dark-themed, scroll-driven landing page that combines Darknode's two-color discipline with the Codrops scroll-reactive depth pattern. The hero section opens with a dithered code screenshot (Aceternity Dither Shader) that resolves to clarity on scroll — a visual metaphor for "Clean resolves your codebase context." Below the fold, the "How It Works" section uses Z-depth scroll navigation: four depth layers represent Clean's pipeline stages (Raw Code → Tree Sitter Parsing → TOON Compression → Shared Context Delivery), each with its own mood palette that shifts the background. Scroll velocity controls particle density in a data-flow visualization threading through all four layers. Pin each stage for focused reading, then release. The entire experience should feel like Linear meets the Codrops depth gallery — technically precise, buttery smooth, developer-sophisticated.

**STACK:**
- Next.js 15 (App Router)
- React 19
- Tailwind CSS v4
- GSAP 3.12+ (ScrollTrigger, SplitText)
- Three.js + React Three Fiber (@react-three/fiber, @react-three/drei)
- Framer Motion (for DOM element animations)
- Aceternity UI (Dither Shader component)
- Geist font (mono + sans — matches developer aesthetic)
- Lucide React (icons)

**REFERENCE URLS:**
1. https://www.awwwards.com/sites/darknode — Color system, scroll pinning, dark theme execution
2. https://tympanus.net/codrops/2026/03/09/building-a-scroll-reactive-3d-gallery-with-three-js-velocity-and-mood-based-backgrounds/ — Z-depth navigation, velocity-as-signal, mood palettes
3. https://ui.aceternity.com/components/dither-shader — Hero dithering effect, scroll-linked resolution

**KEY CONSTRAINTS:**
- Clean is developer infrastructure (MCP proxy, token compression). The result MUST feel like Linear/Vercel/Resend — technically sophisticated, fast, precise. Not flashy, not agency-portfolio, not startup-generic.
- No gradients blobs. No glassmorphism. No rainbow mesh gradients. These are consumer SaaS clichés.
- Two-color system maximum: dark base (#0A0A0F or similar near-black) + one electric accent (suggest #00E5FF cyan or #4ADE80 green — test both).
- Performance budget: LCP < 1.5s, total JS < 200KB gzipped (excluding Three.js scene assets). Lazy-load the 3D depth gallery below the fold.
- Typography: Geist Mono for code/technical elements, Geist Sans for headings/body. No decorative fonts.
- Mobile: The 3D depth gallery degrades to a stacked card layout with CSS scroll-snap on mobile. Dither shader runs at half resolution on mobile (`devicePixelRatio: 1`).
- Avoid: particle.js (cliché), globe visualizations (overused in dev tools), animated SVG logo reveals (played out), "trusted by" logo bars above the fold (move to social proof section below).
