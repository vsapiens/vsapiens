# GitHub Pages Personal Site — Implementation Plan

> **For Claude:** REQUIRED SUB-SKILL: Use superpowers:executing-plans to implement this plan task-by-task.

**Goal:** Build a dark & techy personal site at `vsapiens.github.io` — hybrid CV/portfolio with blog, projects, experience, and contact pages.

**Architecture:** Astro static site with Tailwind CSS, content collections for blog, GitHub API for projects, Formspree for contact form. Multi-page layout with persistent navbar/footer. Deployed via GitHub Actions to GitHub Pages.

**Tech Stack:** Astro 5, Tailwind CSS 4, TypeScript, GitHub REST API, Formspree, GitHub Actions

**Design Doc:** `docs/plans/2026-03-09-github-pages-site-design.md`

**Project Location:** `C:\Users\erick\Documents\vsapiens.github.io` (new repo, separate from profile README)

---

### Task 1: Scaffold Astro Project

**Files:**
- Create: `C:\Users\erick\Documents\vsapiens.github.io/` (entire project)

**Step 1: Create Astro project**

```bash
cd /c/Users/erick/Documents
npm create astro@latest vsapiens.github.io -- --template minimal --no-install --typescript strict
cd vsapiens.github.io
```

**Step 2: Install dependencies**

```bash
npm install
npm install -D @astrojs/tailwind tailwindcss @tailwindcss/typography
```

**Step 3: Configure Astro with Tailwind**

`astro.config.mjs`:
```js
import { defineConfig } from 'astro/config';
import tailwind from '@astrojs/tailwind';

export default defineConfig({
  site: 'https://vsapiens.github.io',
  integrations: [tailwind()],
});
```

**Step 4: Configure Tailwind theme**

`tailwind.config.mjs`:
```js
/** @type {import('tailwindcss').Config} */
export default {
  content: ['./src/**/*.{astro,html,js,jsx,md,mdx,svelte,ts,tsx,vue}'],
  theme: {
    extend: {
      colors: {
        bg: {
          primary: '#0d1117',
          elevated: '#161b22',
          surface: '#1c2333',
        },
        accent: {
          purple: '#a78bfa',
          'purple-dim': '#7c5cbf',
          'purple-glow': 'rgba(167, 139, 250, 0.3)',
        },
        text: {
          primary: '#c9d1d9',
          secondary: '#8b949e',
          heading: '#e6edf3',
        },
      },
      fontFamily: {
        mono: ['"JetBrains Mono"', '"Fira Code"', 'monospace'],
        sans: ['"Inter"', 'system-ui', 'sans-serif'],
      },
    },
  },
  plugins: [require('@tailwindcss/typography')],
};
```

**Step 5: Create global CSS**

`src/styles/global.css`:
```css
@tailwind base;
@tailwind components;
@tailwind utilities;

@import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700&family=JetBrains+Mono:wght@400;500;700&display=swap');

@layer base {
  body {
    @apply bg-bg-primary text-text-primary font-sans;
  }

  ::selection {
    @apply bg-accent-purple/30 text-white;
  }

  ::-webkit-scrollbar {
    @apply w-2;
  }

  ::-webkit-scrollbar-track {
    @apply bg-bg-primary;
  }

  ::-webkit-scrollbar-thumb {
    @apply bg-accent-purple-dim rounded-full;
  }
}
```

**Step 6: Verify build**

```bash
npm run build
```
Expected: Build succeeds with no errors.

**Step 7: Commit**

```bash
git init
git add -A
git commit -m "feat: scaffold Astro project with Tailwind CSS"
```

---

### Task 2: Base Layout — Navbar, Footer, SEO Component

**Files:**
- Create: `src/components/SEO.astro`
- Create: `src/components/Navbar.astro`
- Create: `src/components/Footer.astro`
- Create: `src/layouts/BaseLayout.astro`
- Modify: `src/pages/index.astro`

**Step 1: Create SEO component**

`src/components/SEO.astro`:
```astro
---
interface Props {
  title: string;
  description: string;
  image?: string;
  type?: string;
}

const {
  title,
  description,
  image = '/og-default.png',
  type = 'website',
} = Astro.props;

const canonicalURL = new URL(Astro.url.pathname, Astro.site);
const fullTitle = title === 'Home'
  ? 'Erick González — Performance Engineer | Backend Dev | AI Builder'
  : `${title} — Erick González`;
---

<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<meta name="generator" content={Astro.generator} />
<link rel="icon" type="image/svg+xml" href="/favicon.svg" />
<link rel="canonical" href={canonicalURL} />

<title>{fullTitle}</title>
<meta name="description" content={description} />

<meta property="og:type" content={type} />
<meta property="og:url" content={canonicalURL} />
<meta property="og:title" content={fullTitle} />
<meta property="og:description" content={description} />
<meta property="og:image" content={new URL(image, Astro.site)} />

<meta name="twitter:card" content="summary_large_image" />
<meta name="twitter:title" content={fullTitle} />
<meta name="twitter:description" content={description} />
<meta name="twitter:image" content={new URL(image, Astro.site)} />
```

**Step 2: Create Navbar component**

`src/components/Navbar.astro`:
```astro
---
const navLinks = [
  { href: '/', label: 'Home' },
  { href: '/experience', label: 'Experience' },
  { href: '/projects', label: 'Projects' },
  { href: '/blog', label: 'Blog' },
  { href: '/education', label: 'Education' },
  { href: '/contact', label: 'Contact' },
  { href: '/resume', label: 'Resume' },
];

const currentPath = Astro.url.pathname;
---

<nav class="fixed top-0 left-0 right-0 z-50 bg-bg-primary/90 backdrop-blur-md border-b border-white/5">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8">
    <div class="flex items-center justify-between h-16">
      <a href="/" class="font-mono font-bold text-lg text-accent-purple hover:text-white transition-colors">
        ~/vsapiens
      </a>

      <!-- Desktop Nav -->
      <div class="hidden md:flex items-center space-x-1">
        {navLinks.map((link) => (
          <a
            href={link.href}
            class:list={[
              'px-3 py-2 rounded-md text-sm font-medium transition-colors',
              currentPath === link.href
                ? 'text-accent-purple bg-accent-purple/10'
                : 'text-text-secondary hover:text-accent-purple hover:bg-accent-purple/5',
            ]}
          >
            {link.label}
          </a>
        ))}
      </div>

      <!-- Mobile Menu Button -->
      <button
        id="mobile-menu-btn"
        class="md:hidden text-text-secondary hover:text-accent-purple transition-colors"
        aria-label="Toggle menu"
      >
        <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
          <path id="menu-icon" stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16M4 18h16" />
        </svg>
      </button>
    </div>

    <!-- Mobile Nav -->
    <div id="mobile-menu" class="hidden md:hidden pb-4">
      {navLinks.map((link) => (
        <a
          href={link.href}
          class:list={[
            'block px-3 py-2 rounded-md text-sm font-medium transition-colors',
            currentPath === link.href
              ? 'text-accent-purple bg-accent-purple/10'
              : 'text-text-secondary hover:text-accent-purple hover:bg-accent-purple/5',
          ]}
        >
          {link.label}
        </a>
      ))}
    </div>
  </div>
</nav>

<script>
  const btn = document.getElementById('mobile-menu-btn');
  const menu = document.getElementById('mobile-menu');
  btn?.addEventListener('click', () => {
    menu?.classList.toggle('hidden');
  });
</script>
```

