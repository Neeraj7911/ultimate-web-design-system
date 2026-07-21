---
name: ultimate-web-design-system
description: >
  The ultimate web design and generation system. Use when building ANY type of website including landing pages, SaaS products, portfolios, ecommerce stores, agency sites, dashboards, 3D experiences, waitlist pages, fintech apps, wellness brands, developer tools, editorial sites, and more. This skill contains 1000+ design prompts, 50+ UI component patterns, and comprehensive style systems covering glassmorphism, neo-brutalism, Swiss minimalism, dark cinematic, warm aurora, liquid glass, and dozens more aesthetics. Triggers on any request to build, design, or generate a website, web page, landing page, hero section, footer, navbar, or any web UI component.
---

# Ultimate Web Design System

A comprehensive, production-grade web design system with 1000+ curated design prompts, 50+ premium UI component patterns, and 10+ complete style systems. This skill enables generation of ANY type of website with stunning, premium aesthetics.

## Limitations
- This skill provides design systems, patterns, and implementation guidance. Always verify responsive behavior, accessibility, and performance in the target environment.
- External video/image URLs referenced in patterns may change over time. Use them as inspiration and replace with project-specific assets.
- Component code examples use React/TypeScript/Tailwind. Adapt syntax for other frameworks as needed.

---

## 1. WEBSITE TYPE DETECTION

When the user requests a website, FIRST classify it into one or more of these categories, then apply the corresponding design system from Section 3:

| Category | Triggers | Recommended Style |
|----------|----------|-------------------|
| **SaaS Landing** | "saas", "product", "startup", "app landing" | Neo-Brutalist, Liquid Glass Hero, Aurora Background |
| **Portfolio** | "portfolio", "personal site", "showcase" | Swiss Minimalist, Deep Red Atmospheric, Editorial |
| **Ecommerce** | "store", "shop", "ecommerce", "product page" | Clean Fintech, Warm Organic, Neo-Brutalist |
| **Agency** | "agency", "studio", "design firm" | Deep Red Atmospheric, Cinematic Video Hero |
| **Dashboard** | "dashboard", "admin", "analytics", "panel" | Refined Glass, Dark Data |
| **3D Experience** | "3d", "interactive", "immersive", "webgl" | Tubes Background, Three.js Canvas, Particle Systems |
| **Waitlist/Launch** | "waitlist", "coming soon", "launch", "early access" | Dark Cinematic, Liquid Glass, Aurora |
| **Fintech** | "fintech", "banking", "crypto", "defi", "stablecoin" | Clean Fintech, Refined Glass, Warm Aurora |
| **Wellness/Lifestyle** | "wellness", "meditation", "health", "organic" | Softly Pastel, Warm Organic, Nature-Forward |
| **Developer Tool** | "devtool", "cli", "api", "sdk", "developer" | Warm Aurora CLI, Command Palette, Dark Keycap |
| **Editorial/Blog** | "blog", "magazine", "editorial", "content" | Swiss Typography, Serif Editorial, High Contrast |
| **Event/Conference** | "event", "conference", "meetup", "summit" | Cinematic Video, Deep Red, Glass Panels |

---

## 2. CORE DESIGN TOKENS (Universal Defaults)

Apply these as baseline tokens for ALL generated websites. Override per-style as needed.

### Typography Pairings (Premium, Anti-Generic)
```
DISPLAY FONTS (Headlines):
- "Instrument Serif" — Cinematic, editorial, luxury
- "Cabinet Grotesk" — Bold, neo-brutalist, startup energy
- "Clash Display" — Swiss brutalist, high-impact
- "Outfit" — Clean geometric, wellness/lifestyle
- "General Sans" — Developer tools, modern SaaS
- "Satoshi" — Functional, clean body + headings
- "Fustat" — Rounded, friendly SaaS

BODY FONTS:
- "Inter" — Universal body text (weights 300-600)
- "Satoshi" — Clean alternative body
- "Geist Sans" — Developer-oriented body

MONO FONTS:
- "JetBrains Mono" — Data, code, metrics
- "Geist Mono" — Developer tools, CLI

ACCENT/DISPLAY FONTS:
- "Reenie Beanie" — Handwritten, wellness
- "Playfair Display" — Serif editorial, luxury
```

