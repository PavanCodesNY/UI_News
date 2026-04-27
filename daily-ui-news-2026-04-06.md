# Daily UI Intelligence Report — April 6, 2026

---

## 1. Artefakt — https://artefakt.co (Awwwards SOTD April 4, 2026)

**WHAT IT DOES:**
Artefakt is a hybrid production company site running a full-bleed WebGL canvas overlaid with minimal DOM content. The layout uses a single-column vertical scroll with pinned 3D scenes that transition between project showcases. Each project section features a full-viewport WebGL scene (Three.js) with GSAP ScrollTrigger-driven camera movements and shader-based reveal transitions. The visual hierarchy is enforced through a strict black/white palette with oversized serif typography layered on top of 3D geometry. Navigation uses a sliding panel with parallax depth stacking.

**PACKAGES:**
- three (^0.162.0) — 3D scene rendering
- @react-three/fiber (^8.x) or vanilla Three.js — depending on framework
- @react-three/drei — helpers for camera controls, environment maps
- gsap (^3.12) — timeline orchestration
- gsap/ScrollTrigger — scroll-pinning and scrubbing
- gsap/SplitText — text reveal animations
- lenis (^1.x) — smooth scroll normalization
- vite or next — bundler/framework
- Inter or custom serif font (likely GT Super or similar editorial serif)

**IMPLEMENTATION:**
1. **Component tree:** `<App>` → `<SmoothScroll provider={Lenis}>` → `<WebGLCanvas>` (fixed, z-index: 0) + `<DOMOverlay>` (relative, z-index: 1, pointer-events: none on non-interactive areas).
2. **Scroll-pinned sections:** Each project is a `<section>` with `height: 300vh`. GSAP ScrollTrigger pins the viewport while scrubbing a Three.js camera timeline. Config: `pin: true, scrub: 1, anticipatePin: 1`.
3. **3D transitions:** Camera animates on a spline path between project scenes. Use `THREE.CatmullRomCurve3` for the path. Easing: `power2.inOut`. Duration mapped to scroll distance (300vh per scene).
4. **Shader reveals:** Custom fragment shader with a noise-based wipe. Uniform `uProgress` driven by ScrollTrigger. Transition formula: `step(noise(uv * 8.0) + uProgress, 0.5)`.
5. **Typography:** Oversized display text (clamp(4rem, 8vw, 10rem)), `font-weight: 300`, `letter-spacing: -0.03em`, `mix-blend-mode: difference` against the WebGL canvas.
6. **Gallery panel:** CSS `transform: translateX(100%)` → `translateX(0)` on click. Transition: `0.6s cubic-bezier(0.76, 0, 0.24, 1)`.
7. **Responsive:** Below 768px, 3D scenes replaced with high-res video fallbacks. DOM overlay becomes full-width single column.
8. **Performance:** `THREE.WebGLRenderer` with `antialias: false`, `powerPreference: 'high-performance'`. Texture compression with KTX2. Instanced meshes for repeated geometry. `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`.

**WHY IT WORKS:**
The black/white constraint forces the 3D content to carry all visual interest, creating extreme contrast between the geometric complexity of the WebGL scenes and the typographic simplicity of the overlay. Gestalt principle of figure-ground is maximized. Scroll-pinning creates a cinematic pacing that prevents the user from rushing past content — each section demands attention through motion.

**GOOD / BAD:**
- GOOD: The scroll-driven 3D camera system is genuinely best-in-class — smooth, purposeful, and each transition reveals new spatial information.
- BAD: Black/white palette limits emotional range; mobile fallback to video loses the interactive dimension entirely.

**CLEAN APPLICATION:**
Apply this scroll-pinned 3D transition system to Clean's landing page product demo section. Instead of static screenshots, build a WebGL scene showing agent context flowing between nodes. Each scroll-pinned section reveals a different Clean capability (context sharing, token reduction, team awareness). Use the same `mix-blend-mode: difference` typography trick with Clean's brand colors against a dark 3D canvas.

---

## 2. Vast — https://vastspace.com (Awwwards SOTD April 2, 2026)

**WHAT IT DOES:**
Vast's site presents their space station program through a full-screen 3D model viewer integrated with editorial content. The hero loads a detailed GLTF model of Haven-1 rendered in Three.js with environment-mapped PBR materials. Scroll controls orbit the camera around the station while text panels slide in from the sides. The layout alternates between immersive 3D viewports (100vh, fixed position) and overlapping editorial cards that slide up from below. Lighting uses a single dominant directional light with subtle bloom post-processing to simulate space lighting.

