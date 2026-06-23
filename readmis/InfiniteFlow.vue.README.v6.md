# README v6 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se agregaron fondos al pasar el cursor por los recuadros y se ajustaron las flechas del flujo.

Cambios principales:

- Los nodos ahora cambian de fondo con `:hover`.
- El nodo activo tiene un fondo hover naranja.
- Los nodos no activos tienen un fondo hover azul.
- Las flechas SVG se redujeron de tamano.
- Las rutas mantienen sus flechas visibles de forma permanente.
- Las lineas punteadas conservan movimiento continuo.
- Se redujo el grosor de las rutas para que las flechas se vean mas finas.

## Por que se modifico

Se modifico porque el usuario pidio que los recuadros tengan fondo al pasar el cursor y que las flechas sean permanentes, pero de menor tamano.

La idea es acercar mas la presentacion a la referencia visual: cajas oscuras que reaccionan al cursor y flechas tecnicas, pequenas y siempre presentes.

## Paso a paso de la modificacion

1. Se redujo `markerWidth` y `markerHeight` en los marcadores SVG.
2. Se ajustaron `refX` y `refY` para alinear las puntas de flecha pequenas.
3. Se cambio el path de la flecha de `M0,0 L0,10 L13,5 z` a `M0,0 L0,6 L8,3 z`.
4. Se redujo el `stroke-width` de `.flow-path`.
5. Se dejo `opacity: 0.95` en las rutas base para que siempre sean visibles.
6. Se mantuvo `marker-end` en todas las rutas.
7. Se agrego `.node:hover` con fondo azul oscuro.
8. Se agrego `.node.active:hover` con fondo naranja oscuro.
9. Se corrigio una duplicacion de ancho en `.node-index`.

## Codigo de ejemplo

Este ejemplo muestra como se redujo el tamano de las flechas SVG.

```vue
<marker
  id="flow-arrow"
  markerWidth="8"
  markerHeight="8"
  refX="7"
  refY="3"
  orient="auto"
  markerUnits="strokeWidth"
>
  <!-- Flecha mas pequena que la version anterior -->
  <path d="M0,0 L0,6 L8,3 z" fill="#ffb28a" />
</marker>
```

Este ejemplo muestra como las rutas mantienen flecha permanente.

```vue
<path
  v-for="path in paths"
  :key="path.id"
  class="flow-path"
  :class="[path.kind, { lit: pathLit(path.to) }]"
  :d="path.d"
  :marker-end="path.kind === 'generate' ? 'url(#generate-arrow)' : 'url(#flow-arrow)'"
/>
```

Este ejemplo muestra el CSS de las rutas permanentes y mas finas.

```css
.flow-path {
  /* Ruta visible aunque no sea el paso activo */
  opacity: 0.95;

  /* Linea mas fina para que la flecha no se vea grande */
  stroke-width: 2.2;

  /* Mantiene el movimiento de lineas punteadas */
  stroke-dasharray: 12 18;
  animation: dash-flow 1100ms linear infinite;
}

.flow-path.lit {
  /* El paso activo se resalta sin agrandar demasiado la flecha */
  stroke-width: 2.8;
}
```

Este ejemplo muestra el fondo al pasar el cursor por un nodo.

```css
.node:hover {
  /* Fondo azul oscuro para indicar interaccion */
  border-color: rgba(139, 215, 255, 0.68);
  background:
    linear-gradient(135deg, rgba(125, 211, 252, 0.14), rgba(11, 15, 20, 0.82)),
    rgba(13, 20, 28, 0.92);
  box-shadow: 0 0 0 4px rgba(125, 211, 252, 0.06);
}

.node.active:hover {
  /* El nodo activo conserva el lenguaje naranja */
  border-color: #ffb28a;
  background:
    linear-gradient(135deg, rgba(255, 178, 138, 0.16), rgba(36, 28, 24, 0.9)),
    rgba(36, 28, 24, 0.9);
  box-shadow: 0 0 0 5px rgba(255, 178, 138, 0.14);
}
```

## Resultado del cambio

El lienzo ahora responde mejor al cursor y las flechas se sienten mas cercanas a la referencia: permanentes, finas, pequenas y con movimiento continuo.
