# README v1 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se cambio el comportamiento de los pop-ups y del texto grande superior.

Antes, el detalle aparecia como un pop-up pegado al recuadro. Ahora se abre una ventana negra centrada, parecida a una modal, pero sin cubrir toda la pantalla. La ventana ocupa aproximadamente un tercio del ancho de la pantalla.

Tambien se redujo el texto grande `Agentes IA en movimiento`. Esas letras eran el titulo general de la presentacion, pero estaban demasiado grandes y tapaban el lienzo. Ahora funcionan como encabezado pequeno de contexto y no compiten con los recuadros.

## Por que se modifico

Se modifico porque el titulo enorme dificultaba leer el diagrama y hacia que los nodos quedaran visualmente cubiertos.

Tambien se modifico porque el pop-up anterior estaba pegado al recuadro y podia mezclarse con otros elementos del lienzo. Una ventana centrada permite leer mejor la descripcion sin perder el contexto de la presentacion.

## Paso a paso de la modificacion

1. Se elimino el pop-up dentro de cada recuadro.
2. Se agrego una ventana modal centrada fuera del nodo.
3. Se creo `selectedNode`, un valor calculado que encuentra el nodo seleccionado.
4. Se mantuvo `selectedNodeId` para saber que tema fue clickeado.
5. Se hizo que la ventana aparezca solo cuando existe un nodo seleccionado.
6. Se agrego un boton `x` para cerrar la ventana.
7. Se agrego una caja `Resumen` dentro de la ventana para mostrar el texto corto del nodo.
8. Se redujo el tamano del titulo principal para que no tape el lienzo.
9. Se redujo el ancho del bloque `hud`.
10. Se agregaron estilos oscuros para que la ventana se parezca a la referencia negra.
11. Se limito la ventana a `clamp(340px, 33vw, 520px)` para que no ocupe toda la pantalla.

## Codigo de ejemplo

Este ejemplo muestra como abrir una ventana centrada usando el nodo seleccionado.

```vue
<template>
  <!-- Recuadro del tema principal -->
  <article
    v-for="node in nodes"
    :key="node.id"
    class="node"
  >
    <!-- Al hacer click se guarda el id del nodo seleccionado -->
    <button
      class="node-title"
      type="button"
      @click.stop="togglePopup(node.id)"
    >
      {{ node.title }}
    </button>
  </article>

  <!-- Modal centrado: aparece solo si hay un nodo seleccionado -->
  <section v-if="selectedNode" class="modal-layer">
    <article
      class="modal-window"
      role="dialog"
      :aria-label="`Detalle de ${selectedNode.title}`"
    >
      <!-- Boton para cerrar la ventana -->
      <button class="modal-close" type="button" @click="closePopup">
        x
      </button>

      <!-- Categoria del tema -->
      <span class="modal-kicker">{{ selectedNode.type }}</span>

      <!-- Tema principal -->
      <h2>{{ selectedNode.title }}</h2>

      <!-- Descripcion larga del tema -->
      <p>{{ selectedNode.detail }}</p>

      <!-- Resumen corto del tema -->
      <div class="modal-note">
        <span>Resumen</span>
        <strong>{{ selectedNode.text }}</strong>
      </div>
    </article>
  </section>
</template>

<script setup>
import { computed, ref } from 'vue'

// Lista de temas disponibles en el lienzo
const nodes = [
  {
    id: 'result',
    type: 'Cierre',
    title: 'Resultado',
    text: 'Entrega una respuesta, accion o evidencia.',
    detail: 'El flujo termina con una salida medible.'
  }
]

// Guarda el id del nodo que el usuario clickeo
const selectedNodeId = ref(null)

// Busca el objeto completo del nodo seleccionado
const selectedNode = computed(() => {
  return nodes.find((node) => node.id === selectedNodeId.value)
})

// Abre el modal si estaba cerrado; lo cierra si ya estaba abierto
const togglePopup = (nodeId) => {
  selectedNodeId.value = selectedNodeId.value === nodeId ? null : nodeId
}

// Cierra la ventana modal
const closePopup = () => {
  selectedNodeId.value = null
}
</script>
```

Este ejemplo muestra el CSS clave para que la ventana sea negra, centrada y ocupe solo un tercio de la pantalla.

```css
.modal-layer {
  /* Capa fija para centrar la ventana en la pantalla */
  position: fixed;
  inset: 0;
  z-index: 30;
  display: grid;
  place-items: center;

  /* No pinta un fondo negro de pantalla completa */
  pointer-events: none;
}

.modal-window {
  /* Ocupa cerca de un tercio de la pantalla, con limites minimo y maximo */
  width: clamp(340px, 33vw, 520px);
  max-height: min(560px, 70vh);

  /* Estilo oscuro como la referencia */
  color: #e5e7eb;
  background: #090d14;
  border: 1px solid rgba(148, 163, 184, 0.26);
  border-radius: 8px;
  box-shadow: 0 34px 90px rgba(3, 7, 18, 0.42);

  /* Permite hacer click dentro de la ventana */
  pointer-events: auto;
}

.hud h1 {
  /* Reduce el titulo para que no tape el lienzo */
  font-size: clamp(28px, 3vw, 44px);
  line-height: 1.02;
}
```

## Resultado del cambio

El lienzo queda mas limpio. El titulo ya no tapa los nodos y las descripciones se leen en una ventana negra centrada, compacta y controlada.
