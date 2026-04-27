# Daily UI Intelligence Report — April 3, 2026

---

## 1. iyO — https://iyo.design (Awwwards SOTD April 3, 2026)

**WHAT IT DOES:**
A high-fidelity 3D product configurator and e-commerce showcase for iyO, a screenless computing hardware startup. The site uses a full-viewport WebGL canvas layered beneath a Webflow DOM for text/nav, with Three.js rendering interactive 3D product models that users can rotate, zoom, and customize in real time. Navigation is non-linear (infinite scroll + unusual menu patterns), and the site integrates spatial audio that shifts based on scroll position and user interaction — sound design as UI feedback. The configurator allows material/color swaps on the 3D model with real-time PBR material updates.

**PACKAGES:**
- three (Three.js r160+) — 3D rendering engine
- @react-three/fiber or raw Three.js (Webflow embed suggests vanilla)
- @react-three/drei — if R3F; otherwise custom loaders
- webflow — CMS and DOM layer
- howler.js or Tone.js — spatial/positional audio
- gsap + ScrollTrigger — scroll-driven animation sequencing
- lenis or locomotive-scroll — smooth scroll with velocity tracking
- postprocessing or three/examples/jsm/postprocessing — bloom, depth-of-field
- GLTFLoader + DRACOLoader — compressed 3D model loading
- Inter or custom sans-serif — typography

