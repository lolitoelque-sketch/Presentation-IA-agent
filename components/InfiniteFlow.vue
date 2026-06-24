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
              markerWidth="8"
              markerHeight="8"
              refX="7"
              refY="3"
              orient="auto"
              markerUnits="strokeWidth"
            >
              <path d="M0,0 L0,6 L8,3 z" fill="#ffb28a" />
            </marker>

            <marker
              id="generate-arrow"
              markerWidth="8"
              markerHeight="8"
              refX="7"
              refY="3"
              orient="auto"
              markerUnits="strokeWidth"
            >
              <path d="M0,0 L0,6 L8,3 z" fill="#6ba5c9" />
            </marker>
          </defs>

          <path
            v-for="path in paths"
            :key="path.id"
            class="flow-path"
            :class="[path.kind, { lit: pathLit(path.to) }]"
            :d="path.d"
            :marker-end="path.kind === 'generate' ? 'url(#generate-arrow)' : 'url(#flow-arrow)'"
          />
        </svg>

        <article
          v-for="(node, index) in nodes"
          :key="node.id"
          class="node"
          :class="[node.id, { active: index === activeStep, previous: index < activeStep }]"
          :style="{ left: `${node.x}px`, top: `${node.y}px` }"
          role="button"
          tabindex="0"
          :aria-expanded="selectedNodeId === node.id"
          @click="togglePopup(node.id)"
          @keydown.enter.prevent="togglePopup(node.id)"
          @keydown.space.prevent="togglePopup(node.id)"
        >
          <div class="node-topline">
            <span class="node-index">
              {{ String(index + 1).padStart(2, '0') }}
            </span>
            <span class="node-type">
              {{ node.type }}
            </span>
          </div>

          <div class="node-title">
            {{ node.title }}
          </div>

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
        <button
          class="modal-close"
          type="button"
          aria-label="Cerrar detalle"
          @click="closePopup"
        >
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
import { computed, onBeforeUnmount, onMounted, ref } from 'vue'

const CANVAS_WIDTH = 2400
const CANVAS_HEIGHT = 1300
const NODE_WIDTH = 250
const NODE_HEIGHT = 108
const DESKTOP_SCALE = 0.74

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
  {
    id: 'p1',
    kind: 'flow',
    to: 'chatbot',
    d: 'M420 625 C460 625 470 560 515 555'
  },
  {
    id: 'p2',
    kind: 'flow',
    to: 'agent',
    d: 'M765 555 C835 555 850 615 930 615'
  },
  {
    id: 'p3',
    kind: 'generate',
    to: 'skill',
    d: 'M1180 610 C1260 470 1305 365 1390 360'
  },
  {
    id: 'p4',
    kind: 'flow',
    to: 'database',
    d: 'M1180 630 C1265 630 1320 690 1420 690'
  },
  {
    id: 'p5',
    kind: 'generate',
    to: 'power',
    d: 'M1165 690 C1290 850 1350 995 1460 1010'
  },
  {
    id: 'p6',
    kind: 'flow',
    to: 'result',
    d: 'M1645 360 C1810 360 1880 620 2025 635'
  },
  {
    id: 'p7',
    kind: 'flow',
    to: 'result',
    d: 'M1680 690 C1810 690 1900 650 2025 650'
  },
  {
    id: 'p8',
    kind: 'generate',
    to: 'result',
    d: 'M1725 1010 C1905 965 1945 720 2025 680'
  }
]

const cameraPositions = [
  { x: 520, y: 85, rotate: -1 },
  { x: 340, y: 115, rotate: 0.4 },
  { x: 5, y: 55, rotate: -0.7 },
  { x: -360, y: 235, rotate: 0.8 },
  { x: -390, y: -40, rotate: -0.4 },
  { x: -420, y: -310, rotate: 0.7 },
  { x: -805, y: -5, rotate: -0.5 }
]

const activeStep = ref(0)
const selectedNodeId = ref(null)
const focusedNodeId = ref(null)
const viewportSize = ref({
  width: 1280,
  height: 720
})

const selectedNode = computed(() => {
  return nodes.find((node) => node.id === selectedNodeId.value)
})

const cameraStep = computed(() => {
  if (!focusedNodeId.value) {
    return activeStep.value
  }

  const selectedIndex = nodes.findIndex((node) => node.id === focusedNodeId.value)

  if (selectedIndex === -1) {
    return activeStep.value
  }

  return selectedIndex
})

