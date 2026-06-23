<template>
  <main class="viewport">
    <div class="ambient ambient-a" />
    <div class="ambient ambient-b" />

    <div class="progress" aria-hidden="true">
      <span
        v-for="(item, index) in nodes"
        :key="item.id"
        :class="{ active: index === activeStep, done: index < activeStep }"
      />
    </div>

    <section class="canvas-shell">
      <div class="camera" :style="cameraStyle">
        <svg class="paths" viewBox="0 0 2400 1300" aria-hidden="true">
          <defs>
            <marker
              id="flow-arrow"
              markerWidth="14"
              markerHeight="14"
              refX="11"
              refY="5"
              orient="auto"
              markerUnits="strokeWidth"
            >
              <path d="M0,0 L0,10 L13,5 z" fill="#0f172a" />
            </marker>
          </defs>

          <path
            v-for="path in paths"
            :key="path.id"
            class="flow-path"
            :class="{ lit: pathLit(path.to) }"
            :d="path.d"
            marker-end="url(#flow-arrow)"
          />
        </svg>

        <article
          v-for="(node, index) in nodes"
          :key="node.id"
          class="node"
          :class="[node.id, { active: index === activeStep, previous: index < activeStep }]"
          :style="{ left: `${node.x}px`, top: `${node.y}px` }"
        >
          <div class="node-topline">
            <span class="node-index">{{ String(index + 1).padStart(2, '0') }}</span>
            <span class="node-type">{{ node.type }}</span>
          </div>
          <button
            class="node-title"
            type="button"
            :aria-expanded="selectedNodeId === node.id"
            @click.stop="togglePopup(node.id)"
          >
            {{ node.title }}
          </button>
          <span class="node-action">Ver detalle</span>

        </article>

        <div class="cluster cluster-data">
          <span>Datos</span>
          <i />
          <i />
          <i />
        </div>

        <div class="cluster cluster-automation">
          <span>Acciones</span>
          <i />
          <i />
          <i />
          <i />
        </div>

        <div class="canvas-label label-start">Entrada</div>
        <div class="canvas-label label-core">Nucleo del agente</div>
        <div class="canvas-label label-output">Salida verificable</div>
      </div>
    </section>

    <section
      v-if="selectedNode"
      class="modal-layer"
      aria-live="polite"
      @click.self="closePopup"
    >
      <article
        class="modal-window"
        role="dialog"
        :aria-label="`Detalle de ${selectedNode.title}`"
      >
        <button class="modal-close" type="button" aria-label="Cerrar detalle" @click="closePopup">
          x
        </button>

        <span class="modal-kicker">{{ selectedNode.type }}</span>
        <h2>{{ selectedNode.title }}</h2>
        <p>{{ selectedNode.detail }}</p>

        <div class="modal-note">
          <span>Resumen</span>
          <strong>{{ selectedNode.text }}</strong>
        </div>
      </article>
    </section>
  </main>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

const props = defineProps({
  step: {
    type: Number,
    default: 0
  }
})

const nodes = [
  {
    id: 'user',
    type: 'Inicio',
    title: 'Usuario',
    text: 'Explica una necesidad en lenguaje natural.',
    detail: 'La experiencia empieza con una pregunta, solicitud o caso de negocio.',
    x: 170,
    y: 570
  },
  {
    id: 'chatbot',
    type: 'Interfaz',
    title: 'Chatbot',
    text: 'Ordena la conversacion y pide datos faltantes.',
    detail: 'El bot convierte la intencion inicial en una tarea clara y accionable.',
    x: 515,
    y: 500
  },
  {
    id: 'agent',
    type: 'Decision',
    title: 'Agente IA',
    text: 'Planifica, decide herramientas y controla el flujo.',
    detail: 'El agente evalua contexto, reglas y herramientas antes de ejecutar.',
    x: 930,
    y: 560
  },
  {
    id: 'skill',
    type: 'Herramienta',
    title: 'Skill',
    text: 'Ejecuta una capacidad especializada.',
    detail: 'Las skills encapsulan tareas como leer documentos, validar datos o generar respuestas.',
    x: 1390,
    y: 300
  },
  {
    id: 'database',
    type: 'Contexto',
    title: 'Base de datos',
    text: 'Aporta registros, historiales y reglas internas.',
    detail: 'Los datos mantienen la respuesta conectada al negocio y a la realidad operativa.',
    x: 1420,
    y: 630
  },
  {
    id: 'power',
    type: 'Automatizacion',
    title: 'Power Automate',
    text: 'Dispara procesos y actualiza sistemas.',
    detail: 'La IA no se queda en texto: puede crear tickets, enviar avisos y cerrar tareas.',
    x: 1460,
    y: 950
  },
  {
    id: 'result',
    type: 'Cierre',
    title: 'Resultado',
    text: 'Entrega una respuesta, accion o evidencia.',
    detail: 'El flujo termina con una salida medible: decision, documento, registro o proceso iniciado.',
    x: 2025,
    y: 590
  }
]

