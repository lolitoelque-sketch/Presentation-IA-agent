# README v1 - slides.md

Este README documenta el cambio nuevo realizado en `slides.md`. No reemplaza los README anteriores.

## Que se cambio

Se agrego una slide inicial propia que solo muestra el titulo:

```txt
Flujo operativo de agentes IA sobre un lienzo interactivo
```

Despues de esa slide, la presentacion continua con el lienzo interactivo usando `InfiniteFlow`.

## Por que se modifico

Se modifico porque el titulo principal no debia aparecer encima del lienzo. El titulo necesitaba su propio momento de presentacion, y luego el mapa debia quedar limpio para explicar el flujo.

## Paso a paso de la modificacion

1. Se agrego una nueva primera slide con `layout: full`.
2. Dentro de esa slide se creo un bloque `section.title-cover`.
3. La slide contiene solamente un `h1` con el titulo principal.
4. Se agrego CSS en `slides.md` para centrar el titulo.
5. Se agrego un fondo con grilla suave para mantener continuidad visual con el lienzo.
6. Se dejaron las slides existentes de `InfiniteFlow` despues de la portada.
7. Los pasos del lienzo se mantuvieron igual: `step="0"` hasta `step="6"`.

## Codigo de ejemplo

Este ejemplo muestra como crear una slide de titulo independiente antes del lienzo.

```md
---
layout: full
---

<!-- Slide inicial: solo presenta el titulo de la exposicion -->
<section class="title-cover">
  <h1>Flujo operativo de agentes IA sobre un lienzo interactivo</h1>
</section>

<!-- Estilos propios de la portada -->
<style>
.title-cover {
  /* Ocupa toda la pantalla de la slide */
  position: fixed;
  inset: 0;

  /* Centra el titulo vertical y horizontalmente */
  display: grid;
  place-items: center;

  /* Mantiene espacio interno para que el texto no toque bordes */
  padding: 72px;

  /* Fondo claro con sensacion de lienzo */
  color: #0f172a;
  background: linear-gradient(135deg, #f8fafc 0%, #eef2f7 52%, #ffffff 100%);
}

.title-cover h1 {
  /* Limita el ancho para que el titulo no sea una linea demasiado larga */
  max-width: 980px;
  margin: 0;

  /* Titulo grande para portada, pero responsive */
  font-size: clamp(44px, 6.4vw, 92px);
  line-height: 1;
  text-align: center;
  font-weight: 900;
}
</style>

---
layout: full
---

<!-- Desde aqui empieza el lienzo interactivo -->
<InfiniteFlow :step="0" />
```

## Resultado del cambio

La presentacion ahora empieza con una portada clara y despues pasa al lienzo interactivo. El mapa ya no comparte espacio con el titulo principal.
