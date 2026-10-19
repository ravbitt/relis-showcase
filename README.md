# Relis

**CAD 3D de dominio para la fabricación de muebles tapizados.**

Relis modela el esqueleto de madera de un mueble (palos, uniones, tornillos y
corchetes) en un editor 3D en el navegador, con el objetivo de bajar
automáticamente esa forma a despiece y plan de corte para la fábrica.

🔗 **Demo en vivo:** [relis-showcase.ravbit.dev](https://relis-showcase.ravbit.dev)

> **Para entrar a la demo:** usa cualquier correo y cualquier contraseña. El
> login es simulado, no hay cuentas reales. Todo lo que modeles se guarda solo
> en tu navegador (localStorage).

> El código fuente es privado (proyecto en desarrollo con fines comerciales).
> Si quieres revisarlo en el contexto de un proceso de selección, escríbeme y
> te doy acceso.

<!-- TODO: GIF principal, ~10-20 s, modelando o rotando un mueble real -->
![Editor 3D de Relis](media/editor.gif)

---

## Por qué existe

Los CAD generales (SolidWorks, Fusion) son demasiado pesados y caros para un
taller de tapicería, y las herramientas de la industria textil parten del molde
2D para llegar al 3D. Relis invierte ese flujo: **la geometría es de dominio**.
Un esqueleto es un grafo de palos con uniones declaradas, no un sólido
genérico, y eso permite un editor mucho más simple y rápido para quien diseña
muebles.

## Funcionalidades

| | |
|---|---|
| **Editor 3D estilo Blender** | Herramientas con atajo de una letra: Transformar (S), Alinear (A), Rotar (R), Desplazar (G), Espejar (M), con snap usando Ctrl |
| **Uniones por acople** | Se encastran palos por vértice, arista o cara con picking geométrico preciso |
| **Fijaciones físicas** | Tornillos en puntos libres de una cara; filas de corchetes sobre la costura real entre dos palos, perpendiculares a la cara |
| **Cotas dinámicas** | Mediciones que se recalculan solas al mover las piezas |
| **Outliner** | Grupos con arrastrar y soltar, selección múltiple, ocultar y renombrar |
| **Historial** | Undo/redo transaccional con línea de tiempo navegable |
| **Editor de perfiles** | Cortes booleanos B-Rep reales con OpenCASCADE (WASM en un Web Worker) |
| **Cámara** | Perspectiva y ortográfica, con overlays de visualización |

<!-- TODO: capturas -->
| Fijaciones | Cotas | Catálogo |
|---|---|---|
| ![](media/fijaciones.png) | ![](media/cotas.png) | ![](media/catalogo.png) |

## Stack

- **React 19 + TypeScript + Vite**
- **react-three-fiber / drei / three.js** para el viewport, el picking y los gizmos
- **opencascade.js** (OpenCASCADE compilado a WASM, ~63 MB) cargado bajo demanda en un Web Worker
- **Tailwind v4 + shadcn/ui**
- Persistencia local por ahora; backend planificado con FastAPI + PostgreSQL
- Desplegado con Docker + nginx en servidor propio (Coolify, detrás de Cloudflare)

## Desafíos técnicos destacados

- **Geometría de fijaciones con sentido físico.** Un corchete une dos aristas
  (una por palo). La dirección de inserción se calcula una sola vez por fila
  a partir de la normal real de la cara, descartando la cara de contacto con
  el palo vecino. Así la fila queda paralela incluso en esquinas a inglete y
  en ensambles en T con maderas de distinto ancho.
- **Detección de costuras reales.** Se validan las aristas por cercanía
  efectiva en el tramo compartido y no por largo parecido, para no aceptar
  pares de aristas que no se tocan de verdad.
- **Modelo de datos paramétrico.** Cotas, fijaciones y cortes guardan
  referencias locales (palo + feature + offset) en lugar de posiciones
  mundiales, y se resuelven en vivo contra las dimensiones actuales del palo.
- **Robustez frente a datos persistidos.** Cada entidad pasa una validación
  de forma en tiempo de ejecución antes de renderizarse, para que un dato
  guardado con un esquema viejo no tumbe el árbol de React.
- **Peso del bundle.** three/r3f se separan con `React.lazy` y el motor CAD
  (~63 MB) solo se descarga cuando se abre el editor de perfiles.

## Estado

En desarrollo activo: v1 (esqueleto y fijaciones). En la hoja de ruta están
los mecanismos, la espuma, el tapizado con aplanado 3D→2D a moldes y la
salida de despiece.

---

**Joaquín Contreras** · [LinkedIn](#) · [Email](mailto:joaquin.contreras.dp@gmail.com) <!-- TODO: link de LinkedIn -->

© 2026 Joaquín Contreras. Todos los derechos reservados.
