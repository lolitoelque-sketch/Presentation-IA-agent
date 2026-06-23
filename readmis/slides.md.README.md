# README - slides.md

Este README explica como se modifico `slides.md`, por que se hizo el cambio y que codigo sirve como ejemplo para controlar el movimiento de camara desde Slidev.

## Por que se modifico

`slides.md` controla las paginas de la presentacion. El componente `InfiniteFlow` recibe una propiedad llamada `step`, y esa propiedad indica que parte del lienzo debe enfocar la camara.

Se modifico para agregar un slide adicional con `step="6"`, porque el nuevo flujo visual tiene siete puntos:

1. Usuario
2. Chatbot
3. Agente IA
4. Skill
5. Base de datos
6. Power Automate
7. Resultado

Antes habia seis slides. Al agregar el paso final, la camara puede llegar tambien al nodo `Resultado`.

## Paso a paso de la modificacion

1. Se mantuvo el layout `full` en cada slide para que el componente ocupe toda la pantalla.
2. Se mantuvo el mismo componente `InfiniteFlow` en todas las slides.
3. Se cambio solamente el valor de `step` en cada pagina.
4. Cada valor de `step` corresponde a una posicion de camara dentro de `InfiniteFlow.vue`.
5. Se agrego una septima slide con `step="6"` para completar el recorrido.

## Codigo de ejemplo

Este ejemplo muestra como Slidev puede reutilizar el mismo componente varias veces, cambiando solo una propiedad. Cada slide representa una parada distinta de la camara.

```md
---
layout: full
---

<!-- Primer paso: enfoca el inicio del lienzo -->
<InfiniteFlow :step="0" />

---
layout: full
---

<!-- Segundo paso: mueve la camara hacia el chatbot -->
<InfiniteFlow :step="1" />

---
layout: full
---

<!-- Tercer paso: mueve la camara hacia el agente principal -->
<InfiniteFlow :step="2" />

---
layout: full
---

<!-- Cuarto paso: enfoca una herramienta o skill -->
<InfiniteFlow :step="3" />

---
layout: full
---

<!-- Quinto paso: baja hacia la base de datos -->
<InfiniteFlow :step="4" />

---
layout: full
---

<!-- Sexto paso: muestra la capa de automatizacion -->
<InfiniteFlow :step="5" />

---
layout: full
---

<!-- Septimo paso: cierra el recorrido en el resultado -->
<InfiniteFlow :step="6" />
```

## Como se conecta con InfiniteFlow.vue

El valor de `step` enviado desde `slides.md` se recibe en el componente:

```js
// Slidev envia el numero de paso desde cada slide
const props = defineProps({
  step: {
    type: Number,
    default: 0
  }
})

// El paso activo decide que posicion de camara usar
const activeStep = computed(() => {
  return Math.min(Math.max(props.step, 0), nodes.length - 1)
})
```

Luego `InfiniteFlow.vue` busca la posicion correspondiente:

```js
// Cada objeto define donde se ubica la camara en ese paso
const cameraPositions = [
  { x: 610, y: 95, scale: 0.64, rotate: -1 },
  { x: 420, y: 130, scale: 0.92, rotate: 0.4 },
  { x: 35, y: 50, scale: 0.9, rotate: -0.7 },
  { x: -425, y: 250, scale: 1.02, rotate: 0.8 },
  { x: -455, y: -95, scale: 0.94, rotate: -0.4 },
  { x: -490, y: -380, scale: 0.9, rotate: 0.7 },
  { x: -920, y: -20, scale: 0.82, rotate: -0.5 }
]
```

## Resultado del cambio

`slides.md` ahora funciona como un guion de camara. Cada pagina no crea una vista nueva desde cero, sino que reutiliza el mismo lienzo y le indica que punto debe enfocar.