const paths = [
  { id: 'p1', to: 'chatbot', d: 'M390 650 C455 650 460 595 515 590' },
  { id: 'p2', to: 'agent', d: 'M780 590 C855 590 850 640 930 650' },
  { id: 'p3', to: 'skill', d: 'M1185 610 C1265 455 1305 385 1390 390' },
  { id: 'p4', to: 'database', d: 'M1190 680 C1280 675 1320 710 1420 720' },
  { id: 'p5', to: 'power', d: 'M1160 770 C1285 890 1340 1015 1460 1040' },
  { id: 'p6', to: 'result', d: 'M1645 390 C1825 390 1885 610 2025 665' },
  { id: 'p7', to: 'result', d: 'M1695 720 C1810 720 1900 680 2025 680' },
  { id: 'p8', to: 'result', d: 'M1745 1040 C1905 1000 1945 755 2025 720' }
]

const canvasScale = 0.74

const cameraPositions = [
  { x: 520, y: 85, rotate: -1 },
  { x: 340, y: 115, rotate: 0.4 },
  { x: 5, y: 55, rotate: -0.7 },
  { x: -360, y: 235, rotate: 0.8 },
  { x: -390, y: -40, rotate: -0.4 },
  { x: -420, y: -310, rotate: 0.7 },
  { x: -805, y: -5, rotate: -0.5 }
]

const activeStep = computed(() => {
  return Math.min(Math.max(props.step, 0), nodes.length - 1)
})

const selectedNodeId = ref(null)

const selectedNode = computed(() => {
  return nodes.find((node) => node.id === selectedNodeId.value)
})

const cameraStyle = computed(() => {
  const p = cameraPositions[activeStep.value] || cameraPositions[0]

  return {
    transform: `translate(-50%, -50%) translate3d(${p.x}px, ${p.y}px, 0) scale(${canvasScale}) rotate(${p.rotate}deg)`
  }
})

watch(activeStep, () => {
  selectedNodeId.value = null
})

const togglePopup = (nodeId) => {
  selectedNodeId.value = selectedNodeId.value === nodeId ? null : nodeId
}

const closePopup = () => {
  selectedNodeId.value = null
}

const pathLit = (targetId) => {
  const targetIndex = nodes.findIndex((node) => node.id === targetId)
  return targetIndex <= activeStep.value
}
</script>

<style scoped>
.viewport {
  position: fixed;
  inset: 0;
  overflow: hidden;
  color: #0f172a;
  background:
    radial-gradient(circle at 20% 18%, rgba(34, 197, 94, 0.16), transparent 26%),
    radial-gradient(circle at 85% 16%, rgba(14, 165, 233, 0.16), transparent 28%),
    linear-gradient(135deg, #f8fafc 0%, #eef2f7 46%, #f6f7fb 100%);
  font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
}

.viewport::before {
  content: "";
  position: absolute;
  inset: -80px;
  background-image:
    linear-gradient(rgba(15, 23, 42, 0.07) 1px, transparent 1px),
    linear-gradient(90deg, rgba(15, 23, 42, 0.07) 1px, transparent 1px);
  background-size: 52px 52px;
  transform: rotate(-2deg) scale(1.05);
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.92), rgba(0, 0, 0, 0.55));
}

.ambient {
  position: absolute;
  border-radius: 999px;
  filter: blur(12px);
  opacity: 0.6;
}

.ambient-a {
  right: 8%;
  top: 9%;
  width: 180px;
  height: 180px;
  background: rgba(45, 212, 191, 0.26);
}

.ambient-b {
  left: 10%;
  bottom: 8%;
  width: 220px;
  height: 220px;
  background: rgba(251, 191, 36, 0.2);
}