**Step 3: Create Footer component**

`src/components/Footer.astro`:
```astro
---
const currentYear = new Date().getFullYear();
---

<footer class="border-t border-white/5 bg-bg-primary">
  <div class="max-w-6xl mx-auto px-4 sm:px-6 lg:px-8 py-8">
    <div class="flex flex-col md:flex-row items-center justify-between gap-4">
      <p class="text-text-secondary text-sm">
        &copy; {currentYear} Erick González. Built with
        <a href="https://astro.build" class="text-accent-purple hover:underline" target="_blank" rel="noopener">Astro</a>.
      </p>

      <div class="flex items-center space-x-4">
        <!-- GitHub -->
        <a href="https://github.com/vsapiens" target="_blank" rel="noopener" class="text-text-secondary hover:text-accent-purple transition-colors" aria-label="GitHub">
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
            <path d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.905 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57A12.02 12.02 0 0024 12c0-6.63-5.37-12-12-12z" />
          </svg>
        </a>
        <!-- LinkedIn -->
        <a href="https://www.linkedin.com/in/erickfgonzalez/" target="_blank" rel="noopener" class="text-text-secondary hover:text-accent-purple transition-colors" aria-label="LinkedIn">
          <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24">
            <path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.065 2.064 2.064 0 112.063 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z" />
          </svg>
        </a>
        <!-- Email -->
        <a href="mailto:erick@vsapiens.dev" class="text-text-secondary hover:text-accent-purple transition-colors" aria-label="Email">
          <svg class="w-5 h-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M3 8l7.89 5.26a2 2 0 002.22 0L21 8M5 19h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
          </svg>
        </a>
      </div>
    </div>
  </div>
</footer>
```

**Step 4: Create BaseLayout**

`src/layouts/BaseLayout.astro`:
```astro
---
import Navbar from '../components/Navbar.astro';
import Footer from '../components/Footer.astro';
import SEO from '../components/SEO.astro';
import '../styles/global.css';

interface Props {
  title: string;
  description: string;
  image?: string;
  type?: string;
}

const { title, description, image, type } = Astro.props;
---

<!doctype html>
<html lang="en">
  <head>
    <SEO title={title} description={description} image={image} type={type} />
  </head>
  <body class="min-h-screen flex flex-col">
    <Navbar />
    <main class="flex-1 pt-16">
      <slot />
    </main>
    <Footer />
  </body>
</html>
```

**Step 5: Update index.astro to use layout**

`src/pages/index.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Home" description="Erick González — Performance Engineer, Backend Dev, and AI Builder.">
  <div class="max-w-6xl mx-auto px-4 py-20 text-center">
    <h1 class="text-4xl font-bold text-text-heading">Coming Soon</h1>
    <p class="text-text-secondary mt-4">Site under construction.</p>
  </div>
</BaseLayout>
```

**Step 6: Create favicon**

`public/favicon.svg`:
```svg
<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 36 36">
  <rect width="36" height="36" rx="6" fill="#0d1117"/>
  <text x="50%" y="54%" dominant-baseline="middle" text-anchor="middle" font-family="monospace" font-weight="bold" font-size="20" fill="#a78bfa">V</text>
</svg>
```

**Step 7: Verify build and dev server**

```bash
npm run build
npm run dev
```
Expected: Build succeeds. Dev server at `localhost:4321` shows "Coming Soon" with navbar and footer.

**Step 8: Commit**

```bash
git add -A
git commit -m "feat: add base layout with navbar, footer, and SEO component"
```

---

### Task 3: Home Page — Hero, About, Skills

**Files:**
- Create: `src/components/Hero.astro`
- Create: `src/components/About.astro`
- Create: `src/components/SkillsGrid.astro`
- Modify: `src/pages/index.astro`

**Step 1: Create Hero component**

`src/components/Hero.astro`:
```astro
---
const avatarUrl = 'https://github.com/vsapiens.png';
---

<section class="min-h-screen flex items-center justify-center relative overflow-hidden">
  <!-- Animated grid background -->
  <div class="absolute inset-0 bg-[linear-gradient(rgba(167,139,250,0.03)_1px,transparent_1px),linear-gradient(90deg,rgba(167,139,250,0.03)_1px,transparent_1px)] bg-[size:60px_60px]"></div>

  <!-- Radial glow -->
  <div class="absolute top-1/2 left-1/2 -translate-x-1/2 -translate-y-1/2 w-[600px] h-[600px] bg-accent-purple/5 rounded-full blur-3xl"></div>

  <div class="relative z-10 text-center px-4">
    <!-- Avatar -->
    <div class="mb-8 inline-block">
      <div class="w-32 h-32 rounded-full ring-2 ring-accent-purple ring-offset-4 ring-offset-bg-primary overflow-hidden shadow-[0_0_40px_rgba(167,139,250,0.2)]">
        <img src={avatarUrl} alt="Erick González" class="w-full h-full object-cover" loading="eager" />
      </div>
    </div>

    <!-- Name -->
    <h1 class="text-4xl sm:text-5xl md:text-6xl font-bold text-text-heading mb-4">
      Erick González
    </h1>

    <!-- Tagline with typing effect -->
    <div class="h-8 mb-8">
      <span id="typed-tagline" class="text-lg sm:text-xl text-accent-purple font-mono"></span>
      <span class="animate-pulse text-accent-purple font-mono">|</span>
    </div>

    <!-- CTA Buttons -->
    <div class="flex flex-col sm:flex-row items-center justify-center gap-4">
      <a href="/projects" class="px-6 py-3 bg-accent-purple text-white font-medium rounded-lg hover:bg-accent-purple-dim transition-colors">
        View Projects
      </a>
      <a href="/resume" class="px-6 py-3 border border-accent-purple text-accent-purple font-medium rounded-lg hover:bg-accent-purple/10 transition-colors">
        Download Resume
      </a>
    </div>
  </div>
</section>

<script>
  const phrases = [
    'Performance Engineer',
    'Backend Dev',
    'AI Builder',
  ];
  let phraseIndex = 0;
  let charIndex = 0;
  let isDeleting = false;
  const el = document.getElementById('typed-tagline');

  function type() {
    if (!el) return;
    const current = phrases[phraseIndex];

    if (isDeleting) {
      el.textContent = current.substring(0, charIndex - 1);
      charIndex--;
    } else {
      el.textContent = current.substring(0, charIndex + 1);
      charIndex++;
    }

    let delay = isDeleting ? 50 : 100;

    if (!isDeleting && charIndex === current.length) {
      delay = 2000;
      isDeleting = true;
    } else if (isDeleting && charIndex === 0) {
      isDeleting = false;
      phraseIndex = (phraseIndex + 1) % phrases.length;
      delay = 500;
    }

    setTimeout(type, delay);
  }

  type();
</script>
```

