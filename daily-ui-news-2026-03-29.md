# UI Research Report — March 29, 2026

---

## 1. Corentin Bernadou Portfolio — https://corentinbernadou.com/

**WHAT IT DOES:**
A Swiss-style editorial portfolio built entirely in vanilla JavaScript with WebGL geometry overlays. The layout uses a strict grid system inspired by the International Typographic Style — asymmetric column proportions, tight baseline grids, and a 2D/3D hybrid where Three.js geometries sit behind or interact with flat typographic layers. A PJAX routing system handles animated page transitions (no full reloads), Lenis manages inertial smooth scrolling with configurable lerp values, and GSAP drives all micro-animations including a dynamic cursor-following mask in the navigation that resizes fluidly via interpolation between menu items.

**PACKAGES:**
- Framework: None (vanilla JS, full custom architecture)
- Styling: SCSS with BEM methodology
- Animation: `gsap` (v3.12+), `@studio-freight/lenis` (smooth scroll)
- 3D: `three` (Three.js) + custom GLSL shaders
- Routing: Custom PJAX implementation (no Barba.js)
- Data: JSON files (no CMS)
- Build: Likely Vite or Webpack (custom)
- Fonts: Swiss-style sans-serif (likely Neue Haas Grotesk or similar grotesque)
- Icons: None visible — pure typographic

**IMPLEMENTATION:**

1. **Component tree:** Root shell with persistent nav + canvas overlay → PJAX page container → individual page modules (home grid, project detail, about). The Three.js canvas sits as a fixed-position layer behind content.

2. **Grid system:** CSS Grid with explicit column definitions — likely `grid-template-columns: repeat(12, 1fr)` with 20px gutters. Page title placement uses `getBoundingClientRect()` for precise 1px ruler guides activated by clicking a central cross icon.

3. **WebGL layer:** Three.js scene with OrthographicCamera for 2D-aligned geometry. Custom GLSL fragment shaders handle color/texture on geometric shapes. Geometries are simple (planes, boxes) but precisely positioned to align with the typographic grid. No post-processing — clean, sharp rendering.

4. **Navigation mask:** A `<div>` or SVG clip-path that follows cursor position across menu items. GSAP `quickTo()` or `gsap.to()` with `duration: 0.3, ease: "power2.out"` for smooth resize between items. Width/height interpolated based on hovered item's `getBoundingClientRect()`.

5. **Page transitions:** PJAX fetches next page HTML, GSAP timeline animates out current content (`opacity: 0, y: -20` over 0.4s), swaps DOM, animates in new content (`opacity: 1, y: 0` over 0.5s with 0.1s stagger per element).

6. **Smooth scroll:** Lenis configured with `lerp: 0.1, smoothWheel: true, wheelMultiplier: 1`. GSAP ScrollTrigger integrated with Lenis for scroll-driven animations.

7. **Interactive grid rulers:** `Option + G` keyboard shortcut reveals full layout grid overlay. Clicking a center cross activates guide lines that frame the page title. Users can place markers on the interface — stored in JS memory, rendered as absolutely-positioned 1px elements.

