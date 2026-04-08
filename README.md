# AI EVAL — Multi-Provider AI Testing Platform

> **A zero-dependency, single-file browser tool for testing, benchmarking, and comparing AI APIs across 7 major providers.**

[![Live Demo](https://img.shields.io/badge/Live%20Demo-ai--api--test.netlify.app-00d4ff?style=flat-square)](https://ai-api-test.netlify.app/)
[![Version](https://img.shields.io/badge/version-5.0-00ff88?style=flat-square)](#)
[![License](https://img.shields.io/badge/license-MIT-orange?style=flat-square)](#license)
[![No Dependencies](https://img.shields.io/badge/dependencies-none-green?style=flat-square)](#)

---

## What Is AI EVAL?

AI EVAL is a single HTML file that runs entirely in your browser. It lets you connect to any of 7 AI providers using your own API keys, run chat conversations, measure performance, stress-test rate limits, and compare models — all without a backend server, build step, or installation.

Your API keys are stored **only in browser memory** for the duration of your session. They are never sent to any server other than the AI provider's own API, and they are cleared the moment you close or refresh the tab.

---

## Features

### Provider Support
AI EVAL connects to 7 providers out of the box, each with pre-configured model lists and provider-specific request formatting:

| Provider | Key Format | Quota Type | Models Included |
|---|---|---|---|
| **OpenAI** | `sk-…` | Usage-based | gpt-4o, gpt-4o-mini, gpt-4-turbo, gpt-3.5-turbo |
| **Claude (Anthropic)** | `sk-ant-…` | Usage-based | claude-opus-4-5, claude-sonnet-4-5, claude-3-5-haiku, claude-3-opus |
| **Gemini (Google)** | `AIza…` | Quota-based | gemini-2.0-flash, gemini-2.0-flash-lite, gemini-1.5-pro, gemini-1.5-flash |
| **DeepSeek** | `sk-…` | Usage-based | deepseek-chat, deepseek-reasoner |
| **Groq** | `gsk_…` | Rate-limited | llama-3.1-8b, llama-3.3-70b, llama-4-scout, llama-4-maverick, qwen3-32b, gpt-oss-120b |
| **OpenRouter** | `sk-or-…` | Usage-based | gpt-4o, claude-3.5-sonnet, gemini-2.0-flash, llama-3.1-70b, mistral-7b, deepseek-r1 |
| **Together AI** | `your-key…` | Usage-based | Llama-3-70B, Llama-3-8B, Mixtral-8x7B, Qwen2.5-72B, DeepSeek-R1 |

### Chat Mode
A persistent, multi-turn conversational interface with full message history. Supports system prompts and tracks cumulative token usage (input, output, and total) across the entire conversation. Individual messages can be copied. Chat history can be exported. The typing indicator and bubble-style UI make it easy to read back through a session.

### Run API Test (Single-Shot)
Sends a single request and returns a full metrics breakdown: latency in milliseconds, exact token counts (input and output), tokens-per-second, finish reason, and the token source (exact from API, or estimated). Use this for precise, one-off measurements.

### Token Test
Sends a sequence of progressively larger prompts (configurable number of steps, 2–10) to probe how a model handles increasing context sizes. Reports estimated input size, actual tokens in/out, latency, and status for every step. Summarizes the max safe token count, average output tokens, and average latency across all steps.

### Stress Test
Fires a configurable number of sequential requests (3–20) with a configurable delay between them (0–3000 ms) to detect rate limits, latency degradation, and failure patterns under load. Each request is shown individually with a status indicator (success, error, rate-limited, slow). Summarizes total successes, errors, rate limit hits (HTTP 429), and average latency.

### Session Stats
A live dashboard that aggregates all requests made in the current session: total successes, total errors, average latency, total tokens consumed, and a success-rate progress bar. Resets with the RESET button.

### Final Insights Panel
After running tests, an insights panel appears with provider-specific recommendations: best model for rapid iteration and testing, best model for production use, and a recommended strategy based on the observed success rate and latency of your session.

### Activity Log
A timestamped, scrollable log of every action taken during your session — provider selections, key saves, request starts, successes, errors, retries, and rate limit warnings. Useful for debugging unexpected behavior.

### Customer Support Panel
A contact form at the bottom of the page that lets users report bugs or send feedback. It is powered by [Formspree](https://formspree.io/) and requires configuration before it will work (see setup instructions below).

---

## Technical Highlights

- **Zero dependencies.** No npm, no bundler, no framework. One HTML file.
- **No backend.** API calls go directly from the browser to each provider's endpoint.
- **FOUC prevention.** Fonts are loaded via `<link>` with `media="print"` + `onload` swap, eliminating the 1–3 second layout flash common with `@import`.
- **Scroll performance.** The full-viewport scan-line overlay uses `will-change: transform` and `translateZ(0)` to promote it to a dedicated GPU compositing layer, preventing per-frame repaints during scroll.
- **Layout containment.** All panels use `contain: content`, which tells the browser that layout and paint changes inside a panel cannot affect anything outside it. Off-screen panels are skipped during paint entirely.
- **Retry with exponential backoff.** API calls retry up to 3 times with delays of 1s, 2s, and 4s (capped at 8s) on transient errors. Auth failures (401/403) are not retried.
- **30-second request timeout.** Every fetch is wrapped in an `AbortController` that cancels the request after 30 seconds.
- **Token estimation fallback.** When a provider does not return exact token counts, AI EVAL estimates them at 4 characters per token and marks the source as "Estimated."
- **DOM cache.** All `getElementById` lookups are cached on first access to avoid repeated DOM queries.
- **Specific CSS transitions.** All `transition` declarations target specific properties rather than `all`, preventing unnecessary style recalculation.

---

## Getting Started

### Option 1 — Use the Hosted Version

Visit [ai-api-test.netlify.app](https://ai-api-test.netlify.app/) and start immediately. No setup required for basic use.

### Option 2 — Run Locally

1. Download `ai-eval-v5.html`.
2. Open it in any modern browser (Chrome, Firefox, Edge, Safari).
3. That's it.

There is no server to start, no `npm install` to run, and no environment variables to configure. You can open the file directly from your filesystem with `file://` URLs.

### Option 3 — Deploy Your Own Copy

Upload `ai-eval-v5.html` to any static hosting service:

- **Netlify** — drag and drop the file into the Netlify dashboard.
- **GitHub Pages** — commit the file to a repository and enable Pages in the repository settings.
- **Vercel** — deploy via the Vercel CLI or connect a GitHub repository.
- **Any web server** — copy the file to your server's public directory.

> **Important:** If you deploy your own copy, you must configure Formspree for the Customer Support panel to work. See below.

---

## Configuration

### ⚠️ Required: Formspree Setup (Customer Support Panel)

The contact form at the bottom of the page uses [Formspree](https://formspree.io/) to deliver messages to your inbox. **If you fork or self-host this project, you must replace the placeholder Formspree endpoint with your own ID, or the form will silently fail.**

**How to set it up:**

1. Go to [formspree.io](https://formspree.io/) and create a free account.
2. Create a new form. Formspree will give you a form ID (e.g., `mjzgtouy`).
3. Open `ai-eval-v5.html` in a text editor and search for this line inside the `submitSupport` function:

```javascript
const resp = await fetch('https://formspree.io/f/YOUR_ID', {
  method: 'POST',
  body: fd,
  headers: { Accept: 'application/json' }
});
```

4. Replace `YOUR_ID` with your actual Formspree form ID. For example, if your ID is `mjzgtouy`:

```javascript
const resp = await fetch('https://formspree.io/f/mjzgtouy', {
  method: 'POST',
  body: fd,
  headers: { Accept: 'application/json' }
});
```

Without this change, the support form will appear to work (it shows no error until it hits the Formspree endpoint) but messages will not be delivered anywhere. **This is a required step for any self-hosted deployment.**

---

## How to Use

### Step 1 — Select a Provider
Click one of the 7 provider chips (OpenAI, Claude, Gemini, DeepSeek, Groq, OpenRouter, Together). The chip will highlight in the provider's color.

### Step 2 — Add Your API Key
Paste your API key into the key field and click **Add**. The key is stored in browser memory only and will not survive a page refresh. The connection badge in the header will change from `✕ NOT SET` to `✓ ACTIVE` when a key is successfully saved.

### Step 3 — Select a Model
Choose a model from the dropdown. The model info panel below it shows context window size, speed tier, and provider name.

### Step 4 — Adjust Parameters
Use the sliders to set **Max Output Tokens** (256–4096) and **Temperature** (0.0–2.0).

### Step 5 — Test

- **Chat** — type a message and press Send (or Ctrl/Cmd+Enter) for a conversational session with full history.
- **Run API Test** — sends your chat input as a single-shot request with full metrics output.
- **Token Test** — runs a sweep of increasing prompt sizes and reports behavior at each step.
- **Stress Test** — fires multiple requests in sequence; configure count and delay with the sliders.

---

## Security Notes

- **API keys are session-only.** They exist only in the `S.keys` JavaScript object in memory. They are not written to `localStorage`, `sessionStorage`, cookies, or any external service.
- **Keys are cleared on page close.** Refreshing or closing the tab permanently removes all stored keys.
- **Do not share your session.** The warning banner at the top of the page is there as a reminder: if you share your screen or browser tab while a key is active, it is visible to anyone watching.
- **Direct provider calls.** Every API request goes from your browser directly to the provider's API endpoint. No intermediary server sees your key or your messages.

---

## Project Structure

This project is intentionally a single file.

```
ai-eval-v5.html
├── <head>
│   ├── Font preloading (JetBrains Mono, Syne) with FOUC prevention
│   └── <style> — full CSS design system (~380 lines)
├── <body>
│   ├── Header (sticky, GPU-composited)
│   ├── Warning banner
│   ├── .main (CSS Grid: 340px left col + fluid right col)
│   │   ├── Left column
│   │   │   ├── AI Provider panel (chips, key input, model select, sliders)
│   │   │   ├── Chat panel (bubble UI, token bar, action buttons)
│   │   │   └── Session Stats panel
│   │   └── Right column
│   │       ├── Tabbed panel (API Test, Token Test, Stress Test, Log)
│   │       ├── Final Insights panel
│   │       └── Customer Support panel (Formspree)
│   └── <script> — full application logic (~800 lines)
│       ├── PROVIDERS registry (7 providers, models, request builders, parsers)
│       ├── State object (S)
│       ├── Token Intelligence (TI)
│       ├── DOM cache (g)
│       ├── Provider flow (pick, save, activate)
│       ├── API call engine (retry, backoff, timeout, token fallback)
│       ├── Chat engine (history, export, clear)
│       ├── Run API Test
│       ├── Token Test
│       ├── Stress Test
│       ├── Support form (Formspree)
│       ├── Session stats + insights
│       └── UI utilities (toast, log, tabs, keyboard shortcut)
```

---

## Adding a New Provider

To add a new provider, add an entry to the `PROVIDERS` object in the `<script>` section following this pattern:

```javascript
yourprovider: {
  name: 'Your Provider',
  hint: 'key-prefix…',          // placeholder text for the key input
  color: '#hexcolor',            // accent color for the chip
  glowCls: 'prov-yourprovider', // CSS class for the active chip state
  quotaType: 'Usage-based',     // shown in the Token Intelligence panel
  models: [
    { id: 'model-id', lbl: 'Display name', tags: ['FAST'], ctx: '128K', spd: 'Fast' },
  ],
  buildRequest(messages, model, maxTok, temp) {
    return {
      url: 'https://api.yourprovider.com/v1/chat/completions',
      headers: { 'Content-Type': 'application/json', 'Authorization': `Bearer ${this._key}` },
      body: { model, max_tokens: maxTok, temperature: temp, messages },
    };
  },
  parseResponse(d) {
    const c = d.choices?.[0], u = d.usage || {};
    return {
      text: c?.message?.content || '',
      tokIn: u.prompt_tokens || null,
      tokOut: u.completion_tokens || null,
      finish: c?.finish_reason || 'unknown',
      source: 'API (exact)',
    };
  },
},
```

Then add a chip button in the HTML:

```html
<button class="provider-chip" data-p="yourprovider" onclick="pickProvider('yourprovider')">
  ◈ Your Provider
</button>
```

And add a chip active style in the CSS:

```css
.provider-chip.prov-yourprovider {
  border-color: #hexcolor;
  color: #hexcolor;
  background: rgba(r, g, b, .09);
  box-shadow: 0 0 10px rgba(r, g, b, .22), inset 0 0 8px rgba(r, g, b, .05);
}
```

---

## Browser Compatibility

AI EVAL uses standard modern web APIs and has no polyfills. It requires a browser that supports:

- `fetch` with `AbortController`
- `async/await`
- CSS Grid and custom properties
- `performance.now()`
- `navigator.clipboard.writeText()`

This covers all evergreen browsers: Chrome 80+, Firefox 80+, Edge 80+, Safari 14+.

---

## Contributing

Contributions are welcome. Since the entire project is a single HTML file, the contribution flow is straightforward:

1. Fork this repository.
2. Edit `ai-eval-v5.html`.
3. Test your changes by opening the file locally in a browser.
4. Open a pull request with a clear description of what you changed and why.

Please do not introduce external dependencies, build steps, or frameworks. The single-file, zero-dependency nature of this project is intentional.

---

## License

MIT License. You are free to use, modify, and distribute this project for any purpose. Attribution is appreciated but not required.

---

## Acknowledgements

- [JetBrains Mono](https://www.jetbrains.com/legalforms/monospace/) — monospace font
- [Syne](https://fonts.google.com/specimen/Syne) — display font
- [Formspree](https://formspree.io/) — contact form backend
- All 7 AI providers for maintaining publicly accessible APIs
