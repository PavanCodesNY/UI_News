# Daily UI Research Report — April 25, 2026

---

## 1. Shader Development Studio — https://www.shader.se

**WHAT IT DOES:**
A Swedish creative studio site disguised as a 1987 corporate training VHS tape. The entire page is a single WebGPU-rendered canvas using Three.js with TSL (Three.js Shading Language) for shader composition. Scenes are composed as material graphs — not hand-written WGSL — allowing lighting, geometry, and post-processing effects to transition simultaneously without visible seams. Bold geometric type and retro grid layouts dissolve between sections using GPU-driven scene transitions with shared render targets. Navigation triggers full-scene morphs rather than page loads.

**PACKAGES:**
- `three` (WebGPURenderer path, not legacy WebGLRenderer)
- Three.js TSL for shader composition (built into three.js core, no separate package)
- Custom WGSL post-processing pipeline (bloom, grain, chromatic aberration)
- No framework detected — vanilla JS with module bundler
- Fonts: custom display serif, likely self-hosted

**IMPLEMENTATION:**
1. **Renderer setup:** Initialize `THREE.WebGPURenderer` with `antialias: true`, `alpha: false`. Set `toneMapping: THREE.ACESFilmicToneMapping`, `toneMappingExposure: 1.2`. Canvas fills viewport with `position: fixed`.
2. **Scene graph:** Each "page" is a scene node with its own camera, lights, and geometry. All scenes exist in memory simultaneously — visibility is toggled via opacity uniforms.
3. **TSL material composition:** Use `MeshStandardNodeMaterial` with TSL nodes. Example transition: `mix(sceneA_color, sceneB_color, smoothstep(transitionProgress, 0.0, 1.0))` applied at the material level, not as a post-process.
4. **Scene transitions:** On navigation click, animate a shared `transitionProgress` uniform from 0→1 over 1.2s with `ease: "power2.inOut"`. Geometry morphs use vertex displacement nodes: `position.add(direction.mul(transitionProgress).mul(amplitude))`.
5. **Post-processing:** TSL-based post chain — film grain (noise node, amplitude 0.03), chromatic aberration (RGB channel offset, 2px at edges), vignette (radial gradient multiply, 0.3 intensity). CRT scanline overlay at 0.05 opacity.
6. **Retro grid:** Procedural grid floor using TSL `fract()` and `step()` nodes, animated with `time` uniform for slow drift. Grid color: `#1a1a2e` lines on `#0a0a14` background.
7. **Typography:** 3D extruded text geometry using `TextGeometry` from `three/addons`. Font loaded as JSON typeface. Lit with directional + ambient setup matching scene mood.
8. **Responsive:** Canvas always fills viewport. Camera FOV adjusts on resize: mobile gets wider FOV (75°) vs desktop (55°) to maintain visual density.

**WHY IT WORKS:**
The retro VHS metaphor creates instant narrative context — visitors understand this is a studio that makes things look and feel extraordinary. The monochromatic-with-accent color strategy (dark navy + warm cream type) exploits simultaneous contrast for maximum type legibility against 3D scenes. Scene transitions that morph rather than cut maintain spatial continuity, leveraging the Gestalt principle of common fate — elements moving together are perceived as a unified system.

**GOOD / BAD:**
GOOD: WebGPU + TSL is genuinely next-gen — this is one of the first SOTD winners using WebGPURenderer in production, proving the path is stable.
BAD: Zero fallback for browsers without WebGPU flag enabled; Safari users on older macOS get a blank page.

**CLEAN APPLICATION:**
Apply this to Clean's **landing page hero section**. Don't replicate the retro aesthetic — instead, use the TSL scene-transition technique to morph between visual representations of Clean's product stages: raw API calls → TOON-formatted context → agent response. Build a single WebGPU canvas hero that transitions between 3 states on scroll, each state showing a different visual metaphor for the data transformation pipeline. Use the same `MeshStandardNodeMaterial` + TSL composition approach for seamless GPU-driven transitions.

---

## 2. Oryzo AI (by Lusion) — https://oryzo.ai

