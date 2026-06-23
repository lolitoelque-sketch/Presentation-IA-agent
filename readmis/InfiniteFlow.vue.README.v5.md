# README v5 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se ajustaron los tamanos del lienzo para acercarlos mas a la imagen de referencia y se animaron las lineas punteadas.

Cambios principales:

- Los recuadros ahora son mas bajos y compactos.
- Los titulos de los recuadros bajaron de tamano.
- El texto `Ver detalle` paso a verse como subtitulo discreto, no como boton/pildora.
- Las lineas punteadas ahora se mueven.
- Se agregaron dos estilos de flecha:
  - `flow`: flecha naranja punteada principal.
  - `generate`: flecha azul punteada secundaria.
- Se agrego un segundo marcador SVG para las flechas azules.

## Por que se modifico

Se modifico porque el usuario pidio usar los mismos tamanos de la ultima imagen compartida y replicar las flechas punteadas en movimiento.

La referencia tiene cajas compactas, tipografia monoespaciada y lineas punteadas activas. Este cambio conserva el flujo actual, pero ajusta la escala visual para parecerse mas a esa composicion.

## Paso a paso de la modificacion

1. Se agrego un segundo marcador SVG llamado `generate-arrow`.
2. Se agrego una propiedad `kind` a cada ruta del arreglo `paths`.
3. Se cambio el `class` dinamico de las rutas para incluir `path.kind`.
4. Se cambio `marker-end` para elegir flecha naranja o azul segun el tipo de ruta.
5. Se redujo el ancho visual de los nodos.
6. Se redujo el padding de los nodos.
7. Se redujo el tamano del indice del nodo.
8. Se bajo el tamano del titulo del nodo.
9. Se transformo `Ver detalle` en texto secundario simple.
10. Se agrego `@keyframes dash-flow` para mover las lineas punteadas.
11. Se aplico `animation` sobre `.flow-path`.
12. Se creo un estilo especial para `.flow-path.generate`.

## Codigo de ejemplo

Este ejemplo muestra como separar las flechas por tipo.

```vue
<svg class="paths" viewBox="0 0 2400 1300" aria-hidden="true">
  <defs>
    <!-- Flecha naranja para el flujo principal -->
    <marker id="flow-arrow" markerWidth="14" markerHeight="14" refX="11" refY="5" orient="auto">
      <path d="M0,0 L0,10 L13,5 z" fill="#ffb28a" />
    </marker>

    <!-- Flecha azul para rutas secundarias o generativas -->
    <marker id="generate-arrow" markerWidth="14" markerHeight="14" refX="11" refY="5" orient="auto">
      <path d="M0,0 L0,10 L13,5 z" fill="#6ba5c9" />
    </marker>
  </defs>

  <!-- Cada path decide su color y marcador con path.kind -->
  <path
    v-for="path in paths"
    :key="path.id"
    class="flow-path"
    :class="[path.kind, { lit: pathLit(path.to) }]"
    :d="path.d"
    :marker-end="path.kind === 'generate' ? 'url(#generate-arrow)' : 'url(#flow-arrow)'"
  />
</svg>
```

Este ejemplo muestra como definir rutas principales y secundarias.

```js
const paths = [
  // Ruta principal naranja
  { id: 'p1', kind: 'flow', to: 'chatbot', d: 'M420 625 C460 625 470 560 515 555' },

  // Ruta secundaria azul
  { id: 'p3', kind: 'generate', to: 'skill', d: 'M1180 610 C1260 470 1305 365 1390 360' }
]
```

Este ejemplo muestra la animacion de las lineas punteadas.

```css
.flow-path {
  /* Linea punteada naranja */
  fill: none;
  stroke: rgba(255, 178, 138, 0.5);
  stroke-width: 3;
  stroke-linecap: round;
  stroke-dasharray: 12 18;

  /* Mueve los segmentos punteados para simular flujo */
  animation: dash-flow 1100ms linear infinite;
}

.flow-path.generate {
  /* Variante azul para rutas secundarias */
  stroke: rgba(107, 165, 201, 0.56);
  stroke-dasharray: 5 12;
  animation-duration: 1500ms;
}

@keyframes dash-flow {
  to {
    /* Desplaza el patron punteado */
    stroke-dashoffset: -30;
  }
}
```

Este ejemplo muestra el ajuste de tamano de los recuadros.

```css
.node {
  /* Caja mas compacta como la referencia */
  width: 250px;
  min-height: 108px;
  padding: 14px 18px;
}

.node-title {
  /* Titulo mas pequeno para que el nodo respire */
  font-size: 28px;
  line-height: 1;
}

.node-action {
  /* Texto secundario discreto, no pildora */
  display: block;
  margin-top: 8px;
  border: 0;
  background: transparent;
}
```

## Resultado del cambio

El lienzo conserva el mismo flujo interactivo, pero ahora se acerca mas a la referencia: cajas mas compactas, flechas punteadas diferenciadas y movimiento continuo en las rutas.