**Step 2: Create About component**

`src/components/About.astro`:
```astro
<section class="py-20">
  <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
    <h2 class="text-2xl font-bold text-text-heading font-mono mb-2">
      <span class="text-accent-purple">~/</span>about
    </h2>
    <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-8"></div>

    <div class="bg-bg-elevated border border-white/5 rounded-lg p-6 sm:p-8 space-y-4 text-text-primary leading-relaxed">
      <p>
        I'm a Software Engineer who landed in Performance Engineering — and never looked back.
        I spend my days making systems faster, diagnosing bottlenecks at the infrastructure level,
        and building backends with <span class="text-accent-purple font-medium">Node.js/TypeScript</span>
        and <span class="text-accent-purple font-medium">Python</span> that actually hold up under pressure.
      </p>
      <p>
        Lately, I've been diving deep into the AI space, working with
        <span class="text-accent-purple font-medium">LangChain agent frameworks</span>
        and using LLMs to tackle data modeling problems in ways that would've taken weeks
        just a couple of years ago.
      </p>
      <p>
        Whether I'm profiling a slow query, wiring up an AI agent pipeline, or teaching someone
        how to debug a race condition — I'm most at home when the problem is hard and the
        solution has to be precise.
      </p>
    </div>
  </div>
</section>
```

**Step 3: Create SkillsGrid component**

`src/components/SkillsGrid.astro`:
```astro
---
const skillCategories = [
  {
    title: 'Languages',
    skills: [
      { name: 'TypeScript', icon: '🟦' },
      { name: 'Python', icon: '🐍' },
      { name: 'JavaScript', icon: '⚡' },
      { name: 'SQL', icon: '🗃️' },
    ],
  },
  {
    title: 'Frameworks & Runtime',
    skills: [
      { name: 'Node.js', icon: '🟩' },
      { name: 'LangChain', icon: '🔗' },
      { name: 'Astro', icon: '🚀' },
    ],
  },
  {
    title: 'Infrastructure',
    skills: [
      { name: 'Docker', icon: '🐳' },
      { name: 'Linux', icon: '🐧' },
      { name: 'PostgreSQL', icon: '🐘' },
      { name: 'Git', icon: '📦' },
    ],
  },
  {
    title: 'AI / ML',
    skills: [
      { name: 'LLM Agents', icon: '🤖' },
      { name: 'Data Modeling', icon: '📐' },
      { name: 'Prompt Engineering', icon: '💬' },
    ],
  },
];
---

<section class="py-20 bg-bg-elevated/30">
  <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
    <h2 class="text-2xl font-bold text-text-heading font-mono mb-2">
      <span class="text-accent-purple">~/</span>skills
    </h2>
    <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-8"></div>

    <div class="grid grid-cols-1 sm:grid-cols-2 gap-6">
      {skillCategories.map((category) => (
        <div class="bg-bg-elevated border border-white/5 rounded-lg p-6">
          <h3 class="text-sm font-mono text-accent-purple uppercase tracking-wider mb-4">
            {category.title}
          </h3>
          <div class="flex flex-wrap gap-2">
            {category.skills.map((skill) => (
              <span class="px-3 py-1.5 bg-bg-surface rounded-md text-sm text-text-primary border border-white/5 hover:border-accent-purple/30 hover:shadow-[0_0_10px_rgba(167,139,250,0.1)] transition-all">
                {skill.icon} {skill.name}
              </span>
            ))}
          </div>
        </div>
      ))}
    </div>
  </div>
</section>
```

**Step 4: Update index.astro**

`src/pages/index.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import Hero from '../components/Hero.astro';
import About from '../components/About.astro';
import SkillsGrid from '../components/SkillsGrid.astro';
---

<BaseLayout title="Home" description="Erick González — Performance Engineer, Backend Dev, and AI Builder. Making systems faster and building with AI.">
  <Hero />
  <About />
  <SkillsGrid />
</BaseLayout>
```

**Step 5: Verify build and visual check**

```bash
npm run build && npm run dev
```
Expected: Home page shows hero with typing animation, about section, and skills grid. Dark theme with purple accents.

**Step 6: Commit**

```bash
git add -A
git commit -m "feat: add home page with hero, about, and skills sections"
```

---

### Task 4: Experience Page — Timeline

**Files:**
- Create: `src/data/experience.json`
- Create: `src/components/ExperienceTimeline.astro`
- Create: `src/pages/experience.astro`

**Step 1: Create experience data file**

`src/data/experience.json`:
```json
[
  {
    "company": "Company Name",
    "role": "Performance Engineer",
    "startDate": "2023-01",
    "endDate": "Present",
    "description": [
      "Led performance optimization efforts reducing API latency by 40%",
      "Built automated load testing pipelines with custom tooling",
      "Diagnosed and resolved critical infrastructure bottlenecks"
    ],
    "tech": ["Node.js", "TypeScript", "PostgreSQL", "Docker", "Linux"]
  },
  {
    "company": "Previous Company",
    "role": "Backend Developer",
    "startDate": "2021-06",
    "endDate": "2022-12",
    "description": [
      "Developed RESTful APIs serving 10K+ requests per second",
      "Implemented CI/CD pipelines and containerized deployments",
      "Mentored junior developers on backend best practices"
    ],
    "tech": ["Python", "Node.js", "Docker", "PostgreSQL"]
  }
]
```
> **Note:** User will replace placeholder data with actual experience.