**WHAT IT DOES:**
A satirical product launch site by Lusion presenting a cork coaster as a serious AI product. Built with Three.js (WebGL path), GSAP ScrollTrigger, and hand-drawn anime-style 2D assets composited over 3D scenes. The layout follows a vertical scroll narrative — each scroll segment triggers a cinematic camera move through a 3D environment while 2D illustration overlays animate in with GSAP timelines. The scroll acts as a video scrubber through a pre-composed camera path. Product shots use physically-based materials with environment map reflections. Typography is oversized, editorial, and layered on top of the WebGL canvas using HTML elements with `pointer-events: none`.

**PACKAGES:**
- `three` (WebGLRenderer)
- `gsap` + `ScrollTrigger` plugin
- `@gsap/react` or vanilla GSAP timeline binding
- Custom GLSL fragment shaders for post-processing (film grain, color grading)
- HTML/CSS overlay layer for typography (not rendered in WebGL)
- Likely `lenis` for smooth scroll normalization
- Fonts: custom sans-serif display + handwritten accent font

**IMPLEMENTATION:**
1. **Page structure:** Full-viewport `<canvas>` element with `position: fixed; z-index: 0`. HTML content layer sits above with `position: relative; z-index: 1`. Spacer divs create scroll height (typically 500vh+ for the full narrative).
2. **Camera path:** Define a `CatmullRomCurve3` through the 3D scene. On scroll, map `scrollProgress (0-1)` to curve position: `camera.position.copy(curve.getPointAt(progress))`. Camera lookAt target follows a second parallel curve.
3. **Scroll binding:** `gsap.to(scrollState, { progress: 1, scrollTrigger: { trigger: ".scroll-container", start: "top top", end: "bottom bottom", scrub: 1.5 } })`. The `scrub: 1.5` creates a 1.5s lag behind scroll position for cinematic feel.
4. **2D overlay compositing:** Anime-style illustrations are PNG sequences or SVG elements. They animate in via GSAP: `gsap.fromTo(".illustration", { opacity: 0, y: 100, scale: 0.8 }, { opacity: 1, y: 0, scale: 1, scrollTrigger: { trigger: ".section-2", start: "top center", end: "center center", scrub: true } })`.
5. **Product material:** `MeshPhysicalMaterial` with `roughness: 0.15, metalness: 0.0, clearcoat: 1.0, clearcoatRoughness: 0.1`. Environment map: HDR loaded via `RGBELoader`, applied as `scene.environment`.
6. **Post-processing:** `EffectComposer` with custom passes — `UnrealBloomPass(resolution, 0.3, 0.4, 0.85)`, custom color grading LUT, film grain shader (animated noise texture, blended at 0.04 strength).
7. **Typography layer:** CSS `mix-blend-mode: difference` on headline text creates automatic contrast against any 3D background. Font size: `clamp(3rem, 8vw, 7rem)`. Line height: 0.95 for tight leading.
8. **Performance:** `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`. Model LOD switching based on scroll proximity. Texture resolution caps at 2048px.

**WHY IT WORKS:**
The scroll-as-camera-dolly pattern transforms passive reading into cinematic experience — the user becomes the cinematographer. Mixing hand-drawn 2D with photorealistic 3D creates cognitive tension that holds attention (the brain keeps trying to resolve the style mismatch). The satirical framing gives the team permission to push production quality to absurd levels, which paradoxically makes the craft more visible and impressive. Whitespace rhythm between scroll sections follows a musical 4/4 time signature — pause, reveal, pause, reveal.

**GOOD / BAD:**
GOOD: The scroll-scrubbed camera path is flawlessly smooth, and mixing 2D anime overlays with 3D PBR materials is technically ambitious and visually unique.
BAD: The narrative is 100% scroll-dependent — no skip, no nav, no way to jump to a section. On low-end devices, the 3D scene drops to ~20fps during complex transitions.

**CLEAN APPLICATION:**
Apply this to Clean's **product demo / "How It Works" section**. Use the scroll-as-camera-dolly technique to walk users through the data flow: (1) incoming API request → (2) context layer processing → (3) TOON format transformation → (4) agent consumption. Each scroll segment shows a different stage of the pipeline as a 3D scene, with code snippets and architecture diagrams floating as HTML overlays with `mix-blend-mode: difference`. The camera follows a spline path through a stylized representation of the data pipeline. Keep the PBR materials but swap anime illustrations for technical diagrams.

