# Daily UI Scout — April 26, 2026

---

## 1. Oryzo AI (by Lusion) — https://oryzo.ai

WHAT IT DOES:
A cinematic scroll-driven product page that presents a fictional cork coaster as a premium AI product. The layout is a single vertical scroll column with full-viewport sections. A central 3D product model (rendered via Three.js WebGL canvas) is pinned to the viewport center while the camera orbits, zooms, and tracks along a predefined spline path keyed to scroll position using GSAP ScrollTrigger. Text sections fade in/out with opacity + translateY transforms synced to the same scroll timeline. The color system is ultra-constrained: dark background (#100904) with a single warm accent (#FF8539), creating extreme contrast that forces the eye to the 3D model and key CTAs. Lighting in the 3D scene shifts per section — warm rim lights on the product during "feature" sections, dramatic backlighting during the "reveal" moment.

PACKAGES:
- three (Three.js for WebGL rendering)
- @types/three
- gsap (with ScrollTrigger, ScrollSmoother plugins)
- next (Next.js framework)
- react, react-dom
- @react-three/fiber (optional — Lusion uses raw Three.js but R3F is the clean equivalent)
- @react-three/drei (for environment, staging helpers)
- lenis (smooth scroll — likely used for scroll normalization)
- Fonts: custom sans-serif, tight tracking (looks like a Grotesk variant — use `Inter Tight` or `Satoshi` as npm-available alternatives)

IMPLEMENTATION:
1. **Component tree:** `<App>` → `<SmoothScrollProvider>` (Lenis wrapper) → `<HeroSection>` → `<WebGLCanvas>` (fixed position, z-index: 0) → `<SectionOverlays>` (position: relative, z-index: 1, pointer-events: none on non-interactive elements).
2. **3D scene setup:** Load a GLB/GLTF model of the product. Place it at origin. Create a `CatmullRomCurve3` camera path with 6-8 control points mapping to scroll sections. On each frame, interpolate camera position along curve based on normalized scroll progress (0→1). Use `camera.lookAt(productCenter)` with slight offset for parallax.
3. **Scroll binding:** GSAP ScrollTrigger with `scrub: 1` (1-second smoothing). Pin the canvas. Each text section triggers at specific scroll percentages: `start: "top center", end: "bottom center"`. Text animations: `opacity: 0→1→0`, `y: 40→0→-40`, `duration` mapped to scroll distance.
4. **Lighting transitions:** Use GSAP timeline to animate `pointLight.intensity` and `pointLight.color` between sections. Key values: ambient at 0.1, key light oscillates 0.5→2.0, rim light color shifts from `#FF8539` to `#FFFFFF` at product reveal.
5. **Color/Typography:** Background `#100904`. Text `#F5F0EB` (warm off-white). Accent `#FF8539`. Font-size hierarchy: hero headline 8vw clamp(48px, 8vw, 120px), section heads 3.5vw, body 1rem/1.6. Letter-spacing: -0.03em on headlines.
6. **Performance:** Use `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`. Compress GLB with Draco. Lazy-load 3D scene after hero text is visible. Use `will-change: transform` on animated DOM elements, remove after animation completes.

WHY IT WORKS:
The two-color constraint creates an almost monochromatic field that makes the 3D object the singular focal point — a textbook application of figure-ground segregation (Gestalt). The scroll-linked camera orbit gives the user a sense of agency without requiring any interaction beyond scrolling, which reduces cognitive load while maintaining engagement. The warm accent (#FF8539) against near-black creates a contrast ratio above 7:1, hitting AAA accessibility on key text while feeling premium rather than utilitarian.

GOOD: The scroll-to-camera binding is butter-smooth with no jank, and the 2-color system is a masterclass in restraint that most dev-tool sites should study.
BAD: Content is thin — the satirical concept means there's no real information architecture to learn from. On mobile, the 3D scene likely drops to lower fidelity without graceful degradation messaging.

CLEAN APPLICATION:
Apply this to Clean's landing page hero and product demo section. Use the identical scroll-linked 3D approach but with an abstract visualization of the MCP proxy architecture — a glowing node-graph or data-flow mesh that orbits as the user scrolls through feature sections. Constrain the palette to Clean's brand colors (2-3 max). Each scroll section reveals a product capability (context layer, TOON format, agent routing) with the 3D visualization morphing to represent each concept. Direct instruction to design agent: "Build a scroll-driven hero with a pinned Three.js canvas showing an abstract network mesh. 6 scroll sections, each with a headline + 2-line description fading in. Camera orbits the mesh, and mesh topology changes per section using morph targets or vertex shader displacement."

---

## 2. Shader Development Studio — https://www.shader.se

WHAT IT DOES:
A portfolio site for a creative development studio built entirely on Next.js + React + Three.js. The layout uses a full-screen WebGL canvas as the primary visual layer with DOM content overlaid. Project showcases are presented as interactive 3D cards or scenes that respond to cursor position — each project thumbnail is not a flat image but a shader-driven plane with displacement mapping, creating a pseudo-3D parallax effect on hover. Navigation is minimal: a single-level menu that triggers smooth page transitions where the current WebGL scene morphs into the next using shader-based dissolve/distortion transitions. The overall grid system is asymmetric — large project cards occupy 60-70% width with metadata (client, tech, year) in a tight sidebar column.

PACKAGES:
- next (Next.js 14+)
- react, react-dom
- three
- @react-three/fiber
- @react-three/drei (for `Image`, `Text`, `useTexture` utilities)
- @react-three/postprocessing (for bloom, vignette effects)
- gsap (for DOM transitions, cursor following)
- glsl-noise (for shader displacement)
- lenis or @studio-freight/lenis (smooth scrolling)
- Fonts: Grotesk/Mono pairing — use `Space Grotesk` + `JetBrains Mono`

IMPLEMENTATION:
1. **Component tree:** `<Layout>` → `<WebGLLayer>` (fixed, full viewport) + `<DOMLayer>` (scrollable overlay). Use React context to share scroll position and cursor coords between DOM and WebGL layers.
2. **Project cards as shader planes:** In the R3F scene, render each project as a `<mesh>` with `<planeGeometry>`. Apply a custom `ShaderMaterial` with vertex displacement: `position.z += sin(uv.x * 3.14) * cos(uv.y * 3.14) * uHover * 0.15` where `uHover` is a float 0→1 animated on mouseenter/leave via GSAP. Fragment shader samples the project image texture with slight RGB offset: `vec3 color = vec3(texture2D(uTex, uv + vec2(0.003, 0.0) * uHover).r, texture2D(uTex, uv).g, texture2D(uTex, uv - vec2(0.003, 0.0) * uHover).b)`.
3. **Page transitions:** On route change, animate current scene's uniforms (`uProgress` 0→1) to distort/dissolve geometry. Use a noise-based threshold in the fragment shader: `if (snoise(uv * 4.0) < uProgress) discard;`. Simultaneously fade in the new page's DOM content with `opacity` and `clipPath: inset()` animation.
4. **Cursor interaction:** Custom cursor div (12px circle, `mix-blend-mode: difference`). Track mouse with GSAP `quickTo` for 60fps smoothing. Scale cursor to 48px on interactive elements. Pass normalized mouse position to WebGL shaders as `uMouse` uniform.
5. **Typography:** Headline: `Space Grotesk`, 600 weight, -0.04em tracking, 6vw/clamp. Body: 16px/1.5. Metadata labels: `JetBrains Mono`, 12px, uppercase, 0.1em tracking, 50% opacity.
6. **Responsive:** Below 768px, disable WebGL hover effects, fall back to CSS `transform: scale(1.02)` on touch. Reduce canvas resolution to 1x DPR.

WHY IT WORKS:
The shader-driven hover states create a tactile quality that makes the portfolio feel like a physical object reacting to touch — this leverages the Gestalt principle of common fate (elements that move together are perceived as related). The minimal DOM overlay lets the WebGL layer breathe, establishing a clear visual hierarchy where craft speaks louder than chrome. The mono/grotesk font pairing signals "technical competence" without being cold.

GOOD: The shader hover effect on project cards is genuinely novel and directly communicates the studio's technical capability — form matches content perfectly.
BAD: SEO is likely poor since primary content lives in WebGL. The site is resource-intensive and would struggle on low-end Android devices.

CLEAN APPLICATION:
Apply the shader-driven hover card pattern to Clean's documentation or integration showcase page. Each integration partner (Slack, GitHub, Linear, etc.) gets a card with a subtle shader displacement on hover, signaling interactivity and technical depth. Direct instruction: "Create an integration grid page where each card is a shader plane in R3F. On hover, apply vertex displacement and chromatic aberration to the partner logo/screenshot. Use a 3-column grid on desktop, single column on mobile with CSS fallback. Cards link to integration docs."

---

## 3. Off Menu — https://www.offmenu.design

WHAT IT DOES:
A product studio site for agentic AI interfaces. The layout breaks from the standard agency portfolio grid — instead, it uses a spatial, almost OS-like interface where project cards float in a 2.5D space with subtle depth layering via CSS transforms and z-index stacking. The hero section features kinetic typography where the studio tagline animates word-by-word with staggered clip-path reveals. Below the fold, case studies are presented as expandable cards that grow from thumbnail to full-width on click, with content sliding in from the edges. The overall aesthetic is "agentic" — dark backgrounds, monospaced accents, terminal-like UI elements mixed with fluid motion, bridging the gap between developer tool and design studio.

PACKAGES:
- next (Next.js)
- react, react-dom
- framer-motion (now `motion` — for layout animations, AnimatePresence, shared layout transitions)
- tailwindcss
- @radix-ui/react-* (for accessible primitives — dialog, tooltip, accordion)
- gsap (for scroll-linked sequences)
- clsx or tailwind-merge (conditional class composition)
- Fonts: `Geist` (Vercel's font — sans + mono variants)

IMPLEMENTATION:
1. **Hero kinetic type:** Split tagline into `<span>` per word. Use Framer Motion `variants` with staggered delay: `{ clipPath: "inset(100% 0 0 0)" }` → `{ clipPath: "inset(0% 0 0 0)" }`, transition: `{ duration: 0.6, ease: [0.76, 0, 0.24, 1], delay: i * 0.08 }`. Words should be `display: inline-block` for independent animation.
2. **Floating card layout:** Use CSS Grid for base positions, then apply `transform: translateZ()` and `rotate3d()` with small random values per card to create depth. On scroll, parallax the cards at different rates using `transform: translateY(calc(var(--scroll) * var(--speed)))` where `--speed` is 0.02-0.08 per card.
3. **Expandable case studies:** Framer Motion `layoutId` on each card. On click, animate from grid position to full-width overlay using shared layout animation. Content inside uses `AnimatePresence` with staggered children. Close animation reverses the layout transition.
4. **Terminal aesthetic elements:** Scattered UI accents — small `<code>` blocks with `font-family: "Geist Mono"`, `background: rgba(255,255,255,0.05)`, `border: 1px solid rgba(255,255,255,0.1)`, `border-radius: 6px`, `padding: 2px 6px`. Use these for labels like "AI-NATIVE", "AGENTIC", status indicators.
5. **Color system:** Background `#0A0A0A`. Text `#FAFAFA`. Muted text `#666`. Accent: electric blue `#3B82F6` or green `#10B981` for interactive states. Borders: `rgba(255,255,255,0.08)`.
6. **Motion principles:** Every animation uses `ease: [0.76, 0, 0.24, 1]` (aggressive ease-out). Duration: 0.4-0.6s for entrances, 0.3s for micro-interactions. No bounce, no spring — everything is crisp and intentional.

WHY IT WORKS:
The "agentic" aesthetic — dark mode, monospace accents, terminal-like elements — creates instant credibility with the developer audience. The floating card layout breaks the grid monotony that plagues 90% of agency sites, creating visual tension that demands exploration. The clip-path text reveal is more sophisticated than a simple fade-in because it implies the content is being "computed" or "rendered," reinforcing the AI/agent theme semantically through motion.

GOOD: The aesthetic perfectly matches the positioning (agentic AI studio). The expandable card interaction is satisfying and information-dense without feeling cluttered.
BAD: The floating card layout could feel disorienting if overused — needs to be balanced with stable grid sections for content hierarchy.

CLEAN APPLICATION:
Apply this entire design language to Clean's landing page. Clean IS an agentic infrastructure product — this aesthetic is native to Clean's identity. Direct instruction: "Rebuild Clean's hero using clip-path word reveals on the tagline. Use the dark terminal aesthetic with monospace code accents for feature labels. Case studies / use-case sections should use expandable cards with shared layout animations. Color: #0A0A0A background, warm accent for CTAs. Every motion uses [0.76, 0, 0.24, 1] easing. This is the primary reference for Clean's entire visual identity."

---

## 4. Ruinart — Digital Fresco — https://fresque.ruinart.com

WHAT IT DOES:
An immersive WebGL experience that digitizes Ruinart's physical fresco into a pannable, zoomable, narrative-driven web experience. The layout eschews traditional scrolling — instead, users navigate a massive 2D canvas (the fresco artwork) using drag/swipe gestures, with hotspots scattered across the illustration that expand into content panels when activated. On mobile, navigation uses native swipe gestures. The fresco artwork itself is rendered as a high-resolution tiled texture on a WebGL plane, with parallax depth layers separating foreground elements from background. An AR mode allows on-site visitors to overlay the digital fresco onto the physical wall. Transitions between hotspot content use a radial reveal animation emanating from the tap point.

PACKAGES:
- three (or pixi.js for 2D WebGL rendering)
- gsap (ScrollTrigger not needed — using drag-based navigation)
- @use-gesture/react (for pan, pinch, drag gesture recognition)
- leva (dev-only, for tuning parameters)
- next or nuxt (SSR framework)
- react-spring or framer-motion (for content panel animations)
- Fonts: elegant serif for headings (use `Playfair Display` or `Cormorant Garamond`), clean sans for body

IMPLEMENTATION:
1. **Canvas navigation:** Render the fresco as a large plane in a Three.js orthographic scene. Implement pan with `@use-gesture/react`'s `useDrag` — map drag delta to camera position offset with momentum decay: `velocity * 0.95` per frame until < 0.01. Implement pinch-zoom mapped to orthographic camera zoom (0.5x to 3x range).
2. **Tiled texture loading:** Split the high-res fresco into 512x512 tiles. Load tiles in view frustum only (LOD system). Use `THREE.TextureLoader` with an `IntersectionObserver`-like spatial check against camera bounds. Tiles fade in with opacity animation on load.
3. **Hotspot system:** Define hotspot positions as UV coordinates on the fresco plane. Render hotspot indicators as animated SVG circles overlaid via CSS (positioned with `project()` from 3D to screen space). On click, expand a `<dialog>` or custom panel from the click point using `clipPath: circle()` animation: `circle(0% at ${x}px ${y}px)` → `circle(100% at ${x}px ${y}px)`, duration 0.5s.
4. **Parallax depth:** Separate fresco into 3-4 depth layers (background landscape, midground architecture, foreground figures). Each layer is a separate plane at different z-positions. On pan, offset each layer by `panDelta * layerDepth` where layerDepth ranges 0.8→1.2.
5. **AR integration:** Use WebXR or 8th Wall for on-site AR overlay. Detect a physical marker (the real fresco), anchor the digital fresco to it, and render supplementary layers.
6. **Responsive:** On mobile, single-finger drag for pan, two-finger pinch for zoom. Hotspot tap targets minimum 44x44px. Content panels slide up from bottom (sheet pattern) rather than expanding radially.

WHY IT WORKS:
Replacing scroll with spatial navigation transforms passive content consumption into active exploration — this triggers the brain's spatial memory system (method of loci), making the content more memorable than a linear scroll. The radial reveal from tap point maintains spatial coherence (the content emerges from where you touched, not from an arbitrary edge), which is a direct application of the animation principle of "locality." The parallax depth layers add dimensionality without the performance cost of full 3D.

GOOD: The gesture-based navigation is genuinely more engaging than scrolling for exploratory content. The tiled loading system handles massive artwork without choking.
BAD: Discoverability is a problem — users may not realize there are hotspots, and the lack of a progress indicator means users don't know how much content they've missed.

CLEAN APPLICATION:
Apply the spatial canvas navigation pattern to Clean's architecture documentation or system overview page. Instead of a linear docs page, render Clean's architecture as an interactive node-graph canvas that users can pan and zoom. Each node (MCP proxy, context layer, agent, tool) is a hotspot that expands into a detail panel. Direct instruction: "Build an interactive architecture explorer using a pannable WebGL canvas. Render Clean's system diagram as a node graph. Nodes are clickable hotspots that expand radial content panels with technical details. Use @use-gesture for pan/zoom. This replaces a static architecture diagram with an explorable system map."

---

## 5. Codrops Scroll-Reactive 3D Gallery — https://tympanus.net/codrops/2026/03/09/building-a-scroll-reactive-3d-gallery-with-three-js-velocity-and-mood-based-backgrounds/

WHAT IT DOES:
A scroll-driven WebGL gallery where images are stacked along the Z-axis in a Three.js scene. As the user scrolls, the camera moves through the depth stack, bringing each image into focus. The key innovation is velocity-reactive motion — scroll speed affects the visual presentation: fast scrolling causes images to blur and spread apart (increased Z-spacing + motion blur shader), while slow scrolling brings them closer with crisp rendering. Each image carries a dominant color palette that dynamically shifts the page background using a smooth LERP between palette values. The DOM layer shows editorial text that fades in sync with image focus.

PACKAGES:
- three
- @react-three/fiber
- @react-three/drei (`useTexture`, `shaderMaterial`)
- @react-three/postprocessing (`EffectComposer`, `Bloom`, custom motion blur effect)
- lenis (smooth scroll with velocity reporting)
- gsap (for DOM text animations)
- color-thief or vibrant.js (to extract dominant colors from images at build time — precompute and store as JSON)
- tailwindcss
- next

IMPLEMENTATION:
1. **Scene structure:** Create N `<mesh>` planes along Z-axis, spaced 2-3 units apart. Each plane has a `planeGeometry(1.6, 1, 1, 1)` (16:10 aspect) with a `ShaderMaterial` that applies the image texture.
2. **Scroll-to-depth binding:** Use Lenis `onScroll` callback to get `velocity` and `progress`. Map `progress` (0→1) to camera Z position along the image stack: `camera.position.z = lerp(startZ, endZ, progress)`. Apply easing via exponential interpolation: `current += (target - current) * 0.08` per frame.
3. **Velocity-reactive effects:** Track `Math.abs(velocity)`. When velocity > threshold (e.g., 2.0), increase Z-spacing between planes dynamically: `plane.position.z = baseZ + velocity * 0.3`. Apply a custom motion blur post-processing pass: horizontal blur kernel size = `clamp(velocity * 0.01, 0, 0.02)`.
4. **Mood-based backgrounds:** Precompute 3 dominant colors per image using `vibrant.js` at build time. Store as `[{bg: "#1a1a2e", mid: "#16213e", accent: "#0f3460"}, ...]`. On each frame, determine the "focused" image (closest to camera) and LERP the `document.body.style.backgroundColor` toward that image's palette: `currentColor = lerpColor(currentColor, targetColor, 0.03)`.
5. **Text sync:** Each image has associated editorial text. Use GSAP with ScrollTrigger to fade text in/out based on which image is in "focus zone" (within 1 unit of camera Z). Text animation: `opacity: 0→1`, `y: 20→0`, `filter: blur(4px)→blur(0)`, duration 0.4s.
6. **Performance:** Use `THREE.CompressedTextureLoader` for images. Set `antialias: false` on renderer and use FXAA post-processing instead. Limit post-processing to desktop (detect via `navigator.maxTouchPoints`).

WHY IT WORKS:
Velocity-reactive design creates a direct physical metaphor — fast scrolling = rushing past (blur), slow scrolling = studying (clarity). This maps digital interaction to real-world spatial navigation, making the interface feel intuitive without instruction. The mood-based background shifts create emotional continuity between images, using color as a narrative device rather than just decoration. The Z-axis stacking breaks the left-right/up-down convention, adding dimensionality that makes the gallery feel like a space rather than a page.

GOOD: The velocity-reactive system is technically elegant and creates a genuinely different browsing experience. The palette extraction + background LERP is a reusable pattern for any image-heavy page.
BAD: Z-axis scrolling can cause motion sickness in some users. No keyboard navigation or accessibility alternative for the spatial interaction.

CLEAN APPLICATION:
Apply the velocity-reactive scroll and mood-based color shifting to Clean's changelog or product updates page. Each update/release is a "card" stacked in depth-space. Scrolling fast gives a sense of rapid iteration; scrolling slowly lets users study each release. Background shifts per release to match the feature's visual identity. Direct instruction: "Build a changelog page where release cards are Z-stacked in Three.js. Scroll drives camera through the stack. Fast scroll triggers motion blur, slow scroll brings clarity. Each release has a color palette that shifts the page background. DOM overlay shows release notes per focused card. This makes Clean's changelog feel like time-travel through the product's history."

---

## BUILD DIRECTIVE

TARGET: Clean's landing page hero + product demo section (tryclean.ai homepage above the fold through the first 3 feature sections)

APPROACH: Combine the scroll-driven 3D camera orbit from Oryzo AI with the agentic dark-mode aesthetic from Off Menu. The hero opens with a clip-path word-by-word reveal of Clean's tagline against #0A0A0A. Below the tagline, a Three.js canvas renders an abstract network mesh (nodes = agents, edges = MCP connections, glowing particles = data flowing through the context layer). As the user scrolls, the camera orbits the mesh while feature sections fade in — each section highlights one Clean capability (MCP proxy, TOON format, context layer) and the mesh morphs to visualize that concept (nodes cluster, edges reroute, particles change color). The palette is strictly 3 colors: near-black background, off-white text, and a single warm or electric accent for CTAs and the mesh glow. Monospace code accents (Geist Mono) label technical terms. Every animation uses the [0.76, 0, 0.24, 1] cubic-bezier. The overall feel: "this is infrastructure that thinks" — technically sophisticated, fast-loading, zero visual noise.

STACK:
- next (14+ with App Router)
- react, react-dom
- three
- @react-three/fiber
- @react-three/drei
- @react-three/postprocessing (bloom for mesh glow)
- gsap (with ScrollTrigger, ScrollSmoother)
- framer-motion (for DOM animations, clip-path reveals)
- lenis (smooth scroll normalization)
- tailwindcss
- geist (Vercel font package — sans + mono)
- clsx

REFERENCE URLS:
- https://oryzo.ai — scroll-driven 3D camera orbit, constrained color system, section-based scroll animation
- https://www.offmenu.design — agentic dark aesthetic, kinetic typography, terminal-style UI accents, expandable cards
- https://www.shader.se — shader-driven hover states, WebGL/DOM layer separation, technical studio credibility

KEY CONSTRAINTS:
- Clean is developer infrastructure — the result must feel like a tool built by engineers, not a marketing page dressed by a design agency. No gradients-for-gradients-sake, no stock imagery, no rounded-everything friendly SaaS aesthetic.
- Performance budget: < 3s LCP on 4G, < 500KB JS initial bundle. Lazy-load the Three.js scene after hero text is visible. Use Draco compression on any 3D models.
- The 3D mesh must be procedurally generated (not a loaded model) so it can morph between states. Use `THREE.BufferGeometry` with custom vertex positions + instanced spheres for nodes.
- Mobile: disable WebGL, show a static SVG version of the mesh with CSS animations. Don't ship Three.js to mobile bundles (dynamic import with `next/dynamic`).
- Accessibility: all text must be in the DOM (not rendered in WebGL). The 3D canvas is decorative — add `aria-hidden="true"`. Scroll animations must respect `prefers-reduced-motion`.
- Avoid: particle effects that look like a 2018 music festival site, blue-purple gradient blobs, "floating shapes" that convey no meaning, anything that looks like it was made in Spline and embedded as an iframe.
