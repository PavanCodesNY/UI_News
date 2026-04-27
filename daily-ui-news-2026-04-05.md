# Daily UI Intelligence Report — April 5, 2026

---

## 1. Vast (vastspace.com) — https://www.vastspace.com/

**WHAT IT DOES:**
Vast's site is a brand-forward corporate experience for a space station company. The layout system uses full-bleed hero sections with oversized typographic headlines set in a custom modernist sans-serif inspired by 1970s NASA visual identity (think Helvetica-adjacent, high x-height, wide tracking). The page is structured as a vertical scroll narrative: hero with ambient video/CGI of the Haven-1 station, then alternating full-width media sections with overlaid text blocks. Navigation is a minimal top bar with wordmark left, nav links right, and a subtle translucent backdrop-filter blur. The color system is dark navy (#0A0E1A range) with warm white (#F5F0EB) text and selective orange/amber accents for CTAs. Scroll-driven parallax on imagery creates depth between foreground text and background CGI assets.

**PACKAGES:**
- Next.js or Astro (SSR/SSG framework)
- GSAP + ScrollTrigger (scroll-driven parallax, text reveals)
- Framer Motion (UI micro-interactions, nav transitions)
- Tailwind CSS (utility styling)
- Three.js or embedded video (hero 3D/CGI sequences)
- Inter or custom sans-serif font (modernist, wide tracking)
- Lenis or Locomotive Scroll (smooth scroll normalization)

**IMPLEMENTATION:**
1. **Component tree**: `<Layout>` → `<Nav>` (fixed, `backdrop-filter: blur(12px)`, `background: rgba(10,14,26,0.7)`) → `<Hero>` (100vh, video/WebGL background, `<h1>` with `font-size: clamp(3rem, 8vw, 7rem)`, `letter-spacing: 0.04em`, `font-weight: 300`) → `<Section>` (repeating full-bleed blocks with `padding: 120px 0`) → `<Footer>`.
2. **Scroll parallax**: GSAP ScrollTrigger on hero media — `y: "-20%"` across scroll range `"top top", "bottom top"`. Text sections use `scrub: 1` with `opacity: 0 → 1`, `y: 40 → 0` reveals.
3. **Color tokens**: `--bg-deep: #0A0E1A`, `--text-primary: #F5F0EB`, `--accent: #E8732A`, `--surface: #141824`.
4. **Typography scale**: H1 `clamp(48px, 7vw, 96px)`, H2 `clamp(32px, 4vw, 56px)`, body `18px/1.6`, all with `letter-spacing: 0.02em`.
5. **Responsive**: Single column below 768px, two-column text+media above 1024px. Max-width container `1400px` with `padding: 0 clamp(24px, 4vw, 80px)`.
6. **Performance**: Lazy-load video assets, use `will-change: transform` on parallax layers, `IntersectionObserver` for section entry animations.

**WHY IT WORKS:**
The 1970s NASA modernist aesthetic creates instant technical credibility through historical association — engineering heritage signals engineering competence. The dark-dominant palette with warm white text produces a cinema-grade contrast ratio (~15:1) that makes every section feel like a title card. Generous whitespace (120px+ section padding) forces the eye to process one idea at a time, which is the visual equivalent of speaking slowly and clearly.

**GOOD / BAD:**
GOOD: One of the best examples of "serious technical company" web presence — the restraint in color and motion conveys confidence without trying hard.
BAD: The site is imagery-heavy with large video assets that may cause significant LCP issues on slower connections; the dark palette can feel cold without enough humanizing elements.