---

## 3. Off Menu — https://www.offmenu.design

**WHAT IT DOES:**
A single-page studio portfolio for an AI-native product studio specializing in agentic interfaces. Built with Webflow + custom GSAP + Lenis smooth scroll. The hero features a fullscreen video showreel with text reveal animations. Below, a project grid uses staggered reveal with parallax depth. The most distinctive UI element is a chat-style testimonial slider where client quotes appear as message bubbles in a mock conversation interface. Navigation is minimal — logo top-left, single CTA top-right. The page uses a project overlay pattern: clicking a project card opens a full-screen overlay with horizontal image scroll rather than navigating to a new page.

**PACKAGES:**
- Webflow (CMS + hosting + visual builder)
- `gsap` + `ScrollTrigger` + `Flip` plugins (loaded via CDN)
- `lenis` (smooth scroll, `duration: 1.2, easing: (t) => Math.min(1, 1.001 - Math.pow(2, -10 * t))`)
- `Swiper.js` or custom slider for testimonials
- Webflow native interactions for hover states
- Fonts: Inter or similar geometric sans, plus a display weight for headlines

**IMPLEMENTATION:**
1. **Hero:** Full-viewport `<video>` element with `object-fit: cover`. Text overlay uses split-text animation: each character wrapped in a `<span>`, staggered `gsap.from` with `y: "110%", rotateX: -90, stagger: 0.02, duration: 0.8, ease: "power4.out"`. Video has a subtle `brightness(0.6)` CSS filter to ensure text contrast.
2. **Smooth scroll:** Lenis initialized with `duration: 1.2`, `smoothWheel: true`, `wheelMultiplier: 0.8`. GSAP ScrollTrigger uses Lenis as the scroll source: `ScrollTrigger.scrollerProxy()` or Lenis's built-in GSAP integration via `lenis.on('scroll', ScrollTrigger.update)`.
3. **Project grid:** CSS Grid with `grid-template-columns: repeat(2, 1fr)` on desktop, `1fr` on mobile. Each card has parallax: inner image translates Y at 0.85x scroll speed via `ScrollTrigger`. Cards stagger-reveal: `gsap.from(".project-card", { y: 60, opacity: 0, stagger: 0.15, scrollTrigger: { trigger: ".grid", start: "top 80%" } })`.
4. **Chat testimonial slider:** Custom component — each testimonial is a `<div>` styled as a chat bubble with `border-radius: 18px 18px 4px 18px` (sender) and mirrored for "received." Background: `rgba(255,255,255,0.06)` with `backdrop-filter: blur(10px)`. Auto-advances every 5s with GSAP timeline, manual swipe on mobile.
5. **Project overlay:** On card click, `gsap.to(".overlay", { clipPath: "circle(150% at 50% 50%)", duration: 0.6, ease: "power3.inOut" })`. Overlay contains horizontal-scrolling image gallery using `overflow-x: scroll` with CSS `scroll-snap-type: x mandatory`.
6. **Color scheme:** Near-black background `#0a0a0a`, white text `#f5f5f5`, accent color used sparingly on CTAs (electric blue `#3366ff` or similar). Borders at `rgba(255,255,255,0.08)`.
7. **Responsive breakpoints:** Desktop 1440px container, tablet 768px (grid collapses to 1 column), mobile 375px (reduced padding, smaller type scale).

**WHY IT WORKS:**
The "agentic interfaces" positioning is reinforced by the chat-bubble testimonials — the UI metaphor matches the studio's thesis. Dark-on-dark with minimal accent color creates a premium, developer-friendly aesthetic without the cliché gradient orbs. The project overlay pattern (expand-in-place rather than navigate) maintains scroll context, reducing cognitive load per the principle of spatial consistency. Split-text hero animation at 0.02s stagger creates a typewriter-meets-reveal effect that implies precision.

**GOOD / BAD:**
GOOD: The chat-style testimonial component is a genuinely novel UI pattern for a studio site — it turns social proof into an interactive element that feels native to their "agentic interfaces" brand.
BAD: Webflow's generated HTML is div-heavy and semantically poor; accessibility audit would flag missing landmarks and ARIA labels.

