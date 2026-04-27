# Daily UI Intelligence Brief — April 1, 2026

---

## 1. Arnaud Rocca Portfolio — https://arnaudrocca.fr/

WHAT IT DOES:
A freelance creative developer portfolio built on Nuxt.js (SSG) with a reusable GSAP animation system and a custom OGL-based fluid WebGL layer. The home page presents project thumbnails in a grid; on hover, a fluid distortion simulation (Navier-Stokes fragment shader via OGL) warps the thumbnail imagery in real-time. Page transitions use GSAP-driven crossfades with staggered text split animations. The entire animation layer is architected as auto-registered GSAP effects using Vite's `import.meta.glob()` pattern — each effect lives in its own file, follows a naming convention, and is discovered at build time. A custom Vue composable wraps GSAP's `useGSAP` React hook equivalent, scoping all ScrollTriggers and timelines to specific elements/refs with reactive dependency support.

PACKAGES:
- `nuxt` (v3, SSG mode) — framework
- `gsap` + `gsap/ScrollTrigger` + `gsap/SplitText` — animation engine
- `ogl` — minimal WebGL renderer (fluid simulation shaders)
- `vite` — bundler (glob imports for effect auto-registration)
- `sass` / `scss` — styling
- Font: likely a geometric sans (inspect site; fallback recommendation: `Inter` or `Space Grotesk` via `@fontsource/inter`)

IMPLEMENTATION:
1. **Project structure**: `/effects/` folder containing individual GSAP effect files (e.g., `fadeUp.ts`, `splitReveal.ts`, `staggerIn.ts`). Each exports a GSAP effect registration object. An `index.ts` barrel file uses `import.meta.glob('./*.ts', { eager: true })` to auto-register all effects with `gsap.registerEffect()`.
2. **Vue composable — `useAnimation()`**: Wraps GSAP context creation, accepts a template ref as scope. On mount, creates `gsap.context()` scoped to that ref. On unmount, reverts. Watches reactive deps to re-trigger animations.
3. **Fluid WebGL layer**: Create an OGL `Renderer` and `Camera` in a parent `<WebGLCanvas>` component. Use Vue's `provide/inject` to share the renderer instance with child components. The `FluidSimulation` class implements a post-processing fluid distortion shader based on Pavel Dobryakov's Navier-Stokes GLSL (advection, divergence, pressure, gradient subtraction passes). Mouse position drives the velocity field via pointer events mapped to UV space.
4. **Key animation configs**:
   - Text reveal: `gsap.from('.split-char', { y: '110%', opacity: 0, duration: 0.8, ease: 'power3.out', stagger: 0.03 })`
   - Page transition out: `gsap.to('.page-content', { opacity: 0, y: -20, duration: 0.4, ease: 'power2.inOut' })`
   - Thumbnail hover scale: `gsap.to('.thumb', { scale: 1.03, duration: 0.6, ease: 'power2.out' })`
   - Fluid distortion intensity on hover: lerp from 0 to 0.5 over ~300ms
5. **Responsive**: On mobile (< 768px), the WebGL fluid layer is disabled entirely — replaced with a CSS cross-fade between thumbnails. Scroll-based animations wrapped in `gsap.matchMedia()` with breakpoints at 768px and 1024px.
6. **Performance**: Nuxt SSG for static HTML. `<noscript>` CSS fallbacks for no-JS accessibility. OGL chosen over Three.js for ~10x smaller bundle. Fluid sim runs at half resolution (canvas width/2, height/2) and uses `requestAnimationFrame` with frame skipping on low-end devices.

WHY IT WORKS:
The portfolio achieves authority through restraint — minimal color palette with maximum motion sophistication. The fluid distortion acts as a physicality signal: it says "this developer understands shaders" without being gratuitous. Gestalt proximity groups the project grid naturally, while the staggered text reveals create a rhythmic reading cadence that guides the eye top-to-bottom. The consistent easing (`power3.out` for entrances, `power2.inOut` for transitions) creates a unified motion language.

GOOD: Best-in-class reusable animation architecture — the auto-registered GSAP effects pattern is production-grade and directly portable.
BAD: The fluid simulation, while beautiful, adds ~150KB of shader code and a persistent GPU draw call that mobile devices don't need.