.progress {
  position: absolute;
  right: clamp(28px, 5vw, 72px);
  top: clamp(34px, 6vh, 72px);
  display: flex;
  gap: 10px;
  z-index: 12;
}

.progress span {
  width: 12px;
  height: 12px;
  border: 2px solid rgba(15, 23, 42, 0.32);
  border-radius: 999px;
  background: rgba(255, 255, 255, 0.64);
  transition: width 450ms ease, background 450ms ease, border-color 450ms ease;
}

.progress span.done,
.progress span.active {
  border-color: #0f172a;
  background: #0f172a;
}

.progress span.active {
  width: 34px;
}

.canvas-shell {
  position: absolute;
  inset: 0;
  z-index: 3;
  perspective: 1400px;
}

.camera {
  position: absolute;
  left: 50%;
  top: 50%;
  width: 2400px;
  height: 1300px;
  transform-origin: center center;
  transition: transform 1200ms cubic-bezier(0.22, 1, 0.36, 1);
  will-change: transform;
}

.camera::before {
  content: "";
  position: absolute;
  inset: 0;
  background-image:
    linear-gradient(rgba(15, 23, 42, 0.09) 1px, transparent 1px),
    linear-gradient(90deg, rgba(15, 23, 42, 0.09) 1px, transparent 1px),
    linear-gradient(rgba(15, 23, 42, 0.035) 1px, transparent 1px),
    linear-gradient(90deg, rgba(15, 23, 42, 0.035) 1px, transparent 1px);
  background-size: 140px 140px, 140px 140px, 28px 28px, 28px 28px;
  border: 1px solid rgba(15, 23, 42, 0.06);
}

.paths {
  position: absolute;
  inset: 0;
  z-index: 1;
  overflow: visible;
}

.flow-path {
  fill: none;
  stroke: rgba(15, 23, 42, 0.28);
  stroke-width: 5;
  stroke-linecap: round;
  stroke-dasharray: 12 16;
  transition: stroke 450ms ease, stroke-width 450ms ease, opacity 450ms ease;
}

.flow-path.lit {
  stroke: #0f172a;
  stroke-width: 7;
  opacity: 1;
}

.node {
  position: absolute;
  z-index: 3;
  width: 275px;
  min-height: 150px;
  padding: 24px;
  border: 2px solid rgba(15, 23, 42, 0.12);
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.82);
  box-shadow: 0 18px 46px rgba(15, 23, 42, 0.12);
  backdrop-filter: blur(12px);
  transition:
    transform 520ms ease,
    box-shadow 520ms ease,
    border-color 520ms ease,
    background 520ms ease;
}

.node.active {
  transform: translateY(-12px);
  border-color: #14b8a6;
  background: #ffffff;
  box-shadow: 0 30px 72px rgba(20, 184, 166, 0.26);
}

.node.previous {
  border-color: rgba(15, 23, 42, 0.34);
}

.node-topline {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 22px;
  color: #64748b;
  font-size: 14px;
  font-weight: 800;
  text-transform: uppercase;
}

.node-index {
  display: grid;
  place-items: center;
  width: 42px;
  height: 42px;
  border-radius: 999px;
  color: #f8fafc;
  background: #0f172a;
}

.node-title {
  display: block;
  width: 100%;
  padding: 0;
  border: 0;
  color: #0f172a;
  background: transparent;
  cursor: pointer;
  font: inherit;
  text-align: left;
}

.node-title {
  margin: 0;
  font-size: 34px;
  font-weight: 900;
  line-height: 1;
  letter-spacing: 0;
}

.node-title:hover,
.node-title:focus-visible {
  color: #0f766e;
}

.node-title:focus-visible {
  outline: 3px solid rgba(20, 184, 166, 0.34);
  outline-offset: 6px;
  border-radius: 4px;
}

.node-action {
  display: inline-flex;
  margin-top: 20px;
  padding: 8px 12px;
  border: 1px solid rgba(15, 23, 42, 0.12);
  border-radius: 999px;
  color: #475569;
  background: rgba(248, 250, 252, 0.88);
  font-size: 14px;
  font-weight: 800;
}

.chatbot,
.skill {
  border-top-color: #2563eb;
}

.agent {
  border-top-color: #14b8a6;
}

.database {
  border-top-color: #f59e0b;
}

.power {
  border-top-color: #dc2626;
}