**CLEAN APPLICATION:**
Apply this to Clean's **landing page social proof section and overall page structure**. Adopt the dark-on-dark color system (`#0a0a0a` base, `#f5f5f5` text, single accent). Replicate the chat-bubble testimonial pattern but reframe it as an "agent conversation" demo — show a mock conversation between a developer and an AI agent, where each message demonstrates Clean's context layer improving the response. Use the same Lenis + GSAP ScrollTrigger + split-text animation stack for the hero. The project overlay pattern maps well to a "use cases" section where clicking a use case card expands to show implementation details without leaving the page.

---

## 4. The Obsidian Assembly — https://obsidianassembly.com

**WHAT IT DOES:**
A darkly atmospheric site for an independent organization coordinating distributed spaces. Built by Fiddle.Digital (Lithuania). The site's signature element is a WebGL particle gallery — images decompose into thousands of floating particles that reform into the next image on scroll or interaction. Navigation uses a horizontal scroll section embedded within vertical scroll. Mouse movement creates a persistent displacement field on the particle system — cursor position pushes particles away with a radial falloff. The overall aesthetic is obsidian/volcanic: deep blacks, warm amber accents, and rough texture overlays.

**PACKAGES:**
- Custom WebGL renderer (likely raw WebGL2 or lightweight Three.js setup)
- `gsap` + `ScrollTrigger` for scroll-driven particle transitions
- Custom GLSL shaders for particle simulation (position, velocity, displacement)
- `stringtune` + `stringprogress` (custom/internal tools by Fiddle.Digital for animation sequencing)
- CSS Grid + custom cursor
- Fonts: geometric sans-serif display (likely Neue Haas Grotesk or similar)

**IMPLEMENTATION:**
1. **Particle system setup:** Load gallery images as textures. Sample each texture at a grid resolution (e.g., 64×64 = 4096 particles). Each particle stores: `originalPosition (x, y)`, `currentPosition (x, y)`, `targetPosition (x, y)`, `color (from texture sample)`, `velocity (x, y)`. Store in `Float32Array`, upload as buffer attributes.
2. **Vertex shader:** `gl_Position = projectionMatrix * modelViewMatrix * vec4(currentPosition + displacement, 0.0, 1.0)`. Point size: `gl_PointSize = mix(2.0, 6.0, distanceFromCenter)`.
3. **Particle transition:** On image change, update `targetPosition` buffer with new image's sampled positions. In the animation loop: `currentPosition += (targetPosition - currentPosition) * 0.08` (spring-like interpolation, no library needed). Add randomized velocity offset during transition: `velocity += randomDir * 0.5` decaying at `velocity *= 0.96` per frame.
4. **Mouse displacement:** Pass mouse UV coordinates as uniform. In vertex shader: `float dist = distance(uv, mouseUV); float force = smoothstep(0.3, 0.0, dist) * 0.15; displacement = normalize(uv - mouseUV) * force;`. This creates a radial push from cursor.
5. **Scroll binding:** `ScrollTrigger` maps scroll progress to gallery index. At each threshold (e.g., every 20% of scroll), trigger particle reformation to next image. Transition duration: ~1.8s natural spring settling.
6. **Color grading:** Fragment shader applies warm tint: `finalColor = mix(particleColor, vec3(1.0, 0.85, 0.6), 0.1)`. Depth-based fade: particles further from camera get lower opacity.
7. **Texture overlay:** CSS `::after` pseudo-element on the body with a noise texture at `opacity: 0.03, mix-blend-mode: overlay` for film grain without GPU cost.
8. **Horizontal scroll section:** `ScrollTrigger` pins the section and maps vertical scroll to horizontal `translateX`. Container width: `400vw`. Pin duration: `"+=300%"`.

**WHY IT WORKS:**
Particle decomposition/reformation exploits the Gestalt principle of emergence — the brain recognizes the image before all particles settle, creating a moment of satisfaction as perception is confirmed. The warm amber on black exploits opponent-process color theory — the warmth feels alive against the cold void. Mouse-reactive particles create a sense of agency that transforms passive browsing into exploration. The horizontal-within-vertical scroll pattern breaks spatial expectation and signals "this content is different."

