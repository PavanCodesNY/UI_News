# UI Intelligence Report — April 18, 2026

---

## 1. Oryzo AI — https://oryzo.ai/

**WHAT IT DOES:**
A cinematic product launch site by Lusion Ltd that presents a fictional cork coaster as a serious AI-era wearable product. The site uses a full-viewport WebGL canvas rendered via Three.js with GSAP-driven scroll-sequenced scenes. Each scroll segment triggers camera transitions, material shader swaps, and post-processing filter changes (bloom, chromatic aberration, film grain) to create a fake-but-convincing Apple-style product reveal. The layout is a single vertical scroll column where the DOM layer (typography, CTAs) floats over the WebGL canvas with `pointer-events: none` pass-through, synced to scroll position via GSAP ScrollTrigger. Scored 7.86/10 on Awwwards SOTD April 14.

**PACKAGES:**
- `three` (Three.js for WebGL rendering)
- `gsap` + `ScrollTrigger` plugin (scroll-driven animation sequencing)
- `postprocessing` or Three.js `EffectComposer` (bloom, chromatic aberration, film grain)
- Custom GLSL shaders (material transitions, environment mapping)
- SideFX Houdini (offline mesh/particle baking exported as geometry buffers)
- Lenis or custom smooth scroll implementation

**IMPLEMENTATION:**
1. **Component tree:** Single `<canvas>` element spanning full viewport with `position: fixed`. DOM content layer with `position: absolute` stacked on top, scroll height set to ~500vh to create scroll runway.
2. **Scene setup:** Three.js scene with a single hero product mesh (cork coaster) centered at origin. PBR material with custom environment map. Camera on a GSAP timeline that orbits the product: `gsap.timeline({ scrollTrigger: { trigger: ".scroll-container", start: "top top", end: "bottom bottom", scrub: 1 } })`.
3. **Shader transitions:** Fragment shader uses `uniform float uProgress` (0-1 mapped to scroll) to interpolate between material states — matte cork → metallic sheen → wireframe reveal. Key uniform: `mix(baseColor, emissiveColor, smoothstep(0.3, 0.5, uProgress))`.
4. **Post-processing stack:** EffectComposer chain: UnrealBloomPass (strength: 0.4, radius: 0.8, threshold: 0.6) → ChromaticAberrationEffect (offset: vec2(0.002)) → FilmPass (noiseIntensity: 0.15, scanlineIntensity: 0.0).
5. **Typography overlay:** Large sans-serif type at 8-12vw, `mix-blend-mode: difference`, `opacity` animated with GSAP `fromTo` tied to scroll position.
6. **Color palette:** 2 colors — HEX #E59291 (dusty rose) and #E7D497 (warm gold) on dark (#0A0A0A) background.
7. **Performance:** Geometry instancing for particle bursts, texture compression (KTX2), requestAnimationFrame throttled to visible viewport only.

**WHY IT WORKS:**
The site weaponizes the Apple product launch template — cinematic camera orbits, dramatic lighting reveals, sparse typography — against a mundane object, creating cognitive dissonance that holds attention. The visual hierarchy is ruthlessly simple: one object, one message per viewport section, no competing elements. The scroll-scrubbed animation gives users agency (Gestalt principle of common fate), making the experience feel interactive rather than passive video.

**GOOD / BAD:**
GOOD: Production quality rivals actual tech product launches; proves WebGL storytelling doesn't need complex scenes — one object + great lighting is enough.
BAD: Heavy WebGL payload (~3MB+ initial); the satirical premise means no real conversion goal — pure art project.