### Animation Easings (Premium Feel)
```css
/* Standard premium transition */
transition: all 0.3s cubic-bezier(0.16, 1, 0.3, 1);

/* Bouncy/playful (neo-brutalist buttons) */
transition: all 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);

/* Smooth reveal (scroll animations) */
transition: all 0.8s cubic-bezier(0.22, 1, 0.36, 1);

/* Heavy/weighty glass panels */
transition: all 0.6s cubic-bezier(0.16, 1, 0.3, 1);

/* Spring physics (Framer Motion) */
{ type: "spring", stiffness: 100, damping: 20 }
{ type: "spring", stiffness: 500, damping: 30, mass: 0.5 }
```

### Scroll Reveal Pattern
```css
.reveal {
  opacity: 0;
  transform: translateY(30px);
  transition: all 0.8s cubic-bezier(0.22, 1, 0.36, 1);
}
.reveal.active {
  opacity: 1;
  transform: translateY(0);
}
```
```js
const observer = new IntersectionObserver((entries) => {
  entries.forEach(entry => {
    if (entry.isIntersecting) entry.target.classList.add('active');
  });
}, { threshold: 0.1, rootMargin: '0px 0px -50px 0px' });
document.querySelectorAll('.reveal').forEach(el => observer.observe(el));
```

---

## 3. DESIGN SYSTEMS (Style Catalog)

### 3A. LIQUID GLASS (Light Mode)
**Use for:** SaaS hero sections, navbars, waitlist pages, modern product sites
**Palette:** White base, subtle blue glows, clean transparency
```css
.liquid-glass {
  background: rgba(255, 255, 255, 0.01);
  background-blend-mode: luminosity;
  backdrop-filter: blur(4px);
  -webkit-backdrop-filter: blur(4px);
  border: none;
  box-shadow: inset 0 1px 1px rgba(255, 255, 255, 0.1);
  position: relative;
  overflow: hidden;
}
.liquid-glass::before {
  content: '';
  position: absolute;
  inset: 0;
  border-radius: inherit;
  padding: 1.4px;
  background: linear-gradient(180deg,
    rgba(255,255,255,0.45) 0%, rgba(255,255,255,0.15) 20%,
    rgba(255,255,255,0) 40%, rgba(255,255,255,0) 60%,
    rgba(255,255,255,0.15) 80%, rgba(255,255,255,0.45) 100%);
  -webkit-mask: linear-gradient(#fff 0 0) content-box, linear-gradient(#fff 0 0);
  -webkit-mask-composite: xor;
  mask-composite: exclude;
  pointer-events: none;
}
```
**Key specs:** Navbar pill (backdrop-blur-[50px], bg rgba(255,255,255,0.3), rounded-[16px]), inner highlight shadow, 1px gradient border via mask-composite.

### 3B. REFINED GLASS (Dark Dashboard)
**Use for:** Fintech dashboards, AI SaaS, data-heavy panels, premium management platforms
**Palette:** #050505 base, #0a0a0a panels, accents Blue #3B82F6, Emerald #10B981, Rose #F43F5E
**Fonts:** Outfit (display) + Inter (body) + JetBrains Mono (data)
```
Glass panels: backdrop-filter: blur(35px) saturate(150%)
Background: linear-gradient(180deg, rgba(255,255,255,0.09), rgba(255,255,255,0.02))
Border: 1px solid rgba(255,255,255,0.10)
Inner highlight: 0 1px 0 rgba(255,255,255,0.14)
Ambient glow: radial-gradient circles at corners, ~640-720px, 3-4% white opacity
```
**Layout:** 280px fixed sidebar + bento-grid main content. Staggered entrance animations (0.05s increments).

### 3C. NEO-BRUTALIST (High Energy)
**Use for:** Creator economy, edtech, bold startups, playful brands
**Palette:** Primary #FFE17C (yellow), Background #171E19 (charcoal), Accent #B7C6C2 (sage)
**Fonts:** Cabinet Grotesk (Extrabold headings) + Satoshi (body)
```
ALL elements: 2px solid black borders
Standard shadow: box-shadow: 4px 4px 0px 0px #000000
Large shadow: box-shadow: 8px 8px 0px 0px #000000
Button hover: transform: translate(4px, 4px); box-shadow: 4px 4px 0px 0px #000
Dot pattern: radial 32x32 pattern at 10% opacity on yellow backgrounds
```
**Rules:** NO gradients. NO soft shadows. NO rounded corners > 12px. ZERO blur radius on all shadows.