const isCompactViewport = computed(() => {
  const { width, height } = viewportSize.value
  return width <= 900 || height <= 560
})

const canvasScale = computed(() => {
  if (!isCompactViewport.value) {
    return DESKTOP_SCALE
  }

  const { width, height } = viewportSize.value
  const fitScale = Math.min(width / 640, height / 520) * 0.86
  return Math.min(0.68, Math.max(0.52, fitScale))
})

const cameraStyle = computed(() => {
  const basePosition = cameraPositions[cameraStep.value] || cameraPositions[0]
  const scale = canvasScale.value

  if (!isCompactViewport.value) {
    return {
      transform: `translate(-50%, -50%) translate3d(${basePosition.x}px, ${basePosition.y}px, 0) scale(${scale}) rotate(${basePosition.rotate}deg)`
    }
  }

  const node = nodes[cameraStep.value] || nodes[0]
  const targetX = node.x + NODE_WIDTH / 2
  const targetY = node.y + NODE_HEIGHT / 2
  const lift = viewportSize.value.height <= 520 ? 22 : 46
  const x = -scale * (targetX - CANVAS_WIDTH / 2)
  const y = -scale * (targetY - CANVAS_HEIGHT / 2) - lift

  return {
    transform: `translate(-50%, -50%) translate3d(${x}px, ${y}px, 0) scale(${scale}) rotate(${basePosition.rotate * 0.4}deg)`
  }
})

const updateViewportSize = () => {
  if (typeof window === 'undefined') {
    return
  }

  viewportSize.value = {
    width: window.innerWidth,
    height: window.innerHeight
  }
}

const goToStep = (step) => {
  const minStep = 0
  const maxStep = nodes.length - 1

  activeStep.value = Math.min(Math.max(step, minStep), maxStep)
  selectedNodeId.value = null
  focusedNodeId.value = null
}

const goNext = () => {
  goToStep(activeStep.value + 1)
}

const goPrev = () => {
  goToStep(activeStep.value - 1)
}

const handleKeyboardNavigation = (event) => {
  const nextKeys = ['ArrowRight', 'ArrowDown', 'PageDown', ' ']
  const prevKeys = ['ArrowLeft', 'ArrowUp', 'PageUp']

  if (![...nextKeys, ...prevKeys].includes(event.key)) {
    return
  }

  event.preventDefault()
  event.stopPropagation()

  if (nextKeys.includes(event.key)) {
    goNext()
    return
  }

  goPrev()
}

onMounted(() => {
  updateViewportSize()
  window.addEventListener('resize', updateViewportSize, { passive: true })
  window.addEventListener('keydown', handleKeyboardNavigation, true)
})

onBeforeUnmount(() => {
  if (typeof window !== 'undefined') {
    window.removeEventListener('resize', updateViewportSize)
    window.removeEventListener('keydown', handleKeyboardNavigation, true)
  }
})

const togglePopup = (nodeId) => {
  selectedNodeId.value = selectedNodeId.value === nodeId ? null : nodeId
  focusedNodeId.value = nodeId
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
  color: #d7deea;
  background:
    radial-gradient(circle at 20% 18%, rgba(125, 211, 252, 0.08), transparent 30%),
    radial-gradient(circle at 82% 78%, rgba(255, 178, 138, 0.07), transparent 28%),
    linear-gradient(135deg, #0b0f14 0%, #10141b 48%, #0b0f14 100%);
  font-family: "Cascadia Code", "JetBrains Mono", "Fira Code", Consolas, ui-monospace, monospace;
}

.viewport::before {
  content: "";
  position: absolute;
  inset: -80px;
  background-image:
    linear-gradient(rgba(125, 211, 252, 0.055) 1px, transparent 1px),
    linear-gradient(90deg, rgba(125, 211, 252, 0.055) 1px, transparent 1px);
  background-size: 52px 52px;
  transform: rotate(-2deg) scale(1.05);
  mask-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.82), rgba(0, 0, 0, 0.36));
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
  background: rgba(125, 211, 252, 0.11);
}

.ambient-b {
  left: 10%;
  bottom: 8%;
  width: 220px;
  height: 220px;
  background: rgba(255, 178, 138, 0.09);
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
  border: 1px solid rgba(139, 148, 166, 0.56);
  border-radius: 999px;
  background: rgba(15, 20, 27, 0.72);
  transition: width 450ms ease, background 450ms ease, border-color 450ms ease;
}

