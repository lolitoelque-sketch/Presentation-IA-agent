# README v4 - components/InfiniteFlow.vue

Este README documenta el cambio nuevo realizado en `components/InfiniteFlow.vue`. No reemplaza los README anteriores.

## Que se cambio

Se copio el estilo visual de la referencia oscura tipo flujo tecnico, manteniendo intactos el lienzo interactivo, los nodos, las rutas, la camara y los modales.

El cambio fue principalmente visual:

- Fondo oscuro.
- Tipografia monoespaciada.
- Grilla sutil tipo tablero tecnico.
- Nodos transparentes con borde fino.
- Titulos en azul claro.
- Rutas punteadas en naranja.
- Nodo activo con borde naranja.
- Modal oscuro con el mismo lenguaje visual.

## Por que se modifico

Se modifico porque el usuario pidio copiar solo el estilo de la imagen de referencia, sin cambiar el contenido ni la forma de visualizar el flujo.

La presentacion ahora se siente mas tecnica y parecida a una visualizacion de flujo de desarrollo, pero conserva el mismo mapa de agentes IA.

## Paso a paso de la modificacion

1. Se cambio el fondo claro por un fondo oscuro.
2. Se cambio la fuente a una familia monoespaciada.
3. Se reemplazo la grilla clara por una grilla oscura con puntos.
4. Se cambiaron los colores de texto principales a azul claro.
5. Se cambiaron las flechas y rutas a naranja.
6. Se convirtieron los nodos en cajas oscuras semitransparentes.
7. Se redujo el efecto de sombra para acercarlo al estilo tecnico de la referencia.
8. Se marco el nodo activo con borde naranja.
9. Se adapto el modal para que mantenga el mismo estilo oscuro.
10. Se mantuvo la estructura del componente sin alterar los datos del flujo.

## Codigo de ejemplo

Este ejemplo muestra como definir una base visual oscura con fuente monoespaciada.

```css
.viewport {
  /* Pantalla completa del lienzo */
  position: fixed;
  inset: 0;
  overflow: hidden;

  /* Colores del estilo tecnico oscuro */
  color: #d7deea;
  background:
    radial-gradient(circle at 20% 18%, rgba(125, 211, 252, 0.08), transparent 30%),
    radial-gradient(circle at 82% 78%, rgba(255, 178, 138, 0.07), transparent 28%),
    linear-gradient(135deg, #0b0f14 0%, #10141b 48%, #0b0f14 100%);

  /* Fuente tipo terminal como la referencia */
  font-family: "Cascadia Code", "JetBrains Mono", "Fira Code", Consolas, ui-monospace, monospace;
}
```

Este ejemplo muestra la grilla oscura del lienzo.

```css
.camera::before {
  /* Capa visual del tablero */
  content: "";
  position: absolute;
  inset: 0;

  /* Puntos pequenos y lineas de grilla amplia */
  background-image:
    radial-gradient(circle, rgba(139, 148, 166, 0.22) 1px, transparent 1.5px),
    linear-gradient(rgba(125, 211, 252, 0.07) 1px, transparent 1px),
    linear-gradient(90deg, rgba(125, 211, 252, 0.07) 1px, transparent 1px);
  background-size: 40px 40px, 160px 160px, 160px 160px;

  /* Borde del lienzo tecnico */
  border: 1px solid rgba(139, 148, 166, 0.18);
  border-radius: 6px;
}
```

Este ejemplo muestra como se estilizaron los nodos sin cambiar su funcionamiento.

```css
.node {
  /* Mantiene posicion absoluta dentro del lienzo */
  position: absolute;

  /* Caja estilo diagrama tecnico */
  width: 305px;
  min-height: 150px;
  padding: 24px;
  border: 1px solid rgba(139, 148, 166, 0.34);
  border-radius: 4px;
  background: rgba(11, 15, 20, 0.76);
}

.node.active {
  /* El paso activo se marca con naranja */
  transform: translateY(-12px);
  border-color: #ffb28a;
  background: rgba(36, 28, 24, 0.88);
}

.node-title {
  /* El tema principal se ve como comando/titulo tecnico */
  color: #8bd7ff;
  font-size: 34px;
  font-weight: 900;
}
```

## Resultado del cambio

El lienzo mantiene el mismo flujo y la misma interaccion, pero ahora tiene una estetica oscura, tecnica y monoespaciada como la referencia visual solicitada.