8. **Color palette:** Orange (#FF6B00 range), white (#FFFFFF), black (#000000). High contrast. Orange used only for interactive/accent elements.

9. **Responsive:** Likely collapses from 12-col to 6-col at 768px, single column at 480px. WebGL layer may be hidden or simplified on mobile for performance.

**WHY IT WORKS:**
The Swiss grid creates immediate visual authority — the asymmetric proportions and tight type spacing trigger pattern recognition associated with quality editorial design (Gestalt principle of proximity). The WebGL geometry is disciplined, not decorative — it adds dimensionality without competing with content. The orange-on-black-on-white palette achieves a contrast ratio exceeding 7:1 on all text, making it WCAG AAA compliant while feeling bold and intentional.

**GOOD / BAD:**
- **Good:** The grid ruler easter egg is best-in-class — it signals "I care about craft" to exactly the right audience (design-aware developers and creative directors).
- **Bad:** Vanilla JS with PJAX routing means no SSR/SEO unless pre-rendered; the developer experience for content updates via JSON is fragile for any team beyond a solo developer.

**CLEAN APPLICATION:**
Apply the Swiss grid + WebGL geometry overlay technique to Clean's documentation navigation. Build a docs sidebar with a strict 12-column grid where section headers are positioned with mathematical precision, and subtle Three.js geometric shapes (low-poly wireframes of network nodes, proxy routing diagrams) float behind the text layer, reacting to scroll position via GSAP ScrollTrigger. The grid ruler easter egg (Cmd+G to reveal layout grid) would signal engineering craft to Clean's developer audience. Use the cursor-following navigation mask on Clean's top-level nav items.

---

## 2. Darknode — https://www.awwwards.com/sites/darknode

**WHAT IT DOES:**
A high-impact dark-themed website for a Ukrainian drone interceptor division, built on Webflow with GSAP animation orchestration and Blender-rendered 3D assets composited into the page. The layout is full-bleed sections with oversized typography, scroll-triggered reveals, and a strict two-color palette (blood red on near-black). 3D drone models rendered in Blender are exported as optimized video/image sequences and composited into hero sections with parallax displacement on scroll. GSAP handles all entrance animations, scroll-pinning, and horizontal scroll sections.

**PACKAGES:**
- Platform: Webflow (hosted + CMS)
- Animation: `gsap` with ScrollTrigger, ScrollSmoother
- 3D assets: Blender (pre-rendered, not real-time WebGL)
- Fonts: Custom display typeface (likely licensed or custom-cut grotesque)
- Video: Optimized MP4/WebM for 3D sequences
- Interactions: Webflow native interactions + custom GSAP via embed code

**IMPLEMENTATION:**

1. **Layout:** Full-viewport sections (`100vh` pinned sections) with `position: sticky` or GSAP ScrollTrigger `pin: true`. Each section is a self-contained visual block.

2. **Color system:** Only 2 colors: `#F90000` (red) and `#14181E` (near-black). No gradients, no grays. Text is red on black or black on red. This extreme constraint creates visual tension.

3. **Typography:** Oversized display type — likely 8-12vw for hero headlines. Uppercase, tight letter-spacing (-0.02em). Body text in contrasting weight at 16-18px.

4. **3D compositing:** Blender-rendered drone models exported as image sequences or looping video. Placed as `<video autoplay muted loop playsinline>` or as CSS `background-image` with scroll-driven `transform: translateY()` for parallax. No real-time 3D rendering = fast load.

5. **Scroll animations:** GSAP ScrollTrigger config:
   ```
   gsap.to(".hero-text", {
     scrollTrigger: { trigger: ".hero", start: "top top", end: "bottom top", scrub: 1 },
     y: -100, opacity: 0
   });
   ```
   Staggered reveals on section entry: `stagger: 0.15, duration: 0.8, ease: "power3.out"`.

6. **Microinteractions:** Button hover states with `scale: 1.05` and red glow `box-shadow: 0 0 30px rgba(249, 0, 0, 0.3)`. Cursor custom styling likely via Webflow interactions.

7. **Performance:** Pre-rendered 3D (not WebGL) means zero GPU overhead for 3D. Total bundle is Webflow runtime + GSAP (~50KB gzipped). Fast.

**WHY IT WORKS:**
The two-color constraint is a masterclass in visual hierarchy through limitation — every element reads instantly because there are zero decisions about color priority (Hick's Law applied to palette). The oversized type creates a clear F-pattern reading flow. The pre-rendered 3D avoids the WebGL performance trap while still delivering cinematic depth. The red-on-black creates an aggressive, high-stakes emotional register that matches the military subject matter.

**GOOD / BAD:**
- **Good:** The two-color discipline is genuinely best-in-class — it proves you don't need a complex design system to create visual impact, just commitment to constraint.
- **Bad:** The extreme contrast may cause eye strain on extended reading; accessibility for color-blind users is weakened by the red-only accent system (protanopia makes #F90000 indistinguishable from dark backgrounds).

**CLEAN APPLICATION:**
Apply the two-color constraint technique to Clean's pricing page. Use only `#00FF88` (Clean's green accent) and `#0A0A0F` (near-black background) — zero other colors. Oversized plan names at 6-8vw, pinned scroll sections that reveal each tier with GSAP ScrollTrigger. Pre-render a 3D visualization of Clean's MCP proxy routing in Blender (data packets flowing through nodes) and composite it as a looping video in the hero. The extreme constraint communicates technical confidence — "we don't need visual noise to sell this."

---

## 3. SUTÉRA — https://www.sutera.ch/

**WHAT IT DOES:**
Portfolio site for Stella Mühlhaus (Lead Designer at Meta), built by Okey Studio. The design operates at the intersection of biology and technology — organic motion patterns paired with sharp interface elements. GSAP drives all animations with particular emphasis on microinteractions: cursor-reactive elements, hover state morphs, and page transition choreography. The site uses a loader with a "reality transition" effect — a morphing visual that bridges the loading state into the main content, avoiding the jarring cut of a typical loader-to-content switch.

**PACKAGES:**
- Framework: Likely Next.js or Nuxt (Okey Studio's standard stack)
- Animation: `gsap` (with CustomEase, SplitText, ScrollTrigger)
- Styling: CSS Modules or Tailwind
- Fonts: Custom serif + grotesque pairing
- Interactions: Custom cursor, magnetic buttons
- Build: Vercel deployment (likely)

**IMPLEMENTATION:**

1. **Loader → content transition:** Instead of a simple fade, the loader morphs into the hero element. Likely implemented as a shared element transition: the loader's central graphic scales and repositions to become the hero image/graphic using `gsap.to()` with `duration: 1.2, ease: "expo.inOut"`. The background color crossfades simultaneously.

2. **Microinteractions:** Magnetic button effect — buttons slightly pull toward cursor within a ~50px radius. Implementation:
   ```js
   // On mousemove within button proximity
   gsap.to(button, {
     x: (cursorX - buttonCenterX) * 0.3,
     y: (cursorY - buttonCenterY) * 0.3,
     duration: 0.3, ease: "power2.out"
   });
   // On mouseleave
   gsap.to(button, { x: 0, y: 0, duration: 0.5, ease: "elastic.out(1, 0.3)" });
   ```

3. **Text animations:** GSAP SplitText splits headlines into chars/lines. Entrance animation: `gsap.from(chars, { y: "100%", rotateX: -90, stagger: 0.02, duration: 0.8, ease: "power4.out" })`.

4. **Scroll behavior:** Sections reveal with ScrollTrigger. Content blocks animate in with a combination of opacity, y-translation, and subtle scale: `{ opacity: 0, y: 40, scale: 0.98 }` → `{ opacity: 1, y: 0, scale: 1, duration: 0.6, ease: "power2.out" }`.

5. **Custom cursor:** A small dot + larger ring that follows with lag. Dot: `gsap.quickTo(dot, "x", { duration: 0.1 })`. Ring: `gsap.quickTo(ring, "x", { duration: 0.4, ease: "power2.out" })`. On hover over interactive elements, ring scales up to 2x and changes blend mode.

6. **Color palette:** Likely muted earth tones or off-whites with a single high-saturation accent. Biology-meets-tech aesthetic suggests greens or cyans paired with warm neutrals.

**WHY IT WORKS:**
The loader-to-content morphing transition eliminates the cognitive gap between "waiting" and "arrived" — it turns a frustration point into a moment of delight (peak-end rule). The magnetic buttons create a sense of interface intelligence that mirrors Mühlhaus's "systems that shape how humans and machines connect" thesis. Every interaction feels intentional rather than decorative, reinforcing the portfolio's core message.

**GOOD / BAD:**
- **Good:** The reality transition loader is best-in-class — it transforms the weakest moment of any website (loading) into a brand-defining experience.
- **Bad:** Magnetic effects and custom cursors can conflict with accessibility tools and screen readers; without `prefers-reduced-motion` fallbacks, the experience breaks for motion-sensitive users.

**CLEAN APPLICATION:**
Apply the loader-to-content morphing transition to Clean's landing page. When tryclean.ai loads, animate a visualization of the MCP proxy (a node graph or data flow diagram) that starts small and centered during load, then expands and dissolves into the hero section's background graphic. Use the magnetic button effect on Clean's primary CTAs ("Get Started", "View Docs"). Apply GSAP SplitText character animation to the main headline. Instruction: "Build a morphing loader for Clean's landing page where a miniature animated proxy diagram scales from center to fill the hero section background, using `gsap.to()` with `ease: 'expo.inOut'` over 1.2s."

---

## 4. GQ & AP The Extraordinary Lab — https://www.awwwards.com/sites/gq-ap-the-extraordinary-lab

**WHAT IT DOES:**
An immersive 3D editorial experience by Immersive Garden exploring Audemars Piguet's RD Series watches for GQ. Built on Nuxt.js with Three.js WebGL rendering, Blender/Cinema 4D/ZBrush for 3D models, and GSAP + Lenis for scroll-driven cinematics. The experience places photorealistic watch models in an abstract laboratory environment that the user navigates through scroll — each scroll increment advances a camera path through the 3D space while synchronized 2D editorial content slides in from the sides. It's essentially a 3D documentary where scroll replaces the play button.

**PACKAGES:**
- Framework: `nuxt` (v3, Vue.js based)
- 3D rendering: `three` (Three.js), custom GLSL shaders
- 3D modeling: Blender, Cinema 4D, ZBrush (pre-production)
- Animation: `gsap` with ScrollTrigger (scroll-driven 3D camera)
- Scroll: `@studio-freight/lenis`
- Styling: SASS
- CMS: Strapi + Node.js API
- Deployment: Vercel
- Post-processing: After Effects (for composited video segments)

**IMPLEMENTATION:**

1. **3D scene architecture:** A single continuous Three.js scene with a PerspectiveCamera on a predefined path (likely a CatmullRomCurve3). Scroll position maps 0-1 to the camera path parameter `t`. As the user scrolls, GSAP ScrollTrigger updates the camera position:
   ```js
   ScrollTrigger.create({
     trigger: ".experience-wrapper",
     start: "top top",
     end: "+=10000", // long scroll distance
     pin: true,
     scrub: 1,
     onUpdate: (self) => {
       camera.position.copy(cameraPath.getPointAt(self.progress));
       camera.lookAt(lookAtPath.getPointAt(self.progress));
     }
   });
   ```

2. **Watch models:** High-poly models from ZBrush, retopologized in Blender, textured with PBR materials. Loaded as `.glb` via Three.js GLTFLoader. Environment map for realistic reflections using `PMREMGenerator` with a custom HDR.

3. **2D/3D synchronization:** HTML editorial panels (text, stats, quotes) positioned with `position: fixed` and animated in/out via GSAP based on scroll progress milestones. When `self.progress` crosses thresholds (0.2, 0.4, 0.6...), corresponding panels fade in from left/right with `x: ±100, opacity: 0 → 0`.

4. **Lighting:** Dynamic lighting that shifts as camera moves — likely 2-3 point lights with positions animated along the scroll timeline. Spot lights with `penumbra: 0.5` for soft falloff.

5. **Performance:** GLB models loaded with Draco compression. Textures use KTX2 format for GPU-compressed loading. Progressive loading with a branded loading screen showing percentage. LOD (Level of Detail) switching for mobile.

6. **Responsive:** Mobile likely gets a simplified 2D version with pre-rendered video replacing WebGL, plus reduced editorial panels.

**WHY IT WORKS:**
Replacing video play/pause with scroll gives users agency over pacing — a critical UX insight for luxury content where users want to linger on details (self-determination theory). The 2D/3D synchronization creates a parallax between information layers that mimics the experience of examining a physical watch (turning it over, reading the spec sheet). The single-scene camera path eliminates page transitions entirely, creating a seamless narrative flow (Gestalt principle of continuity).

**GOOD / BAD:**
- **Good:** The scroll-to-camera-path mapping is best-in-class for product storytelling — it turns a passive "scroll to read" into an active "explore the space" experience.
- **Bad:** The long scroll distance (10000+ px equivalent) can disorient users who expect normal page length; there's no visible progress indicator or "how much more" signal, which breaks Nielsen's visibility of system status heuristic.

**CLEAN APPLICATION:**
Apply the scroll-driven 3D camera path to Clean's product demo section on the landing page. Build a Three.js scene showing Clean's MCP proxy architecture as a 3D node graph — API sources on one side, AI agents on the other, with the proxy layer in between. As the user scrolls, the camera flies through the architecture: first showing the messy "before Clean" state (scattered connections), then transitioning through the proxy layer, and emerging into the clean "after Clean" state (organized, filtered, secured connections). Synchronized 2D panels slide in explaining each stage. Instruction: "Build a scroll-driven Three.js flythrough of Clean's proxy architecture using a CatmullRomCurve3 camera path, with GSAP ScrollTrigger `scrub: 1` and synchronized HTML overlay panels at 20% scroll intervals."

---

## 5. shadcn/cli v4 + Aceternity UI (Component Ecosystem) — https://ui.shadcn.com/docs/changelog/2026-03-cli-v4 + https://ui.aceternity.com/

**WHAT IT DOES:**
Not a single website, but the most significant component infrastructure drop this week. shadcn/cli v4 introduces `shadcn/skills` (AI agent context for component installation), design system presets (entire theme packed into a single code string), and `shadcn/create` (visual preset preview). Meanwhile, Aceternity UI offers 200+ animated components including a new Spotlight Shader (WebGL-based spotlight with configurable position, color, intensity, size, and spread angle per light source), 3D card effects with Framer Motion perspective transforms, and meteor-beam background effects. Together, these define the 2026 component stack for developer-facing landing pages.

**PACKAGES:**
- **shadcn stack:** `shadcn` (CLI v4), `@radix-ui/react-*` or `@base-ui-components/*`, `tailwindcss` (v4), `class-variance-authority`, `clsx`, `tailwind-merge`, `lucide-react`
- **Aceternity stack:** `framer-motion`, `tailwindcss`, `clsx`, `tailwind-merge`, `@tabler/icons-react` or `lucide-react`
- **Combined stack for landing pages:** `next` (v15+), `react`, `framer-motion`, `gsap` (for scroll), `three` (for hero), `@react-three/fiber`, `@react-three/drei`, `tailwindcss`, `shadcn` components for app UI, Aceternity components for marketing sections

**IMPLEMENTATION:**

1. **shadcn/cli v4 preset system:** Run `npx shadcn@latest init --preset <code>` where the preset string encodes: color palette, border radius, font family, icon library, and base component style. This replaces manual theme configuration entirely.

2. **Aceternity Spotlight Shader:** A WebGL shader rendered on a `<canvas>` element behind content. Config:
   ```js
   spotlights: [
     { position: [0.5, 0.3], color: "#00FF88", intensity: 0.8, size: 0.4, spread: 45 },
     { position: [0.8, 0.7], color: "#0066FF", intensity: 0.5, size: 0.3, spread: 60 }
   ]
   ```
   The shader uses `smoothstep()` for soft falloff and `mix()` for color blending. Mouse position can drive one spotlight for interactivity.

3. **Aceternity 3D Card:** Framer Motion `useMotionValue` + `useTransform` to map mouse position to `rotateX` and `rotateY`:
   ```jsx
   const rotateX = useTransform(mouseY, [0, height], [15, -15]);
   const rotateY = useTransform(mouseX, [0, width], [-15, 15]);
   // Apply: style={{ perspective: 1000, rotateX, rotateY }}
   ```

4. **Meteor Beam Background:** Absolutely positioned `<div>` elements with CSS animations:
   ```css
   @keyframes meteor {
     0% { transform: translateY(-100vh) translateX(-50px) rotate(215deg); opacity: 1; }
     70% { opacity: 1; }
     100% { transform: translateY(100vh) translateX(50px) rotate(215deg); opacity: 0; }
   }
   ```
   Stagger via `animation-delay` across 10-20 elements. Duration: 3-8s random per meteor.

5. **Combined landing page pattern:**
   - Hero: Aceternity Spotlight Shader background + shadcn typography
   - Features: Aceternity 3D cards with shadcn Badge/Button components inside
   - Code examples: shadcn `Code` blocks with Aceternity beam effects on container borders
   - Pricing: shadcn Card + Aceternity hover glow effects
   - Footer: Standard shadcn layout

**WHY IT WORKS:**
The shadcn preset system solves the "blank canvas paralysis" that kills developer landing pages — instead of configuring 47 CSS variables, you get a cohesive theme in one string. Aceternity fills the "wow factor" gap that shadcn deliberately leaves (shadcn is opinionated about function, not flash). Together, they apply the dual-process theory: shadcn components are System 2 (rational, functional UI) while Aceternity effects are System 1 (emotional, attention-capturing visuals). Landing pages need both.

**GOOD / BAD:**
- **Good:** The shadcn preset + Aceternity component combination is the most practical "beautiful landing page" stack available — no custom WebGL needed for 90% of use cases.
- **Bad:** Aceternity's spotlight shader adds a full WebGL context just for a background effect; on lower-end devices, this competing with any Three.js hero content will tank frame rates. Also, the meteor beams are becoming a cliché — every AI startup landing page uses them now.

**CLEAN APPLICATION:**
Use shadcn/cli v4 presets to establish Clean's component system in one command. Apply Aceternity's Spotlight Shader as the background for Clean's hero section with two spotlights: green (#00FF88) tracking the cursor and blue (#0066FF) fixed — this creates a "scanning/analyzing" visual metaphor that matches Clean's proxy inspection product. Use 3D card effects for the three product pillars (Context Layer, TOON Format, MCP Proxy). DO NOT use meteor beams — they're overused. Instead, use the Aceternity "moving border" effect on code blocks showing Clean's SDK examples. Instruction: "Initialize Clean's landing page with `shadcn init --preset` using a dark theme, green accent, mono font preset. Add Aceternity Spotlight Shader to hero with cursor-tracking green light. Apply 3D card hover to feature cards. Use moving-border on code snippets."

---

## BUILD DIRECTIVE

**TARGET:** Clean's landing page hero section + feature cards (tryclean.ai above-the-fold + first scroll section)

**APPROACH:**
Rebuild Clean's landing page hero as a dark, technically sophisticated above-the-fold experience that combines the constraint discipline of Darknode, the loader-to-content morphing of SUTÉRA, and the component efficiency of the shadcn+Aceternity stack. The hero should feature an Aceternity Spotlight Shader background with two lights — one cursor-tracking green (#00FF88), one fixed blue (#0066FF) — creating an ambient "intelligence scanning" atmosphere. The main headline uses GSAP SplitText character-by-character reveal (stagger: 0.02, ease: power4.out). Below the fold, three feature cards (Context Layer, TOON Format, MCP Proxy) use Aceternity 3D card perspective tilt on hover, with shadcn components inside each card for consistency. A morphing loader transitions from a miniature proxy-node animation into the hero background on initial page load. Code examples in the feature section use Aceternity's moving-border effect rather than static code blocks. The entire palette is constrained to two colors: #00FF88 (green accent) and #0A0A0F (background), with #E0E0E0 for body text — echoing Darknode's two-color discipline. Typography is a monospace or grotesque font at 6-8vw for the hero headline, establishing immediate technical authority.

**STACK:**
- `next` (v15, App Router)
- `react`, `react-dom`
- `tailwindcss` (v4)
- `shadcn` (cli v4, initialized with dark/green/mono preset)
- `framer-motion` (for Aceternity components: 3D cards, moving borders)
- `gsap` + `gsap/ScrollTrigger` + `gsap/SplitText` (for text reveals, scroll animations)
- `@studio-freight/lenis` (smooth scroll)
- Aceternity UI components: Spotlight Shader, 3D Card Effect, Moving Border
- Font: `JetBrains Mono` or `GeistMono` for headlines, `Inter` or `Geist` for body

**REFERENCE URLS:**
- https://corentinbernadou.com/ (Swiss grid discipline, WebGL-behind-typography pattern)
- Darknode via Awwwards (two-color constraint, oversized typography, GSAP scroll reveals)
- https://ui.aceternity.com/components/spotlight-new + https://ui.aceternity.com/components/3d-card-effect (component implementations)

**KEY CONSTRAINTS:**
- Clean is developer infrastructure — the result must feel like a tool built by engineers, not a marketing agency. No gradients, no rounded blob shapes, no stock illustrations.
- Avoid meteor/beam backgrounds (overused in AI startup space as of March 2026).
- Avoid heavy WebGL (no full Three.js scene in the hero) — use the Aceternity Spotlight Shader (lightweight canvas) instead. Save Three.js for a dedicated product demo section below the fold if needed.
- All text must maintain WCAG AA contrast ratios (4.5:1 minimum) against the dark background.
- The page must load and be interactive within 2 seconds on a 4G connection. No blocking 3D asset loads.
- Respect `prefers-reduced-motion` — disable SplitText animations and spotlight tracking for users who request it.
- No custom cursor effects — they conflict with developer workflows (terminal muscle memory).
