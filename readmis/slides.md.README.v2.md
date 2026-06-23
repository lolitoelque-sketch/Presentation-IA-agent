# README v2 - slides.md

Este README documenta el cambio nuevo realizado en `slides.md`. No reemplaza los README anteriores.

## Que se cambio

Se adapto la slide inicial de titulo al estilo oscuro de la referencia.

El contenido de la portada no cambio: sigue mostrando el mismo titulo y luego continua con el lienzo interactivo.

## Por que se modifico

Se modifico para que la portada y el lienzo tengan el mismo lenguaje visual. Antes la portada tenia un estilo claro, mientras que el nuevo lienzo usa una estetica oscura tecnica.

## Paso a paso de la modificacion

1. Se cambio el fondo claro por un fondo oscuro.
2. Se cambio la tipografia a monoespaciada.
3. Se agrego una grilla sutil con puntos, como en la referencia.
4. Se cambio el color del titulo a azul claro.
5. Se agrego una etiqueta visual `EL FLUJO` usando `::after`.
6. Se mantuvo el titulo original.
7. Se mantuvo la secuencia de slides con `InfiniteFlow`.

## Codigo de ejemplo

Este ejemplo muestra la base de la portada oscura.

```css
.title-cover {
  /* La portada ocupa toda la slide */
  position: fixed;
  inset: 0;
  display: grid;
  place-items: center;

  /* Estilo oscuro tecnico */
  color: #8bd7ff;
  background:
    radial-gradient(circle at 28% 22%, rgba(125, 211, 252, 0.08), transparent 28%),
    radial-gradient(circle at 78% 76%, rgba(255, 178, 138, 0.08), transparent 30%),
    linear-gradient(135deg, #0b0f14 0%, #10141b 52%, #0b0f14 100%);

  /* Fuente tipo terminal */
  font-family: "Cascadia Code", "JetBrains Mono", "Fira Code", Consolas, ui-monospace, monospace;
}
```

Este ejemplo muestra la etiqueta superior de estilo tecnico.

```css
.title-cover::after {
  /* Texto pequeno decorativo de contexto */
  content: "EL FLUJO";
  position: absolute;
  left: clamp(28px, 5vw, 70px);
  top: clamp(26px, 6vh, 58px);

  /* Estetica de referencia: gris, espaciado y mayusculas */
  color: #8b94a6;
  font-size: 16px;
  font-weight: 800;
  letter-spacing: 0.32em;
}
```

## Resultado del cambio

La portada ahora comparte el mismo estilo oscuro del lienzo interactivo, manteniendo intacto el flujo de la presentacion.
