# CLAUDE.md — laurennowak.ai

## Always Do First
- Read this file completely before touching any code
- Invoke the `/frontend-design` command before writing any frontend code, every session

## Project Stack
- React (single-file .jsx components)
- Deployed on Vercel
- Domain: laurennowak.ai (DNS via Namecheap)
- Node.js required for local dev
- No separate CSS files — all styles are inline or in-component

## Design System

### Typography
- Font stack: `-apple-system, BlinkMacSystemFont, 'SF Pro Text', 'SF Pro Display', sans-serif`
- Monospace: `'SF Mono', SFMono-Regular, ui-monospace, monospace`
- All text is **lowercase** — no sentence case, no title case, no capitals except proper nouns and "I"
- Body text: 15-17px, line-height 1.7-1.9
- Headings: 22-28px, font-weight 500, letter-spacing -0.02em
- Labels/tags: 10-11px, font-family mono, letter-spacing 0.06-0.12em, uppercase (exception to lowercase rule — labels only)

### Colors
- Background: `#0a0a0a`
- Surface: `#111111`
- Border: `#1e1e1e`
- Border light: `#2a2a2a`
- Text primary: `#f0f0f0`
- Text muted: `#666666`
- Text dim: `#444444`
- Accent: `#2563EB` (blue — this is the only accent color)
- Warning: `#ca8a04`
- Danger: `#dc2626`
- Success: `#16a34a`
- Tag background: `#1a1a1a`

### Spacing
- Max content width: 680px, centered
- Page padding: 48px top/bottom, 24px left/right
- Section spacing: 32-48px between sections
- Element spacing: 12-16px between related elements

### Component Patterns
- Inputs and textareas: transparent background, border `1px solid #1e1e1e`, border-radius 6-8px, no outline on focus
- Buttons: border-radius 6px, font-weight 500 for primary, no font-weight for secondary
- Primary button: background `#2563EB`, white text
- Secondary button: transparent background, border `1px solid #1e1e1e`, muted text
- Cards/sections: background `#111111`, border `1px solid #1e1e1e`, border-radius 8px
- Loading states: pulsing dot animation, monospace text, lowercase

### Icons
- Use lucide-react only
- Size: 11-14px for inline, 18-20px for standalone
- strokeWidth: 1.8 always
- No emojis anywhere

## Voice and Copy Rules
- All copy is lowercase (matches the design system)
- No em dashes anywhere — use periods or commas instead
- No filler phrases: "doing the heavy lifting," "the real question is," "here's the thing," "what most people miss"
- Short sentences. Direct. Let the facts do the work.
- Section labels are uppercase mono (the only uppercase exception)

## Build Patterns

### API Calls (Anthropic)
- Model: `claude-sonnet-4-6` (current latest — update if a newer model is confirmed)
- System prompt placement: embed the system prompt inside the user message content, not as a separate `system` parameter. This is required for compatibility with how these artifact-style API calls are structured. Do not change this pattern.
  ```js
  // correct
  messages: [{ role: "user", content: `SYSTEM: ${systemPrompt}\n\nUSER: ${userInput}` }]

  // incorrect — do not use
  system: systemPrompt,
  messages: [{ role: "user", content: userInput }]
  ```
- Always include retry logic with exponential backoff (3 attempts, 1s delay * attempt number)
- Always sanitize input before sending (strip non-ASCII, smart quotes, em dashes)
- Always parse JSON with `.replace(/```json/g, "").replace(/```/g, "").trim()` before JSON.parse
- max_tokens: 1500 for structured output, 2500 for longer generation

### Word Doc Export
- Use in-browser DOCX generation via ZIP construction (no external library)
- The export pattern uses raw XML written into a ZIP structure. Key implementation notes:
  - Build the docx file manually using JSZip or equivalent in-browser zip construction
  - Include `word/document.xml`, `word/_rels/document.xml.rels`, `[Content_Types].xml`, and `_rels/.rels`
  - Serialize content as Open XML — paragraphs as `<w:p>`, runs as `<w:r>`, text as `<w:t>`
  - Trigger download via `URL.createObjectURL(blob)` with `application/vnd.openxmlformats-officedocument.wordprocessingml.document`
- Button label: "export to word doc"
- Download filename: descriptive kebab-case (e.g. "brand-check-report.docx")

### Loading States
- Pulsing dot: `width 8px, height 8px, border-radius 50%, background #2563EB, animation pulse`
- Pulse keyframe: `0%, 100% { opacity: 0.4 } 50% { opacity: 1 }`
- Label: monospace, 12px, lowercase, ends with "..."

### Transitions
- Never use `transition-all`
- Always specify the exact property: e.g. `transition: opacity 0.2s ease`, `transition: border-color 0.15s ease`

### Example Inputs
- Every tool must have at least one "load example" button
- Examples should be realistic and specific, not generic placeholders
- Example button: top-right of input area, secondary button style

## Existing Builds (do not change aesthetics or layout of these — logic fixes are fine)
1. `multi-agent-orchestrator-v6.jsx` — marketing brief pipeline, 4 agents
2. `marketing-ops-audit-v2.jsx` — ops risk audit, structured JSON output
3. `brand-check-v2.jsx` — brand compliance audit, pptx/pdf upload

## Hard Rules
- Never use emojis
- Never use em dashes
- Never capitalize body copy
- Never use Inter, Roboto, or Arial as the primary font
- Never use default Tailwind blue/indigo palette
- Never use a separate system parameter in Anthropic API calls (see API Calls section)
- Never add sections or features not explicitly requested
- Never use `transition-all` — always specify the exact CSS property being transitioned
- Always match the existing dark theme when building new components
- Always test that API calls follow the retry + sanitize pattern