**Step 2: Create ExperienceTimeline component**

`src/components/ExperienceTimeline.astro`:
```astro
---
import experienceData from '../data/experience.json';

function formatDate(dateStr: string): string {
  if (dateStr === 'Present') return 'Present';
  const [year, month] = dateStr.split('-');
  const date = new Date(parseInt(year), parseInt(month) - 1);
  return date.toLocaleDateString('en-US', { month: 'short', year: 'numeric' });
}
---

<div class="relative">
  <!-- Timeline line -->
  <div class="absolute left-4 md:left-1/2 top-0 bottom-0 w-px bg-accent-purple/20"></div>

  <div class="space-y-12">
    {experienceData.map((job, index) => (
      <div class:list={['relative flex flex-col md:flex-row gap-8', index % 2 === 0 ? 'md:flex-row' : 'md:flex-row-reverse']}>
        <!-- Timeline dot -->
        <div class="absolute left-4 md:left-1/2 w-3 h-3 bg-accent-purple rounded-full -translate-x-1/2 mt-6 shadow-[0_0_10px_rgba(167,139,250,0.4)]"></div>

        <!-- Spacer -->
        <div class="hidden md:block md:w-1/2"></div>

        <!-- Card -->
        <div class="ml-10 md:ml-0 md:w-1/2 md:px-8">
          <div class="bg-bg-elevated border border-white/5 rounded-lg p-6 border-l-2 border-l-accent-purple">
            <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between mb-2">
              <h3 class="text-lg font-semibold text-text-heading">{job.role}</h3>
              <span class="text-sm text-text-secondary font-mono">
                {formatDate(job.startDate)} — {formatDate(job.endDate)}
              </span>
            </div>
            <p class="text-accent-purple font-medium mb-3">{job.company}</p>
            <ul class="space-y-1 mb-4">
              {job.description.map((item) => (
                <li class="text-text-secondary text-sm flex items-start gap-2">
                  <span class="text-accent-purple mt-1">▸</span>
                  <span>{item}</span>
                </li>
              ))}
            </ul>
            <div class="flex flex-wrap gap-1.5">
              {job.tech.map((t) => (
                <span class="px-2 py-0.5 bg-bg-surface rounded text-xs text-text-secondary border border-white/5">
                  {t}
                </span>
              ))}
            </div>
          </div>
        </div>
      </div>
    ))}
  </div>
</div>
```

**Step 3: Create experience page**

`src/pages/experience.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import ExperienceTimeline from '../components/ExperienceTimeline.astro';
---

<BaseLayout title="Experience" description="Erick González's professional experience — Performance Engineering, Backend Development, and AI.">
  <section class="py-20">
    <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold text-text-heading font-mono mb-2">
        <span class="text-accent-purple">~/</span>experience
      </h1>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-12"></div>

      <ExperienceTimeline />
    </div>
  </section>
</BaseLayout>
```

**Step 4: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/experience` page shows alternating timeline cards with purple accents and dots.

**Step 5: Commit**

```bash
git add -A
git commit -m "feat: add experience page with timeline component"
```

---

### Task 5: Projects Page — GitHub API

**Files:**
- Create: `src/lib/github.ts`
- Create: `src/components/ProjectCard.astro`
- Create: `src/pages/projects.astro`

**Step 1: Create GitHub API helper**

`src/lib/github.ts`:
```ts
export interface GitHubRepo {
  name: string;
  description: string | null;
  html_url: string;
  homepage: string | null;
  language: string | null;
  stargazers_count: number;
  forks_count: number;
  topics: string[];
}

const GITHUB_USERNAME = 'vsapiens';

const languageColors: Record<string, string> = {
  TypeScript: '#3178c6',
  JavaScript: '#f1e05a',
  Python: '#3572A5',
  HTML: '#e34c26',
  CSS: '#563d7c',
  Shell: '#89e051',
  Astro: '#ff5a03',
};

export function getLanguageColor(language: string): string {
  return languageColors[language] ?? '#8b949e';
}

export async function fetchPinnedRepos(): Promise<GitHubRepo[]> {
  // GitHub REST API doesn't directly expose pinned repos,
  // so we fetch public repos sorted by stars as a proxy.
  // For true pinned repos, we'd need the GraphQL API.
  const res = await fetch(
    `https://api.github.com/users/${GITHUB_USERNAME}/repos?sort=updated&per_page=6&type=public`,
    {
      headers: {
        Accept: 'application/vnd.github.v3+json',
        'User-Agent': 'vsapiens-portfolio',
      },
    }
  );

  if (!res.ok) {
    console.error(`GitHub API error: ${res.status}`);
    return [];
  }

  const repos: GitHubRepo[] = await res.json();
  // Filter out the profile README repo and forks
  return repos.filter((r) => r.name !== GITHUB_USERNAME);
}
```

**Step 2: Create ProjectCard component**

`src/components/ProjectCard.astro`:
```astro
---
import { getLanguageColor, type GitHubRepo } from '../lib/github';

interface Props {
  repo: GitHubRepo;
}

const { repo } = Astro.props;
---

