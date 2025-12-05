<template>
  <main class="app-shell">
    <section class="card">
      <p class="eyebrow">Security sandbox</p>
      <h1>DOM-based XSS Playground</h1>
      <p class="lede">
        Experiment with how unsafe DOM insertion turns raw user input into executable markup. This component intentionally
        reflects the vulnerability for educational purposes--do not use this pattern in production.
      </p>

      <label class="field" for="user-input">
        <span class="field__label">User-controlled payload</span>
        <textarea
          id="user-input"
          v-model="userInput"
          rows="4"
          placeholder="Try a bold tag, a script, or an image with onerror..."
        ></textarea>
      </label>

      <div class="actions">
        <button type="button" @click="inject">Inject markup</button>
        <span class="hint">Example: &lt;img src onerror=&quot;alert('xss')&quot;&gt;</span>
      </div>

      <section class="output" aria-live="polite">
        <div class="output__title">
          <h2>Rendered Output</h2>
          <span class="pill pill--danger">Vulnerable</span>
        </div>
        <div ref="outputBox" class="box"></div>
      </section>
    </section>
  </main>
</template>

<script>
export default {
  data() {
    return {
      userInput: ""
    }
  },
  methods: {
    inject() {
      this.$refs.outputBox.innerHTML = this.userInput
    }
  }
}
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600&display=swap");

:root {
  --ink: #0f172a;
  --muted: #475569;
  --bg: linear-gradient(135deg, #0b1221, #1f2a44 60%, #2b1158);
  --card: rgba(255, 255, 255, 0.06);
  --border: rgba(255, 255, 255, 0.1);
  --accent: #f97316;
  --danger: #ef4444;
}

body {
  margin: 0;
  min-height: 100vh;
  font-family: "Space Grotesk", "Trebuchet MS", sans-serif;
  background-image: var(--bg);
  color: #f8fafc;
}

.app-shell {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  padding: 3rem 1.5rem;
}

.card {
  width: min(720px, 100%);
  background: var(--card);
  border: 1px solid var(--border);
  border-radius: 1.5rem;
  padding: 2.5rem;
  box-shadow: 0 30px 80px rgba(10, 9, 30, 0.45);
  backdrop-filter: blur(18px);
}

.eyebrow {
  text-transform: uppercase;
  letter-spacing: 0.35em;
  font-size: 0.75rem;
  margin: 0 0 1rem;
  color: var(--muted);
}

h1 {
  margin: 0 0 1rem;
  font-size: clamp(2rem, 4vw, 2.75rem);
  color: #f1f5f9;
}

.lede {
  margin: 0 0 2rem;
  color: var(--muted);
  line-height: 1.6;
}

.field {
  display: flex;
  flex-direction: column;
  gap: 0.65rem;
}

.field__label {
  font-size: 0.85rem;
  text-transform: uppercase;
  letter-spacing: 0.12em;
  color: var(--muted);
}

textarea {
  resize: vertical;
  min-height: 120px;
  border-radius: 1rem;
  border: 1px solid var(--border);
  background: rgba(15, 23, 42, 0.35);
  color: #f8fafc;
  padding: 1rem 1.2rem;
  font-family: inherit;
  font-size: 1rem;
  transition: border 0.2s ease, box-shadow 0.2s ease;
}

textarea:focus {
  outline: none;
  border-color: var(--accent);
  box-shadow: 0 0 0 3px rgba(249, 115, 22, 0.25);
}

.actions {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 1rem;
  margin: 1.75rem 0 2rem;
}

button {
  background: var(--accent);
  border: none;
  color: #0b0d14;
  font-weight: 600;
  letter-spacing: 0.04em;
  border-radius: 999px;
  padding: 0.95rem 2.4rem;
  cursor: pointer;
  transition: transform 0.15s ease, box-shadow 0.15s ease;
}

button:hover {
  transform: translateY(-1px);
  box-shadow: 0 12px 30px rgba(249, 115, 22, 0.35);
}

.hint {
  color: var(--muted);
  font-size: 0.9rem;
}

.output {
  background: rgba(15, 23, 42, 0.45);
  border-radius: 1.25rem;
  border: 1px dashed rgba(239, 68, 68, 0.4);
  padding: 1.75rem;
}

.output__title {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  margin-bottom: 1rem;
}

.output__title h2 {
  margin: 0;
  font-size: 1.1rem;
  letter-spacing: 0.04em;
  text-transform: uppercase;
}

.pill {
  padding: 0.25rem 0.75rem;
  border-radius: 999px;
  font-size: 0.75rem;
  letter-spacing: 0.08em;
  text-transform: uppercase;
}

.pill--danger {
  background: rgba(239, 68, 68, 0.15);
  color: var(--danger);
  border: 1px solid rgba(239, 68, 68, 0.4);
}

.box {
  min-height: 140px;
  border-radius: 1rem;
  border: 1px solid rgba(239, 68, 68, 0.25);
  padding: 1.25rem;
  background: rgba(15, 23, 42, 0.35);
  box-shadow: inset 0 0 0 1px rgba(15, 23, 42, 0.8);
}

@media (max-width: 640px) {
  .card {
    padding: 1.75rem;
  }

  .actions {
    flex-direction: column;
    align-items: stretch;
  }

  button {
    width: 100%;
    text-align: center;
  }
}
</style>
