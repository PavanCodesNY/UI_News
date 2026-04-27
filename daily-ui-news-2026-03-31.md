# UI Research Brief — March 31, 2026

---

## 1. Fluid Glass — https://fluid.glass/

**WHAT IT DOES:**
Architectural glazing company site built on Nuxt.js + GSAP + Vercel. Uses a near-monochrome dark palette (#212325 primary, #0B1012 secondary) with horizontal scroll sections and unusual navigation patterns. The layout system is a full-viewport horizontal carousel for project showcases with GSAP-driven scroll-hijacking transitions between project cards. Portfolio-style image reveals use clip-path animations and opacity fades timed to scroll position. Minimal footer design with stacked typographic hierarchy.

**PACKAGES:**
- Framework: Nuxt.js 3 (Vue 3 SSR)
- Animation: GSAP (ScrollTrigger, ScrollSmoother)
- Hosting: Vercel
- Fonts: Google Fonts API (likely a geometric sans — Inter or similar given the minimal aesthetic)
- CSS: Scoped Vue components with CSS custom properties for the 2-color palette

**IMPLEMENTATION:**
1. **Layout:** Full-viewport sections (`100vw x 100vh`) arranged horizontally. Use GSAP ScrollTrigger with `horizontal: true` and `pin: true` to hijack vertical scroll and translate sections along the x-axis.
2. **Color system:** CSS custom properties: `--bg-primary: #0B1012; --bg-secondary: #212325; --text: #ffffff;` — only 2 background colors plus white text. High contrast ratio (~18:1).
3. **Project reveals:** Each project card uses `clip-path: inset(100% 0 0 0)` transitioning to `clip-path: inset(0)` on scroll enter, with `opacity: 0 → 1` and `y: 40px → 0` over 0.8s with `power2.out` easing.
4. **Typography:** Large display headings (clamp(3rem, 5vw, 6rem)) with tight letter-spacing (-0.02em). Body text at 1rem/1.6 line-height.
5. **Navigation:** Non-standard — likely a slide-out panel or overlay triggered by a hamburger icon, keeping the main viewport clean for immersive project imagery.
6. **Performance:** Images lazy-loaded with Nuxt Image module. GSAP animations use `will-change: transform` and GPU-composited properties only (transform, opacity).
7. **Responsive:** Breakpoints at 768px (switch from horizontal to vertical scroll) and 1024px. Mobile gets standard vertical scroll with stacked cards.

**WHY IT WORKS:**
The extreme chromatic restraint (2 colors) forces all visual hierarchy onto typography scale and spatial rhythm — a masterclass in contrast through size rather than color. The horizontal scroll creates a gallery-like browsing cadence that mirrors the physical experience of walking through architectural spaces. The near-black palette with high-contrast white text creates a premium, architectural gravitas that aligns perfectly with the brand.

**GOOD / BAD:**
- GOOD: The 2-color constraint produces an unmistakable brand identity; the horizontal scroll genuinely serves the content (wide architectural photography).
- BAD: Horizontal scroll can disorient users without clear affordances; accessibility concerns with non-standard navigation patterns.

**CLEAN APPLICATION:**
Apply this to Clean's **docs navigation** or **product architecture page** by using the horizontal scroll pattern to walk users through the MCP proxy pipeline stages (Request → Context Layer → TOON Transform → Agent Response). Use the same 2-color dark palette approach but with Clean's brand colors. Direct instruction: "Build a horizontal scroll explainer section on tryclean.ai that shows the 4-stage data flow, each stage as a full-viewport card with a code snippet and diagram, using GSAP ScrollTrigger horizontal pinning."

---

## 2. Utopia Tokyo — https://www.utopiatokyo.com/

**WHAT IT DOES:**
A cyberpunk-aesthetic interactive experience merging Japanese mask culture with dystopian surveillance UI. Built on Webflow with GSAP animations (grew from a Niccolo Miranda Awwwards Masterclass). The hero features a 3D-rendered Samurai mask floating against a red-dominant (#ff2c6b) background. Layout uses oversized glitchy monospaced typography with giant kanji characters as graphic anchors mid-scroll. Cross-hatch scan lines and corner-bracket UI elements create a surveillance-state HUD aesthetic. 14 mask collection pages with individual scroll-triggered reveals.

**PACKAGES:**
- Platform: Webflow (hosted)
- Animation: GSAP (ScrollTrigger, SplitText for character-level text animation)
- Typography: Monospaced display font (likely Space Mono or IBM Plex Mono), custom kanji rendering
- 3D: Pre-rendered 3D masks (Cinema4D/Blender exports as optimized WebP/PNG sequences or embedded video)
- Effects: CSS blend modes, SVG filters for scan-line overlays, CSS `mix-blend-mode: difference`

**IMPLEMENTATION:**
1. **Hero section:** Full-viewport with centered 3D mask asset. Background: solid #ff2c6b (hot pink/red). Mask floats with subtle parallax — `translateY` tied to scroll position via GSAP ScrollTrigger scrub.
2. **Typography system:** Display: monospaced at clamp(4rem, 8vw, 10rem). Body: monospaced at 0.875rem. Glitch effect achieved via CSS `text-shadow` with offset copies in cyan and magenta (#00ffff, #ff00ff) displaced by 2-3px, animated with `@keyframes glitch` at random intervals.
3. **Scan-line overlay:** Full-viewport `::after` pseudo-element with `background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.1) 2px, rgba(0,0,0,0.1) 4px)`. `pointer-events: none; position: fixed; z-index: 9999`.
4. **HUD bracket frames:** SVG corner brackets positioned absolutely at card corners, animated with `stroke-dashoffset` on scroll enter. `stroke-dasharray: 100; stroke-dashoffset: 100 → 0` over 0.6s.
5. **Color palette:** Primary: #ff2c6b (red-pink), Background: #0a0a0a (near-black), Accent: #ffffff, Glitch accents: #00ffff, #ff00ff.
6. **Scroll-driven mask reveals:** Each mask section uses GSAP timeline with `scrollTrigger: { trigger: section, start: "top center", end: "bottom center", scrub: 1 }`. Mask scales from 0.8 → 1.0, rotates Y 15° → 0°, opacity 0 → 1.
7. **Responsive:** Kanji characters scale down proportionally. Mobile loses the parallax float effect but keeps the glitch typography.

**WHY IT WORKS:**
The single accent color (#ff2c6b) against near-black creates extreme figure-ground separation — Gestalt law of figure/ground at maximum potency. The monospaced font + HUD elements trigger "technical interface" schema in the viewer's mind, giving the creative content a procedural, systematic feel. The scan-line texture adds physical materiality to a digital surface, bridging physical and digital perception.

**GOOD / BAD:**
- GOOD: The HUD/surveillance aesthetic is executed with restraint — the scan lines and bracket frames add atmosphere without overwhelming the actual mask imagery.
- BAD: Heavy reliance on pre-rendered 3D assets means no real-time interactivity with the masks; Webflow's GSAP integration can be brittle at scale.

**CLEAN APPLICATION:**
Apply the HUD bracket-frame pattern and monospaced typography system to Clean's **dashboard analytics view**. Direct instruction: "Create a real-time metrics dashboard for Clean that uses corner-bracket SVG frames around each metric card, monospaced typography for all numerical data, subtle scan-line overlay on the background, and a single accent color (#ff2c6b or Clean's brand equivalent) for highlighted metrics. Use GSAP to animate bracket frames drawing on scroll-enter and numbers counting up."

---

## 3. SUTÉRA — https://www.sutera.ch/

**WHAT IT DOES:**
Portfolio site for Stella Mühlhaus (Meta lead designer) showcasing work at the intersection of biology and technology — robotic extensions, perceptual interfaces. Built by Okey Studio on Nuxt.js with WebGL and GSAP. Features a "Reality By Design" concept with smooth transitions between project sections. Uses WebGL filters and effects for image treatments, gesture/interaction-driven navigation, and sophisticated microinteractions throughout. The visual language bridges organic (biological) and synthetic (technological) aesthetics.

**PACKAGES:**
- Framework: Nuxt.js 3
- Animation: GSAP (ScrollTrigger, SplitText)
- 3D/WebGL: Three.js or custom WebGL shaders for image distortion effects
- Styling: SCSS/CSS Modules with Nuxt
- Fonts: Likely a humanist sans-serif paired with a geometric display weight
- Hosting: Vercel or Netlify

**IMPLEMENTATION:**
1. **Loader/Reality Transition:** Custom WebGL shader transition on page load — a displacement map distortion that "materializes" the page content from noise into clarity. Fragment shader with `uniform float progress` controlling a noise displacement that lerps from 1.0 (fully distorted) to 0.0 (clear) over 1.5s with ease-in-out.
2. **Project grid:** CSS Grid with `grid-template-columns: repeat(auto-fill, minmax(400px, 1fr))` and `gap: 2rem`. Cards use `aspect-ratio: 4/3`.
3. **WebGL image effects:** On hover, project images get a displacement/ripple WebGL shader — likely using a DistortionPass with a noise texture. The effect `amplitude` goes from 0.0 → 0.05 over 0.4s on mouseenter, back to 0.0 on mouseleave.
4. **Microinteractions:** Cursor follows mouse with spring physics (`stiffness: 150, damping: 15`). Custom cursor grows 2x on interactive elements. Nav items have underline reveals using `scaleX(0) → scaleX(1)` with `transform-origin: left`.
5. **Typography:** Display at clamp(2.5rem, 4vw, 5rem) with generous letter-spacing (0.05em). Muted color palette — off-whites (#f5f0eb), warm grays (#8a8580), deep charcoals (#1a1918).
6. **Gesture navigation:** Supports swipe on touch devices for project navigation. Detects `touchstart`/`touchmove` delta and triggers GSAP timeline to slide between projects.
7. **Responsive breakpoints:** 480px, 768px, 1024px, 1440px. Grid collapses to single column below 768px.

**WHY IT WORKS:**
The WebGL shader transitions create a sense of physical materiality — content "forms" rather than just appearing, which directly embodies the brand's "Reality By Design" concept. The warm, muted color palette (off-whites and warm grays) deliberately avoids the cold blues of typical tech portfolios, signaling that this technology is human-centered. The microinteractions (custom cursor, hover distortions) create constant low-level feedback that makes the site feel alive.

**GOOD / BAD:**
- GOOD: The WebGL displacement shader on page load is memorable and brand-aligned; the warm palette is distinctive in a sea of cold-toned tech sites.
- BAD: WebGL effects may stutter on low-end devices; the biology/technology concept could be communicated more clearly in the content architecture.

**CLEAN APPLICATION:**
Apply the WebGL displacement transition and warm-muted palette to Clean's **onboarding flow**. Direct instruction: "Build Clean's onboarding wizard with a WebGL noise-to-clarity transition between each step — as the user advances, the next step 'materializes' from visual noise using a displacement shader. Use a warm neutral palette (#f5f0eb backgrounds, #1a1918 text) instead of cold blues. Add a custom cursor that grows on interactive elements and apply subtle displacement shaders on hover for clickable cards."

---

## 4. Waabi — https://waabi.ai/

**WHAT IT DOES:**
Autonomous driving AI company site built with React, Next.js, and Motion (Framer Motion). Uses a bold 2-color palette (#ff2c6b hot pink and #ffffff white) with storytelling-driven scroll experience. The site weaves narrative sections about Physical AI with animated transitions between content blocks. Features colorful typography-forward design with strong storytelling structure, microinteractions, and interaction design patterns that guide users through the company's vision for autonomous vehicles and their Waabi World simulator.

**PACKAGES:**
- Framework: Next.js (React)
- Animation: Motion (Framer Motion) for declarative animations
- Styling: Tailwind CSS or CSS Modules
- Typography: Custom sans-serif (likely Geist, Inter, or a proprietary face)
- Icons: Lucide React or custom SVG
- Hosting: Vercel

**IMPLEMENTATION:**
1. **Color system:** Extreme 2-color palette: `--primary: #ff2c6b; --bg: #ffffff;` with occasional dark sections inverting to `--bg: #0a0a0a; --text: #ffffff`. The pink serves as the sole accent for CTAs, highlights, and interactive elements.
2. **Storytelling scroll:** Content organized as a vertical narrative with full-viewport "chapter" sections. Each chapter animates in using Framer Motion `variants`: `{ hidden: { opacity: 0, y: 60 }, visible: { opacity: 1, y: 0, transition: { duration: 0.8, ease: [0.25, 0.4, 0.25, 1] } } }`.
3. **Typography hierarchy:** Display headings at clamp(3rem, 6vw, 7rem) with tight tracking (-0.03em). Subheadings at 1.5rem. Body at 1.125rem/1.7 line-height. Font-weight contrast: 800 for display, 400 for body.
4. **Home transitions:** Section-to-section transitions use `layout` animations from Framer Motion — shared layout elements (like a vehicle silhouette) morph between sections as the user scrolls.
5. **Interactive elements:** Hover states on cards use `whileHover={{ scale: 1.02, transition: { type: "spring", stiffness: 300, damping: 20 } }}`. CTAs pulse subtly with `animate={{ boxShadow: ["0 0 0 0 rgba(255,44,107,0.4)", "0 0 0 12px rgba(255,44,107,0)"] }}` on a 2s infinite loop.
6. **Microinteractions:** Scroll progress indicator, animated stat counters using `useMotionValue` and `useTransform`, text split animations on section enter.
7. **Performance:** Next.js SSR with dynamic imports for animation-heavy components. Images served via Next/Image with priority loading for above-fold content.

**WHY IT WORKS:**
The single-accent-color approach (#ff2c6b) creates an unmistakable brand identity in a sea of multi-color tech sites. The storytelling scroll structure (borrowed from longform journalism) keeps the reader engaged through narrative momentum rather than feature-listing. The section transitions with shared layout elements create visual continuity that reinforces the "one unified AI brain" messaging.

**GOOD / BAD:**
- GOOD: The storytelling scroll structure is genuinely engaging and makes complex AI technology accessible; the 2-color palette is bold and memorable.
- BAD: Heavy reliance on scroll-triggered reveals means users who jump/skip miss context; the pink accent may not read as "enterprise-ready" to some B2B buyers.

**CLEAN APPLICATION:**
Apply the storytelling scroll structure and shared-layout transitions to Clean's **landing page hero and product story section**. Direct instruction: "Rebuild Clean's landing page as a storytelling scroll: Hero → Problem (context fragmentation) → Solution (Clean's MCP proxy) → How It Works (animated diagram) → Social Proof → CTA. Use Framer Motion `layout` animations so that a central 'data flow' graphic morphs between sections. Use a single bold accent color against white/dark backgrounds. Each section is a full-viewport 'chapter' with `variants`-based entrance animations."

---

## 5. Framer Shaders + Chromatic Aberration — https://www.framer.com/updates/shaders

**WHAT IT DOES:**
Framer's March 24, 2026 Shaders update introduced native shader support directly in the Framer editor — drag-and-drop GPU-accelerated visual effects including animated gradients, image effects, and particle systems. The March 26 follow-up added a Chromatic Aberration shader with Radial, Swirl, Horizontal, and Vertical modes. These are production-ready, highly performant WebGL shaders exposed through a no-code interface but with customizable parameters. This represents a paradigm shift: GPU-computed visual effects that previously required custom Three.js/GLSL code are now accessible as drag-and-drop components.

**PACKAGES:**
- Platform: Framer (hosted, no-code)
- Under the hood: WebGL 2.0, custom GLSL fragment shaders
- Equivalent code stack for replication: React + Three.js (or `@react-three/fiber`) + `@react-three/drei` for shader materials, OR raw WebGL with custom fragment shaders
- For non-Framer implementation: `npm install three @react-three/fiber @react-three/drei` + custom ShaderMaterial
- Alternative: `gl-react` for 2D shader effects in React

**IMPLEMENTATION:**
1. **Chromatic Aberration shader (replicate in code):**
   ```glsl
   // Fragment shader
   uniform sampler2D tDiffuse;
   uniform float intensity; // 0.0 - 0.05
   uniform int mode; // 0=radial, 1=swirl, 2=horizontal, 3=vertical
   varying vec2 vUv;
   void main() {
     vec2 dir = vUv - 0.5; // radial mode
     float r = texture2D(tDiffuse, vUv + dir * intensity).r;
     float g = texture2D(tDiffuse, vUv).g;
     float b = texture2D(tDiffuse, vUv - dir * intensity).b;
     gl_FragColor = vec4(r, g, b, 1.0);
   }
   ```
2. **Integration in React:** Use `@react-three/postprocessing` with a custom ChromaticAberrationEffect, or build a `ShaderMaterial` in R3F with the above GLSL.
3. **Animated gradients:** CSS `@property` for animatable gradient stops, OR a WebGL mesh with a gradient fragment shader using `sin(time)` oscillation on color stops.
4. **Particle systems:** Use Three.js `Points` with `BufferGeometry` and custom vertex shader for GPU-driven particle positions. Typical config: 5000-10000 particles, size attenuation enabled, additive blending.
5. **Performance:** All effects run on GPU via fragment shaders — zero main-thread blocking. Target 60fps on mid-range hardware by keeping shader complexity under 50 instructions per fragment.
6. **Responsive:** Shader resolution scales with `devicePixelRatio` but caps at 2x to prevent GPU overload on high-DPI displays.

**WHY IT WORKS:**
Shader effects operate at the texture level, creating visual richness that CSS cannot replicate — real light physics (chromatic aberration = how actual camera lenses distort light), genuine noise patterns, and mathematically smooth gradients. This taps into the uncanny valley of "it feels real but I can't explain why" — the brain processes shader-generated gradients as more natural than CSS linear-gradient because the color interpolation happens in a perceptually uniform color space.

**GOOD / BAD:**
- GOOD: GPU-accelerated effects at zero main-thread cost; chromatic aberration adds cinematic quality that signals production-grade polish; Framer's drag-and-drop abstraction democratizes what was previously shader-engineer territory.
- BAD: Shader effects can be overused — chromatic aberration on everything becomes gimmicky fast; Framer lock-in means you can't easily export these effects to a custom codebase.

**CLEAN APPLICATION:**
Apply the chromatic aberration and animated gradient shaders to Clean's **landing page hero section**. Direct instruction: "Add a subtle chromatic aberration post-processing effect (intensity: 0.003, radial mode) to the hero section's background image or 3D element on tryclean.ai. Layer an animated gradient shader underneath using Clean's brand colors with slow oscillation (period: 8s). The chromatic aberration should intensify slightly on scroll (0.003 → 0.008 over first 500px of scroll) using ScrollTrigger to control the shader uniform. Implement with @react-three/postprocessing in a Next.js app."

---

## BUILD DIRECTIVE

**TARGET:** Clean landing page hero + product story section (tryclean.ai above-the-fold through first CTA)

**APPROACH:**
Rebuild Clean's landing page hero as a storytelling scroll (inspired by Waabi's narrative structure) with a dark, 2-color foundation (inspired by Fluid Glass's chromatic restraint). The hero opens with a full-viewport section featuring Clean's tagline over a WebGL animated gradient with subtle chromatic aberration post-processing (from Framer Shaders research). As the user scrolls, the page transitions through "chapters" — Problem (context fragmentation), Solution (Clean's MCP proxy), How It Works (animated pipeline diagram with HUD bracket frames from Utopia Tokyo's surveillance UI aesthetic applied to data-flow nodes), and Social Proof. Use Framer Motion `layout` animations so a central pipeline/data-flow graphic morphs between sections. Each chapter entrance uses opacity + y-translate variants with custom cubic-bezier easing. The overall palette is near-black (#0B1012) with a single bold accent color, warm neutral for text containers (inspired by SUTÉRA's #f5f0eb), and monospaced typography for code/technical elements.

**STACK:**
- `next` (Next.js 14+ with App Router)
- `react`, `react-dom`
- `framer-motion` (scroll animations, layout transitions, entrance variants)
- `three`, `@react-three/fiber`, `@react-three/drei`, `@react-three/postprocessing` (hero WebGL gradient + chromatic aberration)
- `gsap` + `@gsap/react` (ScrollTrigger for scroll-synced shader uniforms and SVG bracket-frame animations)
- `tailwindcss` (utility styling)
- `shadcn/ui` (base UI components — buttons, cards)
- `aceternity-ui` components: Spotlight, Background Beams, Bento Grid (for the features section)
- `lucide-react` (icons)
- `geist` font (monospaced for code, sans for body — Vercel's native font, aligns with dev-tool identity)

**REFERENCE URLS:**
- https://fluid.glass/ — dark 2-color palette, horizontal project showcase, GSAP scroll patterns
- https://waabi.ai/ — storytelling scroll structure, single-accent-color system, Framer Motion chapter transitions
- https://www.sutera.ch/ — WebGL displacement transitions, warm neutral palette, microinteraction patterns

**KEY CONSTRAINTS:**
- Clean is developer infrastructure (MCP proxy / context layer for AI agents). The result must feel like a tool built by engineers, not a marketing agency. Think Vercel, Linear, or Raycast — not Dribbble.
- NO gradients-for-gradients-sake. Every visual effect must communicate something: the chromatic aberration suggests data passing through a lens/proxy; the pipeline diagram with HUD brackets suggests system monitoring; the storytelling scroll explains a technical concept sequentially.
- Performance is non-negotiable: Lighthouse performance score ≥ 90, LCP < 2.5s, no layout shift. WebGL effects must be lazy-loaded below-fold or rendered on an offscreen canvas composited via CSS.
- Avoid: carousels, stock photography, generic SaaS blue gradients, "trusted by" logo bars without real logos, vague feature descriptions. Every word and visual must be specific to Clean's actual product.
- Typography must include monospaced elements (code snippets, terminal-style animations) to signal developer credibility. Use Geist Mono for code, Geist Sans for body.
- Mobile-first responsive design. WebGL effects gracefully degrade to CSS fallbacks (animated gradient via `@property`, no chromatic aberration) on mobile/low-power devices.
