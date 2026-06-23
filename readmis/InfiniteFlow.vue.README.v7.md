# README v7 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se hizo que todo el recuadro sea clickeable, se agrego un efecto de relieve al pasar el cursor y se acelero el movimiento de las lineas punteadas.

Cambios principales:

- El click ya no depende solo del titulo.
- Todo el `article.node` abre o cierra el modal.
- Se agrego soporte de teclado con `Enter` y `Space`.
- El hover ahora sobresale con `translateY`, `scale` y sombra.
- El nodo activo tiene un hover mas marcado en naranja.
- Las lineas punteadas se mueven mas rapido.

## Por que se modifico

Se modifico porque el usuario pidio que cada parte del recuadro sea clickeable, que el hover tenga un efecto de relieve y que el movimiento de las lineas punteadas sea mas rapido.

Este cambio mejora la interaccion: el usuario no necesita apuntar exactamente al titulo, puede hacer click en cualquier zona del recuadro.

## Paso a paso de la modificacion

1. Se movio el evento `@click` desde el titulo hacia el `article.node`.
2. Se cambio el titulo de `button` a `div`, para evitar un boton dentro de otro elemento clickeable.
3. Se agrego `role="button"` al recuadro.
4. Se agrego `tabindex="0"` para permitir foco por teclado.
5. Se agregaron eventos `@keydown.enter` y `@keydown.space`.
6. Se agrego `cursor: pointer` al nodo completo.
7. Se aumento el relieve en `.node:hover`.
8. Se aumento el relieve en `.node.active:hover`.
9. Se redujo la duracion de animacion de `.flow-path`.
10. Se redujo la duracion de animacion de `.flow-path.generate`.

## Codigo de ejemplo

Este ejemplo muestra como hacer clickeable todo el recuadro.

```vue
<article
  class="node"
  role="button"
  tabindex="0"
  :aria-expanded="selectedNodeId === node.id"
  @click="togglePopup(node.id)"
  @keydown.enter.prevent="togglePopup(node.id)"
  @keydown.space.prevent="togglePopup(node.id)"
>
  <!-- El titulo ya no es un boton separado -->
  <div class="node-title">
    {{ node.title }}
  </div>

  <!-- Cualquier parte del recuadro puede abrir el detalle -->
  <span class="node-action">Ver detalle</span>
</article>
```

Este ejemplo muestra el efecto de relieve al pasar el cursor.

```css
.node {
  /* Indica que todo el recuadro es interactivo */
  cursor: pointer;
}

.node:hover {
  /* El recuadro sobresale visualmente */
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

.node.active:hover {
  /* El nodo activo mantiene el acento naranja */
  transform: translateY(-14px) scale(1.03);
  border-color: #ffb28a;
  background:
    linear-gradient(135deg, rgba(255, 178, 138, 0.16), rgba(36, 28, 24, 0.9)),
    rgba(36, 28, 24, 0.9);
}
```

Este ejemplo muestra como se acelero el movimiento de las lineas.

```css
.flow-path {
  /* Antes era mas lento; ahora se mueve con mas energia */
  animation: dash-flow 620ms linear infinite;
}

.flow-path.generate {
  /* Las rutas secundarias tambien se aceleran */
  animation-duration: 820ms;
}
```

## Resultado del cambio

El recuadro completo se siente interactivo, el hover tiene efecto de profundidad y las lineas punteadas comunican mejor movimiento continuo.
