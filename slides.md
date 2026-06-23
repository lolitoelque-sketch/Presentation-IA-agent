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
  place-items: center;
  padding: 72px;
  color: #0f172a;
  background:
    radial-gradient(circle at 28% 22%, rgba(20, 184, 166, 0.18), transparent 28%),
    radial-gradient(circle at 78% 76%, rgba(251, 146, 60, 0.16), transparent 30%),
    linear-gradient(135deg, #f8fafc 0%, #eef2f7 52%, #ffffff 100%);
  overflow: hidden;
}

.title-cover::before {
  content: "";
  position: absolute;
  inset: -80px;
  background-image:
    linear-gradient(rgba(15, 23, 42, 0.07) 1px, transparent 1px),
    linear-gradient(90deg, rgba(15, 23, 42, 0.07) 1px, transparent 1px);
  background-size: 54px 54px;
  transform: rotate(-2deg);
}

.title-cover h1 {
  position: relative;
  max-width: 980px;
  margin: 0;
  font-size: clamp(44px, 6.4vw, 92px);
  line-height: 1;
  letter-spacing: 0;
  text-align: center;
  font-weight: 900;
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
