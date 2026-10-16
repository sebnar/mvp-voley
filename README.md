# MVP Voley — Planilla de Voley

Prototipo para registrar partidos de voleibol **en vivo** desde una tablet: saques y ataques de los
dos equipos se marcan con dos toques sobre la cancha (origen y destino), y la app calcula sola el
marcador, la rotación y los complejos K1/K2, y genera las planillas (K1 por rotación, K2 por
jugador, saques, recepción, defensa, errores y punto a punto).

## Contenido

| Ruta | Qué es |
|---|---|
| `prototipo/registro.html` | El prototipo (un solo archivo HTML, sin dependencias). |
| `prototipo/REQUERIMIENTOS.md` | Requerimientos, decisiones y pendientes con los que se construyó. |
| `index.html` | Redirige al prototipo. |
| `serve.ps1` | Servidor local mínimo para abrirlo en el computador (no requiere Node ni Python). |

## Abrirlo en línea

Publicado con GitHub Pages: <https://sebnar.github.io/mvp-voley/>

Se publica solo con cada subida a `main` (flujo `.github/workflows/pages.yml`, que envuelve
`prototipo/registro.html` en una página HTML completa).

## Abrirlo en el computador

```powershell
powershell -ExecutionPolicy Bypass -File serve.ps1
```

Luego abrir <http://localhost:5173>. Los datos se guardan solo en el navegador del dispositivo.

## Estado

Prototipo para validar la velocidad de captura en cancha. Ver los pendientes en
[`prototipo/REQUERIMIENTOS.md`](prototipo/REQUERIMIENTOS.md#8-pendientes-y-preguntas-abiertas).
