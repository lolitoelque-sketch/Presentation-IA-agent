# README v3 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se quito el encabezado del componente `InfiniteFlow.vue`.

Antes el lienzo interactivo mostraba tambien el titulo principal dentro del componente. Ahora el titulo vive en una slide propia dentro de `slides.md`, y `InfiniteFlow.vue` queda dedicado solo al mapa interactivo.

## Por que se modifico

Se modifico porque el usuario pidio que el titulo tenga su propia slide inicial y que despues continue la presentacion del lienzo interactivo.

Al quitar el titulo del componente, el lienzo queda mas limpio: ya no hay un encabezado encima de los nodos, las rutas y los controles visuales.

## Paso a paso de la modificacion

1. Se elimino el bloque HTML `.hud` del template.
2. Se quitaron el titulo y la etiqueta `Mapa operativo` del componente.
3. Se eliminaron los campos `copy` de cada nodo porque ya no se muestran en el encabezado.
4. Se eliminaron las reglas CSS `.hud`, `.kicker` y `.hud h1`.
5. Se elimino la regla responsive de `.hud`.
6. Se mantuvo intacta la logica del lienzo, la camara y los modales.

## Codigo de ejemplo

Este ejemplo muestra como dejar el componente solo con el lienzo, sin titulo interno.

```vue
<template>
  <main class="viewport">
    <!-- Elementos ambientales del fondo del lienzo -->
    <div class="ambient ambient-a" />
    <div class="ambient ambient-b" />

    <!-- Indicador del paso actual de la presentacion -->
    <div class="progress" aria-hidden="true">
      <span
        v-for="(item, index) in nodes"
        :key="item.id"
        :class="{ active: index === activeStep, done: index < activeStep }"
      />
    </div>

    <!-- Lienzo interactivo con camara en movimiento -->
    <section class="canvas-shell">
      <div class="camera" :style="cameraStyle">
        <!-- Aqui van rutas, nodos y etiquetas del mapa -->
      </div>
    </section>
  </main>
</template>
```

Este ejemplo muestra lo que se elimino conceptualmente.

```vue
<!-- Este bloque ya no vive dentro de InfiniteFlow.vue -->
<div class="hud">
  <!-- Etiqueta superior del titulo -->
  <p class="kicker">Mapa operativo</p>

  <!-- Titulo principal que ahora pertenece a slides.md -->
  <h1>Flujo operativo de agentes IA sobre un lienzo interactivo</h1>
</div>
```

## Resultado del cambio

`InfiniteFlow.vue` ahora muestra solamente el lienzo interactivo. El titulo se presenta primero en una slide independiente y luego el recorrido visual continua sin texto superior que tape el mapa.