.progress span.done,
.progress span.active {
  border-color: #8bd7ff;
  background: #8bd7ff;
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
    radial-gradient(circle, rgba(139, 148, 166, 0.22) 1px, transparent 1.5px),
    linear-gradient(rgba(125, 211, 252, 0.07) 1px, transparent 1px),
    linear-gradient(90deg, rgba(125, 211, 252, 0.07) 1px, transparent 1px);
  background-size: 40px 40px, 160px 160px, 160px 160px;
  border: 1px solid rgba(139, 148, 166, 0.18);
  border-radius: 6px;
}

.paths {
  position: absolute;
  inset: 0;
  z-index: 1;
  overflow: visible;
}

.flow-path {
  fill: none;
  stroke: rgba(255, 178, 138, 0.86);
  stroke-width: 2.2;
  stroke-linecap: round;
  stroke-dasharray: 12 18;
  opacity: 1;
  animation: dash-flow 340ms linear infinite;
  transition: stroke 450ms ease, stroke-width 450ms ease;
}

.flow-path.generate {
  stroke: rgba(107, 165, 201, 0.84);
  stroke-dasharray: 5 12;
  animation-duration: 460ms;
}

.flow-path.lit {
  stroke: #ffb28a;
  stroke-width: 2.8;
}

.flow-path.generate.lit {
  stroke: #6ba5c9;
}

@keyframes dash-flow {
  to {
    stroke-dashoffset: -60;
  }
}

.node {
  position: absolute;
  z-index: 3;
  width: 250px;
  min-height: 108px;
  padding: 14px 18px;
  border: 1px solid rgba(139, 148, 166, 0.34);
  border-radius: 4px;
  background: rgba(11, 15, 20, 0.76);
  box-shadow: none;
  backdrop-filter: blur(12px);
  cursor: pointer;
  transition:
    transform 520ms ease,
    box-shadow 520ms ease,
    border-color 520ms ease,
    background 520ms ease;
}

.node:hover {
  transform: translateY(-12px) scale(1.025);
  border-color: rgba(139, 215, 255, 0.68);
  background:
    linear-gradient(135deg, rgba(125, 211, 252, 0.14), rgba(11, 15, 20, 0.82)),
    rgba(13, 20, 28, 0.92);
  box-shadow:
    0 18px 34px rgba(0, 0, 0, 0.36),
    0 0 0 4px rgba(125, 211, 252, 0.08),
    inset 0 1px 0 rgba(255, 255, 255, 0.08);
}

.node.active {
  transform: translateY(-8px);
  border-color: #ffb28a;
  background: rgba(36, 28, 24, 0.88);
  box-shadow: 0 0 0 5px rgba(255, 178, 138, 0.1);
}

.node.active:hover {
  transform: translateY(-14px) scale(1.03);
  border-color: #ffb28a;
  background:
    linear-gradient(135deg, rgba(255, 178, 138, 0.16), rgba(36, 28, 24, 0.9)),
    rgba(36, 28, 24, 0.9);
  box-shadow:
    0 20px 38px rgba(0, 0, 0, 0.42),
    0 0 0 5px rgba(255, 178, 138, 0.16),
    inset 0 1px 0 rgba(255, 255, 255, 0.1);
}

.node.previous {
  border-color: rgba(139, 148, 166, 0.5);
}

