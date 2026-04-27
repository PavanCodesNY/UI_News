# Daily UI Intelligence Brief — April 2, 2026

---

## 1. iyO — https://iyo.co

**WHAT IT DOES:**
A high-fidelity 3D product showcase and bespoke configurator for iyO, a screenless computing hardware company. The site renders interactive WebGL product models in real-time, allowing users to rotate, zoom, and configure product variants directly in the browser. The layout system pairs a full-viewport WebGL canvas (rendered via Three.js) with overlaid HTML UI elements using CSS pointer-events layering. Navigation is non-standard — infinite scroll sections with sound/audio cues transition between product states. The interaction model treats the 3D object as the primary content, with text and CTAs positioned as secondary floating elements anchored to scroll progress.

**PACKAGES:**
- `three` (Three.js core — WebGL renderer, PerspectiveCamera, OrbitControls)
- `@react-three/fiber` (if React-based) or vanilla Three.js
- `@react-three/drei` (Environment, ContactShadows, useGLTF for model loading)
- `gsap` + `ScrollTrigger` (scroll-linked animation orchestration)
- `howler` or `tone` (audio integration on scroll events)
- `webflow` (site framework — HTML/CSS structure)
- Custom GLSL shaders for material effects
- Font: likely a geometric sans (Inter or custom)

**IMPLEMENTATION:**
1. **Component Tree:** `<App>` → `<Canvas>` (full viewport, `position: fixed; inset: 0; z-index: 0`) → `<ProductModel>` (GLTF loaded via useGLTF/GLTFLoader) + `<Environment preset="studio">` + `<ContactShadows>`. HTML overlay: `<ScrollContainer>` (`position: relative; z-index: 1; pointer-events: none` on non-interactive areas) → `<Section>` blocks at `100vh` each.
2. **3D Model:** Load optimized `.glb` (Draco-compressed, <2MB target). Apply `MeshPhysicalMaterial` with `clearcoat: 1.0`, `roughness: 0.15`, `metalness: 0.8` for the hardware shell. Use environment map (HDR) for reflections.
3. **Scroll-Linked Animation:** GSAP ScrollTrigger pins the canvas. On scroll progress (0→1), interpolate: camera position (`gsap.to(camera.position, { x, y, z })`), model rotation (`gsap.to(model.rotation, { y: Math.PI * 2 })`), material color transitions. Use `scrub: 1.5` for smooth scroll-scrub with 1.5s lag.
4. **Configurator:** Color/variant buttons in HTML overlay. On click, tween material properties: `gsap.to(material.color, { r, g, b, duration: 0.6, ease: "power2.inOut" })`.
5. **Audio:** Trigger subtle UI sounds on section transitions using Howler.js. Sounds should be <50KB each, loaded lazily.
6. **Responsive:** At `max-width: 768px`, reduce model polygon count (LOD swap), disable OrbitControls (use auto-rotate instead), stack text sections below canvas (canvas height: `50vh`).
7. **Performance:** Use `renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))`. Implement `useFrame` throttling. Lazy-load model after initial HTML paint.