### 3D. SWISS MINIMALIST / HIGH CONTRAST
**Use for:** Architecture portfolios, luxury travel, fashion, editorial
**Palette:** Off-white #F2F2F2 background, deep black #111111 text, grays #BFBFBF-#D9D9D9
**Fonts:** Clash Display (bold 700, tracking -0.05em) + Satoshi (medium 500)
```
Echo Effect: Layer 5 instances of same word, each offset by 0.04em
Colors fade: #BFBFBF → #C9C9C9 → #D1D1D1 → #D9D9D9
Background layers: pointer-events: none, position: absolute
Image reveals: clip-path inset animation, 700ms cubic-bezier(0.77, 0, 0.175, 1)
Grayscale-to-color hover transitions on images
```

### 3E. DEEP RED ATMOSPHERIC
**Use for:** Agency sites, creative studios, luxury brands
**Palette:** #050505 base, accent #FF4500 (deep red/orange)
**Fonts:** Playfair Display (serif, italic emphasis) + Inter (body)
```
Floating parallax cards with scroll-offset CSS variables
Dot-grid background: radial-gradient(circle, #333 1px, transparent 1px), 40px spacing
Noise texture overlay: fixed, 5% opacity, mix-blend-mode: overlay
Floating animations: translateY ±20px, 12-14s ease-in-out infinite
Text shadow glow: 0 0 12px rgba(255,255,255,0.71)
```