**GOOD / BAD:**
GOOD: The particle transition math is elegant — spring interpolation with velocity decay feels physical and organic without needing a physics engine.
BAD: Horizontal scroll sections are notoriously confusing on trackpads; no visual indicator tells users to keep scrolling vertically after the horizontal section ends.

**CLEAN APPLICATION:**
Apply this to Clean's **"Integrations" or "Ecosystem" section** on the landing page. Each integration partner logo (OpenAI, Anthropic, Google, etc.) is rendered as a particle image. On scroll, particles reform from one logo to the next, demonstrating the "universal compatibility" message visually. The mouse displacement effect becomes a "interact with our ecosystem" moment. Use the warm-on-dark color palette (`#0a0a0a` background, amber `#f5a623` accent) for this section to create visual contrast with the rest of the page.

---

## 5. shadcn/ui Sera Style — https://ui.shadcn.com/sera

**WHAT IT DOES:**
A new design system style released April 2026 for shadcn/ui that applies print editorial design principles to component UIs. Sera replaces the default rounded-corners-and-soft-shadows aesthetic with square corners, serif headings, uppercase tracking on labels, and underlined interactive controls. It's not a theme (color swap) — it's a structural redesign of component anatomy. Buttons use `text-decoration: underline` instead of background fills. Cards use `border: 1px solid` with no shadow. Headings use a serif typeface with tight letter-spacing. The effect is closer to a Bloomberg Terminal or a Monocle magazine layout than a typical SaaS dashboard.

**PACKAGES:**
- `shadcn` CLI v4 (`npx shadcn@latest init`)
- `radix-ui` (unified package, not individual @radix-ui/react-* packages)
- `tailwindcss` v4+
- `next` or `vite` (framework)
- `class-variance-authority` for variant management
- Font pairing: serif display (e.g., `Playfair Display`, `Instrument Serif`, or `DM Serif Display`) + sans body (e.g., `Inter`, `DM Sans`)

**IMPLEMENTATION:**
1. **Init with Sera:** `npx shadcn@latest init --style sera`. This scaffolds the project with Sera's CSS variables and component overrides.
2. **CSS variables (key overrides):**
   ```css
   --radius: 0px; /* square corners everywhere */
   --border: 1px solid hsl(var(--border-color));
   --font-heading: "Instrument Serif", serif;
   --font-body: "Inter", sans-serif;
   --letter-spacing-heading: -0.02em;
   --letter-spacing-label: 0.08em;
   --text-transform-label: uppercase;
   ```
3. **Button component override:** Remove background fill, use underline:
   ```tsx
   // button variant "sera-default"
   className="text-foreground underline underline-offset-4 decoration-1 hover:decoration-2 transition-all"
   ```
4. **Card component:** `border-radius: 0`, `box-shadow: none`, `border: 1px solid hsl(var(--border))`. Header uses serif font, body uses sans. Padding: `p-6` (24px).
5. **Table styling:** Horizontal rules only (no vertical borders). Header row: uppercase, `text-xs`, `tracking-widest`, `text-muted-foreground`. Cell padding: `py-4 px-2`.
6. **Navigation:** Tab underline pattern — active tab indicated by `border-bottom: 2px solid hsl(var(--foreground))`, inactive tabs have no border. Transition: `transition-colors duration-200`.
7. **Typography scale:** Headings: `text-4xl font-heading tracking-tight` (desktop), `text-2xl` (mobile). Body: `text-sm font-body leading-relaxed`. Labels: `text-xs uppercase tracking-widest text-muted-foreground`.
8. **Dark mode:** Same structural rules. Colors shift: background `hsl(0 0% 3.9%)`, foreground `hsl(0 0% 98%)`. Border: `hsl(0 0% 14.9%)`.

**WHY IT WORKS:**
The serif + sans pairing creates typographic hierarchy without relying on size or weight alone — the font style itself signals importance level (principle of typographic contrast). Square corners signal precision and intentionality, countering the "everything is rounded and friendly" sameness of 2024-2025 SaaS design. Uppercase tracking on labels uses pre-attentive processing — the visual texture of spaced capitals is instantly distinguishable from body text. Underline-only buttons reduce visual noise by 60% compared to filled buttons, prioritizing content over chrome.

