---
layout: full
---

<section class="title-cover">
  <h1>Flujo operativo de agentes IA sobre un lienzo interactivo</h1>
</section>

<style>
.title-cover {
  position: fixed;
  inset: 0;
  display: grid;
  align-content: start;
  justify-items: start;
  padding: clamp(84px, 12vh, 120px) clamp(32px, 5vw, 70px);
  color: #8bd7ff;
  background:
    radial-gradient(circle at 28% 22%, rgba(125, 211, 252, 0.08), transparent 28%),
    radial-gradient(circle at 78% 76%, rgba(255, 178, 138, 0.08), transparent 30%),
    linear-gradient(135deg, #0b0f14 0%, #10141b 52%, #0b0f14 100%);
  overflow: hidden;
  font-family: "Cascadia Code", "JetBrains Mono", "Fira Code", Consolas, ui-monospace, monospace;
}

.title-cover::before {
  content: "";
  position: absolute;
  inset: -80px;
  background-image:
    radial-gradient(circle, rgba(139, 148, 166, 0.22) 1px, transparent 1.5px),
    linear-gradient(rgba(125, 211, 252, 0.06) 1px, transparent 1px),
    linear-gradient(90deg, rgba(125, 211, 252, 0.06) 1px, transparent 1px);
  background-size: 40px 40px, 160px 160px, 160px 160px;
  transform: rotate(-2deg);
}

.title-cover::after {
  content: "EL FLUJO";
  position: absolute;
  left: clamp(28px, 5vw, 70px);
  top: clamp(26px, 6vh, 58px);
  color: #8b94a6;
  font-size: 16px;
  font-weight: 800;
  letter-spacing: 0.32em;
}

.title-cover h1 {
  position: relative;
  max-width: 860px;
  margin: 0;
  font-size: clamp(28px, 3.2vw, 44px);
  line-height: 1.12;
  letter-spacing: 0;
  text-align: left;
  font-weight: 900;
  text-shadow: 0 0 36px rgba(125, 211, 252, 0.16);
}
</style>

---
layout: full
---

<InfiniteFlow :step="0" />

---
layout: full
---

<InfiniteFlow :step="1" />

---
layout: full
---

<InfiniteFlow :step="2" />

---
layout: full
---

<InfiniteFlow :step="3" />

---
layout: full
---

<InfiniteFlow :step="4" />

---
layout: full
---

<InfiniteFlow :step="5" />

---
layout: full
---

<InfiniteFlow :step="6" />
