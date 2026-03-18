<template>
  <div class="case-study">
    <NuxtLink to="/" class="back-link">← Back to Portfolio</NuxtLink>
    <!-- Hero -->
    <header class="hero">
      <h1>Building a Bidirectional Design System Pipeline</h1>
      <p class="hero-subtitle">From Shipping Code to Figma and Back</p>
      <div class="meta-chips">
        <span class="chip">Role: Design Engineer</span>
        <span class="chip">Timeline: 1 day</span>
        <span class="chip">Stack: Next.js 16, React 19, Tailwind CSS, Figma, Tokens Studio, Figma MCP, Claude Code</span>
      </div>
    </header>

    <!-- Overview -->
    <section class="section">
      <h2>Overview</h2>
      <p>
        Extrasensory Studio is a live product site with two shipped applications (FOAM and VEX), built in Next.js with a mature token system in code. The design system existed entirely in CSS and JavaScript — no Figma representation, no shared design language for non-code contexts.
      </p>
      <p>
        This case study documents building a full bidirectional pipeline between the codebase and Figma, using the modern agentic design stack: Tokens Studio for token sync, Figma MCP for design-to-code transfer, and Claude Code as the orchestration layer.
      </p>
    </section>

    <!-- The Challenge -->
    <section class="section">
      <h2>The Challenge</h2>
      <p>The site had a well-structured token system spread across three files:</p>
      <ul class="styled-list">
        <li><code>design-tokens.js</code> — JavaScript exports for React components</li>
        <li><code>globals.css</code> — CSS custom properties with dark/light mode support</li>
        <li><code>tailwind.config.js</code> — Semantic Tailwind classes mapped to CSS variables</li>
      </ul>
      <p>All text colors were WCAG AA compliant. Dark mode was default with a class-based light mode toggle. The tokens were consistent and production-tested.</p>
      <div class="callout">
        <strong>What was missing:</strong> Any of this in Figma. No Variables, no components, no shared language between design exploration and production code. Changes in one context couldn't flow to the other.
      </div>
    </section>

    <!-- Process -->
    <section class="section">
      <h2>Process</h2>

      <div class="process-step">

        <div class="step-content">
          <h3>Token Extraction</h3>
          <p>Pulled every design decision from the codebase into structured DTCG (W3C Design Tokens Community Group) format:</p>
          <ul class="styled-list">
            <li><strong>32 primitive colors</strong> — neutral scale, brand accent (#3300FF), secondary lime (#ccff33)</li>
            <li><strong>22 semantic tokens per theme</strong> — backgrounds, text, borders, accents, focus states</li>
            <li><strong>13 spacing values</strong> — 4px to 96px</li>
            <li><strong>8 border radius values</strong> — 2px to 9999px</li>
            <li><strong>11 font sizes</strong> — 12px to 60px</li>
            <li><strong>3 font families</strong> — Inter (body), SF Mono (code), Bitcount Grid Single (display)</li>
            <li><strong>3 shadow definitions</strong> — with theme-aware opacity</li>
          </ul>
          <p>Split into three files by concern: <code>primitives.tokens.json</code>, <code>dark.tokens.json</code>, <code>light.tokens.json</code>. Each file becomes a token set on import.</p>
          <div class="callout">
            <strong>Key decision:</strong> Semantic tokens (dark/light) use direct hex values, not cross-file references. Tokens Studio doesn't resolve references across sets during import. This was discovered through trial and error — the documentation doesn't surface this limitation clearly.
          </div>
          <img src="/images/design-tokens.png" alt="Tokens Studio showing 131 design tokens across primitives, dark, and light sets" class="section-image" />
        </div>
      </div>

      <div class="process-step">

        <div class="step-content">
          <h3>Tokens Studio Configuration</h3>
          <p>Imported the DTCG folder into Tokens Studio Companion. The import was straightforward once the format was correct, but the <strong>theme configuration</strong> required specific setup:</p>
          <p>Created a Theme Group with two options:</p>
          <ul class="styled-list">
            <li><strong>Dark:</strong> primitives = Source, dark = Enabled, light = Disabled</li>
            <li><strong>Light:</strong> primitives = Source, light = Enabled, dark = Disabled</li>
          </ul>
        </div>
      </div>

      <div class="process-step">

        <div class="step-content">
          <h3>Figma Variables</h3>
          <p>Tokens Studio pushed color variables into Figma with Dark and Light modes automatically. Number variables for spacing, radius, and typography had to be created manually — the plugin only handles colors. Once set up, every component responds to theme switching without additional work.</p>
        </div>
      </div>

      <div class="process-step">

        <div class="step-content">
          <h3>Figma MCP + Site Capture</h3>
          <p>Connected Claude Code to Figma via MCP:</p>
          <pre class="code-block"><code>claude mcp add --transport http --scope user figma "https://mcp.figma.com/mcp"</code></pre>
          <p>Used <code>generate_figma_design</code> to capture the live site directly into Figma. The tool generates a capture ID, injects a capture script into the page, opens the page with hash parameters that trigger the capture, and polls until the capture serializes and uploads to Figma.</p>
          <p>The FOAM product page and VEX product page were captured as separate pages in the same Figma file. Each capture produces editable layers — real frames, text, auto layout, not flattened images.</p>
        </div>
      </div>

      <div class="process-step">

        <div class="step-content">
          <h3>Component Conversion</h3>
          <p>Converted captured layers into Figma Components with Variable bindings:</p>
          <div class="variants-table">
            <table>
              <thead>
                <tr>
                  <th>Variant</th>
                  <th>Fill</th>
                  <th>Border</th>
                  <th>Text</th>
                </tr>
              </thead>
              <tbody>
                <tr>
                  <td>Primary</td>
                  <td><code>accent/default</code></td>
                  <td>none</td>
                  <td><code>accent/text</code></td>
                </tr>
                <tr>
                  <td>Secondary</td>
                  <td>none</td>
                  <td>2px <code>accent/default</code></td>
                  <td><code>text/primary</code></td>
                </tr>
                <tr>
                  <td>Ghost</td>
                  <td>none</td>
                  <td>1px <code>border/subtle</code></td>
                  <td><code>text/secondary</code></td>
                </tr>
              </tbody>
            </table>
          </div>
          <p>All bound to Variables, so switching between Dark and Light modes updates every instance automatically.</p>
          <p><strong>Card</strong>, <strong>Card Featured</strong>, <strong>Header</strong>, <strong>Tab Bar</strong> — same process. Select the layer, create component, swap hardcoded hex values for Variable references, bind radius and padding to number variables.</p>
          <img src="/images/figma-overview.png" alt="Figma file showing captured FOAM page with component layers and variable bindings" class="section-image" />
        </div>
      </div>

      <div class="process-step">

        <div class="step-content">
          <h3>Design System Rules</h3>
          <p>Generated a <code>.figma/design-system-rules.md</code> file using MCP's <code>create_design_system_rules</code> tool. This tells any AI agent working with the codebase:</p>
          <ul class="styled-list">
            <li>Use CSS component classes (<code>.btn-primary</code>, <code>.card</code>) for patterns</li>
            <li>Use Tailwind semantic classes (<code>bg-surface-card</code>, <code>text-content-primary</code>) for one-offs</li>
            <li>Never hardcode colors — always use token variables</li>
            <li>Dark mode is default</li>
          </ul>
        </div>
      </div>
    </section>

    <!-- Architecture Diagram -->
    <section class="section">
      <h2>Architecture</h2>
      <div class="architecture-diagram">
        <div class="arch-row">
          <div class="arch-col">
            <div class="arch-label">Code (Source of Truth)</div>
            <div class="arch-box code-box">
              <span>design-tokens.js</span>
              <span>globals.css</span>
              <span>tailwind.config.js</span>
            </div>
          </div>
          <div class="arch-arrow">
            <span class="arrow-label">DTCG JSON</span>
            <span class="arrow-icon">→</span>
          </div>
          <div class="arch-box middle-box">
            <span>Tokens Studio</span>
            <span class="arch-detail">(dark / light)</span>
          </div>
          <div class="arch-arrow">
            <span class="arrow-icon">→</span>
          </div>
          <div class="arch-col">
            <div class="arch-label">Figma</div>
            <div class="arch-box figma-box">
              <span>Figma Variables</span>
              <span class="arch-detail">(dark / light)</span>
            </div>
          </div>
        </div>

        <div class="arch-row">
          <div class="arch-box code-box">
            <span>src/components/</span>
          </div>
          <div class="arch-arrow">
            <span class="arrow-label">MCP capture</span>
            <span class="arrow-icon">→</span>
          </div>
          <div class="arch-box figma-box wide-box">
            <span>Figma Layers</span>
            <span class="arch-detail">Button (3 variants) / Card / Header / Tab Bar</span>
          </div>
        </div>

        <div class="arch-row">
          <div class="arch-box figma-box">
            <span>Figma selection</span>
          </div>
          <div class="arch-arrow">
            <span class="arrow-label">MCP read</span>
            <span class="arrow-icon">→</span>
          </div>
          <div class="arch-box middle-box">
            <span>Claude Code</span>
            <span class="arch-detail">(uses design system rules)</span>
          </div>
          <div class="arch-arrow">
            <span class="arrow-icon">→</span>
          </div>
          <div class="arch-box code-box">
            <span>Production code</span>
          </div>
        </div>

        <div class="arch-bidirectional">
          <span>Bidirectional flow:</span> Code changes → re-capture → Figma updated <span class="arch-separator">|</span> Figma designs → get_design_context → code generated using project conventions
        </div>
      </div>
    </section>

  </div>
</template>

<script setup lang="ts">
useHead({
  title: 'Design System Case Study — Erik Parr',
  meta: [
    { name: 'description', content: 'Building a bidirectional design system pipeline between a Next.js codebase and Figma using Tokens Studio, Figma MCP, and Claude Code.' }
  ]
})
</script>

<style scoped>
.case-study {
  max-width: 800px;
  margin: 0 auto;
  padding: 4rem 1.5rem;
  font-family: 'Nunito Sans', sans-serif;
  color: var(--color-text);
}

/* Back link */
.back-link {
  display: inline-block;
  margin-bottom: 2rem;
  color: #888;
  text-decoration: none;
  font-size: 0.9rem;
  transition: color 0.2s ease;
}

.back-link:hover {
  color: var(--color-text);
}

/* Hero */
.hero {
  text-align: center;
  margin-bottom: 4rem;
  padding-bottom: 3rem;
  border-bottom: 1px solid var(--color-border);
}

.hero h1 {
  font-size: 2.4rem;
  font-weight: 300;
  line-height: 1.2;
  margin-bottom: 0.75rem;
}

.hero-subtitle {
  font-size: 1.2rem;
  color: #888;
  margin-bottom: 1.5rem;
}

.meta-chips {
  display: flex;
  flex-wrap: wrap;
  justify-content: center;
  gap: 0.5rem;
}

.chip {
  display: inline-block;
  padding: 0.3rem 0.75rem;
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 4px;
  font-size: 0.85rem;
  color: #aaa;
}

/* Sections */
.section {
  margin-bottom: 3.5rem;
  padding-bottom: 3rem;
  border-bottom: 1px solid var(--color-border);
}

.section h2 {
  font-size: 1.6rem;
  font-weight: 300;
  margin-bottom: 1.25rem;
}

.section p {
  font-size: 1.05rem;
  line-height: 1.7;
  margin-bottom: 1rem;
}

/* Lists */
.styled-list {
  list-style: none;
  padding: 0;
  margin-bottom: 1.25rem;
}

.styled-list li {
  position: relative;
  padding-left: 1.25rem;
  margin-bottom: 0.5rem;
  font-size: 1rem;
  line-height: 1.6;
}

.styled-list li::before {
  content: '—';
  position: absolute;
  left: 0;
  color: #666;
}

/* Callout */
.callout {
  background: var(--color-surface);
  border-left: 3px solid var(--color-border);
  padding: 1rem 1.25rem;
  margin: 1.25rem 0;
  border-radius: 0 4px 4px 0;
  font-size: 0.95rem;
  line-height: 1.6;
}

/* Code */
code {
  background: var(--color-surface);
  padding: 0.15rem 0.4rem;
  border-radius: 3px;
  font-size: 0.9em;
  font-family: 'SF Mono', 'Fira Code', monospace;
}

.code-block {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 4px;
  padding: 1rem 1.25rem;
  overflow-x: auto;
  margin: 1rem 0;
}

.code-block code {
  background: none;
  padding: 0;
  font-size: 0.85rem;
}

/* Process Steps */
.process-step {
  display: flex;
  gap: 1.25rem;
  margin-bottom: 2.5rem;
}

.step-content {
  flex: 1;
}

.step-content h3 {
  font-size: 1.2rem;
  font-weight: 400;
  margin-bottom: 0.75rem;
}

/* Section Images */
.section-image {
  width: 100%;
  border-radius: 6px;
  border: 1px solid var(--color-border);
  margin-top: 1.25rem;
}

/* Architecture Diagram */
.architecture-diagram {
  background: var(--color-surface);
  border: 1px solid var(--color-border);
  border-radius: 6px;
  padding: 2rem 1.5rem;
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}

.arch-row {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  flex-wrap: wrap;
  justify-content: center;
}

.arch-col {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.25rem;
}

.arch-label {
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
  color: #666;
}

.arch-box {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.2rem;
  padding: 0.6rem 1rem;
  border: 1px solid var(--color-border);
  border-radius: 4px;
  font-size: 0.85rem;
  min-width: 120px;
  text-align: center;
}

.code-box {
  border-color: var(--color-secondary);
}

.figma-box {
  border-color: #a259ff;
}

.middle-box {
  border-color: #666;
}

.wide-box {
  min-width: 200px;
}

.arch-detail {
  font-size: 0.75rem;
  color: #888;
}

.arch-arrow {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.15rem;
}

.arrow-label {
  font-size: 0.7rem;
  color: #888;
}

.arrow-icon {
  font-size: 1.2rem;
  color: #666;
}

.arch-bidirectional {
  font-size: 0.85rem;
  color: #888;
  text-align: center;
  padding-top: 1rem;
  border-top: 1px solid var(--color-border);
  line-height: 1.6;
}

.arch-separator {
  margin: 0 0.5rem;
  color: #444;
}

/* Tables */
.variants-table {
  overflow-x: auto;
  margin: 1rem 0;
}

table {
  width: 100%;
  border-collapse: collapse;
  font-size: 0.95rem;
}

th, td {
  padding: 0.6rem 1rem;
  text-align: left;
  border-bottom: 1px solid var(--color-border);
}

th {
  font-weight: 400;
  color: #888;
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.03em;
}

/* Responsive */
@media (max-width: 768px) {
  .case-study {
    padding: 2rem 1rem;
  }

  .hero h1 {
    font-size: 1.8rem;
  }

  .meta-chips {
    flex-direction: column;
    align-items: center;
  }

  .process-step {
    flex-direction: column;
    gap: 0.75rem;
  }

  .step-number {
    align-self: flex-start;
  }

  .arch-row {
    flex-direction: column;
  }

  .arch-arrow {
    transform: rotate(90deg);
  }
}
</style>
