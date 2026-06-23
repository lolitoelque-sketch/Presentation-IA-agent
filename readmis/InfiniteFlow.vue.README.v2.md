# README v2 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se quito el texto descriptivo que aparecia debajo del titulo principal.

Tambien se extendio el titulo principal para que describa mejor la presentacion, pero se redujo y controlo su tamano para que no tape los elementos del lienzo.

Titulo nuevo:

```txt
Flujo operativo de agentes IA sobre un lienzo interactivo
```

## Por que se modifico

El texto inferior cambiaba segun el paso de la presentacion, pero visualmente competia con los recuadros del mapa y podia tapar parte del contenido.

El titulo anterior era mas corto, pero necesitaba explicar mejor la idea de la presentacion. Por eso se hizo mas descriptivo, manteniendo un tamano menor y un ancho controlado.

## Paso a paso de la modificacion

1. Se elimino el parrafo dinamico que estaba debajo del titulo.
2. Se reemplazo el titulo `Agentes IA en movimiento` por un titulo mas descriptivo.
3. Se elimino la variable calculada `current`, porque ya no se usa para mostrar el texto inferior.
4. Se aumento el ancho disponible del bloque `hud`.
5. Se redujo el tamano maximo del `h1`.
6. Se aumento ligeramente el `line-height` para que el titulo largo respire mejor.
7. Se elimino la regla CSS `.hud p:last-child`, porque el parrafo ya no existe.
8. Se elimino tambien la regla responsive vieja asociada a ese parrafo.

## Codigo de ejemplo

Este ejemplo muestra como dejar solo el titulo principal, sin descripcion debajo.

```vue
<template>
  <div class="hud">
    <!-- Etiqueta pequena que contextualiza el mapa -->
    <p class="kicker">Mapa operativo</p>

    <!-- Titulo extendido, pero controlado desde CSS para no tapar el lienzo -->
    <h1>Flujo operativo de agentes IA sobre un lienzo interactivo</h1>
  </div>
</template>
```

Este ejemplo muestra el CSS usado para que el titulo sea mas largo sin crecer demasiado.

```css
.hud {
  /* Define una zona amplia, pero limitada, para el encabezado */
  position: absolute;
  left: clamp(30px, 5vw, 76px);
  top: clamp(28px, 6vh, 64px);
  width: min(620px, 54vw);
  z-index: 10;
}

.hud h1 {
  /* Titulo menor que antes para evitar que tape nodos o etiquetas */
  margin: 0;
  font-size: clamp(26px, 2.7vw, 42px);
  line-height: 1.08;
  letter-spacing: 0;
  font-weight: 900;
}
```

Este ejemplo muestra que ya no se necesita calcular `current` si no se imprime descripcion dinamica en el HUD.

```js
// Antes se usaba para mostrar texto debajo del titulo:
// const current = computed(() => nodes[activeStep.value])

// Ahora el HUD solo tiene titulo fijo, por eso se puede eliminar.
```

## Resultado del cambio

La parte superior queda mas limpia: mantiene un titulo descriptivo, pero ya no muestra texto adicional que tape o compita con la descripcion visual de la presentacion.