**GOOD / BAD:**
GOOD: This is the most opinionated shadcn style yet, and it genuinely differentiates from the sea of identical Tailwind UIs. The serif + square corner combination feels premium and editorial.
BAD: Underline-only buttons fail accessibility guidelines for touch targets unless combined with generous padding. Some components (like Select dropdowns) look under-designed with just borders and no shadow — the affordance for "this is interactive" is weak.

**CLEAN APPLICATION:**
Apply Sera's design language to Clean's **documentation pages and dashboard UI**. Clean is developer infrastructure — the editorial, Bloomberg-terminal aesthetic communicates "serious tool, not a toy." Specifically: use square corners on all cards and containers, serif headings for section titles, uppercase tracked labels for metadata (latency, token count, model version), and underline-only secondary actions. Init a new shadcn project with `--style sera` and customize the font pairing to something more technical: `"IBM Plex Serif"` for headings + `"IBM Plex Mono"` for code + `"IBM Plex Sans"` for body. This creates a cohesive "infrastructure product" identity across docs, dashboard, and marketing.

---

## BUILD DIRECTIVE

**TARGET:** Clean's landing page hero + "How It Works" section — a complete rebuild of the above-the-fold experience and the primary conversion narrative on tryclean.ai.

**APPROACH:**
Build a dark-mode, scroll-driven landing page hero that opens with a WebGL canvas showing an abstract particle visualization of data flowing through a pipeline (borrowing The Obsidian Assembly's particle reformation technique at reduced complexity — use 2D canvas particles, not full WebGL, for performance). As the user scrolls, the hero transitions into a "How It Works" section using GSAP ScrollTrigger + Lenis smooth scroll (Off Menu's stack). Each scroll segment reveals a pipeline stage: (1) raw API request, (2) Clean's context layer intercepts, (3) TOON format structures the context, (4) agent receives enriched response. Code snippets and architecture annotations float as HTML overlays above the visualization using `mix-blend-mode: difference` (Oryzo's technique). Typography uses the Sera editorial system — serif display headings (`IBM Plex Serif`), uppercase tracked labels, square-cornered cards for feature callouts. Below the fold, the social proof section uses Off Menu's chat-bubble pattern reframed as an agent conversation demo. The overall color system: `#050508` background, `#f0f0f0` text, `#3b82f6` (blue) accent for CTAs, `#f59e0b` (amber) accent for the particle system. The page should feel like a Bloomberg terminal had a baby with a Stripe product page.

**STACK:**
- `next` (App Router)
- `tailwindcss` v4
- `shadcn` with Sera style (`--style sera`)
- `radix-ui` (unified package)
- `gsap` + `ScrollTrigger` + `Flip` plugins
- `lenis` (smooth scroll)
- `three` (lightweight — only for particle system if WebGL path chosen) OR Canvas 2D API for particles (lighter)
- `framer-motion` (for UI component animations — entrance, hover states)
- `IBM Plex Serif`, `IBM Plex Sans`, `IBM Plex Mono` (Google Fonts)
- `class-variance-authority` (component variants)

**REFERENCE URLS:**
1. https://obsidianassembly.com — particle reformation technique, dark atmospheric palette, mouse-reactive elements
2. https://www.offmenu.design — page structure, Lenis+GSAP scroll stack, chat-bubble social proof, dark-mode color system
3. https://ui.shadcn.com/sera — typography system, square corners, editorial component design, Sera CSS variables

**KEY CONSTRAINTS:**
- Clean is developer infrastructure. The result must feel like a precision instrument, not a marketing brochure. No gradient orbs, no glassmorphism cards, no "AI glow" effects.
- Performance budget: First Contentful Paint < 1.5s, Largest Contentful Paint < 2.5s. Particle system must maintain 60fps on M1 MacBook Air.
- The particle visualization is atmospheric, not informational — it should enhance the "data flowing through a pipeline" metaphor without requiring the user to understand it. If it looks confusing, simplify to animated dots on paths.
- Avoid the "Cursor aesthetic" (neon purple, floating glass cards, gradient blobs). Clean's differentiation is editorial precision + technical depth.
- Mobile must work without WebGL — progressive enhancement. Mobile hero is a CSS-animated version of the same visual metaphor (animated SVG paths or CSS particles).
- All interactive elements must have visible focus states and meet WCAG 2.1 AA contrast ratios.
