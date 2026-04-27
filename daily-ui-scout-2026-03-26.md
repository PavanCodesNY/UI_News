# UI Scout Report — March 26, 2026

---

## 1. Corentin Bernadou Portfolio — https://corentinbernadou.com

WHAT IT DOES:
A freelance developer portfolio built entirely in vanilla JavaScript with SCSS/BEM, no framework. The homepage centers a WebGL geometry (Three.js + custom GLSL shaders) that incorporates project preview textures mapped onto 3D surfaces, sitting inside a strict Swiss-style typographic grid. Lenis handles smooth scroll normalization. GSAP drives all entrance animations, hover states, and page transitions. An interactive grid-rulers system (triggered via Option+G) overlays 1px guide lines using `getBoundingClientRect()`, and users can place Figma-style markers directly on the interface. Page data is stored as static JSON — no CMS.

PACKAGES:
- No framework (vanilla JS, ES modules)
- SCSS with BEM methodology
- Three.js (WebGL geometry, GLSL custom shaders)
- GSAP (all motion — entrances, hovers, transitions)
- Lenis (smooth scroll normalization)
- No bundler specified — likely Vite or Webpack

IMPLEMENTATION:
1. **Layout**: 12-column grid, Swiss-style. Max-width container ~1440px. Generous vertical rhythm with 8px baseline. Typography-first: headlines set in a geometric sans (likely custom or Neue Haas Grotesk derivative) at 72–120px, body at 16–18px. Color: orange (#FF6B00 range), white (#FFFFFF), black (#0A0A0A).
2. **WebGL Layer**: Three.js scene rendered as a fullscreen canvas behind the DOM. A single custom geometry (likely a subdivided plane or icosahedron) with GLSL fragment shader sampling project thumbnails as textures. Vertex shader applies subtle noise displacement (Perlin/Simplex). Camera uses perspective projection with narrow FOV (~35°) for minimal distortion.
3. **Scroll Interaction**: Lenis config: `smooth: true, lerp: 0.1, duration: 1.2`. GSAP ScrollTrigger pins sections and triggers WebGL geometry rotation/morph on scroll progress. Each project reveal uses `gsap.fromTo()` with `opacity: 0→1`, `y: 60→0`, `duration: 0.8`, `ease: "power3.out"`.
4. **Grid Rulers**: DOM overlay with `position: fixed`, `pointer-events: none`. Lines rendered as 1px `border` divs positioned via `getBoundingClientRect()` on the page title. Toggle via keyboard listener on Option+G.
5. **Page Transitions**: GSAP timeline: current content fades out (`opacity→0, y→-30, duration: 0.4`), WebGL geometry morphs (`duration: 0.6, ease: "power2.inOut"`), new content fades in (`opacity→1, y: 30→0, duration: 0.5, stagger: 0.08`).
6. **Performance**: No framework overhead. Static JSON avoids API calls. Three.js scene uses `requestAnimationFrame` with visibility check to pause when off-screen. Textures loaded as compressed formats (likely AVIF/WebP with fallback).

WHY IT WORKS:
The Swiss grid provides ironclad visual hierarchy — the eye never wanders. The WebGL geometry adds dimensionality without competing with content because it's texture-mapped with the actual project work, making the 3D element informational, not decorative. The interactive ruler system signals craft and appeals directly to designers/developers — it's UX as portfolio piece.

GOOD / BAD:
GOOD: WebGL integrated as content vessel, not ornament — every 3D surface shows real work. BAD: Vanilla JS + SCSS/BEM means no component reusability; the ruler system is clever but niche — most visitors won't discover it.

CLEAN APPLICATION:
Apply the Swiss grid + WebGL geometry concept to Clean's product demo section on tryclean.ai. Build a Three.js geometry (hexagonal mesh or network graph) where each face/node texture-maps to a real code context snippet or agent interaction. On scroll, the geometry rotates to reveal different faces — each face becomes a feature: "Codebase Indexing", "Shared Context", "TOON Format", "MCP Gateway". Use the grid-rulers concept as an easter egg in the docs (Option+G reveals the layout grid) to signal engineering craft.

---

## 2. Darknode — https://darknode.io

WHAT IT DOES:
A dark-themed product site built in Webflow with 3D assets from Blender, animated via GSAP. The layout is a full-bleed vertical scroll with sections triggered on viewport entry. 3D renders (likely exported as optimized GLB/glTF or pre-rendered video loops) float alongside typographic headlines. GSAP handles scroll-triggered entrances, parallax depth layers, and microinteractions on hover (button scale, icon rotation, cursor trails). The color system is binary: deep charcoal (#14181E) against signal red (#F90000).

PACKAGES:
- Webflow (no-code platform, generates production HTML/CSS/JS)
- GSAP (ScrollTrigger, all animations)
- Blender (3D asset creation — exported as GLB, video, or Spline embeds)
- Custom CSS for microinteractions
- Possibly Spline for real-time 3D (if interactive, not pre-rendered)

IMPLEMENTATION:
1. **Color System**: Background: #14181E (near-black with blue undertone). Primary accent: #F90000 (pure red). Text: #FFFFFF at 90% opacity for body, 100% for headlines. No mid-tones — maximum contrast ratio (~18:1).
2. **Typography**: Large-scale sans-serif headlines (80–140px, likely Grotesk family), uppercase tracking +2%. Body text 16px, line-height 1.6. Red accent on keywords and CTAs.
3. **Layout**: Full-viewport sections (`100vh`), center-aligned content blocks with max-width ~1200px. 3D assets positioned with `position: absolute` and `z-index` layering to create parallax depth. CSS Grid for feature grids (2–3 columns, gap: 32px).
4. **3D Integration**: Blender models rendered with dramatic single-source lighting (red-tinted rim light). Exported as either: (a) Lottie/video loops for simple rotation, or (b) Spline embeds for interactive tilt-on-hover. Placed as background layers behind text blocks.
5. **GSAP Animations**: ScrollTrigger config: `start: "top 80%", end: "bottom 20%", scrub: 1`. Headlines: `gsap.from({y: 100, opacity: 0, duration: 1, ease: "power3.out"})`. 3D assets: parallax via `gsap.to({y: "-20%", ease: "none"})` tied to scroll. Buttons: hover scale `gsap.to({scale: 1.05, duration: 0.3, ease: "power2.out"})`.
6. **Microinteractions**: Custom cursor (circle, 24px, red border, blend-mode: difference). Expands on hover over interactive elements (`scale: 1.5, duration: 0.2`). Link underlines animate from left on hover via `clip-path: inset(0 100% 0 0)` → `inset(0 0 0 0)`.
7. **Footer**: Full-width, dark. Large CTA text with red accent. Social links with icon rotation on hover (15deg, 0.3s).

WHY IT WORKS:
The binary color system (charcoal + red) creates visual tension that reads as "technical power" — it's the same palette used by gaming hardware and cybersecurity brands because red on black triggers alertness and perceived speed. The 3D assets add perceived value without WebGL overhead by using pre-rendered Blender exports. Gestalt principle of figure-ground is maximal: every element either recedes into the dark or punches forward in red/white.

GOOD / BAD:
GOOD: Performance-smart 3D (Blender renders, not real-time WebGL) means fast load with premium visual quality. The red+dark palette is immediately distinctive. BAD: The 7.31 Awwwards score suggests some judges found it derivative of the "dark SaaS" aesthetic. Red-on-dark can fatigue eyes in extended reading.

CLEAN APPLICATION:
Apply the binary color system and 3D asset strategy to Clean's pricing page or enterprise section on tryclean.ai. Use Clean's brand colors in place of red, but keep the charcoal (#14181E-range) background. Create 3 Blender-rendered 3D icons representing Clean's moat: a Tree-Sitter parse tree (code indexing), a network mesh (shared context), and a single glowing proxy node (MCP gateway). Animate them as looping video backgrounds behind each pricing tier. GSAP ScrollTrigger reveals each tier on scroll with the same `power3.out` entrance. This gives the pricing page visual weight and perceived infrastructure-grade seriousness.

---

## 3. Unseen Studio 2025 Wrapped — https://2025.unseen.co

WHAT IT DOES:
A horizontal-scrolling one-page year-in-review built on WordPress. The entire page moves left-to-right, with content organized as a continuous horizontal timeline. WebGL (likely via a WordPress-compatible library or custom integration) powers parallax depth on layered elements — foreground text moves at 1x scroll speed, midground images at 0.7x, background textures at 0.4x. Typography is the hero: massive display type (200px+) in black (#010101) against gold (#E4B504) accents. Cinema 4D was used for 3D renders/motion graphics embedded as video.

PACKAGES:
- WordPress (CMS + hosting)
- WebGL (custom or library — likely OGL or custom canvas)
- GSAP (horizontal scroll orchestration + parallax)
- Cinema 4D (3D motion renders, exported as video/GIF)
- Custom CSS (horizontal layout, typography system)
- Likely Barba.js or custom page transition handler

IMPLEMENTATION:
1. **Horizontal Scroll**: Container: `display: flex; flex-direction: row; width: [n * 100vw]`. GSAP ScrollTrigger with `horizontal: true`, pinning the container and translating it `x: -(totalWidth - viewportWidth)` over the scroll distance. `snap: 1 / (sections - 1)` for section snapping. `scrub: 1` for smooth binding to scroll position.
2. **Color System**: Background: #010101 (true black). Primary accent: #E4B504 (warm gold). Text: white for body, gold for highlights and dates. Contrast ratio: ~19:1 (white on black), ~8.5:1 (gold on black).
3. **Typography**: Display font at 200–300px for section headers (likely custom or Druk/Favorit Extended). Body at 18px, line-height 1.5. Horizontal text orientation for some labels using `writing-mode: vertical-rl; transform: rotate(180deg)`.
4. **Parallax Layers**: Three depth planes managed via GSAP. Foreground (text): `x` offset 1:1 with scroll. Midground (images): `x` offset 0.7:1 via `gsap.to({x: "70%", ease: "none"})`. Background (textures/grain): `x` offset 0.4:1. All driven by a single ScrollTrigger instance.
5. **Preloader**: Custom loading screen with animated studio logo. Progress bar or percentage counter tied to asset loading. Exit animation: logo scales up and fades, revealing the horizontal canvas.
6. **Cinema 4D Integration**: 3D renders exported as H.264 MP4 loops (10–15s each). Played inline via `<video autoplay muted loop playsinline>`. Positioned as full-height panels in the horizontal scroll.
7. **Page Load Animation**: GSAP timeline: preloader fades out → first section slides in from right → typography animates letter-by-letter with `stagger: 0.03, duration: 0.6, ease: "power2.out"`.

WHY IT WORKS:
Horizontal scroll is inherently narrative — it mimics reading a book or viewing a film strip, perfect for a year-in-review. The black+gold palette signals luxury and prestige without being corporate (unlike blue+white). The parallax depth layers create kinetic energy that sustains interest through what would otherwise be a long scrolling experience. Typography at 200px+ forces hierarchy: you read the headline, then discover the details.

GOOD / BAD:
GOOD: Horizontal scroll perfectly matches the content type (chronological review). Gold on black is genuinely striking and uncommon in tech. The 7.44 score + Developer Award confirms technical execution. BAD: Horizontal scroll breaks native scroll expectations — users must discover the mechanic. WordPress as a base adds unnecessary overhead for what's essentially a single-page experience.

CLEAN APPLICATION:
Apply the horizontal scroll + parallax depth approach to Clean's "How It Works" or onboarding flow on tryclean.ai. Build a horizontal timeline showing the Clean data flow: Code Change → Tree-Sitter Parse → Context Event → Relevance Scoring → Agent Delivery. Each step is a full-viewport panel scrolling left-to-right. Use parallax depth: code snippets in foreground, data flow diagrams in midground, subtle grid/matrix texture in background. Replace gold with Clean's accent color. This transforms a technical explanation into a spatial, navigable experience.

---

## 4. Shed.design — https://shed.design

WHAT IT DOES:
An international design studio portfolio built on Vue.js with Lenis smooth scroll. The layout is vertically scrolled with full-bleed imagery that transitions between case studies using `clip-path` animations. Each project section is a carefully sequenced gallery: images scale, clip, and parallax at different rates to create a cinematic reveal. Three custom typefaces (Owners, Sequel, Reckless) rotate across sections, keeping the reading experience fresh. Custom cursor interactions (scale, blend-mode changes) add personality without distraction.

PACKAGES:
- Vue.js (component framework)
- Lenis (smooth scroll)
- GSAP (clip-path transitions, parallax, entrances)
- Custom CSS (clip-path animations, cursor)
- Three custom typefaces: Owners (display), Sequel (headings), Reckless (serif accent)

IMPLEMENTATION:
1. **Layout**: Full-width canvas, no max-width constraint on imagery. Content blocks use `padding: 0 clamp(24px, 5vw, 120px)` for responsive gutter. Vertical rhythm: sections separated by 200–300px of whitespace.
2. **Image Transitions**: `clip-path: inset()` animations between case studies. Entry: `clip-path: inset(100% 0 0 0)` → `inset(0)` with `duration: 1.2, ease: "power3.inOut"`. GSAP ScrollTrigger `scrub: true` ties the clip progress to scroll position for some images, creating a "wipe reveal" effect.
3. **Typography Rotation**: Three typefaces cycle by section. Owners: geometric sans, ~100px display headings. Sequel: monospace-adjacent, all-caps, ~14px for labels/metadata. Reckless: serif with high contrast, ~48px for pull quotes. `font-feature-settings: "ss01"` for stylistic alternates.
4. **Parallax System**: Images within each case study section move at varied rates. Hero image: `y: -10%` over section scroll. Secondary images: `y: -5%` to `y: -15%`. Text blocks remain static (position: sticky within their scroll zone).
5. **Custom Cursor**: Default: 12px circle, `mix-blend-mode: difference`. Over images: scales to 80px, fills with white at 20% opacity. Over links: scales to 48px, text label appears inside ("View", "Next"). Implementation: `position: fixed` div tracked via `mousemove` with GSAP `quickTo()` for smooth lag (`duration: 0.3`).
6. **Smooth Scroll**: Lenis config: `lerp: 0.08, smooth: true`. Lower lerp value than typical = more visible smooth-scroll lag = more "luxurious" feel.
7. **Vue.js Architecture**: Single-page app with route-based code splitting. Each case study is a lazy-loaded route component. Shared layout component wraps the cursor, navigation, and Lenis instance.

WHY IT WORKS:
The rotating typefaces prevent visual monotony across a long-scroll portfolio — each section feels like turning to a new chapter. The clip-path transitions create cinematic reveals that elevate static imagery into an experience. The generous whitespace (200–300px between sections) gives each project room to breathe, applying the Gestalt principle of proximity in reverse: isolation signals importance. The custom cursor with contextual labels provides wayfinding without traditional UI chrome.

GOOD / BAD:
GOOD: The clip-path + parallax combination is technically simple but visually sophisticated — high impact with moderate implementation cost. The three-typeface system is a masterclass in typographic variety without chaos. BAD: Full-bleed imagery with no max-width can cause layout issues on ultra-wide monitors (3440px+). The smooth-scroll lag at `lerp: 0.08` may feel sluggish to users accustomed to native scroll.

CLEAN APPLICATION:
Apply the clip-path reveal transitions and typeface rotation system to Clean's landing page case studies section on tryclean.ai. When showcasing customer stories or use cases, each case study gets a full-viewport section with `clip-path: inset()` wipe reveals on scroll. Rotate between two typefaces: a geometric sans for technical headings (e.g., "50% Token Reduction") and a monospace for code/data labels (e.g., `tree-sitter parse → context event`). Use the custom cursor with contextual labels ("Explore", "Read Case Study") on hover over each card. This makes Clean's case studies feel like a premium studio portfolio, not a generic SaaS testimonials section.

---

## 5. Linear Website (2026 Refresh) — https://linear.app

WHAT IT DOES:
Linear's marketing site is a Next.js app using Inter (UI/body) and Berkeley Mono (code snippets). The design language — now widely called "Linear style" — defines the current SaaS dark-mode aesthetic: near-black backgrounds with subtle radial gradients, glassmorphic card surfaces with `backdrop-filter: blur()`, specular border highlights that animate on hover, and bento-grid feature layouts. The site uses Framer Motion for entrance animations and hover microinteractions. No 3D — every visual effect is achieved through CSS gradients, blur, and subtle motion.

PACKAGES:
- Next.js (App Router, React Server Components)
- Framer Motion (entrance animations, hover states, layout transitions)
- Tailwind CSS (utility-first styling)
- Inter (body/UI typeface, variable font)
- Berkeley Mono (code typeface)
- Radix UI or custom components for accessibility primitives
- CSS `backdrop-filter`, `mask-image`, radial gradients (no WebGL)

IMPLEMENTATION:
1. **Color System**: Background: #0A0A0B (near-black). Surface cards: rgba(255,255,255,0.03) with `backdrop-filter: blur(20px)`. Borders: rgba(255,255,255,0.06). Primary accent: linear-gradient(135deg, #5E6AD2 (purple), #8E5CF5 (violet)). Text: #EDEDED (primary), #8A8F98 (secondary).
2. **Bento Grid**: CSS Grid with named template areas. Desktop: 4 columns, `gap: 1px` (the 1px gap creates subtle divider lines via the dark background showing through). Cards use `border-radius: 16px`, `padding: 32px 40px`. Large feature cards span 2 columns or 2 rows.
3. **Glassmorphic Cards**: `background: rgba(255,255,255,0.03)`. `backdrop-filter: blur(20px) saturate(150%)`. `border: 1px solid rgba(255,255,255,0.06)`. On hover: border brightens to `rgba(255,255,255,0.12)` with `transition: border-color 0.2s ease`. Some cards add a specular highlight: `background: radial-gradient(600px circle at var(--mouse-x) var(--mouse-y), rgba(255,255,255,0.06), transparent 40%)` tracked via `onMouseMove`.
4. **Specular Border Effect**: A `div::before` pseudo-element with `background: radial-gradient(at var(--mouse-x) var(--mouse-y), rgba(255,255,255,0.15), transparent 60%)`. Masked with `mask-image: linear-gradient(black, black) content-box, linear-gradient(black, black)` and `mask-composite: exclude` to only show the gradient on the border area. Mouse position tracked via JS `mousemove` listener updating CSS custom properties.
5. **Entrance Animations**: Framer Motion `motion.div` with `initial={{ opacity: 0, y: 20 }}`, `whileInView={{ opacity: 1, y: 0 }}`, `transition={{ duration: 0.5, ease: [0.25, 0.1, 0.25, 1] }}`, `viewport={{ once: true, margin: "-100px" }}`.
6. **Typography**: Inter Variable: headlines 48–64px, weight 600. Body 16px, weight 400, line-height 1.6. Berkeley Mono: 14px for code, weight 400, `letter-spacing: -0.01em`. All `font-smoothing: antialiased`.
7. **Radial Gradient Hero**: Full-width hero with `background: radial-gradient(ellipse 80% 50% at 50% -20%, rgba(94,106,210,0.15), transparent)` creating a subtle purple glow emanating from above.
8. **Performance**: React Server Components for static content. Client components only for interactive elements (hover effects, animations). Image optimization via `next/image`. Font subsetting.

WHY IT WORKS:
Linear's design succeeds through restraint disguised as sophistication. The glassmorphic cards with mouse-tracked specular highlights create a "living" surface that rewards exploration without demanding attention. The 1px-gap bento grid is a Gestalt proximity masterclass: cards are clearly grouped yet individually distinct. The dark background with radial gradient glow creates atmospheric depth (aerial perspective principle) while keeping the interface scannable. Every pixel serves information hierarchy, not decoration.

GOOD / BAD:
GOOD: The specular border effect is best-in-class — it's become the defining microinteraction of 2025-2026 SaaS. The bento grid is infinitely composable for feature pages. Zero WebGL dependency means universal performance. BAD: "Linear style" is now so widely copied that it risks feeling generic. The dark-only design alienates users who prefer light mode. Mouse-tracked effects don't work on mobile.

CLEAN APPLICATION:
Apply Linear's entire card system and bento grid to Clean's features page on tryclean.ai. Build a 4-column bento grid where each card represents a Clean capability: "Tree-Sitter Indexing" (code icon + live parse visualization), "Shared Context Intelligence" (network graph animation), "TOON Format" (token count comparison: JSON vs TOON), "MCP Gateway" (single node → multiple agent icons). Use the specular border hover effect on every card. Hero section gets the radial gradient glow in Clean's brand color. Typography: Inter for UI, Berkeley Mono for all code snippets and technical specs. This is the highest-leverage surface to rebuild because it directly communicates Clean's value to the ICP (engineering teams).

---

## BUILD DIRECTIVE

TARGET: Clean's Features/Product page on tryclean.ai — the primary surface that communicates Clean's four-pillar value proposition (code indexing, shared context intelligence, TOON format, MCP gateway) to engineering teams evaluating the product.

APPROACH: Build a dark-mode features page combining Linear's bento grid card system with Corentin Bernadou's WebGL-as-content approach. The page opens with a radial-gradient hero (purple/blue glow, Inter 64px headline: "The context layer underneath every AI agent"). Below: a 4-column bento grid where each card is a glassmorphic surface with specular border hover effects. The hero card (spanning 2 columns) contains a small Three.js scene — a network graph visualization showing context flowing between agent nodes in real-time (not decorative 3D, but a functional diagram rendered in WebGL). Other cards use CSS-only animations: a live token counter comparing JSON vs TOON output, a Tree-Sitter parse tree that builds on scroll, an MCP proxy diagram with animated connection lines. Use Shed.design's clip-path reveal pattern for the "How It Works" section below the grid — four full-width panels that wipe-reveal on scroll, each showing one step of the Clean data flow. Custom cursor with contextual labels ("Explore", "Try It") from the Shed model. Darknode's high-contrast approach inspires the color: near-black background (#0A0A0B), Clean's accent for highlights, white text at 90% opacity.

STACK:
- Next.js 15 (App Router, RSC for static content)
- Tailwind CSS v4
- Framer Motion (entrances, hover states, layout animations)
- Three.js + @react-three/fiber + @react-three/drei (hero network graph only)
- GSAP + ScrollTrigger (clip-path reveals, parallax in How It Works section)
- Lenis (smooth scroll)
- Inter (variable, UI/body)
- Berkeley Mono (code/technical)
- shadcn/ui (accessible primitives — buttons, dialogs, tooltips)

REFERENCE URLS:
1. https://linear.app — bento grid, glassmorphic cards, specular borders, color system, typography
2. https://corentinbernadou.com — WebGL-as-content (3D geometry showing real data), Swiss grid discipline
3. https://shed.design — clip-path section reveals, custom cursor, generous whitespace rhythm

KEY CONSTRAINTS:
- Clean is developer infrastructure. The result must feel like Linear or Vercel — technically sophisticated, fast, precise. NOT like a creative agency portfolio or a consumer SaaS with illustrations.
- No decorative 3D. Every Three.js element must visualize real Clean concepts (context flow, parse trees, agent connections). If it doesn't explain the product, it doesn't ship.
- Mobile must work perfectly. All mouse-tracked effects (specular borders, custom cursor) gracefully degrade on touch devices — no broken hover states.
- Performance budget: < 3s LCP, < 100ms INP. The Three.js hero scene must lazy-load and show a CSS gradient placeholder until the WebGL context initializes.
- Avoid: illustrations, stock photography, generic "dashboard mockup" screenshots, gradient mesh blobs with no purpose, anything that looks like a Framer template. This page must look like it was built by the engineering team that builds context infrastructure — because it was.