.result {
  border-top-color: #111827;
}

.cluster {
  position: absolute;
  z-index: 2;
  display: grid;
  grid-template-columns: repeat(2, 54px);
  gap: 14px;
  padding: 20px;
  border: 1px solid rgba(15, 23, 42, 0.12);
  border-radius: 8px;
  background: rgba(255, 255, 255, 0.46);
}

.cluster span {
  grid-column: 1 / -1;
  color: #64748b;
  font-size: 16px;
  font-weight: 800;
  text-transform: uppercase;
}

.cluster i {
  display: block;
  width: 54px;
  height: 54px;
  border: 1px solid rgba(15, 23, 42, 0.12);
  border-radius: 8px;
  background: linear-gradient(135deg, rgba(20, 184, 166, 0.2), rgba(255, 255, 255, 0.82));
}

.cluster-data {
  left: 1260px;
  top: 805px;
}

.cluster-automation {
  left: 1650px;
  top: 1080px;
}

.cluster-automation i {
  background: linear-gradient(135deg, rgba(248, 113, 113, 0.2), rgba(255, 255, 255, 0.82));
}

.canvas-label {
  position: absolute;
  z-index: 2;
  padding: 10px 14px;
  border: 1px solid rgba(15, 23, 42, 0.12);
  border-radius: 999px;
  color: #475569;
  background: rgba(255, 255, 255, 0.62);
  font-size: 16px;
  font-weight: 800;
  text-transform: uppercase;
}

.label-start {
  left: 145px;
  top: 475px;
}

.label-core {
  left: 900px;
  top: 455px;
}

.label-output {
  left: 1985px;
  top: 500px;
}

.modal-layer {
  position: fixed;
  inset: 0;
  z-index: 30;
  display: grid;
  place-items: center;
  pointer-events: none;
}

.modal-window {
  position: relative;
  width: clamp(340px, 33vw, 520px);
  max-height: min(560px, 70vh);
  padding: 44px 46px;
  border: 1px solid rgba(148, 163, 184, 0.26);
  border-radius: 8px;
  color: #e5e7eb;
  background:
    linear-gradient(135deg, rgba(15, 23, 42, 0.96), rgba(3, 7, 18, 0.98)),
    #090d14;
  box-shadow: 0 34px 90px rgba(3, 7, 18, 0.42);
  pointer-events: auto;
}

.modal-close {
  position: absolute;
  right: 20px;
  top: 18px;
  display: grid;
  place-items: center;
  width: 34px;
  height: 34px;
  border: 1px solid rgba(148, 163, 184, 0.2);
  border-radius: 999px;
  color: #94a3b8;
  background: rgba(15, 23, 42, 0.72);
  cursor: pointer;
  font-size: 18px;
  line-height: 1;
}

.modal-close:hover,
.modal-close:focus-visible {
  color: #ffffff;
  border-color: #fb923c;
}

.modal-kicker {
  display: block;
  margin-bottom: 22px;
  color: #94a3b8;
  font-size: 15px;
  font-weight: 900;
  letter-spacing: 0;
  text-transform: uppercase;
}

.modal-window h2 {
  margin: 0;
  color: #fb923c;
  font-size: clamp(34px, 4vw, 52px);
  font-weight: 900;
  line-height: 1;
  letter-spacing: 0;
}

.modal-window p {
  margin: 28px 0 0;
  color: #f8fafc;
  font-size: clamp(19px, 1.8vw, 25px);
  font-weight: 700;
  line-height: 1.45;
}

.modal-note {
  margin-top: 48px;
  padding: 22px;
  border: 1px solid rgba(148, 163, 184, 0.28);
  border-radius: 6px;
  background: rgba(15, 23, 42, 0.56);
}

.modal-note span {
  display: block;
  margin-bottom: 12px;
  color: #7dd3fc;
  font-size: 14px;
  font-weight: 900;
  text-transform: uppercase;
}

.modal-note strong {
  display: block;
  color: #bae6fd;
  font-size: clamp(17px, 1.5vw, 21px);
  line-height: 1.4;
}

@media (max-width: 800px) {
  .progress {
    left: 24px;
    right: auto;
    top: auto;
    bottom: 24px;
  }

  .camera {
    transition-duration: 900ms;
  }

  .modal-window {
    width: min(86vw, 420px);
    padding: 34px 28px;
  }
}
</style>