<div class="bg-bg-elevated border border-white/5 rounded-lg p-6 hover:border-accent-purple/30 hover:shadow-[0_0_20px_rgba(167,139,250,0.08)] hover:-translate-y-1 transition-all duration-300 flex flex-col h-full">
  <div class="flex items-start justify-between mb-3">
    <h3 class="text-lg font-semibold text-text-heading font-mono truncate">
      {repo.name}
    </h3>
    <svg class="w-5 h-5 text-text-secondary flex-shrink-0 ml-2" fill="none" stroke="currentColor" viewBox="0 0 24 24">
      <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
    </svg>
  </div>

  <p class="text-text-secondary text-sm flex-1 mb-4">
    {repo.description || 'No description provided.'}
  </p>

  <div class="flex items-center justify-between text-sm">
    <div class="flex items-center gap-3">
      {repo.language && (
        <span class="flex items-center gap-1.5">
          <span class="w-3 h-3 rounded-full" style={`background-color: ${getLanguageColor(repo.language)}`}></span>
          <span class="text-text-secondary">{repo.language}</span>
        </span>
      )}
      {repo.stargazers_count > 0 && (
        <span class="flex items-center gap-1 text-text-secondary">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M11.049 2.927c.3-.921 1.603-.921 1.902 0l1.519 4.674a1 1 0 00.95.69h4.915c.969 0 1.371 1.24.588 1.81l-3.976 2.888a1 1 0 00-.363 1.118l1.518 4.674c.3.922-.755 1.688-1.538 1.118l-3.976-2.888a1 1 0 00-1.176 0l-3.976 2.888c-.783.57-1.838-.197-1.538-1.118l1.518-4.674a1 1 0 00-.363-1.118l-3.976-2.888c-.784-.57-.38-1.81.588-1.81h4.914a1 1 0 00.951-.69l1.519-4.674z" />
          </svg>
          {repo.stargazers_count}
        </span>
      )}
    </div>

    <div class="flex items-center gap-2">
      <a href={repo.html_url} target="_blank" rel="noopener" class="text-text-secondary hover:text-accent-purple transition-colors" aria-label="Source code">
        <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
          <path d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.905 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57A12.02 12.02 0 0024 12c0-6.63-5.37-12-12-12z" />
        </svg>
      </a>
      {repo.homepage && (
        <a href={repo.homepage} target="_blank" rel="noopener" class="text-text-secondary hover:text-accent-purple transition-colors" aria-label="Live demo">
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
          </svg>
        </a>
      )}
    </div>
  </div>
</div>
```

**Step 3: Create projects page**

`src/pages/projects.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import ProjectCard from '../components/ProjectCard.astro';
import { fetchPinnedRepos } from '../lib/github';

const repos = await fetchPinnedRepos();
---

<BaseLayout title="Projects" description="Open source projects and repositories by Erick González.">
  <section class="py-20">
    <div class="max-w-5xl mx-auto px-4 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold text-text-heading font-mono mb-2">
        <span class="text-accent-purple">~/</span>projects
      </h1>
      <p class="text-text-secondary mb-2">Public repositories from GitHub, refreshed on each build.</p>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-12"></div>

      {repos.length > 0 ? (
        <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-6">
          {repos.map((repo) => (
            <ProjectCard repo={repo} />
          ))}
        </div>
      ) : (
        <p class="text-text-secondary text-center py-12">No projects found. Check back later.</p>
      )}

      <div class="text-center mt-12">
        <a
          href="https://github.com/vsapiens?tab=repositories"
          target="_blank"
          rel="noopener"
          class="inline-flex items-center gap-2 px-5 py-2.5 border border-accent-purple text-accent-purple rounded-lg hover:bg-accent-purple/10 transition-colors font-medium text-sm"
        >
          View all on GitHub
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M17 8l4 4m0 0l-4 4m4-4H3" />
          </svg>
        </a>
      </div>
    </div>
  </section>
</BaseLayout>
```

**Step 4: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/projects` page shows repo cards fetched from GitHub API.

**Step 5: Commit**

```bash
git add -A
git commit -m "feat: add projects page with GitHub API integration"
```

---

### Task 6: Blog — Content Collections & Pages

**Files:**
- Create: `src/content.config.ts`
- Create: `src/content/blog/hello-world.md`
- Create: `src/data/external-posts.json`
- Create: `src/components/BlogCard.astro`
- Create: `src/pages/blog/index.astro`
- Create: `src/pages/blog/[slug].astro`

**Step 1: Define content collection schema**

`src/content.config.ts`:
```ts
import { defineCollection, z } from 'astro:content';
import { glob } from 'astro/loaders';

const blog = defineCollection({
  loader: glob({ pattern: '**/*.md', base: './src/content/blog' }),
  schema: z.object({
    title: z.string(),
    description: z.string(),
    date: z.coerce.date(),
    tags: z.array(z.string()).default([]),
    draft: z.boolean().default(false),
  }),
});

export const collections = { blog };
```

**Step 2: Create a sample blog post**

`src/content/blog/hello-world.md`:
```markdown
---
title: "Hello World — Welcome to My Blog"
description: "First post on my personal site. Here's what I'll be writing about."
date: 2026-03-09
tags: ["meta", "introduction"]
---

# Hello World

Welcome to my blog. I'll be writing about performance engineering, backend development, AI agents, and lessons learned building software that has to work under pressure.

## What to Expect

- **Performance deep dives** — profiling, bottleneck analysis, optimization war stories
- **Backend patterns** — Node.js, TypeScript, Python, and the architectures that hold up
- **AI & LLMs** — LangChain agents, prompt engineering, data modeling with AI
- **Engineering culture** — debugging mindset, code review, mentoring

Stay tuned.
```

**Step 3: Create external posts data**

`src/data/external-posts.json`:
```json
[
  {
    "title": "Sample External Post",
    "description": "This is a placeholder for an external blog post on another platform.",
    "date": "2026-01-15",
    "tags": ["example"],
    "url": "https://dev.to/vsapiens",
    "platform": "Dev.to"
  }
]
```

**Step 4: Create BlogCard component**

`src/components/BlogCard.astro`:
```astro
---
interface Props {
  title: string;
  description: string;
  date: Date | string;
  tags: string[];
  href: string;
  isExternal?: boolean;
  platform?: string;
  readTime?: string;
}

const { title, description, date, tags, href, isExternal = false, platform, readTime } = Astro.props;

const formattedDate = new Date(date).toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});
---

<a
  href={href}
  target={isExternal ? '_blank' : undefined}
  rel={isExternal ? 'noopener' : undefined}
  class="block bg-bg-elevated border border-white/5 rounded-lg p-6 hover:border-accent-purple/30 hover:shadow-[0_0_20px_rgba(167,139,250,0.08)] hover:-translate-y-1 transition-all duration-300"
>
  <div class="flex items-start justify-between mb-2">
    <h3 class="text-lg font-semibold text-text-heading leading-snug pr-4">{title}</h3>
    {isExternal && (
      <span class="flex-shrink-0 px-2 py-0.5 bg-bg-surface rounded text-xs text-accent-purple border border-accent-purple/20">
        {platform || 'External'}
      </span>
    )}
  </div>

  <p class="text-text-secondary text-sm mb-4 line-clamp-2">{description}</p>

  <div class="flex items-center justify-between text-xs text-text-secondary">
    <div class="flex items-center gap-3">
      <time datetime={new Date(date).toISOString()}>{formattedDate}</time>
      {readTime && <span>{readTime}</span>}
    </div>
    <div class="flex flex-wrap gap-1.5">
      {tags.slice(0, 3).map((tag) => (
        <span class="px-2 py-0.5 bg-bg-surface rounded text-text-secondary">#{tag}</span>
      ))}
    </div>
  </div>
</a>
```