**CLEAN APPLICATION:**
Apply this to Clean's **product demo section** on tryclean.ai. Build a scroll-sequenced WebGL scene showing a single visualization of Clean's context graph: start with scattered, disconnected agent nodes (chaos), scroll to reveal context lines connecting them (Clean's value), end on a unified glowing network (the vision). Use the same cinematic camera orbit + post-processing stack. Instruction to design agent: "Build a Three.js scroll-sequenced product visualization showing 5-8 floating agent icons that progressively connect via glowing lines as the user scrolls. Camera orbits from a wide shot to a tight hero shot. Use bloom post-processing. Dark background (#0A0A0C), accent color Clean blue. Total scroll runway: 400vh, 4 scenes."

---

## 2. The Obsidian Assembly — https://obsidianassembly.com/

**WHAT IT DOES:**
A dark-themed organizational portfolio featuring a WebGL particle gallery system, custom smooth scrolling via the `stringtune` library, and mouse-reactive visual effects. The layout uses a full-bleed particle canvas as the background layer with content sections that trigger particle behavior changes on scroll. Particles reorganize into gallery formations when image sections enter the viewport, then disperse on exit. Navigation relies on scroll-driven layout transitions with `stringprogress` for animation progress tracking. Awwwards SOTD April 15, plus Developer Award.

**PACKAGES:**
- `stringtune` (custom animation orchestration library)
- `stringprogress` (scroll progress tracking)
- WebGL (raw or lightweight wrapper — not Three.js based)
- Custom smooth scroll implementation (Lenis-style momentum)
- GLSL shaders for particle rendering
- CSS `mix-blend-mode` for text/particle compositing

**IMPLEMENTATION:**
1. **Particle system:** WebGL fragment shader renders ~10,000-50,000 particles as GL_POINTS. Each particle has position, velocity, target-position, and opacity attributes stored in Float32Arrays. On each frame, particles lerp toward target positions: `position += (target - position) * 0.05`.
2. **Gallery mode:** When a gallery section enters viewport (tracked by `stringprogress`), particle target positions are set to UV coordinates of the gallery image, effectively "assembling" the image from particles. Particle color sampled from image texture at each particle's UV position.
3. **Mouse interaction:** `mousemove` event updates a uniform `vec2 uMouse` in the particle shader. Particles within a radius of the mouse are displaced: `if (distance(position, uMouse) < 0.1) position += normalize(position - uMouse) * 0.02`.
4. **Scroll architecture:** `stringtune` orchestrates animation timelines tied to scroll offset. Each content section has entry/exit triggers that swap particle target states. Smooth scroll uses `requestAnimationFrame` with lerped scroll position (factor: 0.08).
5. **Dark UI:** Background #000000, text in off-white (#E8E8E8), monospace or geometric sans-serif type. Content sections use generous vertical padding (20-30vh between sections).
6. **Responsive breakpoints:** Particle count reduced to ~5,000 on mobile. Mouse interaction replaced with device orientation on touch devices.

**WHY IT WORKS:**
The particle system creates a unified visual language that bridges content sections — instead of discrete pages, the experience feels like one continuous organism. The dark palette with bright particles creates extreme luminance contrast (Weber's Law), making the particles feel like light sources. Mouse reactivity triggers the brain's motion detection system (peripheral vision is more motion-sensitive), keeping users engaged even when not directly reading content.

**GOOD / BAD:**
GOOD: Particle-to-image assembly is a genuinely novel reveal mechanism; dark UI feels premium and technical.
BAD: `stringtune` is a niche library with limited community support; particle gallery images can be hard to read at low particle counts; accessibility (screen reader) is essentially zero for the visual layer.

**CLEAN APPLICATION:**
Apply this to Clean's **landing page hero section**. Instead of particles assembling into images, have particles represent individual "context signals" that flow between agent icons. On mouse hover, particles scatter and regroup. Instruction to design agent: "Build a WebGL particle background for Clean's hero with ~8,000 particles in Clean's blue (#3B82F6) on a #09090B background. Particles flow in streams between 3-4 floating icons representing different AI agents (Claude, Cursor, Copilot logos as simple geometric shapes). On mouse proximity, particles disperse and reform. Overlay the hero headline 'Unified context for every agent' in Inter Bold at 6vw with `mix-blend-mode: difference`."

---

## 3. Max Mara — Jacket Circle — https://thejacketcirclegame.maxmara.com/

**WHAT IT DOES:**
A fashion-meets-interactive-art microsite by Adoratorio Studio that uses GSAP + PixiJS to create a 2D GPU-accelerated experience centered on the Olimpia jacket. The layout is a series of full-viewport "rooms" (imaginary worlds) that transition via GSAP-powered morph animations. Each room uses PixiJS displacement maps and filter chains to create fabric-like warping effects on background illustrations. Navigation is horizontal swipe/scroll with momentum, creating a carousel of immersive scenes. Typography is oversized editorial style with animated letter spacing on transitions. Awwwards SOTD April 17, scored 7.34/10. Tech: HTML5, GSAP, PixiJS.

**PACKAGES:**
- `gsap` + `ScrollTrigger` + `Draggable` plugins
- `pixi.js` (PixiJS v7/v8 for 2D GPU rendering)
- `@pixi/filter-displacement` (fabric warping effects)
- `@pixi/filter-color-matrix` (color grading per scene)
- Custom displacement map textures (Perlin noise-based, 512x512px)
- CSS custom properties for color palette transitions

**IMPLEMENTATION:**
1. **PixiJS canvas:** Full-viewport `<canvas>` rendered by PixiJS Application. Each "room" is a PixiJS Container with layered Sprites (background illustration, midground elements, foreground fabric textures).
2. **Displacement effect:** `PIXI.filters.DisplacementFilter` applied to each room's container. Displacement map is a grayscale Perlin noise texture animated with `gsap.to(displacementSprite, { x: "+=100", y: "+=50", duration: 8, repeat: -1, yoyo: true, ease: "sine.inOut" })`. Creates slow, organic fabric-ripple effect.
3. **Scene transitions:** Horizontal scroll (or drag via GSAP Draggable) triggers a timeline: current room scales down to 0.9 with opacity fade, displacement intensity spikes (scale: 50 → 200 → 50), next room fades in. Total transition duration: 1.2s, ease: `"power3.inOut"`.
4. **Color palette:** Two primary colors — #E59291 (dusty rose), #E7D497 (warm gold) — applied via PixiJS ColorMatrixFilter per room. CSS `--bg-color` and `--text-color` custom properties transition with `transition: color 0.8s ease`.
5. **Typography animation:** Letters animated with GSAP SplitText: each letter has `opacity: 0, y: 40` → `opacity: 1, y: 0` staggered at 0.03s per letter on room entry. Letter-spacing animates from `0.5em` → `0.05em` over 0.8s.
6. **Microinteractions:** Cursor changes to custom SVG circle scaling on hover. Parallax on mouse move: foreground at 1.5x mouse delta, background at 0.3x.

**WHY IT WORKS:**
The displacement filter creates a tactile, fabric-like quality that directly maps to the product category (fashion), achieving rare thematic coherence between tech and content. The two-color palette creates cohesive identity across rooms while horizontal navigation breaks the vertical-scroll mental model, signaling "this is an experience, not a webpage." The editorial typography scale (15-20vw headlines) creates visual weight that competes with the PixiJS animation layer.

**GOOD / BAD:**
GOOD: PixiJS displacement maps are lightweight (~50KB per texture) yet create premium-feeling motion; horizontal navigation with momentum feels app-like.
BAD: Horizontal scroll is unintuitive on desktop without clear affordance; PixiJS text rendering is less sharp than DOM text at large sizes.

**CLEAN APPLICATION:**
Apply PixiJS displacement transitions to Clean's **pricing page or feature comparison section**. As users scroll between Clean's pricing tiers or feature categories, use displacement map transitions instead of standard fade/slide. Instruction to design agent: "Use PixiJS with DisplacementFilter on Clean's pricing section. Three cards (Free, Pro, Enterprise) transition horizontally with displacement warp effect on swipe. Displacement map: 512x512 Perlin noise. Transition duration: 0.8s. Background: dark (#0A0A0C). Card backgrounds: subtle glass-morphism. Accent: Clean blue glow on active card."

---

## 4. Odd Ritual — https://www.awwwards.com/sites/odd-ritual

**WHAT IT DOES:**
A minimal e-commerce brand site for a golf-culture clothing brand, built by Studio Malvah using GSAP, Three.js, and Barba.js for seamless page transitions. The layout follows a strict grid system with generous whitespace. Product pages feature Three.js-rendered product models that rotate on hover with cloth simulation. Page transitions use Barba.js to swap content with GSAP-animated clip-path reveals, maintaining a persistent navigation layer. Color palette is deliberately restrained — white background, navy blue accent (#1A1A2E), no gradients. Awwwards SOTD April 12; design 7.79, usability 7.27, creativity 7.39, content 7.7.

**PACKAGES:**
- `gsap` + `ScrollTrigger` + `Flip` plugins
- `three` (Three.js for product 3D renders)
- `@barba/core` (seamless page transitions)
- `contentful` SDK (headless CMS for product data)
- CSS Grid with `auto-fill` / `minmax()` for product grid
- Custom cursor (SVG-based, GSAP-animated scale)

**IMPLEMENTATION:**
1. **Page transitions:** Barba.js intercepts `<a>` clicks. Exit animation: `gsap.to(".page-content", { clipPath: "inset(0 0 100% 0)", duration: 0.6, ease: "power3.inOut" })`. Enter animation: reverse clip-path on new content. Navigation bar persists (excluded from Barba container).
2. **Product grid:** CSS Grid: `grid-template-columns: repeat(auto-fill, minmax(320px, 1fr))`. Gap: 2px. Product cards have no border/shadow — just image + text. Hover: image scales to 1.03 over 0.4s, product title letter-spacing tightens.
3. **3D product view:** On product detail pages, Three.js canvas shows the garment on a mannequin. `OrbitControls` limited to horizontal rotation (±45°). Lighting: one key directional light (intensity 1.2) + ambient (0.3). Material: `MeshStandardMaterial` with roughness 0.7 for fabric feel.
4. **Scroll behavior:** Lenis smooth scroll with `lerp: 0.1`. Product images use GSAP ScrollTrigger parallax: `y: "-15%"` scrubbed over viewport passage.
5. **Typography:** Headlines in geometric sans at 4-5vw, body at 16px/1.6. All caps for navigation and category labels. Letter-spacing: 0.08em on labels, -0.02em on headlines.
6. **Color:** Background #FFFFFF, text #1A1A2E (dark navy), accent #3A3AFF (electric blue) used only for hover states and CTAs. Max 3 colors total.

**WHY IT WORKS:**
Extreme restraint creates perceived luxury — when every pixel is deliberate, nothing feels cheap. The 3-color maximum forces typographic hierarchy to do the heavy lifting (size, weight, spacing, case), which reads as sophisticated. Barba.js transitions eliminate the "white flash" of page loads, making a multi-page e-commerce site feel like a single-page app. Minimal approach means sub-1s load times, which itself communicates quality.

**GOOD / BAD:**
GOOD: Proves you don't need heavy animation to win design awards — restraint and typography are enough; Barba.js transitions are buttery smooth.
BAD: Low contrast on some text elements (7.27 usability score); Three.js for clothing adds weight without clear ROI vs high-quality photography.

**CLEAN APPLICATION:**
Apply this minimal approach to Clean's **documentation or changelog pages**. Use Barba.js page transitions with clip-path reveals between doc sections. Strip all decoration — let typography, whitespace, and a single accent color carry the hierarchy. Instruction to design agent: "Build Clean's docs navigation with Barba.js transitions. Page swaps use `clipPath: inset()` GSAP animation (0.5s, power3.inOut). Persistent sidebar navigation. Body text: Inter 16px/1.7. Code blocks: JetBrains Mono. Background: #FAFAFA. Text: #18181B. Accent: #3B82F6 (links and active nav). Zero decorative elements."

---

## 5. Codrops — 3D Product Grid (R3F + GLSL) — https://tympanus.net/codrops/2026/02/24/from-flat-to-spatial-creating-a-3d-product-grid-with-react-three-fiber/

**WHAT IT DOES:**
A tutorial/demo from Codrops (Matt Greenberg, Feb 2026) that transforms a flat e-commerce product grid into a curved 3D spatial layout using React Three Fiber and custom GLSL vertex shaders. Products are arranged in a grid but each card is rendered as a Three.js plane mesh with curved vertex displacement bending the entire grid into a cylindrical surface. Scroll drives the cylinder rotation, creating a spatial browsing experience. Mouse hover triggers individual card depth displacement (z-pop) with spring physics.

**PACKAGES:**
- `react` + `react-dom`
- `@react-three/fiber` (React Three Fiber)
- `@react-three/drei` (helpers: useTexture, Text, Float)
- `three` (Three.js core)
- `@react-spring/three` (spring-physics hover animations)
- `leva` (debug GUI for tweaking values)
- `zustand` (lightweight state management for scroll/hover state)
- Custom GLSL vertex shader (cylindrical displacement)
- `tailwindcss` (for DOM overlay UI)

**IMPLEMENTATION:**
1. **Component tree:**
   ```
   <Canvas>
     <CurvedGrid>
       <ProductCard /> × N (instanced)
     </CurvedGrid>
     <ambientLight intensity={0.4} />
     <directionalLight position={[5, 5, 5]} intensity={1.0} />
   </Canvas>
   ```
2. **Curved grid vertex shader:**
   ```glsl
   uniform float uCurvature; // 0.0 = flat, 1.0 = full cylinder
   uniform float uScrollOffset;
   void main() {
     vec3 pos = position;
     float angle = pos.x * uCurvature + uScrollOffset;
     float radius = 5.0;
     pos.z = cos(angle) * radius - radius;
     pos.x = sin(angle) * radius;
     gl_Position = projectionMatrix * modelViewMatrix * vec4(pos, 1.0);
   }
   ```
3. **Scroll binding:** `useFrame` reads scroll offset from Zustand store (updated by DOM scroll listener). Scroll offset maps to `uScrollOffset` uniform: `uniforms.uScrollOffset.value = THREE.MathUtils.lerp(prev, scrollY * 0.001, 0.1)`.
4. **Hover interaction:** `@react-spring/three` animates hovered card's `position-z` from 0 → 0.5 with `config: { mass: 1, tension: 280, friction: 60 }`. Card also scales to 1.08.
5. **Product images:** Loaded as Three.js textures via `useTexture` from `@react-three/drei`. Applied to `MeshBasicMaterial` with `transparent: true` for rounded corner alpha masking.
6. **Performance:** Instanced rendering via `InstancedMesh` for cards sharing same geometry. Texture atlas for product images to reduce draw calls. Frustum culling enabled.

**WHY IT WORKS:**
Cylindrical warp adds depth perception (binocular disparity simulation) to a flat grid, making product browsing feel spatial and tactile. Spring physics on hover provide kinesthetic feedback that flat CSS transitions can't match. Continuous curve eliminates pagination, creating an "infinite shelf" metaphor. Fitts's Law in 3D — curved surface keeps all items equidistant from viewport center.

**GOOD / BAD:**
GOOD: Lightweight approach to spatial UI — just a vertex shader, no complex 3D models; spring physics feel premium; reusable pattern for any grid content.
BAD: Text readability suffers on curved surfaces at extreme curvature; accessibility for keyboard navigation unaddressed; mobile touch needs separate implementation.

**CLEAN APPLICATION:**
Apply this to Clean's **integrations showcase or MCP server directory** on tryclean.ai. Instead of a flat grid of integration logos, render them on a curved 3D surface. Instruction to design agent: "Build Clean's integrations section as a curved 3D grid using React Three Fiber. Each integration card (GitHub, Slack, Linear, etc.) is a plane mesh with logo texture. Arrange in 4×N grid, apply cylindrical vertex displacement (curvature: 0.6). Scroll rotates cylinder. Hover pops cards forward (z: 0.5, spring tension: 280). Background: #09090B. Card backgrounds: #1A1A1A with 1px #2A2A2A border."

---

## BUILD DIRECTIVE

**TARGET:** Clean's landing page hero section + product demo scroll sequence (tryclean.ai above-the-fold through first product section)

**APPROACH:** Combine Oryzo AI's scroll-sequenced cinematic reveal with The Obsidian Assembly's particle system for a hero that opens on a dark canvas with flowing context-signal particles streaming between agent icons, then transitions via scroll into a product demo showing Clean's context graph assembling in real-time. The hero uses WebGL particles on a fixed canvas. As the user scrolls, particles converge from chaos into structured streams connecting agent nodes (Claude, Cursor, Copilot, GPT icons as simple geometric silhouettes). At scroll position 60%, the 3D scene transitions (Oryzo-style camera orbit) to show a unified context dashboard mock. Typography overlays in the Odd Ritual style — extreme restraint, Inter Bold at 6vw, max 8 words per viewport, `mix-blend-mode: difference`. No gradients, no blurs, no generic SaaS hero patterns. This should feel like infrastructure — cold, precise, powerful.

**STACK:**
- `next` (Next.js 15+ App Router)
- `react`, `react-dom`
- `three`
- `@react-three/fiber`
- `@react-three/drei` (Float, useTexture, Text)
- `@react-three/postprocessing` (Bloom, ChromaticAberration)
- `gsap` + `ScrollTrigger` (scroll-sequenced animation)
- `lenis` (smooth scroll)
- `tailwindcss` (DOM layer styling)
- `framer-motion` (DOM element entry animations)
- `inter` font (variable weight)
- `jetbrains-mono` font (code snippets)

**REFERENCE URLS:**
1. https://oryzo.ai/ — Scroll-sequenced WebGL product reveal (camera, shaders, post-processing)
2. https://obsidianassembly.com/ — Particle system as background layer with scroll-driven state changes
3. Odd Ritual (https://www.awwwards.com/sites/odd-ritual) — Typographic restraint and minimal color palette for developer credibility

**KEY CONSTRAINTS:**
- Clean is developer infrastructure. The result must feel like Linear, Vercel, or Stripe — technically sophisticated, fast, quiet confidence. NOT like a SaaS template, NOT like a marketing agency portfolio, NOT like a crypto/Web3 hype site.
- No decorative gradients, no glassmorphism cards, no floating blobs, no stock photography, no "trusted by" logo bars in the hero.
- Performance budget: First Contentful Paint < 1.5s. Total hero JS < 200KB gzipped. WebGL canvas must maintain 60fps on M1 MacBook Air.
- Particle count: max 8,000 on desktop, 3,000 on mobile. Use instanced rendering.
- Color palette: #09090B (bg), #FAFAFA (text), #3B82F6 (accent/particles), #1E40AF (accent dark). Max 4 colors total.
- The hero headline should be 6-8 words max. No subheadline longer than 15 words. One CTA button. Zero navigation items visible in the hero viewport.