### 3F. RED NOIR (Dark Tech)
**Use for:** AI products, tech platforms, futuristic brands
**Palette:** Black base (#000), accent #EF233C (crimson red)
**Fonts:** Manrope (display, bold) + Inter (body)
```
Spinning border CTA: conic-gradient animation on @property --gradient-angle
Star field: CSS box-shadow particles with translateY keyframes
Grid overlay: linear-gradient lines at 40px spacing, 3% white opacity
Shimmer button: rotating border gradient + dot pattern overlay
```

### 3G. SOFTLY PASTEL (Wellness)
**Use for:** Wellness apps, meditation, lifestyle brands, journaling
**Palette:** #FDFCF8 base, Sage #E8EFE8, Lavender #EFEDF4, Coral #FFB7B2
**Fonts:** Outfit (sans, headings) + Reenie Beanie (cursive accent)
```
Grain overlay: SVG feTurbulence (baseFrequency 0.65), mix-blend-mode: overlay, 35% opacity
Background blobs: high-radius blur, 60% opacity pastels, floating animation
Border-radius: 2rem to 4rem on all containers
Shadows: 0 4px 20px -2px rgba(0,0,0,0.05) — ultra-soft
```

### 3H. WARM AURORA (Developer Tools)
**Use for:** CLI tools, developer SDKs, dark SaaS landing pages
**Palette:** Near-black #07080A, crimson #FF2F3A, coral #FF6B4A, amber #FFB347
**Fonts:** Inter (all UI) + Geist Mono (code/shortcuts only)
```
Aurora blades: large diagonal gradient streaks, mix-blend-mode: screen
CSS keyframes: translate + rotate drift, opacity/scale breathing, 15-22s loops
SVG feTurbulence grain + vignette overlay
Keycap buttons: bg #E6E6E6, text #2F3031, layered shadow stack
Command palette mockup: backdrop-blur, hairline border, warm active row
RULE: NO PURPLE. Strictly warm palette only.
```

### 3I. CINEMATIC VIDEO HERO
**Use for:** Agency sites, product launches, creative studios
```
Fullscreen <video>: autoPlay loop muted playsInline, object-cover, absolute inset-0
Liquid glass navbar over video
Character-by-character text reveal: staggered 30ms delay per char, 500ms transition
HLS streaming via hls.js for .m3u8 sources
Boomerang video: capture frames to canvas, play forward/backward at 30fps
```

### 3J. CLEAN FINTECH
**Use for:** Stablecoin products, banking, DeFi platforms
**Palette:** #F5F5F5 base, cards white, accents dark #2B2644
**Fonts:** TT Norms Pro (or system) + Inter (paragraphs)
```
Brand marquee: infinite translateX animation, 22-30s linear
Video sections: autoplay muted in rounded containers
Pill buttons with trailing arrow circles
Tight negative letter-spacing: -0.03em to -0.04em on all headings
```

---

## 4. COMPONENT PATTERNS (Production-Ready)

### 4A. Navigation Patterns

**Floating Pill Nav (Glass):**
```
Position: sticky/fixed, top-[30px] or top-0, centered, w-fit or full-width
Glass: backdrop-blur-[50px], bg rgba(255,255,255,0.3) or rgba(0,0,0,0.4)
Shape: rounded-[16px] or rounded-full
Border: 1px solid rgba(255,255,255,0.1) or rgba(0,0,0,0.1)
Inner shadow: inset 0px 4px 4px 0px rgba(255,255,255,0.25)
Items: Logo left, links center (hidden mobile), CTA right
Scroll effect: shrink padding + add backdrop-blur + border on scroll > 50px
```

**Mobile Drawer:**
```
Overlay: fixed inset-0 bg-black/40 backdrop-blur-sm, fade transition
Drawer: fixed right-0 w-[85%] max-w-sm bg-white/95, translateX transition
Duration: 500ms ease-[cubic-bezier(0.22,1,0.36,1)]
Staggered links: delay per item (150ms + i * 70ms)
Hamburger animation: Menu/X icon swap with rotate + scale + opacity
```

### 4B. Hero Section Patterns

**Split Hero (Text + Visual):**
```
Grid: grid-cols-1 lg:grid-cols-2, items-center
Left: Badge → H1 → Subtitle → CTA buttons
Right: Browser mockup / Phone mockup / Video / 3D element
Staggered reveal: 0.1s increments per element
```

**Centered Cinematic Hero:**
```
Full viewport: min-h-[100dvh], flex items-center justify-center
Background: video or gradient or aurora
Content: max-w-4xl text-center
Elements: Eyebrow chip → Large H1 → Subtitle → CTA → Logo strip
Typography: Instrument Serif or equivalent display font
```

**Bottom-Anchored Hero:**
```
Content pushed to bottom: flex-1 flex flex-col justify-end, pb-12
Video background with NO overlay (raw)
Character-by-character text reveal
Tag card in bottom-right corner
```

### 4C. Feature/Bento Grid Patterns

**Standard Bento (3-col top, 2-col bottom):**
```
Grid: grid-cols-1 md:grid-cols-2 lg:grid-cols-3, gap-2
Cards: rounded-[20px], p-4, bg-white dark:bg-neutral-900
Border: shadow-[0_0_0_1px_rgba(0,0,0,0.08)]
Dark: shadow-[inset_0_1px_0_0_rgba(255,255,255,0.05)]
Each card: title + description + visual child component
Visuals: animated SVG graphs, node pipelines, activity feeds
```

**Animated Bento Card Types:**
1. Agent Pipeline — SVG node graph with animated flow paths
2. Token Monitor — Bar charts with breathing height animations
3. Activity Feed — Stacked cards with spring-based carousel
4. Knowledge Base — Namespace bars with scanning light beam
5. Tool Inspector — 2x2 grid with progress bars

### 4D. Button Patterns

**Neo-Brutalist Push Button:**
```css
background: #000; color: #fff; padding: 1rem 2rem;
border: 2px solid #000; border-radius: 0.75rem;
box-shadow: 8px 8px 0px 0px #000;
transition: all 0.2s cubic-bezier(0.175, 0.885, 0.32, 1.275);
/* Hover: */ transform: translate(4px, 4px); box-shadow: 4px 4px 0px 0px #000;
```

**Spinning Border CTA:**
```css
.shiny-cta {
  --gradient-angle: 0deg;
  background: linear-gradient(#000, #000) padding-box,
    conic-gradient(from var(--gradient-angle), transparent 0%, accent 15%, transparent 40%) border-box;
  border: 2px solid transparent;
  animation: border-spin 2.5s linear infinite;
}
```

**Pill CTA with Arrow Circle:**
```
Layout: pl-6 pr-2 py-2, rounded-full, bg-white/black
Text: font-medium + gap-3
Arrow: 40px circle, accent-colored bg, ArrowRight icon
Hover: shadow-[0_0_20px_rgba(255,255,255,0.3)], scale-105
```

**Animated Shine Button:**
```
Mask-based text shine: linear-gradient mask sweeping -75deg
Border shine: backgroundSize 200% with translate animation
Spring physics: whileHover scale 1.01, whileTap scale 0.97
```

### 4E. Footer Patterns

**Layered Card Footer:**
```
Outer: bg-[#E9EBEE] rounded-[48px] border
Inner: bg-white rounded-[40px] m-2
Grid: 5-col (brand 2-col + 3 link columns)
Bottom bar: copyright + legal links + separator
Glass text: massive brand name with SVG filter (drop-shadow + inner highlights)
```

**Parallax Image Footer:**
```
Background image: bg-cover bg-center, h-screen overflow-hidden
Foreground layer: motion.div with useTransform parallax
Footer card: absolute top-0, bg-white/95 backdrop-blur
Content: logo + link columns + social icons + legal bar
```

**Dark Minimal Footer:**
```
Background: #1E1E1E or #050505
Large ghost text: 10vw opacity-10 brand name
Columns: brand + navigation + company + contact
Social icons: grayscale → color on hover
Border-top: 1px white at 5% opacity
```

### 4F. Marquee / Social Proof Patterns

**Infinite Brand Marquee:**
```css
@keyframes marquee { from { transform: translateX(0); } to { transform: translateX(-50%); } }
.marquee-track { display: flex; width: max-content; animation: marquee 22s linear infinite; }
```
```
Render brand list TWICE for seamless loop
Items: mx-7 shrink-0, varied font families per brand for authenticity
Container: overflow-hidden, w-full
```

### 4G. 3D / Interactive Background Patterns

**Tubes Cursor (Three.js):**
```
Import threejs-components TubesCursor from CDN
Canvas: absolute inset-0, touch-action: none
Content overlay: relative z-10 pointer-events-none
Click interaction: randomize tube + light colors
```

**CSS Aurora Background:**
```
Multiple diagonal gradient streaks with mix-blend-mode: screen
Animated via @keyframes: translate + rotate drift, opacity pulse
Staggered 15-22s loops, 0% keyframe at full bloom
SVG feTurbulence grain overlay + CSS vignette
```

### 4H. Form Patterns

**Email Capture with Typewriter:**
```
States: Button → Email Input → Success (AnimatePresence)
Typewriter placeholder: 60ms per character interval
Auto-reset to button state after 4s
Glass styling: border-white/20, bg-white/[0.02], backdrop-blur
```

**Waitlist Form:**
```
Pill input: rounded-full, bg-stone-50 or bg-white/5
Submit button: accent color, scales on hover
Inline validation with transition
```

---

## 5. ANIMATION COOKBOOK

### Entrance Animations
```css
@keyframes fadeUp { from { opacity:0; transform:translateY(30px); } to { opacity:1; transform:translateY(0); } }
@keyframes fadeIn { from { opacity:0; } to { opacity:1; } }
@keyframes slideInLeft { from { opacity:0; transform:translateX(-40px); } to { opacity:1; transform:translateX(0); } }
@keyframes slideInRight { from { opacity:0; transform:translateX(40px); } to { opacity:1; transform:translateX(0); } }
@keyframes scaleIn { from { opacity:0; transform:scale(0.9); } to { opacity:1; transform:scale(1); } }
@keyframes wordReveal { from { opacity:0; transform:translateY(100%); filter:blur(4px); } to { opacity:1; transform:translateY(0); filter:blur(0); } }
```

### Perpetual Animations
```css
@keyframes float { 0%,100% { transform:translateY(0); } 50% { transform:translateY(-10px); } }
@keyframes shimmer { from { transform:translateX(-100%); } to { transform:translateX(100%); } }
@keyframes pulse { 0%,100% { opacity:1; } 50% { opacity:0.5; } }
@keyframes marquee { from { transform:translateX(0); } to { transform:translateX(-50%); } }
@keyframes borderSpin { from { --gradient-angle:0deg; } to { --gradient-angle:360deg; } }
```

### Framer Motion Presets
```tsx
// Fade up
initial={{ opacity: 0, y: 20 }} animate={{ opacity: 1, y: 0 }}
transition={{ duration: 0.6, ease: [0.16, 1, 0.3, 1] }}

// Scale in
initial={{ opacity: 0, scale: 0.9 }} animate={{ opacity: 1, scale: 1 }}
transition={{ delay: 0.2, duration: 0.6 }}

// Stagger children
const container = { hidden: {}, show: { transition: { staggerChildren: 0.1 } } };
const item = { hidden: { opacity: 0, y: 20 }, show: { opacity: 1, y: 0 } };

// Spring physics
transition={{ type: "spring", stiffness: 500, damping: 35 }}

// Infinite loop
animate={{ rotate: 360 }}
transition={{ repeat: Infinity, duration: 4, ease: "linear" }}
```

---

## 6. RESPONSIVE BREAKPOINT SYSTEM

All designs MUST be fully responsive. Use this breakpoint strategy:

```
Mobile-first approach:
- Base (< 640px): Single column, stacked layout, full-width elements
- sm (640px): Slight adjustments, side-by-side where appropriate
- md (768px): 2-column grids, show desktop nav
- lg (1024px): Full desktop layout, 3+ columns, large typography
- xl (1280px): Max-width containers, refined spacing
```

**Critical Rules:**
- Hero sections: Use `min-h-[100dvh]` NOT `h-screen` (prevents iOS Safari jumping)
- Never use percentage-based flex math — use CSS Grid instead
- Mobile nav: Always provide hamburger menu with drawer/overlay
- Images: Use responsive sizing with `clamp()` for fluid scaling
- Typography: Scale headlines responsively (text-4xl → md:text-6xl → lg:text-8xl)
- Touch targets: Minimum 44x44px for all interactive elements on mobile

---

## 7. IMPLEMENTATION WORKFLOW

When generating a website, follow this exact sequence:

1. **Classify** the website type (Section 1)
2. **Select** the appropriate design system (Section 3)
3. **Choose** typography pairing from Section 2
4. **Build** CSS foundation: design tokens, animations, utilities
5. **Construct** navigation (Section 4A)
6. **Build** hero section (Section 4B)
7. **Add** content sections using appropriate patterns (Section 4C-4H)
8. **Implement** footer (Section 4E)
9. **Add** animations and micro-interactions (Section 5)
10. **Verify** responsive behavior (Section 6)
11. **Polish**: grain textures, noise overlays, background effects

---

## 8. REFERENCE FILES

For detailed implementation examples, component source code, and additional design prompts, consult the reference files in the `references/` directory:

- **design-prompts-collection.md** — 948 design prompts covering every style, layout, and component type
- **motion-patterns-collection.md** — 122 motion-focused prompts with exact code implementations
- **component-library.md** — 50+ production React/TypeScript UI components with full source

To use a reference, read the relevant section from the reference file for exact implementation details, then adapt to the current project's tech stack and requirements.

---

## 9. QUALITY CHECKLIST

Before delivering any website, verify against this matrix:

- [ ] Typography uses a premium font pairing (not system defaults)
- [ ] Color palette is cohesive with max 1 accent color
- [ ] All interactive elements have hover/active/focus states
- [ ] Animations use premium easings (no linear transitions)
- [ ] Mobile layout is fully responsive with no horizontal scroll
- [ ] Navigation includes mobile hamburger menu
- [ ] Hero section uses `min-h-[100dvh]` not `h-screen`
- [ ] Images have proper aspect ratios and responsive sizing
- [ ] Loading states exist for async content
- [ ] Background effects (grain, blur, gradients) are on fixed layers with `pointer-events: none`
- [ ] z-index usage is systematic (nav: 50, modals: 40, overlays: 30)
- [ ] All animations have proper cleanup in useEffect
- [ ] SEO meta tags, semantic HTML, and heading hierarchy are correct
- [ ] The design does NOT look like generic AI output (no purple glow, no "Acme Corp", no emoji)

---

## 10. EXTERNAL COMPONENT LIBRARIES

You have access to thousands of premium UI components and code snippets in the `references/` directory. When building UIs, consult these extracted databases for inspiration or drop-in code:

- **`references/aceternity_ui_components.md`**: Contains 189 free animated Tailwind components (animations, cards, sections).
- **`references/vengenceui_components.md`**: Contains 132 premium UI components and layout blocks.
- **`references/superdesign_prompts.md`**: Contains 948 components and design prompts.
- **`references/motionsites_prompts.md`**: Contains 122 high-quality design prompts for motion interactions.
- **`references/aura_build_extracted_code.zip`**: Contains a massive database of 24,000+ UI components, pages, and React projects (198MB compressed).

Whenever you need a specific animated component, layout, or section, you should read the relevant markdown files in `references/` for immediate implementation details.