CLEAN APPLICATION:
Apply the GSAP auto-registration architecture to Clean's component library. Create a `/lib/animations/` folder with individual effect files (`fadeUp`, `staggerReveal`, `slideIn`, `counterUp`) using `import.meta.glob()` for zero-config animation registration. Use this system in Clean's landing page hero to animate the headline ("The context layer for AI agents") with a `SplitText` character stagger reveal (0.03s stagger, `power3.out`), and apply the fluid distortion hover effect to Clean's product feature cards to signal technical depth.

---

## 2. Utopia Tokyo — https://utopiatokyo.com/

WHAT IT DOES:
An interactive web experience merging Japanese mask culture with cyberpunk aesthetics, built on Webflow with custom GSAP animations. The site opens with a seizure warning offering Safe Mode vs Glitch Effect toggle. The hero presents a 3D-rendered Samurai mask floating against a red-dominant (#CC0000 on near-black #0A0A0A) background with oversized monospaced typography and giant kanji characters. The core experience is a gallery of 14 Japanese masks displayed in togglable grid/list views, each rendered as a detailed 3D object with animated targeting-reticle frame corners. A personality quiz uses a radar chart where visitors allocate 60 skill points across 6 attributes, matching their profile to a mask. Built from an Awwwards Masterclass by Niccolo Miranda.

PACKAGES:
- Webflow — CMS and hosting
- `gsap` + `gsap/ScrollTrigger` + `gsap/SplitText` — all page animations and scroll-driven reveals
- Custom GLSL / CSS filters for glitch effects (not Three.js — CSS-based distortion with `mix-blend-mode` and keyframed `clip-path` slicing)
- Font: monospaced display (likely `Space Mono` or `JetBrains Mono`), kanji rendered as SVG or custom font
- Radar chart: likely `chart.js` or custom SVG with GSAP-animated paths

IMPLEMENTATION:
1. **Layout system**: CSS Grid for the mask gallery. Grid view: `grid-template-columns: repeat(auto-fill, minmax(280px, 1fr))` with 24px gap. List view: single column with horizontal card layout. Toggle animates with `gsap.to('.gallery', { gridTemplateColumns: '1fr', duration: 0.4 })`.
2. **Hero section**: Full-viewport `100vh` with `position: relative`. Mask image is `position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%)`. Background: `radial-gradient(ellipse at center, #1a0000 0%, #0A0A0A 70%)`. Typography: monospaced, `font-size: clamp(3rem, 8vw, 7rem)`, `color: #CC0000`, `mix-blend-mode: difference`.
3. **Glitch effect**: CSS keyframe animation cycling `clip-path: inset()` values every 50ms with `transform: translate()` offsets of 2-5px. A pseudo-element duplicates the text with `color: #00FFFF` offset by 2px for chromatic aberration. Toggle-able via CSS class `.glitch-active`.
4. **Targeting reticle animation on mask cards**: Four corner elements (`::before`, `::after` on two wrapper divs) with `border` on two sides each, animated with `gsap.to('.corner', { scale: 1.1, opacity: [0.5, 1], duration: 1.2, ease: 'sine.inOut', repeat: -1, yoyo: true })`.
5. **Radar chart quiz**: SVG `<polygon>` with 6 vertices. GSAP morphs the polygon points as users adjust sliders. `gsap.to(polygon, { attr: { points: newPointString }, duration: 0.3, ease: 'power2.out' })`.
6. **Scroll-driven reveals**: Each mask card uses `ScrollTrigger` with `start: 'top 85%'`, `toggleActions: 'play none none none'`. Entry: `gsap.from('.mask-card', { y: 60, opacity: 0, duration: 0.7, ease: 'power2.out', stagger: 0.1 })`.

WHY IT WORKS:
The red-on-black palette exploits maximum luminance contrast for immediate visual impact — the color scheme is borrowed from danger/alert UX patterns but repurposed for cultural drama. The grid/list toggle provides agency, increasing time-on-site. The personality quiz creates investment through the IKEA effect (users value what they help build). The monospaced type signals technical/cyberpunk credibility while the kanji creates exotic texture that rewards visual scanning.

GOOD: The accessibility toggle (Safe Mode) is genuinely best-in-class — it acknowledges that glitch effects can trigger photosensitive conditions while keeping them opt-in.
BAD: The 3D mask renders are pre-baked images, not real-time 3D — the "interactive" promise is slightly overstated. Heavy on Webflow's runtime JS.

CLEAN APPLICATION:
Apply the glitch-toggle pattern and monospaced typography treatment to Clean's developer-facing documentation or CLI showcase section. Use the CSS glitch effect (chromatic aberration + clip-path slicing) on Clean's hero headline as a subtle, toggle-able "terminal aesthetic" that activates on hover. Apply the radar chart interaction pattern to Clean's feature comparison section — let visitors allocate importance across dimensions (speed, security, flexibility, cost, observability) and show how Clean scores on their personal priority matrix.

---

## 3. Seamless 3D Page Transitions (Codrops/Mael Ruffini) — https://tympanus.net/codrops/2026/03/18/building-seamless-3d-transitions-with-webflow-gsap-and-three-js/

WHAT IT DOES:
A gallery-style web experience with a persistent Three.js scene that never reloads between page navigations. Webflow handles layout and CMS; Barba.js intercepts link clicks to prevent full page reloads; GSAP (with SplitText) drives text and UI animations; Three.js renders a continuous 3D scene containing hand-UV-mapped objects (a pen, ceramic cup, Suzanne head) that rotate and transition as users navigate. The JavaScript is built with Vite as a single bundle injected into Webflow. The result: page changes feel like moving through a single 3D space rather than jumping between HTML documents.

PACKAGES:
- Webflow — layout, CMS, hosting
- `three` (Three.js) — 3D scene, camera, renderer, `GLTFLoader`, `OrbitControls`
- `gsap` + `gsap/ScrollTrigger` + `gsap/SplitText` — text animations, UI transitions
- `@barba/core` — SPA-like page transitions without full reload
- `vite` — build tool, single bundle output
- Fonts: likely a serif + mono combination (inspect demo)

IMPLEMENTATION:
1. **Persistent scene architecture**: Initialize `THREE.WebGLRenderer` and append canvas to a fixed-position container (`position: fixed; top: 0; left: 0; z-index: -1; pointer-events: none`). This element persists across Barba transitions. The renderer is created ONCE on initial page load and never destroyed.
2. **Barba.js integration**: `barba.init({ transitions: [{ leave({ current }) { return gsap.to(current.container, { opacity: 0, duration: 0.4 }) }, enter({ next }) { return gsap.from(next.container, { opacity: 0, duration: 0.4 }) } }] })`. Inside `leave`, GSAP also animates the Three.js camera position to transition to the next scene state.
3. **Camera transitions**: On navigation, GSAP tweens `camera.position` and `camera.lookAt` target: `gsap.to(camera.position, { x: targetX, y: targetY, z: targetZ, duration: 1.2, ease: 'power2.inOut' })`. Each page defines its camera position in a config map.
4. **3D objects**: Loaded via `GLTFLoader`. UV maps hand-drawn in Photoshop — sketch-like texture quality. Objects float with subtle `Math.sin(time * 0.5) * 0.1` bobbing animation in the render loop.
5. **Text animations**: On page enter, `SplitText` splits heading into chars, then: `gsap.from('.char', { y: '100%', opacity: 0, duration: 0.6, ease: 'power3.out', stagger: 0.02, delay: 0.3 })`.
6. **Vite build**: `vite build --outDir dist` producing a single `main.js` bundle. Webflow references this as `<script src="/dist/main.js" type="module">`.
7. **Performance**: Three.js renderer uses `antialias: true, alpha: true, powerPreference: 'high-performance'`. Scene has < 10 objects total. Textures compressed to WebP. Render loop uses `requestAnimationFrame` with `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`.

WHY IT WORKS:
The persistent 3D scene eliminates the cognitive "reset" that normally occurs during page transitions — the user's spatial model is maintained, creating a sense of physical place rather than document navigation. The hand-drawn UV textures add warmth and craft that counterbalances the technical complexity, following the wabi-sabi principle. The combination of SplitText stagger reveals with camera movement creates a synchronized choreography that makes navigation feel intentional rather than instantaneous.

GOOD: The persistent-scene pattern is architecturally elegant and creates a genuinely novel browsing experience that most visitors have never encountered.
BAD: Webflow + Vite + Barba + Three.js + GSAP is five abstractions deep — debugging is painful, and Webflow's CMS rerenders can break the Barba integration.

CLEAN APPLICATION:
Apply the persistent 3D scene pattern to Clean's product demo section. Create a fixed Three.js canvas showing Clean's architecture as a 3D node graph (agents → Clean proxy → context layer → tools). As the user scrolls through feature sections, GSAP tweens the camera to focus on the relevant part of the architecture diagram. When the user reaches "MCP Proxy" section, the camera zooms to the proxy node; at "Context Layer" section, it orbits to show the TOON storage. This creates a spatial mental model of Clean's architecture that users retain better than flat diagrams.

---

## 4. Spline Omma Launch — https://omma.build/

WHAT IT DOES:
Spline's Omma is an AI canvas that generates production-ready 3D websites, motion design, and interactive apps from text prompts. The launch page itself showcases the product by embedding live Spline scenes directly into the marketing page — 3D objects that respond to cursor movement with real-time lighting and material changes. The layout uses a full-bleed hero with an embedded Spline viewer (`<spline-viewer>` web component), followed by feature sections that each contain their own interactive 3D scene demonstrating a capability (text-to-3D, text-to-animation, text-to-UI). A multi-agent architecture visualization shows three parallel pipelines (code gen, mesh creation, image generation) converging into a single output.

PACKAGES:
- `@splinetool/runtime` or `@splinetool/react-spline` — 3D scene embedding
- React or Next.js — framework (Spline's marketing stack)
- `tailwindcss` — utility styling
- `framer-motion` — section transitions and scroll animations
- `@fontsource/inter` or system font stack — typography
- Spline scenes exported as `.splinecode` files — self-contained 3D bundles

IMPLEMENTATION:
1. **Hero section**: Full viewport. `<Spline scene="https://prod.spline.design/[hash]/scene.splinecode" />` centered with `width: 100%; height: 100vh; position: relative`. The Spline scene has cursor-tracking enabled — objects rotate toward mouse position via Spline's built-in event system.
2. **Feature sections**: Each section is `min-height: 100vh` with a two-column layout: left column text (heading + description), right column embedded Spline scene. On scroll, `framer-motion`'s `useInView` triggers fade-in: `initial={{ opacity: 0, y: 40 }} animate={{ opacity: 1, y: 0 }} transition={{ duration: 0.6, ease: [0.25, 0.1, 0.25, 1] }}`.
3. **Multi-agent visualization**: Three vertical lanes with animated data-flow particles (CSS `@keyframes` moving dots along SVG paths, or Framer Motion `motion.div` with `x` animation repeating infinitely).
4. **Responsive**: Spline scenes scale via container queries. On mobile (< 768px), scenes are replaced with static renders or simplified scenes with fewer polygons. Text sections go full-width single column.
5. **Loading strategy**: Spline runtime lazy-loaded via dynamic import. Intersection Observer triggers scene loading only when the section enters viewport. Placeholder: blurred static image with skeleton shimmer.
6. **Typography**: Inter or similar neo-grotesque, `font-size: clamp(2.5rem, 5vw, 4.5rem)` for headings, `1.125rem` for body. `letter-spacing: -0.02em` on headings for tightness.

WHY IT WORKS:
Embedding the product directly into the marketing page eliminates the "trust gap" — visitors don't have to imagine what Omma can do, they're already interacting with its output. The parallel-pipeline visualization uses Gestalt's common fate principle (things moving together are perceived as related) to make the multi-agent architecture intuitively understandable. The progressive loading prevents the 3D scenes from creating a perceived performance penalty.

GOOD: Using the actual product as the marketing is the highest-conviction way to sell developer tools — the page IS the proof.
BAD: Spline runtime adds ~2MB to the page. Multiple embedded scenes create cumulative GPU pressure. No fallback for WebGL-unsupported browsers.

CLEAN APPLICATION:
Apply the "product-as-marketing" pattern to Clean's landing page. Instead of describing what the MCP proxy does, embed a live interactive demo directly in the hero section. Show a terminal-style component where visitors type an agent command and watch it flow through Clean's proxy in real-time (animated via GSAP). The multi-agent pipeline visualization directly maps to Clean's architecture: show Agent → MCP Proxy → Context Layer → Tool as an animated flow diagram with particles moving along the connection lines. Each section below the hero should contain an interactive mini-demo, not a static screenshot.

---

## 5. Aceternity UI — Background Ripple + Dotted Glow + Parallax Hero Components — https://ui.aceternity.com/components

WHAT IT DOES:
Aceternity UI's latest component drops include three standout pieces: (1) **Background Ripple Effect** — a grid of cells that animate outward from a click point with opacity and scale changes creating a water-ripple metaphor; (2) **Dotted Glow Background** — a dot-matrix pattern with animated opacity gradients and a radial glow that follows cursor position; (3) **Parallax Hero Images** — mouse-driven parallax where layered images blur, fade, and shift at different depth rates. All built with React, Tailwind CSS, and Framer Motion (now "Motion"). Copy-paste components with zero external dependencies beyond the core stack.

PACKAGES:
- `react` + `react-dom` — framework
- `next` (Next.js 15, App Router) — meta-framework
- `tailwindcss` (v4) — styling
- `motion` (formerly `framer-motion`) — animation engine
- `clsx` + `tailwind-merge` — className utilities
- `lucide-react` — icons

IMPLEMENTATION:
1. **Background Ripple Effect**:
   - Grid container: `display: grid; grid-template-columns: repeat(var(--cols, 20), 1fr); gap: 0`. Each cell is a `<div>` with `width: 24px; height: 24px; background: transparent; border-radius: 50%; transition: background 0.3s`.
   - On click, calculate cell distance from click point. Animate cells with `motion.div` using `animate={{ scale: [1, 1.4, 1], opacity: [0.1, 0.6, 0.1] }}` with `transition={{ delay: distance * 0.04, duration: 0.6, ease: 'easeOut' }}`.
   - Color: `background: radial-gradient(circle, rgba(99, 102, 241, 0.4) 0%, transparent 70%)` (indigo-500 default).

2. **Dotted Glow Background**:
   - Container: `position: relative; overflow: hidden; background: #000`.
   - Dot pattern: CSS `radial-gradient(circle, rgba(255,255,255,0.15) 1px, transparent 1px)` with `background-size: 24px 24px`.
   - Glow: A `<motion.div>` with `width: 400px; height: 400px; background: radial-gradient(circle, rgba(99, 102, 241, 0.3) 0%, transparent 70%); filter: blur(60px); pointer-events: none`. Follows cursor via `onMouseMove` updating `x` and `y` motion values.
   - Opacity animation: `motion.div` wrapping the dot layer with `animate={{ opacity: [0.3, 0.6, 0.3] }} transition={{ duration: 4, repeat: Infinity, ease: 'easeInOut' }}`.

3. **Parallax Hero Images**:
   - Container: `position: relative; perspective: 1000px; overflow: hidden`.
   - Multiple image layers with varying `z` depths. On mouse move, calculate normalized cursor position (-1 to 1). Each layer transforms: `transform: translate3d(${cursorX * depth * 20}px, ${cursorY * depth * 20}px, ${depth * 50}px)`.
   - Deeper layers have `filter: blur(${(1 - depth) * 4}px)` and `opacity: ${0.5 + depth * 0.5}`.
   - Motion values: `const mouseX = useMotionValue(0)` with `useTransform(mouseX, [-1, 1], [-20, 20])` per layer.
   - Spring config: `transition={{ type: 'spring', stiffness: 150, damping: 15, mass: 0.5 }}`.

WHY IT WORKS:
These components exploit the aesthetic-usability effect — beautiful interfaces are perceived as more functional. The ripple effect provides spatial feedback that makes static pages feel alive. The dotted glow creates depth without 3D overhead (fake volumetric lighting via 2D blur). The parallax hero triggers the brain's depth-perception system, creating a "window" effect that draws attention to the focal content. All three use the minimum viable animation — just enough motion to create presence without creating distraction.

GOOD: Zero-dependency, copy-paste architecture means these drop straight into any Next.js project. The spring physics on the parallax feel genuinely tactile.
BAD: The ripple effect with 20x20 grid = 400 DOM nodes animating simultaneously — can jank on low-end devices. Needs `will-change: transform` and possibly canvas fallback.

CLEAN APPLICATION:
Apply the Dotted Glow Background directly to Clean's landing page hero section. Replace any static gradient background with the dot-matrix pattern + cursor-following glow in Clean's brand color (use a cool blue or teal: `rgba(56, 189, 248, 0.3)`). This creates an ambient "data grid" metaphor that signals infrastructure/developer tooling. Apply the Parallax Hero pattern to Clean's architecture diagram section — layer the agent, proxy, and context layer as separate depth planes that shift with cursor movement, creating a spatial sense of the stack layers. Use the Ripple Effect on the pricing page CTA — when users click "Start Free", the ripple emanates from the button, reinforcing the action.

---

## BUILD DIRECTIVE

TARGET: Clean's landing page hero section and above-the-fold experience (tryclean.ai)

APPROACH: Rebuild Clean's hero as a dark-theme, full-viewport section with three layers: (1) a Dotted Glow Background (Aceternity-style dot matrix at 24px grid with cursor-tracking teal glow, `rgba(56, 189, 248, 0.3)`) as the ambient base; (2) a headline using GSAP SplitText character-stagger reveal (0.03s stagger, `power3.out` easing, 0.8s duration) with a monospaced accent font for the product name "Clean"; (3) an interactive architecture mini-demo below the headline — a simplified horizontal flow diagram (Agent → MCP Proxy → Context Layer → Tool) with GSAP-animated particles flowing along SVG paths to show data movement in real-time. The demo should be functional, not decorative — hovering over each node reveals a tooltip with the component's role. Below the hero, feature sections use scroll-triggered `fadeUp` animations with `ScrollTrigger` (start: `top 80%`, stagger: 0.1s). The entire animation system should use the auto-registered GSAP effects pattern from Arnaud Rocca's architecture for maintainability.

STACK:
- `next` (Next.js 15, App Router)
- `react` + `react-dom`
- `tailwindcss` (v4)
- `gsap` + `gsap/ScrollTrigger` + `gsap/SplitText`
- `motion` (Framer Motion) — for cursor-tracking glow and spring-based hover interactions
- `clsx` + `tailwind-merge` — className utilities
- `lucide-react` — icons
- Font: `Inter` for body, `JetBrains Mono` for code/product name accents (via `@fontsource/inter` and `@fontsource/jetbrains-mono`)

REFERENCE URLS:
- https://arnaudrocca.fr/ — GSAP auto-registration architecture, fluid motion system, animation easing palette
- https://ui.aceternity.com/components/dotted-glow-background — Hero background pattern implementation
- https://omma.build/ — Product-as-marketing pattern, embedded interactive demos in feature sections

KEY CONSTRAINTS:
- Clean is developer infrastructure (MCP proxy / context layer for AI agents). The result must feel like a tool built by engineers, not a marketing agency. Think Vercel, Linear, or Raycast — not Webflow templates.
- NO generic purple gradients. Use a dark base (#0A0B0F) with teal/cyan accents (#38BDF8, #06B6D4) and white text (#FAFAFA).
- NO decorative 3D for the sake of 3D. Every animated element must communicate product function.
- The architecture flow diagram is the hero's centerpiece — it must actually demonstrate what Clean does, not just look pretty.
- Performance budget: < 200KB JS (gzipped), first contentful paint < 1.5s, no layout shift from animation loading.
- Mobile: disable the cursor-tracking glow, simplify the architecture diagram to a static SVG with subtle pulse animations, keep SplitText reveals but reduce stagger count.
- Avoid: carousels, stock photography, testimonial sliders, "trusted by" logo bars above the fold. These belong lower on the page.