**PACKAGES:**
- three (^0.162.0)
- @react-three/fiber (^8.x)
- @react-three/drei — Environment, Float, PresentationControls
- @react-three/postprocessing — Bloom, Vignette
- gsap (^3.12)
- gsap/ScrollTrigger
- next (^14.x or ^15.x) — SSR framework
- tailwindcss (^3.4 or ^4.x)
- @fontsource/space-grotesk or similar geometric sans

**IMPLEMENTATION:**
1. **Component tree:** `<Layout>` → `<HeroCanvas>` (position: fixed, inset: 0) + `<ContentStream>` (position: relative, z-index: 10).
2. **3D model:** GLTF loaded with `useGLTF` from drei. Model centered at origin. Initial camera: `position={[0, 0, 5]}`, `fov={45}`.
3. **Scroll-orbit:** ScrollTrigger drives `camera.position` on a circular path: `x = radius * Math.cos(scrollProgress * Math.PI * 2)`, `z = radius * Math.sin(scrollProgress * Math.PI * 2)`. `camera.lookAt(0, 0, 0)` every frame.
4. **Bloom:** `@react-three/postprocessing` EffectComposer with `<Bloom luminanceThreshold={0.8} luminanceSmoothing={0.9} intensity={0.4} />`.
5. **Editorial cards:** `position: sticky; top: 20vh;` with `opacity` and `translateY` driven by per-section ScrollTriggers. Enter: `{ y: 60, opacity: 0 }` → `{ y: 0, opacity: 1, duration: 0.8, ease: "power3.out" }`.
6. **Color:** Deep navy background (#0a0e1a), white text, accent blue (#4d9fff) for interactive elements. The dark space aesthetic reinforced with a subtle star-field particle system (THREE.Points, 2000 particles, random positions in a sphere).
7. **Responsive:** Below 1024px, 3D model scales down and orbit is disabled — model auto-rotates instead. Cards become full-width stacked.
8. **Performance:** Draco-compressed GLTF. LOD switching for the station model (high-poly > 1024px, low-poly < 1024px). RequestAnimationFrame throttled to 30fps when tab is not visible.

**WHY IT WORKS:**
The single dominant 3D object (space station) acts as a persistent visual anchor — the user always knows where they are spatially. Editorial content layered on top creates a documentary feel. The dark color palette isn't just aesthetic — it simulates space, making the product feel real. Whitespace rhythm between editorial cards prevents cognitive overload.

**GOOD / BAD:**
- GOOD: The scroll-orbit mechanic is intuitive and gives the user agency to explore the 3D model without explicit controls — scroll = explore.
- BAD: GLTF model likely 5MB+ even compressed; first contentful paint suffers. No loading state design visible.

**CLEAN APPLICATION:**
Apply the scroll-orbit 3D viewer to Clean's landing page hero. Instead of a space station, model a 3D node graph representing an agent network. As the user scrolls, the camera orbits the graph and new connection lines illuminate between nodes, visualizing context flowing between agents. Use the same deep-dark background (#0a0e1a) with Clean's accent color for active connections. Add the bloom post-processing on active context paths.

---

## 3. Codrops Scroll-Reactive 3D Gallery — https://tympanus.net/codrops/2026/03/09/

**WHAT IT DOES:**
A scroll-reactive 3D gallery where images are mapped onto Three.js plane geometries arranged in 3D space. Scroll velocity is measured and used to drive visual effects: faster scrolling triggers more aggressive perspective distortion, color shifts in the background (mood-based system), and mesh deformation via vertex shaders. The gallery layout is a staggered vertical grid where each image plane has independent parallax depth, creating a layered corridor effect. Background color transitions smoothly based on which image is most centered (mood extraction from dominant image colors).

**PACKAGES:**
- three (^0.162.0)
- @react-three/fiber (^8.x)
- @react-three/drei — useTexture, shaderMaterial
- lenis (^1.x) — smooth scroll with velocity output
- gsap (^3.12) — for DOM element animations alongside WebGL
- glsl-noise — simplex/perlin noise for vertex displacement
- color-thief-browser or vibrant.js — dominant color extraction from images

**IMPLEMENTATION:**
1. **Velocity tracking:** Lenis `onScroll` callback provides `velocity` value. Normalize to 0–1 range: `const normalizedVelocity = Math.min(Math.abs(lenis.velocity) / 5, 1)`.
2. **Plane arrangement:** Images as `THREE.PlaneGeometry(1, aspect, 32, 32)` (32 segments for vertex deformation). Positioned in a staggered grid: odd items offset `x += 1.2`, all items spaced `y -= 1.8`. Z-depth alternates: `z = index % 3 * 0.3`.
3. **Vertex shader deformation:** `uniform float uVelocity;` drives sinusoidal displacement: `pos.z += sin(pos.x * 3.0 + uTime) * uVelocity * 0.3;`. Creates a wave effect that intensifies with scroll speed.
4. **Fragment shader:** Image texture with chromatic aberration proportional to velocity: offset R channel by `uVelocity * 0.01` on X-axis.
5. **Mood-based backgrounds:** On each image, extract dominant color via Vibrant.js during build. Store as uniform. Lerp `scene.background` toward the dominant color of the most-centered image: `background.lerp(targetColor, 0.05)` per frame.
6. **Parallax:** Each plane's scroll offset multiplied by its depth layer: `plane.position.y = baseY + scrollOffset * (1 + zDepth * 0.5)`.
7. **Responsive:** Below 768px, single-column layout. Vertex deformation disabled (perf). Mood backgrounds still active via CSS transitions on a DOM background layer.

**WHY IT WORKS:**
Velocity-reactive feedback creates a direct link between user input and visual output — the site feels alive and responsive to intention, not just position. Mood-based color shifts provide unconscious wayfinding. The layered parallax depth triggers the Gestalt depth perception principle, making a 2D scroll feel three-dimensional.

**GOOD / BAD:**
- GOOD: Velocity-reactive deformation is genuinely novel — it rewards exploration and makes scrolling feel physical.
- BAD: Color extraction at build time limits dynamic content; the mood system only works with pre-processed images.

**CLEAN APPLICATION:**
Apply velocity-reactive scroll effects to Clean's documentation or changelog page. As developers scroll through docs sections, the background subtly shifts to match the "mood" of the section (blue for API reference, green for success states, amber for configuration). Scroll velocity drives subtle mesh deformation on decorative background geometry — making even a docs page feel premium and technically sophisticated.

---

## 4. Artem Shcherban Portfolio — https://liartem.com (Awwwards SOTD April 6, 2026)

**WHAT IT DOES:**
A minimal portfolio built in Webflow with GSAP orchestrating all animation. Two-color palette (black/white) with a modular grid layout. The hero uses a full-viewport split: left side holds oversized sans-serif name treatment, right side shows a project reel auto-playing. Project cards expand in-place using FLIP animations (First-Last-Invert-Play). A sliding contact panel emerges from the right edge. All images use parallax depth on scroll. Typography is the primary design element — no decorative graphics, no 3D.

**PACKAGES:**
- webflow (no-code platform) or replicate with:
  - next (^15.x)
  - tailwindcss (^4.x)
- gsap (^3.12)
- gsap/ScrollTrigger
- gsap/SplitText — character-level text animations
- gsap/Flip — layout transition animations
- lenis (^1.x) — smooth scrolling
- Font: likely Neue Montreal or similar geometric grotesque

**IMPLEMENTATION:**
1. **Hero split:** CSS Grid `grid-template-columns: 1fr 1fr` at desktop. Left: name in `font-size: clamp(3rem, 12vw, 15rem)`, `font-weight: 200`, `line-height: 0.9`. Right: `<video autoplay muted loop playsinline>` with `object-fit: cover`.
2. **Text reveals:** SplitText splits headings into chars. ScrollTrigger staggers them in: `gsap.from(chars, { y: '100%', opacity: 0, stagger: 0.02, duration: 0.8, ease: 'power4.out', scrollTrigger: { trigger: section, start: 'top 80%' } })`.
3. **FLIP card expansion:** On card click: capture First state → toggle `.expanded` class → capture Last state → `Flip.from(state, { duration: 0.6, ease: 'power3.inOut', absolute: true })`. Expanded card fills viewport with project details.
4. **Image parallax:** Each image wrapped in overflow-hidden container. Inner image scaled to 120%. ScrollTrigger drives `y: '-20%'` over the scroll range: `scrub: 0.5, ease: 'none'`.
5. **Contact panel:** Fixed-position `<aside>` with `transform: translateX(100%)`. Toggle class applies `translateX(0)`. CSS transition: `transform 0.5s cubic-bezier(0.76, 0, 0.24, 1)`. Backdrop: `background: rgba(0,0,0,0.5)` with `backdrop-filter: blur(8px)`.
6. **Responsive:** Single column below 768px. Hero video moves below name. Cards stack vertically, FLIP still works but animates height instead of position.

**WHY IT WORKS:**
Radical constraint (two colors, no decoration) forces every pixel to earn its place. The oversized typography creates immediate hierarchy — you know exactly what matters. FLIP animations maintain spatial continuity during state changes, preventing the disorientation of traditional page transitions. Parallax on images adds just enough depth to prevent the minimal palette from feeling flat.

**GOOD / BAD:**
- GOOD: FLIP card expansion is silky smooth and maintains context — best-in-class for portfolio UX.
- BAD: Black/white only works for creative portfolios; for a product company it would feel cold without modification.

**CLEAN APPLICATION:**
Apply the FLIP card expansion pattern to Clean's use-case or customer stories section. Each card shows a customer name and one-line result. On click, the card FLIP-expands to fill the viewport with the full case study — no page navigation, no modal. Use the same SplitText character-reveal animations for section headings throughout Clean's landing page. Adapt the two-color base to Clean's palette (dark charcoal + white + accent).

---

## 5. Linear-Style Dark SaaS Interface — https://linear.app (Latest refresh, 2026)

**WHAT IT DOES:**
Linear's latest design refresh uses a warmer dark gray base (shifting from cool blue-gray) with layered translucent surfaces, subtle gradient glows behind interactive elements, and micro-motion feedback on every interaction. The landing page features scroll-triggered product demos embedded in browser chrome mockups. Hero has an animated gradient mesh background (not a static gradient — the mesh deforms subtly over time). Feature sections use horizontal scroll carousels with snap points. Each feature card has a top-edge glow that pulses on hover.

**PACKAGES:**
- next (^15.x) — App Router, RSC
- tailwindcss (^4.x)
- framer-motion (^11.x) — layout animations, presence, gestures
- @radix-ui/* — accessible primitives (dialog, tooltip, etc.)
- shadcn/ui — component library built on Radix
- clsx + tailwind-merge — conditional class composition
- Font: Inter (--font-sans) or custom geometric sans

**IMPLEMENTATION:**
1. **Gradient mesh hero:** A `<canvas>` element behind the hero content. Draw 4-6 radial gradients at random positions. Each gradient center moves on a Perlin noise path: `x += noise2D(seed, time * 0.0003) * 0.5`. Colors: `hsla(250, 60%, 50%, 0.15)`, `hsla(200, 80%, 40%, 0.1)`. Canvas composited with `globalCompositeOperation: 'lighter'`. Blur: apply `filter: blur(80px)` on the canvas element.
2. **Surface layering:** Background: `#111113`. Card surfaces: `rgba(255, 255, 255, 0.03)` with `border: 1px solid rgba(255, 255, 255, 0.06)`. Hover: `rgba(255, 255, 255, 0.05)`. This creates depth without shadows.
3. **Top-edge glow on cards:** `::before` pseudo-element: `background: linear-gradient(90deg, transparent, rgba(120, 119, 255, 0.5), transparent)`, `height: 1px`, `top: 0`, `opacity: 0` → `opacity: 1` on hover. Transition: `0.3s ease`.
4. **Horizontal feature carousel:** `display: flex; overflow-x: auto; scroll-snap-type: x mandatory;`. Each card: `scroll-snap-align: center; min-width: 80vw;`. Hide scrollbar: `::-webkit-scrollbar { display: none; }`.
5. **Product demo embeds:** Browser chrome mockup as a `<div>` with rounded corners (`border-radius: 12px`), title bar dots, and a nested `<video autoplay muted loop>` or animated screenshot sequence. Entrance: `framer-motion` `whileInView={{ opacity: 1, y: 0 }}` from `initial={{ opacity: 0, y: 40 }}` with `transition={{ duration: 0.6, ease: [0.22, 1, 0.36, 1] }}`.
6. **Micro-motion:** Every button has `transition: all 0.15s ease`. Hover: `transform: translateY(-1px)`, `box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3)`. Active: `transform: translateY(0)`, `box-shadow: 0 2px 4px rgba(0, 0, 0, 0.2)`.
7. **Responsive:** Carousel becomes vertical stack below 768px. Gradient mesh canvas hidden on mobile (replaced with static CSS gradient for performance).

**WHY IT WORKS:**
The warm dark palette reduces eye strain while the translucent surface layering creates perceived depth without heavy shadows — this is the "glass morphism done right" approach. The gradient mesh hero adds organic movement to an otherwise rigid interface, preventing the dark theme from feeling sterile. Micro-motion on every interactive element creates a responsive, high-quality feel that signals engineering craft — exactly what developer tool users expect.

**GOOD / BAD:**
- GOOD: The surface layering system is production-grade and infinitely scalable — every new component automatically inherits visual hierarchy through opacity, not color.
- BAD: The gradient mesh hero is computationally expensive for what it adds; a simpler CSS animation could achieve 80% of the effect at 10% of the cost.

**CLEAN APPLICATION:**
Apply Linear's entire surface layering system to Clean's landing page and dashboard. Use `#111113` base, `rgba(255,255,255,0.03)` cards, `1px solid rgba(255,255,255,0.06)` borders. Add the top-edge glow hover effect to every feature card. Build the gradient mesh hero with Clean's brand colors (replace Linear's purple with Clean's accent). Use the browser-chrome product demo mockups to showcase Clean's context dashboard. This is the single highest-impact pattern to adopt — it immediately signals "developer infrastructure" and "premium craft."

---

## BUILD DIRECTIVE

**TARGET:** Clean landing page hero + feature sections (tryclean.ai)

**APPROACH:**
Rebuild Clean's landing page hero and feature showcase using Linear's dark surface layering system as the foundational design language, combined with Artefakt's scroll-pinned 3D visualization for the product demo section. The hero should feature an animated gradient mesh background (4-5 radial gradients on Perlin noise paths using Clean's brand palette) behind a bold headline and CTA. Below the hero, implement 3-4 scroll-pinned feature sections where a persistent WebGL canvas shows a simplified 3D node graph of agents sharing context — each pinned section highlights a different capability (unified context, token reduction, team awareness) by illuminating different paths in the graph. Feature cards throughout the page use the Linear translucent surface system with top-edge glow hovers. All text entrances use GSAP SplitText character reveals. The overall feel must be: technically sophisticated, fast, dark-mode-native, developer-first.

**STACK:**
- next (^15.x) — App Router, React Server Components
- tailwindcss (^4.x) — utility-first styling
- three (^0.162.0) — 3D agent network visualization
- @react-three/fiber (^8.x) — React renderer for Three.js
- @react-three/drei — helpers (Environment, Float, Line)
- @react-three/postprocessing — Bloom for active context paths
- gsap (^3.12) — animation orchestration
- gsap/ScrollTrigger — scroll-pinned sections
- gsap/SplitText — text reveal animations
- framer-motion (^11.x) — micro-interactions, presence animations
- lenis (^1.x) — smooth scroll
- shadcn/ui — accessible component primitives
- clsx + tailwind-merge — class composition
- Inter or Geist — primary typeface

**REFERENCE URLS:**
1. https://linear.app — Surface layering, dark palette, gradient mesh hero, micro-motion system
2. https://artefakt.co (Awwwards SOTD) — Scroll-pinned 3D transitions, camera path animation, shader reveals
3. https://liartem.com (Awwwards SOTD) — SplitText typography, FLIP card expansion, radical minimalism

**KEY CONSTRAINTS:**
- Clean is developer infrastructure. The result must feel like Linear, Vercel, or Raycast — not a generic SaaS template. No stock photos. No rounded-everything. No pastel gradients.
- Performance budget: LCP < 2.5s, TBT < 200ms. The 3D scene must lazy-load and show a CSS gradient placeholder until the WebGL context is ready.
- No 3D for the sake of 3D. The agent network visualization must communicate real product value (context flowing between agents), not just look cool.
- Dark mode only for V1. Light mode is a distraction.
- Mobile: Replace WebGL with a high-quality animated SVG or Lottie fallback. Never show a broken or slow 3D scene on mobile.
- Typography must be oversized and confident. Minimum hero heading: `clamp(3rem, 8vw, 6rem)`. Letter-spacing: `-0.03em`. Weight: 500–600.
- Avoid: carousels on mobile, autoplay videos without user consent, gradient borders (overplayed), glassmorphism blur heavier than 8px, any animation longer than 0.8s.
