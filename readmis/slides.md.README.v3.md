# README v3 - slides.md

Este README documenta el cambio nuevo realizado en `slides.md`. No reemplaza los README anteriores.

## Que se cambio

Se ajusto el tamano de la slide de titulo para acercarla mas a la ultima imagen de referencia.

Antes el titulo de portada era grande y centrado. Ahora usa una composicion mas parecida a la referencia:

- Etiqueta pequena arriba.
- Titulo alineado a la izquierda.
- Tamano moderado.
- Mayor sensacion de slide tecnica, no de hero grande.

## Por que se modifico

Se modifico porque el usuario pidio usar los mismos tamanos de la ultima imagen compartida.

La referencia usa un encabezado compacto, con una etiqueta superior y un titulo mas pequeno. Este ajuste mantiene la slide propia de titulo, pero cambia sus proporciones para encajar con el estilo tecnico.

## Paso a paso de la modificacion

1. Se cambio el centrado total por alineacion superior izquierda.
2. Se reemplazo `place-items: center` por `align-content: start` y `justify-items: start`.
3. Se ajusto el padding para ubicar el titulo cerca del area superior.
4. Se redujo el tamano maximo del `h1`.
5. Se cambio el texto de centrado a alineacion izquierda.
6. Se mantuvo el fondo oscuro y la grilla.
7. Se mantuvo el mismo titulo y la misma secuencia de slides.

## Codigo de ejemplo

Este ejemplo muestra el ajuste de composicion de la portada.

```css
.title-cover {
  /* Ocupa toda la slide */
  position: fixed;
  inset: 0;
  display: grid;

  /* Coloca el contenido arriba a la izquierda como la referencia */
  align-content: start;
  justify-items: start;
  padding: clamp(84px, 12vh, 120px) clamp(32px, 5vw, 70px);
}

.title-cover h1 {
  /* Titulo moderado, no gigante */
  max-width: 860px;
  margin: 0;
  font-size: clamp(28px, 3.2vw, 44px);
  line-height: 1.12;
  text-align: left;
  font-weight: 900;
}
```

## Resultado del cambio

La portada sigue siendo una slide exclusiva para el titulo, pero ahora tiene proporciones mas cercanas a la referencia tecnica que compartio el usuario.