**WHY IT WORKS:**
The site inverts the traditional text-first hierarchy by making the 3D product the focal point — applying the Gestalt principle of figure-ground, where the floating UI becomes ground to the product figure. The dark background (#0a0a0a) creates maximum luminance contrast against the lit 3D model, drawing the eye immediately. Scroll-linked animation creates a sense of user agency (motion semantics: the user "controls" the reveal), which increases engagement and time-on-site.

**GOOD / BAD:**
- GOOD: The 3D configurator is production-grade — materials, lighting, and interaction polish are best-in-class for e-commerce WebGL.
- BAD: Unusual navigation and infinite scroll patterns likely hurt accessibility and orientation; no clear wayfinding for keyboard/screen reader users.

**CLEAN APPLICATION:**
Apply this to Clean's **product demo section** on the landing page. Instead of a hardware product, render an interactive 3D visualization of Clean's MCP proxy architecture — show data flowing between nodes (agents → Clean proxy → MCP servers) as a Three.js particle/node graph that the user scrubs through on scroll. Use the same scroll-pinned canvas technique with HTML text overlays explaining each stage. Direct instruction: "Build a scroll-pinned WebGL section where scrolling reveals the data flow through Clean's proxy layer step by step, with glowing particle trails connecting nodes on a dark background."

---

## 2. Vast — https://vastspace.com

**WHAT IT DOES:**
Vast builds next-generation space stations, and their website is a cinematic, full-bleed visual experience that won the Awwwards Developer Award on April 2, 2026. The site uses large-format hero imagery/video with scroll-driven parallax layering to create depth. The layout is a vertical scroll narrative — each section is a full-viewport "scene" with a dominant visual (rendered 3D station imagery or high-res photography) and minimal typography overlaid. Transitions between scenes use opacity crossfades and vertical translate animations triggered by scroll position. The typography system uses a high-contrast pairing: large display headings (likely 80-120px desktop) in a condensed sans-serif against small body text (14-16px).

**PACKAGES:**
- `next` (Next.js framework — SSR, image optimization)
- `gsap` + `ScrollTrigger` + `ScrollSmoother` (scroll orchestration, parallax, pinning)
- `@gsap/SplitText` (text reveal animations — character/word-level staggering)
- Possibly `three` for select 3D rendered sections
- `next/image` (optimized image delivery, AVIF/WebP)
- Font: condensed sans-serif display (e.g., `Founders Grotesk Condensed` or `Bebas Neue`) + system/mono for body
- `lenis` or GSAP ScrollSmoother for smooth scroll normalization

**IMPLEMENTATION:**
1. **Layout:** Each "scene" is a `<section>` at `min-height: 100vh; position: relative; overflow: hidden`. Background media is `position: absolute; inset: 0; object-fit: cover`.
2. **Parallax:** GSAP ScrollTrigger on each section's background image: `gsap.to(bgImage, { yPercent: -20, ease: "none", scrollTrigger: { trigger: section, start: "top bottom", end: "bottom top", scrub: true } })`. Foreground text moves at 1x scroll speed (no parallax), creating depth separation.
3. **Text Reveals:** SplitText splits headings into characters. On scroll into view: `gsap.from(chars, { y: 80, opacity: 0, stagger: 0.02, duration: 0.8, ease: "power3.out", scrollTrigger: { trigger: heading, start: "top 85%" } })`.
4. **Scene Transitions:** Between sections, use a `clip-path` or `opacity` crossfade. CSS: `clip-path: inset(0 0 100% 0)` animating to `inset(0)` via ScrollTrigger scrub.
5. **Color System:** Background: `#000000` to `#0a0f1a` (deep navy). Text: `#ffffff` primary, `rgba(255,255,255,0.6)` secondary. Accent: cool blue `#4a90d9`.
6. **Responsive:** At 768px, reduce heading sizes to 40-48px. Replace video backgrounds with optimized stills. Simplify parallax (reduce `yPercent` to -8).
7. **Performance:** Preload hero video/image. Use `will-change: transform` on parallax elements. Debounce ScrollTrigger refresh. Lazy-load below-fold sections with Intersection Observer.

**WHY IT WORKS:**
The cinematic scale triggers "overview effect" awe — large visuals with minimal text creates a high signal-to-noise ratio that communicates ambition and technological mastery. The scroll-driven narrative uses the principle of progressive disclosure, letting users consume information at their own pace. High contrast between massive imagery and sparse typography creates a clear visual hierarchy where the mission (visuals) dominates over the details (text).

**GOOD / BAD:**
- GOOD: Cinematic storytelling is perfectly matched to the brand — the site *feels* like space exploration, creating genuine emotional resonance.
- BAD: Heavy media assets likely cause slow initial load on mobile/low-bandwidth; the sparse content density means users scroll a lot for little information.

**CLEAN APPLICATION:**
Apply this to Clean's **landing page hero and above-fold narrative**. Use the same cinematic, full-bleed section structure but replace space imagery with abstract developer-infrastructure visuals — dark terminal-inspired backgrounds with glowing grid lines, flowing data streams, and code fragments. Direct instruction: "Rebuild Clean's hero as a 3-section scroll narrative: Section 1 is a full-viewport hero with the headline and a subtle animated grid background. Section 2 uses parallax to show the 'before Clean' chaos (scattered API calls). Section 3 reveals the 'after Clean' clarity (unified proxy layer). Use GSAP ScrollTrigger with scrub parallax and SplitText character reveals on headings."

---

## 3. San Rita — https://sanrita.ca

**WHAT IT DOES:**
Atelier San Rita is a Montréal-based creative studio whose portfolio site won Awwwards SOTD on April 1, 2026. The centerpiece is a custom-built interactive 3D map — not a traditional grid or list portfolio, but a navigable 3D world engineered from scratch. Custom models designed in Blender are loaded into a React Three Fiber scene and animated with GSAP. The interaction model lets users explore the studio's work by navigating through a spatial environment rather than scrolling a page. Each "project" is a location on the map that can be clicked to expand into a detail view with smooth 3D camera transitions.

**PACKAGES:**
- `react` + `next` (framework)
- `@react-three/fiber` (React Three Fiber — declarative Three.js)
- `@react-three/drei` (helpers: useGLTF, Environment, Float, Html, OrbitControls)
- `@react-three/postprocessing` (bloom, vignette, chromatic aberration)
- `gsap` (camera animation, UI transitions)
- `three` (underlying Three.js)
- `blender` (3D modeling — export as `.glb`)
- `draco3dgltf` (GLTF compression)
- `tailwindcss` (utility CSS for HTML overlays)
- Font: likely custom or `Space Grotesk` / `General Sans`

**IMPLEMENTATION:**
1. **Scene Setup:** `<Canvas camera={{ fov: 45, near: 0.1, far: 1000, position: [0, 5, 10] }}>` with `<Environment preset="night" />` for moody lighting. Add `<fog attach="fog" args={['#0a0a0a', 10, 50]} />` for depth.
2. **3D Map:** Load Blender-exported `.glb` with `useGLTF`. Each project marker is a child mesh with custom `MeshStandardMaterial` and emissive glow (`emissive: '#4488ff'`, `emissiveIntensity: 0.5`). Use `<Float speed={2} rotationIntensity={0.3}>` on markers for idle animation.
3. **Navigation:** On marker click, animate camera with GSAP: `gsap.to(camera.position, { x: target.x, y: target.y + 2, z: target.z + 3, duration: 1.2, ease: "power2.inOut" })` and `gsap.to(controls.target, { x: target.x, y: target.y, z: target.z, duration: 1.2 })`.
4. **Post-Processing:** `<EffectComposer>` → `<Bloom luminanceThreshold={0.8} intensity={0.3} />` → `<Vignette darkness={0.4} />` for cinematic polish.
5. **Detail View:** When a project is selected, overlay an HTML panel using Drei's `<Html>` component or a CSS-positioned overlay with `transform: translateY(100vh)` → `translateY(0)` transition (duration: 0.6s, ease: cubic-bezier(0.16, 1, 0.3, 1)).
6. **Responsive:** At 768px, replace 3D map with a simplified 2D card grid. Disable post-processing. Use static project thumbnails.
7. **Performance:** Compress all `.glb` models with Draco (<500KB total). Use `<Suspense>` with loading skeleton. Implement frustum culling for off-screen markers.

**WHY IT WORKS:**
The spatial navigation model leverages the Gestalt principle of proximity in 3D space — related projects cluster together, creating implicit categorization without labels. The interactive map provides strong user agency, making portfolio browsing feel like exploration rather than consumption. The Blender-to-web pipeline ensures unique visual identity that cannot be replicated with stock 3D.

**GOOD / BAD:**
- GOOD: The custom 3D world is genuinely differentiated — no other studio portfolio looks or works like this. The Blender + R3F pipeline is production-proven.
- BAD: The 3D navigation paradigm sacrifices discoverability — users may miss projects because there's no complete list view. Accessibility for keyboard/screen reader users is likely very poor.

**CLEAN APPLICATION:**
Apply this to Clean's **documentation navigation or product architecture visualization**. Instead of a studio portfolio, render Clean's MCP ecosystem as an explorable 3D node graph — each MCP server is a node, connections show data flow, and clicking a node opens its documentation/configuration panel. Direct instruction: "Build an interactive 3D architecture diagram for Clean's docs using React Three Fiber. Render MCP servers as glowing nodes in 3D space, connected by animated particle lines. Clicking a node smoothly flies the camera to it and opens an HTML overlay panel with server details. Use Drei's Float for idle animation and GSAP for camera transitions."

---

## 4. Linear.app Rebuild Pattern — https://linear.app

**WHAT IT DOES:**
Linear's website is the gold standard for developer-tool SaaS marketing pages in 2026. The layout is a vertical scroll page with distinct sections, each demonstrating a product feature using the product's own UI as the visual asset (product screenshots, not abstract illustrations). The design system is dark-mode-first with a monochromatic palette punctuated by subtle gradient accents. Animations are restrained and purposeful — elements fade-and-slide in on scroll, hover states use subtle scale transforms, and section transitions use opacity with slight vertical movement. The site has been publicly rebuilt using Next.js + Tailwind + Framer Motion, establishing this as the canonical dev-tool marketing stack.

**PACKAGES:**
- `next` (Next.js 14+ with App Router)
- `tailwindcss` (utility-first CSS)
- `framer-motion` (declarative React animation — `motion.div`, `useInView`, `AnimatePresence`)
- `@radix-ui/react-*` (accessible primitives for navigation, dialogs)
- `clsx` + `tailwind-merge` (conditional class utilities)
- `next/image` (optimized images)
- `next/font` (font optimization — likely `Inter` or `GT America`)
- `lucide-react` (icons)

**IMPLEMENTATION:**
1. **Global Layout:** Dark background `bg-[#0a0a0a]`. Text: `text-white` primary, `text-white/60` secondary. Max content width: `max-w-[1120px] mx-auto px-6`.
2. **Hero Section:** Headline at `text-[56px] md:text-[72px] font-medium leading-[1.05] tracking-[-0.02em]`. Subtitle at `text-[18px] text-white/60 max-w-[540px]`. CTA buttons: primary `bg-[#5e6ad2] hover:bg-[#6b77db] text-white rounded-full px-6 py-3 text-sm font-medium transition-colors duration-200`. Hero image: product screenshot with `rounded-xl border border-white/10 shadow-2xl` and subtle glow: `shadow-[0_0_80px_rgba(94,106,210,0.15)]`.
3. **Scroll Animations:** Framer Motion `useInView` hook on each section. Animate: `initial={{ opacity: 0, y: 24 }}` → `animate={{ opacity: 1, y: 0 }}` with `transition={{ duration: 0.6, ease: [0.16, 1, 0.3, 1] }}`. Stagger children with `delayChildren: 0.1, staggerChildren: 0.08`.
4. **Feature Sections:** Two-column layout: text left, product screenshot right. At `md` breakpoint, `grid grid-cols-2 gap-16 items-center`. Below `md`, stack vertically.
5. **Gradient Accents:** Subtle radial gradients behind key sections: `background: radial-gradient(ellipse at center, rgba(94,106,210,0.08) 0%, transparent 70%)`. Apply to a `::before` pseudo-element or dedicated div.
6. **Hover Effects:** Cards: `transition: transform 200ms ease, box-shadow 200ms ease` → `hover:transform: translateY(-2px); hover:box-shadow: 0 8px 24px rgba(0,0,0,0.4)`.
7. **Navigation:** Sticky top nav with `backdrop-blur-xl bg-black/60 border-b border-white/10`. Nav links: `text-sm text-white/70 hover:text-white transition-colors`.
8. **Performance:** Use Next.js dynamic imports for below-fold sections. Preload hero image. Total JS bundle target: <150KB gzipped.

**WHY IT WORKS:**
Linear's site applies the "product is the hero" principle — by showing real product UI instead of abstract illustrations, it builds immediate credibility with developer audiences who are skeptical of marketing fluff. The restrained animation (no 3D, no WebGL, no particle effects) signals engineering discipline and product focus. The dark palette with minimal color creates a premium perception while reducing visual noise, letting the product screenshots carry the visual weight.

**GOOD / BAD:**
- GOOD: The restraint is the statement — every animation, color choice, and layout decision serves the product. This is the highest-ROI design approach for dev tools.
- BAD: The "Linear dark mode" aesthetic is now widely copied, risking visual sameness across dev-tool landing pages.

**CLEAN APPLICATION:**
Apply this to Clean's **entire landing page structure and design system**. Clean is developer infrastructure and needs to project the same engineering credibility as Linear. Direct instruction: "Use Linear's layout pattern as the structural foundation for Clean's landing page: dark background (#0a0a0a), restrained Framer Motion scroll reveals (opacity + y-translate, 0.6s, custom ease), product screenshots as hero visuals, two-column feature sections, subtle radial gradient accents in brand color. Override Linear's purple with Clean's brand palette. The overall feel should be 'engineering-grade tool, not marketing fluff.'"

---

## 5. Codrops Scroll-Reactive 3D Gallery Technique — https://tympanus.net/codrops/2026/03/09/building-a-scroll-reactive-3d-gallery-with-three-js-velocity-and-mood-based-backgrounds/

**WHAT IT DOES:**
A Codrops tutorial from March 2026 demonstrating a scroll-reactive 3D gallery where Three.js planes displaying images react to scroll velocity — faster scrolling causes geometric distortion, tilt, and blur, while slow scrolling returns to equilibrium. The background color/mood shifts dynamically based on the currently centered item's color palette. This combines GSAP ScrollTrigger for scroll position/velocity tracking with Three.js custom shaders for the distortion effects. The layout is a vertical or horizontal scroll list of items rendered as Three.js planes synced to DOM scroll position.

**PACKAGES:**
- `three` (Three.js core)
- `@react-three/fiber` (or vanilla Three.js)
- `@react-three/drei` (Image, ScrollControls, useScroll)
- `gsap` + `ScrollTrigger` (velocity tracking, scrub animations)
- `glslify` or inline GLSL (custom vertex/fragment shaders for distortion)
- `lenis` (smooth scroll normalization)
- `postprocessing` or `@react-three/postprocessing` (bloom, blur on velocity)
- Font: `Space Mono` or `JetBrains Mono` for developer aesthetic

**IMPLEMENTATION:**
1. **Scroll-Velocity Tracking:** Use GSAP ScrollTrigger's `getVelocity()` method: `ScrollTrigger.create({ onUpdate: (self) => { velocity = self.getVelocity(); } })`. Normalize velocity to 0→1 range: `normalizedVelocity = Math.min(Math.abs(velocity) / 3000, 1)`.
2. **Three.js Plane Setup:** For each gallery item, create a `PlaneGeometry(1.6, 1, 32, 32)` (extra subdivisions for vertex displacement). Apply `ShaderMaterial` with custom vertex shader: `position.z += sin(uv.y * 3.14) * uVelocity * 0.3;` (bows the plane on fast scroll). Fragment shader applies a directional blur: `texture2D(uTexture, vUv + vec2(0.0, uVelocity * 0.01))`.
3. **Mood Background:** Extract dominant color from each image (pre-computed, stored as uniform). On scroll, lerp background color: `currentColor.lerp(targetColor, 0.03)` per frame. Apply to scene background or a fullscreen quad.
4. **DOM-WebGL Sync:** Match each Three.js plane position to its DOM counterpart: `plane.position.y = -(domElement.getBoundingClientRect().top - window.innerHeight / 2) / window.innerHeight * viewportHeight`.
5. **Spring Physics:** Instead of directly applying velocity, use a spring: `springVelocity += (targetVelocity - springVelocity) * 0.08` per frame. This creates smooth acceleration/deceleration of the distortion effect.
6. **Responsive:** At 768px, reduce plane subdivisions to 16x16. Disable blur shader (use simple texture). Reduce spring responsiveness factor to 0.05.
7. **Performance:** Use `InstancedMesh` if items exceed 20. Limit shader complexity on mobile (detect via `renderer.capabilities`). Use texture atlas for gallery images.

**WHY IT WORKS:**
The velocity-reactive distortion creates a visceral connection between user input and visual output — this is motion semantics at its best, where the animation *means* something (speed → energy → distortion). The mood-based background shifts use the Gestalt principle of common region — the background color groups the current item with the viewport, creating focus. The technique turns passive scrolling into an active, game-like interaction.

**GOOD / BAD:**
- GOOD: The velocity-to-distortion mapping is technically brilliant and creates genuine delight. The spring physics prevent jankiness.
- BAD: The technique is demo-ware — beautiful in isolation but hard to integrate into a real product page without feeling like a tech demo.

**CLEAN APPLICATION:**
Apply this to Clean's **use cases or integrations showcase section**. Instead of a gallery of images, render a scrollable list of MCP server integrations (Slack, GitHub, Postgres, etc.) as 3D cards that react to scroll velocity. Direct instruction: "Build a scroll-reactive integration showcase where each MCP server card is a Three.js plane with the server's icon and name. On fast scroll, cards tilt and blur slightly (vertex displacement shader). On slow scroll near a card, the background subtly shifts to that integration's brand color. Use spring physics for smooth transitions. This turns a boring integration list into an interactive, tactile experience."

---

## BUILD DIRECTIVE

**TARGET:** Clean's landing page hero + product demo section (tryclean.ai above-the-fold through first scroll section)

**APPROACH:** Combine Linear's structural discipline with Vast's cinematic scroll storytelling and a touch of the Codrops velocity-reactive technique. Build a dark-mode-first landing page hero that opens with a full-viewport headline + animated grid background (subtle, not flashy — think Linear's restraint with Vast's scale). Below the hero, implement a scroll-pinned product demo section inspired by iyO's canvas technique: a WebGL visualization of Clean's MCP proxy architecture (nodes + particle connections) that the user scrubs through on scroll, with HTML text overlays explaining each stage of the data flow. Use Framer Motion for all HTML animations (fade + y-translate reveals) and GSAP ScrollTrigger for the WebGL scroll-sync. The result should feel like "Linear meets a technical architecture diagram that comes alive."

**STACK:**
- `next` (Next.js 14+ App Router)
- `tailwindcss`
- `framer-motion` (HTML element animations)
- `@react-three/fiber` + `@react-three/drei` + `@react-three/postprocessing` (WebGL product demo)
- `gsap` + `ScrollTrigger` (scroll-pinned WebGL section, parallax)
- `three` (underlying renderer)
- `lenis` (smooth scroll)
- `lucide-react` (icons)
- `clsx` + `tailwind-merge`
- Font: `Inter` (body) + `Geist` or `Söhne` (headings)

**REFERENCE URLS:**
- https://linear.app — structural template, design system, animation restraint
- https://vastspace.com — cinematic scroll storytelling, full-bleed sections, SplitText reveals
- https://sanrita.ca — React Three Fiber + GSAP integration pattern, 3D node interaction

**KEY CONSTRAINTS:**
- Clean is developer infrastructure (MCP proxy layer for AI agents). The result must feel technically sophisticated, fast, and credible — not like a Framer template or a startup-in-a-box landing page.
- AVOID: excessive particle effects, gratuitous 3D for 3D's sake, loading spinners longer than 1s, any animation that doesn't communicate product value, bright/saturated color palettes, stock illustrations, generic SaaS section patterns ("trusted by" logo bars with no real logos).
- DO: use the product's own concepts (MCP servers, agent connections, proxy routing) as visual metaphors in the WebGL section. Show real terminal output or code snippets as product visuals. Keep total page weight under 3MB. Achieve Lighthouse performance score >85.
- Typography must be crisp and developer-friendly — no rounded/playful fonts. Letter-spacing should be tight on headings (tracking: -0.02em to -0.04em).
- The WebGL section should degrade gracefully: if WebGL is unavailable or on low-power devices, fall back to a static SVG diagram with CSS animations.