**IMPLEMENTATION:**
1. **Layout:** Full-viewport `<canvas>` fixed behind a transparent Webflow DOM. The DOM handles text, navigation, and CTAs. The canvas handles all visual storytelling.
2. **3D Scene:** Load DRACO-compressed GLTF model. Set up PBR materials with environment maps (HDR). Camera orbits on a constrained arc controlled by scroll position (map scroll 0-1 to camera theta 0-PI).
3. **Configurator:** Color/material buttons in DOM trigger `material.color.set()` and `material.roughness`/`metalness` updates on the mesh. Animate transitions with gsap.to() on material properties, duration 0.6s, ease: "power2.inOut".
4. **Scroll binding:** Use Lenis for smooth scroll. On each rAF tick, read `lenis.scroll` and `lenis.velocity`. Map scroll to camera position along a spline path. Map velocity to post-processing intensity (faster scroll = more motion blur).
5. **Audio:** Initialize Howler.js spatial sounds on user gesture. Bind panner position to scroll progress. Subtle UI click sounds on configurator interaction (gain: 0.15, duration: 80ms).
6. **Performance:** Use `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`. Implement LOD switching. Lazy-load 3D assets after hero text is visible. Use `IntersectionObserver` to pause rendering when canvas is off-screen.
7. **Responsive:** At viewport < 768px, disable orbit controls, switch to touch-swipe gesture for model rotation. Reduce texture resolution by 50%. Hide audio system.
8. **Color palette:** Dark charcoal background (#0D0D0D), white text (#FAFAFA), accent pulled from product material color.

**WHY IT WORKS:**
The site collapses the gap between physical product and digital representation — the 3D model IS the hero, not a photo of it. Sound design adds a sensory layer that most e-commerce sites completely ignore, creating a premium "unboxing" feel digitally. The constrained color palette (near-monochrome + one product accent) creates extreme visual hierarchy — your eye has nowhere to go but the product.

**GOOD / BAD:**
GOOD: Spatial audio as UI feedback is genuinely novel and creates a luxury feel no screenshot can capture.
BAD: Heavy WebGL payload (~4MB+ initial) means first-load on mobile 4G is likely 5s+; the infinite scroll pattern obscures clear product-to-cart conversion paths.

**CLEAN APPLICATION:**
Apply this to Clean's **product demo section** on the landing page. Instead of a 3D product, render an interactive node graph showing agents connecting through the MCP proxy — nodes pulse, edges animate data flow, and scrolling reveals how context propagates across the team. Use the same scroll-bound camera technique to orbit around the graph as the user scrolls. Instruction to design agent: "Build a WebGL node-graph visualization for Clean's product section using Three.js. Nodes = agents (VS Code, Cursor, Claude icons). Edges = context flowing through Clean's MCP proxy. Scroll drives camera orbit. Hover on a node shows a tooltip with 'Agent received 3.2s context in 47ms'. Dark background #0A0A0F, node glow color #00FF88 (Clean green), edge color #1A1A2E with animated dash pattern."

---

## 2. Vast — https://vast.com (Awwwards SOTD April 2, 2026)

**WHAT IT DOES:**
Built by Antinomy Studio, Vast uses a WebGL + Three.js layer integrated into a Webflow site to create a cinematic storytelling experience for a space company. The layout follows a vertical storytelling scroll — each section is a full-viewport "scene" with typographic titles layered over 3D or video backgrounds. Transitions between scenes use shader-based dissolves (noise-driven alpha masks) rather than CSS transitions. Typography is massive (120px+ headings) with tight letter-spacing and a two-color palette: near-black (#2A2C2F) and signal orange (#FF5623).

**PACKAGES:**
- three (Three.js) — WebGL rendering, custom shaders
- webflow — CMS/DOM structure
- gsap + ScrollTrigger — section pinning, scroll-driven shader uniforms
- glsl-noise or simplex-noise — shader dissolve effects
- lenis — smooth scroll
- Custom GLSL fragment shaders — noise-based transition masks
- Font: likely custom or Grotesk-family (tight spacing, geometric)

**IMPLEMENTATION:**
1. **Section structure:** Each "scene" is a pinned full-viewport section. ScrollTrigger pins each section for 100vh of scroll, during which shader uniforms animate from 0→1.
2. **Transition shader:** Fragment shader uses `smoothstep(noise(uv * 8.0), progress - 0.1, progress + 0.1)` to dissolve between scenes. `progress` uniform is driven by ScrollTrigger's `self.progress`.
3. **Typography:** Headings are 10-14vw, font-weight 700-900, letter-spacing -0.04em, line-height 0.95. Color alternates between #FAFAFA on dark scenes and #2A2C2F on light scenes. Orange #FF5623 used exclusively for CTAs and one accent word per heading.
4. **Video integration:** Background videos are `<video>` elements with `playsinline autoplay muted loop`, scaled with `object-fit: cover`. Three.js VideoTexture maps these to full-screen quads for shader manipulation.
5. **Scroll velocity effects:** On fast scroll, add a subtle vertical motion blur via a directional blur shader (blur amount = clamp(velocity * 0.002, 0, 0.01)).
6. **Responsive breakpoints:** 1440px (full), 1024px (reduce heading to 8vw), 768px (6vw, simplify shaders to CSS opacity transitions), 480px (disable WebGL entirely, fall back to static images).
7. **Performance:** Score 7.65/10 on Awwwards. Optimize by preloading only the current + next scene's assets. Use `requestIdleCallback` for non-critical shader compilation.

**WHY IT WORKS:**
The two-color constraint (#2A2C2F + #FF5623) creates a visual identity so strong the site is recognizable from a 10px thumbnail. The shader dissolves create a sense of physical material transformation that CSS fade/slide transitions cannot achieve — it feels like the screen itself is changing state, not just swapping content. Oversized typography at 10vw+ becomes architecture, not just text.

**GOOD / BAD:**
GOOD: The noise-dissolve shader transitions are technically elegant and create a unique tactile quality that sets this apart from every CSS-transition site.
BAD: Webflow + custom Three.js requires significant custom code that's hard to maintain; the 7.65 Awwwards score suggests usability could be stronger.

**CLEAN APPLICATION:**
Apply this to Clean's **hero section and section transitions** across the entire landing page. Use the two-color constraint pattern: #0A0A0F (near-black) + #00FF88 (Clean green). Oversized typography ("One MCP. Every agent. Total context.") at 10vw. Shader dissolve transitions between sections. Instruction to design agent: "Implement noise-based shader dissolve transitions between each scroll section on Clean's landing page. Use ScrollTrigger pinning with GLSL fragment shaders. Two-color palette: background #0A0A0F, accent #00FF88. Hero heading '10vw, font-weight 800, letter-spacing -0.04em, line-height 0.92'. The dissolve pattern should feel like digital static resolving into clarity — metaphor for agents gaining context through Clean."

---

## 3. San Rita — https://sanrita.com (Awwwards SOTD April 1, 2026)

**WHAT IT DOES:**
A luxury fashion e-commerce site where every page transition mimics the physical experience of touching silk. The site uses GSAP-driven page transitions with clip-path reveals and image scale animations that create a continuous motion language. Layout is editorial: asymmetric grid with large hero images (60-70% viewport), small body text set in a refined serif, and generous whitespace (80-120px section padding). Product images slide in with `clip-path: inset()` animations, scaling from 1.2 to 1.0 during reveal to create a zoom-settle effect.

**PACKAGES:**
- gsap + ScrollTrigger + Flip — page transitions, scroll animations, layout morphing
- barba.js or swup — SPA page transitions without full reload
- splitting.js or gsap SplitText — character/word-level text animation
- CSS clip-path — reveal animations
- lenis — smooth scroll
- Framework: likely Next.js or Nuxt (SPA routing + SSR for SEO)
- Font: serif display (e.g., Editorial New, Freight Display, or Noe Display) + sans body (e.g., Söhne, Neue Haas Grotesk)

**IMPLEMENTATION:**
1. **Page transitions:** On link click, current page content animates out with `gsap.to('.page', { clipPath: 'inset(0 0 100% 0)', duration: 0.8, ease: 'power3.inOut' })`. New page slides in from bottom: `gsap.from('.page-new', { clipPath: 'inset(100% 0 0 0)', duration: 0.8, ease: 'power3.inOut' })`.
2. **Image reveals:** `IntersectionObserver` triggers on scroll. Image container has `overflow: hidden`. Inner image: `gsap.from('.img', { scale: 1.2, clipPath: 'inset(100% 0 0 0)', duration: 1.2, ease: 'power2.out' })`. The parent clip and child counter-scale create the zoom-settle.
3. **Text animations:** Split headings into characters with SplitText. Stagger reveal: `gsap.from('.char', { y: '110%', opacity: 0, duration: 0.6, stagger: 0.02, ease: 'power3.out' })`.
4. **Whitespace system:** Base unit 8px. Section padding: `clamp(64px, 8vw, 120px)`. Grid gutters: 24px at 1440px. Image containers: 60vw max-width for hero, 40vw for secondary.
5. **Typography scale:** Display: 72px / 1.05 line-height / -0.02em tracking. Body: 16px / 1.6 line-height / 0em tracking. Caption: 12px / 1.4 / 0.05em tracking (uppercase).
6. **Color:** Background #F5F2ED (warm off-white), text #1A1A1A, accent gold #B8860B for CTAs only.
7. **Responsive:** At 768px, switch from asymmetric 2-col to single column. Reduce display type to 48px. Simplify page transitions to opacity fades (clip-path animations are janky on low-end mobile GPUs).

**WHY IT WORKS:**
The clip-path + scale combination creates perceived depth — images feel like they're emerging from behind the screen surface, not just appearing. The serif + sans pairing creates a clear typographic hierarchy that reads as "editorial authority." The warm off-white background (#F5F2ED) avoids the clinical feel of pure white while maintaining high contrast ratios (12:1+ with #1A1A1A text).

**GOOD / BAD:**
GOOD: The page transition choreography is best-in-class — every element has a clear entrance/exit, creating a sense of cinematic continuity.
BAD: Whitespace-heavy editorial layout looks stunning on desktop but wastes 40%+ of mobile viewport; the serif display font likely doesn't render well below 32px.

**CLEAN APPLICATION:**
Apply this to Clean's **documentation or blog pages** for a premium editorial feel that differentiates from typical developer docs. Instruction to design agent: "Build page transitions for Clean's docs/blog using GSAP clip-path reveals. On navigation, content slides out with clipPath inset animation (0.8s, power3.inOut), new content slides in. Split headings into characters for stagger animation. But adapt the palette to developer aesthetic: background #0A0A0F, text #E0E0E0, code blocks #161620 with #00FF88 syntax highlighting. Use a monospace display font (JetBrains Mono or Berkeley Mono) for headings instead of serif. Keep the whitespace rhythm (8px base unit, clamp-based section padding)."

---

## 4. Codrops Scroll-Reactive 3D Gallery — https://tympanus.net/codrops/2026/03/09/ (Codrops, March 2026)

**WHAT IT DOES:**
A scroll-driven WebGL gallery where images are stacked along the Z-axis in Three.js, each carrying a color palette that dynamically shifts the background gradient as you scroll through them. The system has three interlocking layers: mood (per-image color palettes drive global background), motion (scroll velocity becomes a signal that modulates scene intensity), and micro-motion (mouse parallax + scroll drift + breath scaling). Images feel like they have physical weight — they drift upward with scroll momentum and tilt toward your cursor.

**PACKAGES:**
- three (Three.js) — 3D rendering, plane geometries, shader materials
- vite — build tool
- vanilla JS (no React)
- Custom GLSL shaders — background gradient blending, image plane materials
- No external animation library — uses raw requestAnimationFrame + lerp
- lil-gui (optional) — debug parameter tuning

**IMPLEMENTATION:**
1. **Scene setup:** `THREE.PerspectiveCamera` with FOV 50, positioned at z=5. Images are `THREE.PlaneGeometry(1, aspect)` with `THREE.ShaderMaterial`. Each plane is placed at increasing Z depths (z = -i * 2.5).
2. **Scroll binding:** Track scroll with a custom scroll manager. Compute `scrollVelocity = (currentScroll - lastScroll) / deltaTime`. Lerp smoothed velocity: `smoothVelocity += (rawVelocity - smoothVelocity) * 0.1`.
3. **Mood system:** Each image has a palette `[bgColor1, bgColor2]`. As scroll passes an image's Z-threshold, lerp background uniforms: `bgUniform.value.lerp(targetColor, 0.05)`. Background is a full-screen quad with a radial gradient shader.
4. **Micro-motion layers:**
   - Mouse parallax: `plane.position.x += (mouseX - plane.position.x) * 0.02` per frame
   - Scroll drift: `plane.position.y += smoothVelocity * 0.001` (images drift with momentum)
   - Breath effect: `plane.scale.setScalar(1.0 + Math.abs(smoothVelocity) * 0.0005)` (faster scroll = subtle scale pulse)
   - Cursor tilt: `plane.rotation.x = mouseY * 0.05; plane.rotation.y = mouseX * 0.05`
5. **Image loading:** `THREE.TextureLoader` with `texture.minFilter = THREE.LinearFilter` for crisp rendering. Load progressively — nearest 3 planes first, then lazy-load rest.
6. **Performance:** Keep draw calls under 20. Use `renderer.info.render.calls` to monitor. Dispose textures for off-screen planes if memory constrained.

**WHY IT WORKS:**
The mood-based background creates an emotional throughline that flat galleries lack — each image transforms the entire environment, making the experience feel curated rather than listed. The velocity-driven micro-motions create a sense of physics without a physics engine; the scroll feels like it has mass. The Z-depth stacking breaks the 2D flatness of the web and creates a spatial metaphor for browsing "through" a collection.

**GOOD / BAD:**
GOOD: The three-system architecture (mood + motion + micro-motion) is composable and reusable — each system can be independently tuned or applied to other contexts.
BAD: Vanilla JS implementation lacks component abstraction; the lerp-heavy approach requires careful frame-rate-independent deltaTime handling to avoid inconsistency across devices.

**CLEAN APPLICATION:**
Apply this to Clean's **customer case studies or testimonials section**. Instead of images, each Z-plane shows a customer logo + metric card (e.g., "37% faster onboarding — Mira AI"). Each customer's brand color drives the background mood shift. Scrolling through customers feels like traversing a depth layer of Clean's ecosystem. Instruction to design agent: "Build a Z-depth scroll gallery for Clean's social proof section using Three.js PlaneGeometry stacked along Z-axis. Each plane = customer card (logo + stat). Palette per customer drives background gradient shift via GLSL. Scroll velocity modulates plane drift + breath scaling. Base background #0A0A0F. Glow colors per customer. Mouse parallax on planes. Use vanilla Three.js + Vite, no React."

---

## 5. Google Stitch 2.0 — https://stitch.withgoogle.com (Google Labs, March 2026)

**WHAT IT DOES:**
Google's complete redesign of Stitch into an AI-native infinite canvas for UI design. The interface is a zoomable, pannable workspace (like Figma) where AI generates and places UI components in spatial relation to each other. The design language is Google's latest Material 3 evolution: soft rounded containers (border-radius 16-24px), subtle elevation through layered shadows (not flat), and a muted warm palette with one saturated accent. The canvas supports voice input, real-time collaboration cursors, and animated generation where UI components "grow" into place from a seed point rather than popping in. Every design exports as clean HTML/CSS.

**PACKAGES:**
- React (likely Next.js or custom framework)
- @google/material-web or custom Material 3 components
- Canvas/WebGL — infinite canvas rendering (likely PixiJS or custom 2D renderer)
- Framer Motion or custom spring physics — component generation animations
- WebSocket — real-time collaboration
- Web Audio API — voice input processing
- Zustand or Redux — complex state management for canvas objects
- TypeScript throughout

**IMPLEMENTATION:**
1. **Infinite canvas:** Render UI components on a 2D canvas layer (not DOM — DOM doesn't scale to 1000+ objects). Use a virtual viewport with `transform: matrix()` for pan/zoom. Object picking via spatial hash grid.
2. **Generation animation:** When AI places a component, animate it in: `opacity 0→1` over 300ms, `scale 0.8→1.0` with spring physics (stiffness: 200, damping: 20), `y offset -20→0` with the same spring. Components "bloom" from their insertion point.
3. **Container design:** Border-radius 16px for cards, 24px for modals. Background #FFFFFF with elevation shadow: `0 1px 3px rgba(0,0,0,0.08), 0 4px 12px rgba(0,0,0,0.04)`. Nested containers use 12px radius.
4. **Color system:** Surface: #FAFAFA. Container: #FFFFFF. Border: #E5E5E5. Text primary: #1A1A1A. Text secondary: #6B6B6B. Accent: Google Blue #4285F4. Success: #34A853. Warning: #FBBC04.
5. **Typography:** Google Sans or Product Sans. Heading: 24px/1.3/medium. Body: 14px/1.5/regular. Label: 12px/1.4/medium. Code: Roboto Mono 13px.
6. **Collaboration:** Each collaborator cursor rendered as a colored circle (24px) with name label. Cursor position broadcasts via WebSocket at 30fps, rendered with lerp smoothing to prevent jitter.
7. **Voice UX:** Microphone button pulses with a ring animation (`box-shadow` keyframe expanding from 0 to 16px spread at opacity 0→0.3→0). Voice input transcribed in real-time as floating text near cursor.

**WHY IT WORKS:**
The "growing into place" generation animation solves the fundamental UX problem of AI-generated content: it makes the output feel crafted rather than dumped. Spatial layout on an infinite canvas lets users maintain mental models of their design space — components have location and proximity, not just list order. The voice-first interaction removes the friction of text prompting while maintaining the spatial context of what you're looking at.

**GOOD / BAD:**
GOOD: The bloom/spring animation for AI-generated components is the most thoughtful AI-output UX pattern seen this year — it communicates "being created" rather than "appeared."
BAD: Google's 350 generations/month cap limits power users; the Material 3 aesthetic, while polished, makes every output look like a Google product rather than reflecting brand identity.

**CLEAN APPLICATION:**
Apply this to Clean's **dashboard/analytics view** where developers see context flow across their team. The infinite canvas metaphor maps directly: agents are nodes on a spatial canvas, context flows are visible connections, and the AI-generation bloom animation can be repurposed for showing new context being routed in real-time. Instruction to design agent: "Build Clean's context flow dashboard as an infinite canvas using a 2D renderer (PixiJS or Canvas API). Agent nodes appear with spring animation (stiffness 200, damping 20, scale 0.8→1.0, opacity 0→1). Context flow edges animate with dashed-line motion (strokeDashoffset animation). Pan/zoom via matrix transform. Dark theme: canvas bg #0A0A0F, node containers #161620 with border-radius 12px, node border 1px #1A1A2E, active node glow #00FF88 with box-shadow 0 0 20px rgba(0,255,136,0.3). Use the collaboration cursor pattern for showing which team members' agents are active."

---

## BUILD DIRECTIVE

**TARGET:** Clean landing page hero section + product visualization

**APPROACH:** Combine Vast's cinematic shader-dissolve section transitions with iyO's scroll-driven 3D scene approach, applied to an interactive context-flow node graph that IS Clean's product story. The hero opens with oversized typography ("One MCP. Total context." at 10vw) over a dark (#0A0A0F) background. As the user scrolls, a Three.js scene fades in via noise-dissolve shader, showing a network of agent nodes connected through a central Clean proxy node. Scroll position drives camera orbit around the graph. Scroll velocity drives edge animation speed (faster scroll = faster context flow). Each section below the hero uses the same shader-dissolve transition pattern. The node graph is not decorative — it IS the product demo. Nodes are labeled (Cursor, Claude, VS Code), edges pulse with data, and hovering a node shows a tooltip with a real metric. The Codrops mood-based background technique applies: as you scroll to different product sections, the background subtly shifts hue (deep navy → deep green → deep purple) to create emotional throughlines.

**STACK:**
- next (Next.js 15 App Router)
- three + @react-three/fiber + @react-three/drei — 3D node graph rendering
- gsap + @gsap/react + ScrollTrigger — section pinning, scroll-driven uniforms, text animations
- lenis — smooth scroll with velocity tracking
- tailwindcss — utility-first styling for DOM layer
- framer-motion — DOM element entrance animations, spring physics for tooltips
- glsl-noise (simplex-noise) — shader dissolve transitions
- @react-three/postprocessing — bloom on active nodes, selective glow
- splitting or gsap SplitText — character-level heading animations
- shadcn/ui — tooltip, popover, button components for UI overlay
- JetBrains Mono + Inter — typography (mono for headings/code, sans for body)

**REFERENCE URLS:**
- Vast (https://vast.com) — shader dissolve transitions, two-color constraint, oversized typography
- iyO (https://iyo.design) — scroll-driven 3D scene, camera orbit binding, dark aesthetic
- Codrops scroll-reactive gallery (https://tympanus.net/codrops/2026/03/09/) — mood-based background shifting, velocity-driven micro-motions, Z-depth spatial metaphor

**KEY CONSTRAINTS:**
- Clean is developer infrastructure — the result must feel like a terminal meets a Bloomberg terminal meets a NASA mission control. NOT a generic SaaS landing page with gradient blobs.
- No stock illustrations. No abstract gradient meshes. Every visual element must directly represent the product (agents, context flow, MCP proxy, team graph).
- Performance budget: LCP < 2.5s, FID < 100ms, CLS < 0.1. The 3D scene must lazy-load after hero text is painted. Use `<link rel="preload">` for the GLTF/font assets.
- Avoid: rounded pastel containers, "AI magic" sparkle animations, generic dashboard mockup screenshots, any animation that doesn't serve comprehension of the product.
- The node graph must be interactive, not a video. Hover states, click-to-explore, and scroll-driven camera changes are required. Static 3D is decoration; interactive 3D is a demo.
- Mobile: gracefully degrade to a 2D animated SVG version of the node graph. Do not ship WebGL to devices with < 4GB RAM. Use `navigator.deviceMemory` to detect.
- Color palette locked: #0A0A0F (bg), #00FF88 (primary accent / node glow), #E0E0E0 (text), #161620 (surface), #1A1A2E (borders/subtle), #FF5623 (CTA / warning accent — borrowed from Vast's signal orange for urgency).
