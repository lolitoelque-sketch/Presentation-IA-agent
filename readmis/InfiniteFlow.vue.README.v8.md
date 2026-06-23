# README v8 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se hizo que la camara enfoque el recuadro clickeado y se acelero aun mas el movimiento de las lineas punteadas.

Cambios principales:

- Al hacer click en un recuadro, el modal se abre como antes.
- Ademas, la camara se mueve hacia el nodo clickeado.
- Las flechas y rutas quedan visibles permanentemente.
- Las lineas punteadas ahora se mueven mas rapido.
- El desplazamiento del patron punteado se aumento para que el efecto sea mas fluido.

## Por que se modifico

Se modifico porque el usuario pidio que cada click en un recuadro tambien enfoque ese elemento con la camara, que las flechas siempre aparezcan y que el efecto de flujo sea mas rapido.

Este cambio hace que la interaccion sea mas clara: el click no solo abre informacion, tambien dirige la vista hacia el elemento seleccionado.

## Paso a paso de la modificacion

1. Se agrego `cameraStep`, un valor calculado para decidir que paso debe enfocar la camara.
2. Si no hay nodo seleccionado, `cameraStep` usa el paso normal de la slide.
3. Si hay nodo seleccionado, `cameraStep` busca el indice de ese nodo.
4. Se cambio `cameraStyle` para usar `cameraStep` en vez de `activeStep`.
5. Se aumento la opacidad base de las rutas a `1`.
6. Se subio la intensidad de color de las rutas base.
7. Se bajo la duracion de animacion de las rutas principales a `340ms`.
8. Se bajo la duracion de animacion de las rutas secundarias a `460ms`.
9. Se aumento el `stroke-dashoffset` final a `-60`.

## Codigo de ejemplo

Este ejemplo muestra como la camara decide que nodo enfocar.

```js
const cameraStep = computed(() => {
  // Si no hay nodo seleccionado, la camara sigue el paso normal de la slide
  if (!selectedNodeId.value) {
    return activeStep.value
  }

  // Si hay nodo seleccionado, la camara enfoca ese nodo
  const selectedIndex = nodes.findIndex((node) => node.id === selectedNodeId.value)
  return selectedIndex === -1 ? activeStep.value : selectedIndex
})
```

Este ejemplo muestra como el estilo de camara usa el nuevo paso calculado.

```js
const cameraStyle = computed(() => {
  // Usa cameraStep para poder enfocar por slide o por click
  const p = cameraPositions[cameraStep.value] || cameraPositions[0]

  return {
    transform: `translate(-50%, -50%) translate3d(${p.x}px, ${p.y}px, 0) scale(${canvasScale}) rotate(${p.rotate}deg)`
  }
})
```

Este ejemplo muestra el ajuste de velocidad de las lineas punteadas.

```css
.flow-path {
  /* Flechas y lineas visibles permanentemente */
  opacity: 1;

  /* Movimiento mas rapido del flujo principal */
  animation: dash-flow 340ms linear infinite;
}

.flow-path.generate {
  /* Movimiento mas rapido de rutas secundarias */
  animation-duration: 460ms;
}

@keyframes dash-flow {
  to {
    /* Mayor desplazamiento para reforzar el efecto fluido */
    stroke-dashoffset: -60;
  }
}
```

## Resultado del cambio

Ahora cada recuadro funciona como punto de enfoque. Al hacer click, la camara viaja hacia ese nodo y las lineas punteadas mantienen un movimiento mas rapido y visible.