**Step 5: Create blog listing page**

`src/pages/blog/index.astro`:
```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import BlogCard from '../../components/BlogCard.astro';
import { getCollection } from 'astro:content';
import externalPosts from '../../data/external-posts.json';

const internalPosts = (await getCollection('blog'))
  .filter((post) => !post.data.draft)
  .map((post) => ({
    title: post.data.title,
    description: post.data.description,
    date: post.data.date,
    tags: post.data.tags,
    href: `/blog/${post.id}`,
    isExternal: false,
    readTime: `${Math.ceil(post.body!.split(/\s+/).length / 200)} min read`,
  }));

const external = externalPosts.map((post) => ({
  ...post,
  date: new Date(post.date),
  href: post.url,
  isExternal: true,
}));

const allPosts = [...internalPosts, ...external].sort(
  (a, b) => new Date(b.date).getTime() - new Date(a.date).getTime()
);
---

<BaseLayout title="Blog" description="Articles on performance engineering, backend development, and AI by Erick González.">
  <section class="py-20">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold text-text-heading font-mono mb-2">
        <span class="text-accent-purple">~/</span>blog
      </h1>
      <p class="text-text-secondary mb-2">Thoughts on performance, backends, and AI.</p>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-12"></div>

      <div class="space-y-4">
        {allPosts.map((post) => (
          <BlogCard
            title={post.title}
            description={post.description}
            date={post.date}
            tags={post.tags}
            href={post.href}
            isExternal={post.isExternal}
            platform={post.isExternal ? (post as any).platform : undefined}
            readTime={!post.isExternal ? (post as any).readTime : undefined}
          />
        ))}
      </div>

      {allPosts.length === 0 && (
        <p class="text-text-secondary text-center py-12">No posts yet. Check back soon.</p>
      )}
    </div>
  </section>
</BaseLayout>
```

**Step 6: Create individual blog post page**

`src/pages/blog/[slug].astro`:
```astro
---
import BaseLayout from '../../layouts/BaseLayout.astro';
import { getCollection, render } from 'astro:content';

export async function getStaticPaths() {
  const posts = await getCollection('blog');
  return posts
    .filter((post) => !post.data.draft)
    .map((post) => ({
      params: { slug: post.id },
      props: { post },
    }));
}

const { post } = Astro.props;
const { Content } = await render(post);

const formattedDate = post.data.date.toLocaleDateString('en-US', {
  year: 'numeric',
  month: 'long',
  day: 'numeric',
});

const readTime = `${Math.ceil(post.body!.split(/\s+/).length / 200)} min read`;
---

<BaseLayout title={post.data.title} description={post.data.description} type="article">
  <article class="py-20">
    <div class="max-w-3xl mx-auto px-4 sm:px-6 lg:px-8">
      <!-- Header -->
      <header class="mb-10">
        <a href="/blog" class="text-accent-purple text-sm font-mono hover:underline mb-4 inline-block">
          ← back to blog
        </a>
        <h1 class="text-3xl sm:text-4xl font-bold text-text-heading mb-4">
          {post.data.title}
        </h1>
        <div class="flex items-center gap-3 text-sm text-text-secondary">
          <time datetime={post.data.date.toISOString()}>{formattedDate}</time>
          <span>·</span>
          <span>{readTime}</span>
        </div>
        <div class="flex flex-wrap gap-1.5 mt-3">
          {post.data.tags.map((tag) => (
            <span class="px-2 py-0.5 bg-bg-surface rounded text-xs text-accent-purple border border-accent-purple/20">
              #{tag}
            </span>
          ))}
        </div>
      </header>

      <!-- Content -->
      <div class="prose prose-invert prose-purple max-w-none
        prose-headings:text-text-heading prose-headings:font-semibold
        prose-p:text-text-primary prose-p:leading-relaxed
        prose-a:text-accent-purple prose-a:no-underline hover:prose-a:underline
        prose-code:text-accent-purple prose-code:bg-bg-surface prose-code:px-1.5 prose-code:py-0.5 prose-code:rounded
        prose-pre:bg-bg-elevated prose-pre:border prose-pre:border-white/5
        prose-blockquote:border-accent-purple prose-blockquote:text-text-secondary
        prose-li:text-text-primary prose-strong:text-text-heading">
        <Content />
      </div>

      <!-- Navigation -->
      <div class="mt-16 pt-8 border-t border-white/5">
        <a href="/blog" class="text-accent-purple font-mono text-sm hover:underline">
          ← all posts
        </a>
      </div>
    </div>
  </article>
</BaseLayout>
```

**Step 7: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/blog` lists all posts. `/blog/hello-world` renders the Markdown post with syntax highlighting and prose styling.

**Step 8: Commit**

```bash
git add -A
git commit -m "feat: add blog with content collections, listing, and post pages"
```

---

### Task 7: Education & Certifications Page

**Files:**
- Create: `src/data/education.json`
- Create: `src/data/certifications.json`
- Create: `src/pages/education.astro`

**Step 1: Create education data**

`src/data/education.json`:
```json
[
  {
    "institution": "University Name",
    "degree": "B.S. in Computer Science",
    "year": "2020",
    "description": "Focused on systems programming and software engineering."
  }
]
```

**Step 2: Create certifications data**

`src/data/certifications.json`:
```json
[
  {
    "name": "AWS Certified Solutions Architect",
    "issuer": "Amazon Web Services",
    "date": "2024-06",
    "credentialUrl": "https://aws.amazon.com/verification"
  }
]
```
> **Note:** User will replace placeholder data with actual credentials.

**Step 3: Create education page**

`src/pages/education.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
import educationData from '../data/education.json';
import certData from '../data/certifications.json';

function formatDate(dateStr: string): string {
  if (!dateStr.includes('-')) return dateStr;
  const [year, month] = dateStr.split('-');
  return new Date(parseInt(year), parseInt(month) - 1).toLocaleDateString('en-US', { month: 'short', year: 'numeric' });
}
---