**CLEAN APPLICATION:**
Apply this to Clean's landing page hero and overall site tone. Clean is developer infrastructure for AI agents — it needs the same "serious engineering company" energy that Vast achieves. Direct instruction: Rebuild tryclean.ai's hero as a full-viewport dark section (#0B0D12 background) with a single oversized headline ("Unified context intelligence for agent teams") in a modernist sans-serif at `clamp(48px, 7vw, 88px)`, `font-weight: 300`, `letter-spacing: 0.03em`. Below the headline, a single-line subhead in `18px` muted text (#8B8F9A). One CTA button with amber/orange accent. No gradients, no glow effects, no particle backgrounds. Let the typography and spacing do the work. This signals "we're infrastructure, not a toy."

---

## 2. Artefakt (artefakt.mov) — https://artefakt.mov/

**WHAT IT DOES:**
Artefakt is a hybrid film production company portfolio. The site uses a stark black (#000000) and white (#FFFFFF) two-color palette as a canvas for cinematic video content. The layout is a full-screen project showcase with GSAP-powered page transitions — each project occupies the full viewport, and navigation between projects uses smooth crossfade or wipe transitions with WebGL shader effects. Typography is oversized editorial serif or display sans, positioned with intentional asymmetry. Video thumbnails scale and morph on hover using WebGL displacement maps. The navigation is minimal — a hamburger or project index that slides in as an overlay panel.

**PACKAGES:**
- WordPress (CMS backend)
- GSAP + ScrollTrigger + SplitText (text animations, page transitions)
- Three.js + custom GLSL shaders (WebGL displacement, video texture effects)
- Barba.js (SPA-like page transitions without framework)
- Lenis (smooth scroll)
- A display typeface (likely custom or something like Neue Montreal, Aeonik, or similar geometric sans)
- Vite (build tooling)

**IMPLEMENTATION:**
1. **Component tree**: `<App>` → `<TransitionManager>` (Barba.js container) → `<ProjectPage>` (full viewport, `100vh 100vw`, video background with Three.js canvas overlay) → `<ProjectInfo>` (absolutely positioned text block, `mix-blend-mode: difference` for contrast against video).
2. **Page transitions**: Barba.js `leave` hook triggers GSAP timeline: current page `opacity: 1 → 0` over `0.6s ease: "power2.inOut"`, simultaneously a WebGL displacement shader distorts the video texture (`uniform float uProgress` animated `0 → 1`), then `enter` hook fades new content in with staggered text reveal via SplitText (`chars` split, `y: 100%, opacity: 0 → y: 0, opacity: 1`, `stagger: 0.02`, `duration: 0.8`, `ease: "power3.out"`).
3. **Video hover effect**: Three.js plane geometry with video texture. On `mouseenter`, a displacement map uniform increases (`uDisplacement: 0 → 0.3` over `0.4s`), creating a ripple/warp. Shader: `vec2 displaced = uv + texture2D(uDisplacementMap, uv).rg * uDisplacement;`.
4. **Typography**: Display heading `font-size: clamp(64px, 10vw, 160px)`, `font-weight: 700`, `text-transform: uppercase`, `line-height: 0.9`. Body `16px/1.5`, `font-weight: 400`.
5. **Color**: Literally `#000` and `#FFF`. That's it. Content provides all color through video.

**WHY IT WORKS:**
The binary black/white palette is a power move — it says "our work speaks for itself" and forces video content to be the sole source of visual richness. The WebGL displacement on hover creates a tactile quality that makes the screen feel like a physical surface reacting to touch. The oversized typography at 10vw+ creates immediate spatial hierarchy without needing any color differentiation — scale alone communicates importance (Gestalt: figure-ground).

**GOOD / BAD:**
GOOD: The WebGL video displacement effect is genuinely best-in-class — it transforms a standard portfolio into a sensory experience.
BAD: The two-color constraint, while striking, can make text readability suffer when overlaid on mid-tone video frames; `mix-blend-mode: difference` is a blunt instrument.

**CLEAN APPLICATION:**
Apply the WebGL displacement hover effect to Clean's product demo section or case study cards. Direct instruction: On the tryclean.ai product section, create interactive cards for each integration (Cursor, Claude Code, Copilot). Each card has a dark (#111) background with a subtle code/terminal screenshot. On hover, apply a Three.js displacement shader that creates a liquid warp effect on the screenshot, revealing an "after" state showing Clean's context being injected. The transition should use `ease: "power2.out"`, `duration: 0.5s`. This makes the product benefit tangible and interactive rather than just described in text.

---

## 3. SŌM — https://www.awwwards.com/sites/som

**WHAT IT DOES:**
SŌM is an e-commerce site for a focus/energy drink brand. The layout uses a scroll-driven narrative with product-centric 3D/video sequences. The hero is a full-viewport product shot (likely 3D-rendered can/bottle) with scroll-triggered camera orbit — as you scroll, the product rotates and zooms. Sections alternate between product beauty shots and benefit copy blocks. The color system is built around orange (#FF3A1F) and white, creating a high-energy, high-contrast palette. GSAP drives scroll-linked animations including text reveals, product transforms, and parallax layers. The 3D product is rendered in WebGL (Three.js), and mouse interaction creates subtle parallax tilt on the product. Sound/audio is integrated for an immersive experience — likely ambient tones or product sound effects triggered by scroll position.

**PACKAGES:**
- Next.js or Nuxt (framework)
- Three.js / React Three Fiber (3D product rendering)
- GSAP + ScrollTrigger (scroll-driven animations)
- Howler.js or Tone.js (audio integration)
- Framer Motion (UI element transitions)
- Tailwind CSS
- Lenis (smooth scroll)
- GLB/GLTF models (product 3D assets)

**IMPLEMENTATION:**
1. **Hero section**: `<Canvas>` (R3F) containing a `<ProductModel>` component. Camera at `[0, 0, 5]`, product at origin. ScrollTrigger drives camera rotation: `gsap.to(camera.rotation, { y: Math.PI * 2, scrollTrigger: { trigger: ".hero", start: "top top", end: "+=300%", scrub: 1, pin: true }})`.
2. **Product lighting**: Three-point lighting — key light `<directionalLight intensity={1.2} position={[5, 5, 5]} />`, fill `<ambientLight intensity={0.3} />`, rim `<pointLight intensity={0.8} position={[-3, 2, -3]} color="#FF3A1F" />` (orange rim for brand color bleed).
3. **Text reveals**: SplitText on headings, words split, `gsap.from(".word", { y: "110%", rotateX: -45, opacity: 0, stagger: 0.05, duration: 0.8, ease: "power3.out", scrollTrigger: { trigger: ".section", start: "top 80%" }})`.
4. **Color tokens**: `--primary: #FF3A1F`, `--bg: #FFFFFF`, `--text: #0A0A0A`, `--surface: #FFF5F2` (light orange tint for alternating sections).
5. **Audio**: `Howler.js` sprite loaded on first interaction. Scroll position mapped to `howl.rate()` for pitch-shifting ambient sound. Volume tied to scroll velocity.
6. **Responsive**: 3D canvas scales down to `60vh` on mobile with simpler orbit (single-axis rotation only). Text sizes: H1 mobile `36px`, desktop `clamp(48px, 6vw, 72px)`.

**WHY IT WORKS:**
The scroll-driven 3D product orbit is the hero pattern of 2026 — it transforms passive scrolling into active exploration, giving the user a sense of agency over the product reveal. The orange (#FF3A1F) on white creates a contrast ratio that's borderline aggressive but perfectly aligned with "energy drink" brand semantics. The audio layer adds a dimension most sites ignore, creating memory through multi-sensory engagement.

**GOOD / BAD:**
GOOD: The scroll-to-3D-orbit mechanic is the single best way to showcase a physical product on web right now — it replaces static photography with interactive discovery.
BAD: Audio on scroll is divisive and can feel intrusive; the orange-heavy palette may cause visual fatigue on long sessions.

**CLEAN APPLICATION:**
Apply the scroll-driven 3D orbit mechanic to Clean's "How It Works" section. Direct instruction: Create a 3D visualization of Clean's architecture — a central glowing node (the context layer) with orbiting satellite nodes (Cursor, Claude Code, Copilot icons). As the user scrolls, the camera orbits the central node, and connection lines animate between satellites and center, showing context flowing. Each scroll checkpoint reveals a new benefit text block ("Shared context across agents", "4-5x token reduction", "Half the task time"). Use Three.js with a dark background (#0B0D12), cyan/teal accent (#00D4AA) for the connection lines, and white text. Pin the section for 300vh of scroll distance.

---

## 4. Artem Shcherban Portfolio — https://artemshcherban.com/portfolio

**WHAT IT DOES:**
A designer portfolio rebuilt from scratch as a systematic design exercise. The layout uses a modular grid system with project cards that have depth — parallax layers, subtle shadows, and hover-triggered micro-animations create a sense of physicality. The navigation is a sliding panel system: contact info and project details slide in from the side as overlay panels rather than navigating to new pages. Scroll behavior uses snap points for section transitions. The entire visual language prioritizes "clean and simple" with small animations, tooltips, icons, and micro-effects as integral system components, not decoration. Typography is carefully controlled with a clear hierarchy. The color palette is minimal — likely a near-white background with dark text and one accent color.

**PACKAGES:**
- Next.js or custom React (framework)
- GSAP + ScrollTrigger + ScrollSnap (scroll behavior)
- Framer Motion (panel transitions, hover micro-interactions)
- Tailwind CSS or custom CSS with design tokens
- Custom icon set
- A clean sans-serif (likely Inter, Satoshi, or General Sans)

**IMPLEMENTATION:**
1. **Grid system**: CSS Grid with `grid-template-columns: repeat(12, 1fr)`, `gap: 24px`, `max-width: 1440px`. Project cards span 4-6 columns, creating asymmetric layouts.
2. **Parallax depth on cards**: Each card has 2-3 layers. On `mousemove`, calculate `rotateX` and `rotateY` based on cursor position relative to card center: `transform: perspective(1000px) rotateX(${(y - centerY) * 0.02}deg) rotateY(${(x - centerX) * 0.02}deg)`. Inner image layer moves inversely for depth: `transform: translate(${(x - centerX) * -0.05}px, ${(y - centerY) * -0.05}px)`.
3. **Sliding panels**: `position: fixed`, `right: -100%`, transition: `right 0.4s cubic-bezier(0.16, 1, 0.3, 1)`. Active state: `right: 0`. Backdrop: `background: rgba(0,0,0,0.4)`, `backdrop-filter: blur(8px)`.
4. **Scroll snap**: `scroll-snap-type: y mandatory` on container, `scroll-snap-align: start` on sections. Combined with GSAP for smooth easing between snap points.
5. **Micro-interactions**: Buttons scale `1 → 1.02` on hover with `transition: 0.2s ease`. Links have underline animated with `scaleX(0) → scaleX(1)` from left, `transform-origin: left`, `transition: 0.3s cubic-bezier(0.16, 1, 0.3, 1)`. Tooltips fade in with `opacity: 0 → 1`, `y: 4 → 0`, `duration: 0.15s`.
6. **Typography**: Heading `font-size: clamp(32px, 4vw, 48px)`, `font-weight: 600`, `letter-spacing: -0.02em`. Body `16px/1.65`, `font-weight: 400`. Caption/meta `13px`, `font-weight: 500`, `text-transform: uppercase`, `letter-spacing: 0.06em`.

**WHY IT WORKS:**
The "system design" approach — where every micro-interaction follows the same easing curve, every tooltip uses the same timing, every panel transition shares the same cubic-bezier — creates unconscious coherence. The user never thinks "this feels polished" but never encounters friction either. This is Gestalt's Law of Similarity applied to motion: consistent animation language creates perceived unity. The sliding panels keep the user in spatial context rather than navigating away, which reduces cognitive load.

**GOOD / BAD:**
GOOD: The systematic approach to micro-interactions is a masterclass in design-engineering alignment — every animation has a rationale, not just an aesthetic.
BAD: Scroll snap can feel restrictive on content-heavy sections and fights user muscle memory on trackpad-heavy interactions.

**CLEAN APPLICATION:**
Apply the systematic micro-interaction framework and sliding panel pattern to Clean's documentation navigation and dashboard settings. Direct instruction: Build Clean's docs site (docs.tryclean.ai) with a sliding detail panel — clicking any API endpoint or configuration option slides in a panel from the right (`cubic-bezier(0.16, 1, 0.3, 1)`, `0.4s`) showing code examples, parameters, and live playground. Use the same easing curve (`cubic-bezier(0.16, 1, 0.3, 1)`) across ALL transitions site-wide: nav hover underlines, tooltip reveals, panel slides, button scales. Unify on `0.2s` for micro (hover), `0.4s` for macro (panels, pages). This creates the "everything just feels right" quality that signals engineering excellence.

---

## 5. Codrops 3D Transitions Pattern (Webflow + GSAP + Three.js + Barba.js) — https://tympanus.net/codrops/2026/03/18/building-seamless-3d-transitions-with-webflow-gsap-and-three-js/

**WHAT IT DOES:**
This is the canonical 2026 tutorial for seamless 3D page transitions. A persistent Three.js scene spans the entire site — it never unmounts or reloads. Barba.js handles SPA-like navigation while GSAP orchestrates the transition choreography. When navigating between pages, the 3D scene morphs (camera moves, objects transform) while 2D UI text fades and re-enters with SplitText stagger animations. The result feels like moving through a single continuous 3D space rather than clicking between pages. The visual style uses hand-drawn 3D models (rough geometry textured with hand-painted UV maps in Photoshop over Blender UV exports), giving a distinctive illustration-meets-3D aesthetic.

**PACKAGES:**
- Webflow (layout/CMS) OR Next.js/Astro (for code-first approach)
- Three.js (persistent 3D scene)
- GSAP + SplitText (text animation choreography)
- Barba.js (page transition management)
- Vite (bundler, single-script output injected into Webflow)
- Lenis (smooth scroll)
- Blender (3D modeling, UV export)

**IMPLEMENTATION:**
1. **Persistent scene**: Three.js `<canvas>` is mounted ONCE at the app root level, `position: fixed; top: 0; left: 0; width: 100vw; height: 100vh; z-index: 0; pointer-events: none;`. Page content sits above with `position: relative; z-index: 1;`.
2. **Barba.js setup**: `barba.init({ transitions: [{ leave({ current }) { return gsap.timeline().to(current.container, { opacity: 0, duration: 0.5, ease: "power2.in" }).to(scene.camera.position, { z: scene.camera.position.z + 2, duration: 0.8, ease: "power2.inOut" }, "<"); }, enter({ next }) { return gsap.timeline().from(next.container, { opacity: 0, duration: 0.5, ease: "power2.out" }).to(scene.camera.position, { z: targetZ, duration: 0.8, ease: "power2.out" }, "<"); }}]})`.
3. **Text choreography**: On page enter, query `[data-animation="title"]` elements, apply SplitText to split by `chars`. Animate: `gsap.from(splitText.chars, { y: "100%", rotateX: -90, opacity: 0, stagger: 0.015, duration: 0.6, ease: "power3.out", delay: 0.3 })`. For `[data-animation="text"]`, split by `lines`, stagger `0.08`.
4. **3D scene transitions**: Each page has a "scene config" object defining camera position, rotation, and which 3D objects are visible. On navigation, GSAP tweens between configs: `gsap.to(camera.position, { x: config.camX, y: config.camY, z: config.camZ, duration: 1.2, ease: "power2.inOut" })`.
5. **Data attribute pattern**: `data-animation="title"` on `<h1>`, `data-animation="text"` on `<p>`, `data-animation="spacer"` on dividers. JavaScript queries these generically — no component-specific code needed.
6. **Build**: Vite bundles all JS into single file, referenced in Webflow custom code header: `<script type="module" src="/dist/main.js"></script>`.

**WHY IT WORKS:**
The persistent 3D scene eliminates the most jarring moment in web navigation — the page reload/remount. By keeping the spatial context continuous, the brain processes navigation as movement rather than teleportation. This aligns with the "spatial computing" mental model users are developing from Apple Vision Pro and similar products. The data-attribute pattern for animations is brilliantly pragmatic — it decouples animation logic from DOM structure, making the system composable and CMS-friendly.

**GOOD / BAD:**
GOOD: The data-attribute animation system is the most portable, reusable animation architecture I've seen — it works with any CMS or framework and requires zero component coupling.
BAD: The persistent Three.js scene consumes GPU resources on every page, including text-heavy pages that don't benefit from 3D; needs careful performance budgeting.

**CLEAN APPLICATION:**
Apply the persistent 3D scene + data-attribute animation system to Clean's entire marketing site. Direct instruction: Rebuild tryclean.ai as a multi-page site with a persistent Three.js background showing an abstract network graph (nodes = agents, edges = context flows). As users navigate between pages (Home, Product, Docs, Pricing), the camera position shifts to different perspectives of the same network graph, and GSAP handles text entry via `[data-animation]` attributes. Use Barba.js for transitions. The network graph subtly animates (nodes pulse, edges flow with particle trails) at all times, reinforcing that Clean is "always on" infrastructure. Camera easing: `power2.inOut`, `duration: 1s`. Text entry: SplitText chars, `stagger: 0.015`, `ease: "power3.out"`.

---

## BUILD DIRECTIVE

**TARGET:** Clean landing page hero + "How It Works" section (tryclean.ai)

**APPROACH:**
Rebuild the tryclean.ai hero as a dark, typographically-driven section inspired by Vast's modernist restraint, combined with a scroll-pinned 3D architecture visualization inspired by SŌM's scroll-driven 3D orbit. The hero should be a full-viewport dark canvas (#0B0D12) with a single massive headline in a geometric sans-serif (Inter or Geist) at `clamp(48px, 7vw, 88px)`, `font-weight: 300`, `letter-spacing: 0.03em`. Below it, a muted one-liner and a single CTA. The background should have a subtle, persistent Three.js particle network (low-density, ~50 nodes connected by thin lines) that reacts to mouse position with gentle parallax. Below the fold, a pinned "How It Works" section with a 3D visualization of Clean's architecture: a central context node with orbiting agent icons. As the user scrolls through 300vh of pinned content, the camera orbits, connection lines animate, and text blocks reveal benefits at each checkpoint. Use the Codrops data-attribute pattern for all text animations (`[data-animation="title"]`, `[data-animation="text"]`) so the system is reusable across every page. Apply Artem Shcherban's unified easing curve (`cubic-bezier(0.16, 1, 0.3, 1)`) to ALL micro-interactions site-wide.

**STACK:**
- Next.js 15 (App Router, SSR)
- React Three Fiber + @react-three/drei (3D scene)
- GSAP + ScrollTrigger + SplitText (scroll animations, text choreography)
- Tailwind CSS v4 (utility styling)
- Framer Motion (UI micro-interactions)
- Lenis (smooth scroll)
- Geist or Inter font (geometric sans-serif)
- Vercel (deployment)

**REFERENCE URLS:**
1. https://www.vastspace.com/ — overall tone, dark palette, modernist typography, restrained motion
2. https://www.awwwards.com/sites/som — scroll-driven 3D product orbit mechanic, pinned scroll sections
3. https://tympanus.net/codrops/2026/03/18/building-seamless-3d-transitions-with-webflow-gsap-and-three-js/ — data-attribute animation system, persistent 3D scene architecture

**KEY CONSTRAINTS:**
- Clean is developer infrastructure. The site must feel technically sophisticated and fast, not flashy or marketing-heavy.
- NO gradient blobs, NO floating orbs, NO glassmorphism cards, NO "AI glow" clichés. These signal consumer product, not infrastructure.
- NO Lottie animations or Rive embeds — keep everything in code (Three.js + GSAP) for performance and credibility with developer audience.
- The 3D background MUST be subtle — low opacity, low density, never competing with text. If you can't read the headline instantly, the 3D is too much.
- Page weight budget: < 500KB initial JS, < 2s LCP on 4G. Lazy-load the 3D scene after hero text is visible.
- Typography does 80% of the work. If you removed all animation and 3D, the page should still look good as a static screenshot.
- Mobile: No 3D on mobile. Fall back to a static dark gradient. Don't compromise performance for effects nobody will appreciate on a phone.