.node-topline {
  display: flex;
  align-items: center;
  justify-content: space-between;
  margin-bottom: 10px;
  color: #8b94a6;
  font-size: 11px;
  font-weight: 800;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.node-index {
  display: grid;
  place-items: center;
  height: 28px;
  width: 28px;
  border-radius: 999px;
  color: #0b0f14;
  background: #8bd7ff;
}

.node-title {
  display: block;
  width: 100%;
  margin: 0;
  min-height: 30px;
  padding: 0;
  border: 0;
  color: #8bd7ff;
  background: transparent;
  font: inherit;
  text-align: left;
  word-break: normal;
  overflow-wrap: anywhere;
  font-size: 28px;
  font-weight: 900;
  line-height: 1;
  letter-spacing: 0;
}

.node-title:hover,
.node-title:focus-visible {
  color: #ffb28a;
}

.node-title:focus-visible {
  outline: 2px solid rgba(255, 178, 138, 0.72);
  outline-offset: 6px;
  border-radius: 4px;
}

.node-action {
  display: block;
  margin-top: 8px;
  padding: 0;
  border: 0;
  border-radius: 0;
  color: #8b94a6;
  background: transparent;
  font-size: 13px;
  font-weight: 800;
}

.chatbot,
.skill {
  border-top-color: #8bd7ff;
}

.agent {
  border-top-color: #ffb28a;
}

.database {
  border-top-color: #8bd7ff;
}

.power {
  border-top-color: #ffb28a;
}

.result {
  border-top-color: #8bd7ff;
}

.cluster {
  position: absolute;
  z-index: 2;
  display: grid;
  grid-template-columns: repeat(2, 54px);
  gap: 14px;
  padding: 20px;
  border: 1px dashed rgba(139, 148, 166, 0.28);
  border-radius: 4px;
  background: rgba(11, 15, 20, 0.42);
}

.cluster span {
  grid-column: 1 / -1;
  color: #8b94a6;
  font-size: 16px;
  font-weight: 800;
  letter-spacing: 0.12em;
  text-transform: uppercase;
}

.cluster i {
  display: block;
  width: 54px;
  height: 54px;
  border: 1px solid rgba(139, 148, 166, 0.22);
  border-radius: 4px;
  background: rgba(125, 211, 252, 0.08);
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
  background: rgba(255, 178, 138, 0.08);
}

.canvas-label {
  position: absolute;
  z-index: 2;
  padding: 10px 14px;
  border: 1px solid rgba(139, 148, 166, 0.28);
  border-radius: 999px;
  color: #8bd7ff;
  background: rgba(11, 15, 20, 0.76);
  font-size: 16px;
  font-weight: 800;
  letter-spacing: 0.05em;
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
  padding: 24px;
  pointer-events: auto;
}

.modal-window {
  position: relative;
  width: clamp(340px, 33vw, 520px);
  max-height: min(560px, 70vh);
  padding: 44px 46px;
  border: 1px solid rgba(148, 163, 184, 0.26);
  border-radius: 4px;
  color: #e5e7eb;
  background:
    radial-gradient(circle at 20% 20%, rgba(125, 211, 252, 0.08), transparent 34%),
    linear-gradient(135deg, rgba(14, 18, 25, 0.98), rgba(8, 12, 18, 0.99)),
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
  border-radius: 4px;
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
  letter-spacing: 0.18em;
  text-transform: uppercase;
}

.modal-window h2 {
  margin: 0;
  color: #ffb28a;
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
  border-radius: 4px;
  background: rgba(15, 23, 42, 0.56);
}

.modal-note span {
  display: block;
  margin-bottom: 12px;
  color: #8bd7ff;
  font-size: 14px;
  font-weight: 900;
  text-transform: uppercase;
}

.modal-note strong {
  display: block;
  color: #b7e7ff;
  font-size: clamp(17px, 1.5vw, 21px);
  line-height: 1.4;
}

@media (max-width: 800px) {
  .progress {
    left: 16px;
    right: auto;
    top: auto;
    bottom: 16px;
    max-width: calc(100vw - 32px);
    gap: 7px;
  }

  .progress span {
    width: 9px;
    height: 9px;
  }

  .progress span.active {
    width: 28px;
  }

  .camera {
    transition-duration: 900ms;
  }

  .node {
    width: 238px;
    min-height: 104px;
    padding: 13px 16px;
  }

  .node-title {
    font-size: 25px;
  }

  .node-action {
    font-size: 12px;
  }

  .canvas-label {
    font-size: 14px;
  }

  .modal-layer {
    place-items: end center;
    padding: 16px;
  }

  .modal-window {
    width: min(100%, 430px);
    max-height: min(520px, 74vh);
    padding: 34px 28px 30px;
    overflow: auto;
  }

  .modal-window h2 {
    font-size: clamp(30px, 8vw, 40px);
  }

  .modal-window p {
    margin-top: 22px;
    font-size: clamp(17px, 4.5vw, 21px);
  }

  .modal-note {
    margin-top: 32px;
  }
}

@media (max-width: 520px) {
  .node {
    width: 226px;
    min-height: 98px;
    padding: 12px 14px;
  }

  .node-title {
    font-size: 23px;
  }

  .cluster,
  .canvas-label {
    opacity: 0.7;
  }
}

@media (prefers-reduced-motion: reduce) {
  .camera,
  .flow-path,
  .node {
    animation: none;
    transition-duration: 1ms;
  }
}
</style>