<BaseLayout title="Education" description="Education and certifications of Erick González.">
  <section class="py-20">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold text-text-heading font-mono mb-2">
        <span class="text-accent-purple">~/</span>education
      </h1>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-12"></div>

      <!-- Education -->
      <h2 class="text-xl font-semibold text-text-heading font-mono mb-6">
        <span class="text-accent-purple">##</span> Education
      </h2>
      <div class="grid gap-4 mb-16">
        {educationData.map((edu) => (
          <div class="bg-bg-elevated border border-white/5 rounded-lg p-6 border-l-2 border-l-accent-purple">
            <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between mb-1">
              <h3 class="text-lg font-semibold text-text-heading">{edu.degree}</h3>
              <span class="text-sm text-text-secondary font-mono">{edu.year}</span>
            </div>
            <p class="text-accent-purple font-medium mb-2">{edu.institution}</p>
            {edu.description && <p class="text-text-secondary text-sm">{edu.description}</p>}
          </div>
        ))}
      </div>

      <!-- Certifications -->
      <h2 class="text-xl font-semibold text-text-heading font-mono mb-6">
        <span class="text-accent-purple">##</span> Certifications
      </h2>
      <div class="grid gap-4 sm:grid-cols-2">
        {certData.map((cert) => (
          <div class="bg-bg-elevated border border-white/5 rounded-lg p-6 border-l-2 border-l-accent-purple">
            <h3 class="text-base font-semibold text-text-heading mb-1">{cert.name}</h3>
            <p class="text-accent-purple text-sm font-medium mb-1">{cert.issuer}</p>
            <p class="text-text-secondary text-xs font-mono mb-3">{formatDate(cert.date)}</p>
            {cert.credentialUrl && (
              <a
                href={cert.credentialUrl}
                target="_blank"
                rel="noopener"
                class="inline-flex items-center gap-1 text-xs text-accent-purple hover:underline"
              >
                Verify credential
                <svg class="w-3 h-3" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M10 6H6a2 2 0 00-2 2v10a2 2 0 002 2h10a2 2 0 002-2v-4M14 4h6m0 0v6m0-6L10 14" />
                </svg>
              </a>
            )}
          </div>
        ))}
      </div>
    </div>
  </section>
</BaseLayout>
```

**Step 4: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/education` shows education cards and certification cards with purple accents.

**Step 5: Commit**

```bash
git add -A
git commit -m "feat: add education and certifications page"
```

---

### Task 8: Contact Page

**Files:**
- Create: `src/pages/contact.astro`

**Step 1: Create contact page**

`src/pages/contact.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Contact" description="Get in touch with Erick González.">
  <section class="py-20">
    <div class="max-w-2xl mx-auto px-4 sm:px-6 lg:px-8">
      <h1 class="text-3xl font-bold text-text-heading font-mono mb-2">
        <span class="text-accent-purple">~/</span>contact
      </h1>
      <p class="text-text-secondary mb-2">Have a question or want to work together? Drop me a message.</p>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-12"></div>

      <!-- Contact Form -->
      <form
        action="https://formspree.io/f/YOUR_FORM_ID"
        method="POST"
        class="space-y-6"
      >
        <div>
          <label for="name" class="block text-sm font-medium text-text-primary mb-1.5">Name</label>
          <input
            type="text"
            id="name"
            name="name"
            required
            class="w-full px-4 py-2.5 bg-bg-elevated border border-white/10 rounded-lg text-text-primary placeholder-text-secondary/50 focus:outline-none focus:border-accent-purple focus:ring-1 focus:ring-accent-purple transition-colors"
            placeholder="Your name"
          />
        </div>

        <div>
          <label for="email" class="block text-sm font-medium text-text-primary mb-1.5">Email</label>
          <input
            type="email"
            id="email"
            name="email"
            required
            class="w-full px-4 py-2.5 bg-bg-elevated border border-white/10 rounded-lg text-text-primary placeholder-text-secondary/50 focus:outline-none focus:border-accent-purple focus:ring-1 focus:ring-accent-purple transition-colors"
            placeholder="you@example.com"
          />
        </div>

        <div>
          <label for="message" class="block text-sm font-medium text-text-primary mb-1.5">Message</label>
          <textarea
            id="message"
            name="message"
            rows="5"
            required
            class="w-full px-4 py-2.5 bg-bg-elevated border border-white/10 rounded-lg text-text-primary placeholder-text-secondary/50 focus:outline-none focus:border-accent-purple focus:ring-1 focus:ring-accent-purple transition-colors resize-none"
            placeholder="What's on your mind?"
          ></textarea>
        </div>

        <button
          type="submit"
          class="w-full px-6 py-3 bg-accent-purple text-white font-medium rounded-lg hover:bg-accent-purple-dim transition-colors"
        >
          Send Message
        </button>
      </form>

      <!-- Social Links -->
      <div class="mt-12 pt-8 border-t border-white/5 text-center">
        <p class="text-text-secondary text-sm mb-4">Or find me elsewhere:</p>
        <div class="flex items-center justify-center gap-6">
          <a href="https://github.com/vsapiens" target="_blank" rel="noopener" class="flex items-center gap-2 text-text-secondary hover:text-accent-purple transition-colors">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M12 0C5.37 0 0 5.37 0 12c0 5.31 3.435 9.795 8.205 11.385.6.105.825-.255.825-.57 0-.285-.015-1.23-.015-2.235-3.015.555-3.795-.735-4.035-1.41-.135-.345-.72-1.41-1.23-1.695-.42-.225-1.02-.78-.015-.795.945-.015 1.62.87 1.845 1.23 1.08 1.815 2.805 1.305 3.495.99.105-.78.42-1.305.765-1.605-2.67-.3-5.46-1.335-5.46-5.925 0-1.305.465-2.385 1.23-3.225-.12-.3-.54-1.53.12-3.18 0 0 1.005-.315 3.3 1.23.96-.27 1.98-.405 3-.405s2.04.135 3 .405c2.295-1.56 3.3-1.23 3.3-1.23.66 1.65.24 2.88.12 3.18.765.84 1.23 1.905 1.23 3.225 0 4.605-2.805 5.625-5.475 5.925.435.375.81 1.095.81 2.22 0 1.605-.015 2.895-.015 3.3 0 .315.225.69.825.57A12.02 12.02 0 0024 12c0-6.63-5.37-12-12-12z"/></svg>
            GitHub
          </a>
          <a href="https://www.linkedin.com/in/erickfgonzalez/" target="_blank" rel="noopener" class="flex items-center gap-2 text-text-secondary hover:text-accent-purple transition-colors">
            <svg class="w-5 h-5" fill="currentColor" viewBox="0 0 24 24"><path d="M20.447 20.452h-3.554v-5.569c0-1.328-.027-3.037-1.852-3.037-1.853 0-2.136 1.445-2.136 2.939v5.667H9.351V9h3.414v1.561h.046c.477-.9 1.637-1.85 3.37-1.85 3.601 0 4.267 2.37 4.267 5.455v6.286zM5.337 7.433a2.062 2.062 0 01-2.063-2.065 2.064 2.064 0 112.063 2.065zm1.782 13.019H3.555V9h3.564v11.452zM22.225 0H1.771C.792 0 0 .774 0 1.729v20.542C0 23.227.792 24 1.771 24h20.451C23.2 24 24 23.227 24 22.271V1.729C24 .774 23.2 0 22.222 0h.003z"/></svg>
            LinkedIn
          </a>
        </div>
      </div>
    </div>
  </section>
</BaseLayout>
```
> **Note:** User must replace `YOUR_FORM_ID` with their actual Formspree form ID (free at formspree.io).

