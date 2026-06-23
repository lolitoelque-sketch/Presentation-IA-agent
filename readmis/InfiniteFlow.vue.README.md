# README - components/InfiniteFlow.vue

Este README explica como se modifico `components/InfiniteFlow.vue`, por que se hizo el cambio y que fragmentos de codigo sirven como referencia para repetir una mejora similar.

## Por que se modifico

El componente se ajusto para que la camara no se acerque ni haga zoom sobre los nodos. Ahora mantiene una escala fija y solo se desplaza por el lienzo, conservando la sensacion de mapa amplio.

Tambien se cambiaron los recuadros: ya no muestran descripcion dentro de la tarjeta. Cada recuadro muestra solo el tema principal y abre un pop-up con la descripcion cuando se hace click en el titulo.

## Paso a paso de la modificacion

1. Se agrego `ref` y `watch` desde Vue para controlar que pop-up esta abierto.
2. Se creo `selectedNodeId` para guardar el identificador del nodo seleccionado.
3. Se reemplazo el titulo plano del nodo por un boton clickeable.
4. Se elimino la descripcion visible dentro del recuadro.
5. Se agrego un pop-up dentro de cada nodo, visible solo si `selectedNodeId` coincide con ese nodo.
6. Se agrego un boton de cierre dentro del pop-up.
7. Se agrego `watch(activeStep)` para cerrar cualquier pop-up al cambiar de slide.
8. Se definio `canvasScale` como una escala fija para evitar que la camara se acerque.
9. Se quitaron los valores `scale` variables de `cameraPositions`.
10. Se actualizo `cameraStyle` para usar siempre la misma escala.
11. Se redujo el efecto activo del nodo: ahora se eleva un poco, pero no se agranda.
12. Se agregaron estilos para pop-up, flecha visual, boton de cierre y estados de foco.

## Codigo de ejemplo

Este ejemplo muestra como abrir un pop-up al hacer click en el tema principal de un recuadro.

```vue
<template>
  <article
    v-for="(node, index) in nodes"
    :key="node.id"
    class="node"
    :class="{ active: index === activeStep }"
  >
    <!-- Boton principal: al hacer click abre o cierra el pop-up del nodo -->
    <button
      class="node-title"
      type="button"
      :aria-expanded="selectedNodeId === node.id"
      @click.stop="togglePopup(node.id)"
    >
      {{ node.title }}
    </button>

    <!-- Texto pequeno para indicar que el nodo tiene detalle -->
    <span class="node-action">Ver detalle</span>

    <!-- Pop-up: aparece solo cuando este nodo esta seleccionado -->
    <div
      v-if="selectedNodeId === node.id"
      class="node-popup"
      role="dialog"
      :aria-label="`Detalle de ${node.title}`"
      @click.stop
    >
      <div class="popup-topline">
        <!-- Categoria del nodo -->
        <span>{{ node.type }}</span>

        <!-- Cierra el pop-up sin avanzar la slide -->
        <button type="button" aria-label="Cerrar detalle" @click.stop="closePopup">x</button>
      </div>

      <!-- Contenido del pop-up -->
      <strong>{{ node.title }}</strong>
      <p>{{ node.detail }}</p>
    </div>
  </article>
</template>

<script setup>
import { computed, ref, watch } from 'vue'

// Recibe desde slides.md el paso actual de la presentacion
const props = defineProps({
  step: {
    type: Number,
    default: 0
  }
})

// Nodo de ejemplo con titulo, tipo y descripcion para pop-up
const nodes = [
  {
    id: 'agent',
    type: 'Decision',
    title: 'Agente IA',
    detail: 'El agente evalua contexto, reglas y herramientas antes de ejecutar.'
  }
]

// Guarda que pop-up esta abierto; null significa ninguno
const selectedNodeId = ref(null)

// Evita pasos fuera del rango del arreglo
const activeStep = computed(() => {
  return Math.min(Math.max(props.step, 0), nodes.length - 1)
})

// Abre el pop-up del nodo o lo cierra si ya estaba abierto
const togglePopup = (nodeId) => {
  selectedNodeId.value = selectedNodeId.value === nodeId ? null : nodeId
}

// Cierra el pop-up actual
const closePopup = () => {
  selectedNodeId.value = null
}

// Al cambiar de slide se cierra el pop-up para evitar estados viejos
watch(activeStep, () => {
  selectedNodeId.value = null
})
</script>
```

Este ejemplo muestra como mover la camara sin acercarla.

```js
// Escala fija: todos los pasos usan el mismo zoom
const canvasScale = 0.74

// Cada posicion cambia desplazamiento y rotacion, pero no escala
const cameraPositions = [
  { x: 520, y: 85, rotate: -1 },
  { x: 340, y: 115, rotate: 0.4 },
  { x: 5, y: 55, rotate: -0.7 }
]

// Genera el transform final de la camara
const cameraStyle = computed(() => {
  const p = cameraPositions[activeStep.value] || cameraPositions[0]

  return {
    // scale(canvasScale) se mantiene igual para evitar acercamientos
    transform: `translate(-50%, -50%) translate3d(${p.x}px, ${p.y}px, 0) scale(${canvasScale}) rotate(${p.rotate}deg)`
  }
})
```

## Resultado del cambio

El lienzo sigue teniendo movimiento, pero ya no hace zoom hacia los temas. Los recuadros se ven mas limpios y las descripciones aparecen solamente cuando el expositor hace click en el tema principal.
