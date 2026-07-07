markdown# Mirka Meal Tracker

A single-file nutrition tracking app built for a specific user profile (perimenopause, CV risk, migraine prophylaxis). Runs entirely in the browser — no backend, no database, no dependencies.

## Features

- **Food logging** — text description, product lookup, or AI photo analysis (via Anthropic API)
- **Supplement tracking** — daily checklist with per-capsule nutrient content, auto-calculated into daily totals
- **Barcode scanner** — camera or manual EAN entry, pulls data from Open Food Facts (3M+ products)
- **Micronutrient dashboard** — daily traffic-light view + 7-day weekly average report
- **Quick add** — repeat yesterday's meals or add frequent items in one tap
- **AI day review** — real-time assessment of what to eat/supplement before end of day
- **AI weekly recommendations** — personalized advice based on 7-day intake history
- **Weight trend** — chart with linear regression and goal projection
- **Offline mode** — pending meals queue, auto-prompt to recalculate when back online
- **Product library** — reusable products with AI label scan or barcode lookup
- **Export/import** — full JSON backup of all data

## Tech

- Single `index.html` file (~180 KB), no build step
- Vanilla JS, CSS variables, localStorage
- Anthropic Claude API for AI features (user provides own API key)
- Open Food Facts API for barcode lookup
- Mobile-first, dark mode, designed for iPhone Safari (Add to Home Screen)

## Setup

1. Clone the repo
2. Enable GitHub Pages (Settings → Pages → main branch / root)
3. Open the deployed URL on your phone
4. Go to Settings in the app → enter your Anthropic API key

## License

Personal project. Not intended for public distribution.