**Step 2: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/contact` page shows form and social links.

**Step 3: Commit**

```bash
git add -A
git commit -m "feat: add contact page with form and social links"
```

---

### Task 9: Resume Page

**Files:**
- Create: `src/pages/resume.astro`
- Create: `public/resume.pdf` (placeholder)

**Step 1: Create resume page**

`src/pages/resume.astro`:
```astro
---
import BaseLayout from '../layouts/BaseLayout.astro';
---

<BaseLayout title="Resume" description="Erick González's resume — Performance Engineer, Backend Dev, AI Builder.">
  <section class="py-20">
    <div class="max-w-4xl mx-auto px-4 sm:px-6 lg:px-8">
      <div class="flex flex-col sm:flex-row sm:items-center sm:justify-between mb-2">
        <h1 class="text-3xl font-bold text-text-heading font-mono">
          <span class="text-accent-purple">~/</span>resume
        </h1>
        <a
          href="/resume.pdf"
          download
          class="mt-4 sm:mt-0 inline-flex items-center gap-2 px-5 py-2.5 bg-accent-purple text-white rounded-lg hover:bg-accent-purple-dim transition-colors font-medium text-sm"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M12 10v6m0 0l-3-3m3 3l3-3m2 8H7a2 2 0 01-2-2V5a2 2 0 012-2h5.586a1 1 0 01.707.293l5.414 5.414a1 1 0 01.293.707V19a2 2 0 01-2 2z" />
          </svg>
          Download PDF
        </a>
      </div>
      <div class="h-px bg-gradient-to-r from-accent-purple/50 to-transparent mb-8"></div>

      <!-- PDF Viewer -->
      <div class="bg-bg-elevated border border-white/5 rounded-lg overflow-hidden">
        <object
          data="/resume.pdf"
          type="application/pdf"
          class="w-full"
          style="height: 80vh;"
        >
          <div class="flex flex-col items-center justify-center py-20 text-center">
            <p class="text-text-secondary mb-4">Unable to display PDF in browser.</p>
            <a
              href="/resume.pdf"
              download
              class="px-5 py-2.5 bg-accent-purple text-white rounded-lg hover:bg-accent-purple-dim transition-colors font-medium text-sm"
            >
              Download Resume
            </a>
          </div>
        </object>
      </div>
    </div>
  </section>
</BaseLayout>
```

**Step 2: Create placeholder PDF note**

Create `public/resume.pdf` — the user should replace this with their actual resume PDF. For now, create a `.gitkeep` to remind them:

```bash
echo "Replace this file with your actual resume PDF." > public/RESUME_PLACEHOLDER.txt
```

**Step 3: Verify build**

```bash
npm run build && npm run dev
```
Expected: `/resume` page shows PDF embed area and download button.

**Step 4: Commit**

```bash
git add -A
git commit -m "feat: add resume page with PDF viewer and download"
```

---

### Task 10: GitHub Actions Deployment

**Files:**
- Create: `.github/workflows/deploy.yml`

**Step 1: Create deployment workflow**

`.github/workflows/deploy.yml`:
```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]
  schedule:
    - cron: '0 0 * * 0'  # Weekly rebuild (refreshes GitHub projects)
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: pages
  cancel-in-progress: true

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - uses: actions/setup-node@v4
        with:
          node-version: 20
          cache: npm

      - run: npm ci
      - run: npm run build

      - uses: actions/upload-pages-artifact@v3
        with:
          path: dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - id: deployment
        uses: actions/deploy-pages@v4
```

**Step 2: Update astro.config.mjs for GitHub Pages output**

Ensure `astro.config.mjs` has `output: 'static'` (default) — no changes needed since Astro defaults to static.

**Step 3: Commit**

```bash
git add -A
git commit -m "ci: add GitHub Actions workflow for Pages deployment"
```

---

### Task 11: Create GitHub Repo & Initial Push

**Step 1: Create the GitHub repo**

```bash
cd /c/Users/erick/Documents/vsapiens.github.io
gh repo create vsapiens.github.io --public --source=. --push
```

**Step 2: Enable GitHub Pages**

```bash
gh api repos/vsapiens/vsapiens.github.io/pages -X POST -f build_type=workflow
```

**Step 3: Verify deployment**

```bash
gh run list --limit 1
```
Expected: GitHub Actions workflow is running. Once complete, site is live at `https://vsapiens.github.io`.

**Step 4: Verify site is live**

```bash
gh run watch
```
Expected: Workflow completes successfully. Visit `https://vsapiens.github.io` to verify.

---

### Task 12: Post-Launch — User Data Population

**No code changes — user action items:**

1. Replace placeholder data in `src/data/experience.json` with actual work history
2. Replace placeholder data in `src/data/education.json` with actual education
3. Replace placeholder data in `src/data/certifications.json` with actual certs
4. Replace `public/RESUME_PLACEHOLDER.txt` with actual `public/resume.pdf`
5. Update `src/data/external-posts.json` with real external blog posts
6. Register at [formspree.io](https://formspree.io), create a form, and replace `YOUR_FORM_ID` in `src/pages/contact.astro`
7. Update email in footer if `erick@vsapiens.dev` is not correct
8. Commit and push — site rebuilds automatically
