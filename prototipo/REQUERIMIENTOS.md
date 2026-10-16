# Planilla de Voley — Requerimientos del prototipo

Documento de los requerimientos con los que se construyó el prototipo de registro en vivo
(`prototipo/registro.html`). Recoge lo definido en las conversaciones de diseño y en los insumos
de la carpeta `insumos/` (explicaciones en texto, planilla de rotaciones en PDF, planilla punto a
punto en Word y fotos de planillas llenas).

- **Estado:** prototipo funcional para validar la captura en vivo. No es la versión final.
- **Prototipo publicado:** https://claude.ai/artifact/9GuVJBdsDBSemMLtx1EBwG
- **Última revisión:** 2026-10-16

---

## 1. Objetivo

Reemplazar las planillas en papel que usa el cuerpo técnico para analizar partidos de voleibol.
Se quiere registrar **en vivo, en la cancha**, saques y ataques de **los dos equipos**, y obtener
automáticamente las mismas planillas que hoy se llenan a mano, más estadísticas que en papel
toman mucho trabajo, como las tendencias por rotación, por jugador y entre partidos.

La regla central del diseño: **cada acción se registra una sola vez** y con ella se llenan todas
las planillas (rotaciones, punto a punto, saques, recepción, defensa y errores).

## 2. Usuarios y contexto de uso

| Aspecto | Definición |
|---|---|
| Quién registra | Normalmente **una sola persona** (entrenador o analista). |
| Cuándo | **En vivo**, durante el partido. Más adelante, repaso y corrección con video. |
| Dispositivo principal | **Tablet en horizontal**. También debe poder usarse en celular y portátil. |
| Equipos analizados | **Ambos**: el propio y el rival. |
| Conectividad | Los coliseos pueden no tener internet: debe funcionar sin conexión. |
| Prioridad | **Velocidad de captura.** Si anotar es más lento que en papel, no se va a usar. |

## 3. Glosario

| Término | Significado |
|---|---|
| **Rotación / "Armador en X"** | Zona (1–6) donde está el armador. Define la posición de todos los demás jugadores. |
| **Roles** | Armador (Arm), Punta 1 (P1), Punta 2 (P2), Central 1 (C1), Central 2 (C2), Opuesto (OP), Líbero (L). |
| **Orden de rotación** | Partiendo del armador: P1 a su lado, C2, OP cruzado con el armador, P2 cruzado con P1 y C1 cruzado con C2. Con el armador en 1: P1 en 2, C2 en 3, OP en 4, P2 en 5 y C1 en 6. |
| **K1 (complejo 1)** | Primer ataque del equipo que recibe, después del saque rival. Se analiza **por rotación**. |
| **K2 (complejo 2)** | Todos los ataques siguientes del rally (contraataques). Se analizan **por jugador**. |
| **Zonas 1–9** | Las 6 zonas clásicas más la 7, 8 y 9 en la **franja media** de cada media cancha. Vista desde el equipo: adelante 4-3-2, franja media 7-8-9 y atrás 5-6-1. |
| **Escala de recepción y defensa** | Doble positiva **#** (3), positiva **+** (2), negativa **−** (1), error **=** (0). |
| **Columnas punto a punto** | **S** saque, **B** bloqueo, **A** ataque (K1), **CO** contraataque (K2), **E** error del rival + **Tipo**. |
| **Tipos de error** | A ataque, S saque, B bloqueo (de la planilla en papel). Agregados: R recepción, D defensa, AR armado o doble, RD red, RT rotación, O otro. |

## 4. Requerimientos funcionales

### 4.1 Equipos y partido

- **RF-01.** Configurar los dos equipos: nombre y número de cada rol (Arm, P1, P2, C1, C2, OP, L).
- **RF-02.** Los números pueden quedar **vacíos** cuando no se conoce la alineación del rival. Se muestra el rol (P1, C2…) hasta completarlos, y se pueden completar en cualquier momento del partido.
- **RF-03.** Opción de jugar **con o sin líbero**. Con líbero, el líbero reemplaza a los centrales en la zaguera (zonas 5, 6 y 1), salvo al central que está en zona 1 cuando su equipo saca.
- **RF-04.** Formato al mejor de 5, al mejor de 3 o set único. **Marcador personalizable:** puntos por set (25 por defecto) y puntos del set decisivo (15 por defecto), de 1 a 99. Siempre se gana con diferencia de 2. Con set único no hay set decisivo.
- **RF-05.** **Inicio de cada set:** antes del primer saque se elige qué equipo saca y en qué zona arranca el armador de cada equipo. Viene propuesto (el saque alterna entre sets) y basta con empezar a anotar si es correcto.
- **RF-06.** Si se cambia la configuración de saque, de rotación o de puntos por set, **se recalcula todo lo ya anotado**.
- **RF-07.** Botón **Reiniciar partido** en la pantalla de registro. Pide un segundo toque para confirmar (se desarma solo a los 4 segundos), borra todo lo anotado y conserva los equipos, el formato, el inicio del set 1 y la configuración.

### 4.2 Registro en la cancha

- **RF-10.** Se muestra la **cancha completa**: equipo izquierdo en **azul** y derecho en **rojo**, con las zonas numeradas y los jugadores en su posición según la rotación actual. Alrededor hay **zona libre** para marcar balones fuera: 3,5 m detrás de cada línea de fondo y 3 m a cada lateral.
- **RF-11.** Cada balón se registra con **dos toques: origen y destino**. Se guarda la posición exacta (no solo la zona), para poder calcular zonas, cuadrantes u otras divisiones en el futuro.
- **RF-12.** El color y el equipo de cada trazo los define **quién hizo la acción**: para el saque, el equipo que tiene el servicio según el marcador; para los ataques, el lado de la cancha donde se tocó el origen.
- **RF-13.** El primer trazo de cada rally es el **saque**. Los siguientes son ataques, clasificados solos como **K1 o K2**.
- **RF-14.** La app **propone el jugador**:
  - **Sacador:** el que está en zona 1.
  - **Atacante:** según la zona de origen y la rotación (zona 4 → punta de adelante, zona 3 → central de adelante, zonas 2 y 1 → opuesto, zona 6 → punta de atrás).
  - **Receptor y defensor:** según dónde cayó el balón.

  Todos se corrigen con un toque.
- **RF-15.** Si el balón sigue en juego, no hay que confirmar nada: el siguiente toque en la cancha empieza el próximo trazo.
- **RF-16.** El marcador, el cambio de saque y la **rotación** se calculan solos. El equipo que recupera el saque rota.
- **RF-17.** Cada trazo muestra un número de orden en su origen y una marca al final: punto (círculo lleno), error (X), bloqueado (raya atravesada) o nada si siguió la jugada.

### 4.3 Saque

- **RF-20.** El origen del saque se marca **fuera de la cancha**, detrás de la línea de fondo. La zona de saque (1, 6 o 5) se calcula **solo por la altura** del toque.
- **RF-21.** *(Configurable.)* **Saque fuera o a la malla = punto automático** para el otro equipo:
  - **Fuera:** el destino está fuera de la cancha.
  - **Malla:** el destino queda en la cancha de quien saca (no pasó la red), sobre la red o hasta 40 cm del otro lado. Esa franja existe porque en pantalla la red es una línea delgada y el dedo cae fácilmente un poco del otro lado. La misma regla aplica a los ataques (RF-42).
- **RF-22.** Saque a la malla: motivo opcional, **"Le faltó potencia"** o **"Salió bajo"**.
- **RF-23.** Saque fuera: botón opcional **"La tocó la recepción"**, que convierte el punto en **ace** del sacador y en **error de recepción (0)** del receptor (se puede elegir quién).
- **RF-24.** Si el saque se marca del lado del equipo que **no** tiene el servicio, se muestra un aviso y **no se da el punto automático**. Si el set no ha empezado, el aviso trae un botón para corregir quién saca. Si ya empezó, sugiere revisar el último punto con Deshacer.

### 4.4 Recepción

- **RF-30.** *(Configurable.)* Después de cada saque se puede calificar la **recepción** (#, +, −, =) e indicar **quién recibió**. Es opcional.
- **RF-31.** Calificar **=** cierra el rally como **ace** del sacador.
- **RF-32.** Un ace registrado con el botón de resultado cuenta también como error de recepción (0), y se puede elegir el receptor.

### 4.5 Ataque, defensa y bloqueo

- **RF-40.** *(Configurable.)* Después de cada ataque que sigue en juego se puede calificar la **defensa** del otro equipo (misma escala) e indicar **quién defendió**. Aplica a **los dos equipos** y es opcional.
- **RF-41.** Calificar la defensa con **=** cierra el rally como **punto del atacante**.
- **RF-42.** *(Configurable.)* Cuando un ataque cae **fuera** o queda **en la malla**, se pregunta **qué pasó**, mostrando en cada opción a quién va el punto:

  | Caso | Opción | Resultado |
  |---|---|---|
  | Fuera | Fuera sin tocar | Error de ataque → punto del rival |
  | Fuera | Tocó el bloqueo | Punto del atacante + error de bloqueo (tipo B) del bloqueador, opcional |
  | Fuera | Lo tocó la defensa y salió | Punto del atacante + error de defensa (0) de quien la tocó |
  | Malla | Le faltó potencia / Salió bajo / Sin detalle | Error de ataque a la malla → punto del rival |
  | Malla | Lo bloquearon | Punto de bloqueo del rival |
  | Ambos | Siguió la jugada | Sin punto (también tocando la cancha) |
  | Ambos | El ataque fue del otro equipo | Corrige el equipo del trazo |

- **RF-43.** Después de un **bloqueo** se puede elegir quién bloqueó (opcional).

### 4.6 Cierre del rally y puntos directos

- **RF-50.** Botones de resultado que se aplican al **último trazo**, con el nombre del equipo en el texto:
  - **Punto** (o **Ace** si el último trazo es un saque);
  - **Error**;
  - **Bloqueo** del otro equipo.
- **RF-51.** **Punto directo** para cada equipo, para puntos que no salen de un trazo (toque de red, doble golpe, rotación, pelota que cae entre dos). Pide de forma opcional el **tipo de error** y el **jugador** que lo cometió.
- **RF-52.** Los detalles opcionales (bloqueador, receptor, motivo, etc.) **nunca bloquean** el registro: tocar la cancha los descarta y se sigue anotando.

### 4.7 Deshacer

- **RF-60.** Botón **Deshacer** grande y **junto a la cancha**, con un texto al lado que dice qué va a deshacer.
- **RF-61.** Deshacer sin límite, en este orden:
  1. el toque de origen pendiente;
  2. el último trazo;
  3. el último punto: lo reabre y deja sus trazos para corregir cómo terminó.
- **RF-62.** Si el punto lo dio la app sola (saque fuera o a la malla, incluso corregido a ace), **un solo Deshacer quita el saque y el punto**.
- **RF-63.** Al reabrir un ataque que cayó fuera o en la malla, se vuelve a preguntar qué pasó.

### 4.8 Planillas y estadísticas

Se generan solas a partir de lo registrado, **por equipo**:

- **RF-70. K1 por rotación:** 6 canchas ("Armador en 1, 6, 5, 4, 3, 2") con las líneas del primer ataque después de recibir. Equivale a la parte de arriba de la planilla en papel.
- **RF-71. K2 por jugador:** una cancha por rol, con todos sus contraataques. Equivale a la parte de abajo de la planilla en papel.
- **RF-72. Saques por jugador:** origen, destino y resultado.
- **RF-73.** Cada cancha muestra los intentos, puntos, errores o bloqueos y la **eficacia** (puntos / intentos).
- **RF-74. Recepción por jugador:** cantidad de cada calificación, total, promedio y porcentaje de positivas (# y +).
- **RF-75. Defensa por jugador:** igual que la recepción.
- **RF-76. Errores por jugador**, separados por tipo y detalle: saque o ataque fuera, a la malla (con motivo), bloqueo que tocó y salió, y errores de punto directo.
- **RF-77. Punto a punto:** tabla por set, igual al formato en Word. Cada fila es el número de punto de ese equipo, con las columnas S, B, A, CO, E y Tipo para cada equipo.
- **RF-78. Últimos puntos:** lista en la pantalla de registro con el marcador y la descripción de cómo se ganó cada punto.

### 4.9 Configuración

- **RF-80.** Pestaña **Configuración** con los comportamientos opcionales, cada uno con su explicación. Se aplican al instante y no borran datos:

  | Grupo | Opción | Por defecto |
  |---|---|---|
  | Saque | Saque fuera o a la malla = punto automático | Activada |
  | Saque | Calificar la recepción después de cada saque | Activada |
  | Ataque | Calificar la defensa después de cada ataque | Activada |
  | Ataque | Preguntar qué pasó cuando un ataque cae fuera o en la malla | Activada |

- **RF-81.** La pestaña **Equipos** queda solo para los equipos y los datos del partido.

## 5. Requerimientos no funcionales

- **RNF-01. Velocidad:** un balón se registra con dos toques. Todo dato extra es opcional y tiene un valor propuesto.
- **RNF-02. Tablet primero:** el diseño principal es para tablet horizontal (cancha a la izquierda y panel a la derecha). En pantallas angostas se apila y no debe haber desplazamiento horizontal.
- **RNF-03. Botones grandes** para usar con el dedo. La pantalla se mantiene encendida mientras se registra, si el navegador lo permite.
- **RNF-04. Sin conexión:** en el prototipo los datos se guardan en el propio dispositivo (`localStorage`).
- **RNF-05. Hora de cada acción:** cada trazo guarda su hora, para poder sincronizarlo después con el video del partido.
- **RNF-06. Tema claro y oscuro.**

## 6. Modelo de datos (prototipo)

El estado del partido **se calcula** a partir de la lista de rallies. No se guardan marcador ni
rotación; se derivan. Por eso corregir la configuración recalcula todo y Deshacer es simple.

```text
Partido
├── cfg
│   ├── formato (bestOf, setPoints 25, tiebreak 15), firstServer
│   ├── equipos A/B: nombre, números por rol, useLibero, startZone
│   ├── sets[n]: { serve, A: zonaArmador, B: zonaArmador }   ← inicio de cada set
│   └── opciones: autoServeErr, gradeReception, gradeDefense, confirmOut
└── rallies[]
    ├── winner: 'A' | 'B'
    ├── how: { col: S|B|A|CO|E, team, role, tipo, detail: fuera|malla, reason: potencia|bajo }
    ├── blockTouch?: { team, role }      ← tocó el bloqueo y salió
    ├── auto?: true                      ← punto dado por la app (saque fuera/malla)
    └── lines[]  (trazos)
        ├── team, kind: saque|K1|K2, role
        ├── from {x,y}, to {x,y}         ← cancha de 18×9 m, red en x = 9
        ├── result: sigue|punto|error|bloqueado, touch?: bloqueo|defensa
        ├── rec?: { role, grade }        ← recepción (en el saque)
        ├── def?: { role, grade }        ← defensa (en los ataques)
        └── t: marca de tiempo
```

## 7. Decisiones tomadas

| # | Decisión | Motivo |
|---|---|---|
| D-01 | Registro en vivo por una persona, con revisión posterior | Así se trabaja hoy en la cancha. |
| D-02 | Tablet como dispositivo principal, sin dejar fuera celular y portátil | Tamaño de la cancha y velocidad de toque. |
| D-03 | Se analizan los dos equipos | Se necesita la información propia y la del rival. |
| D-04 | Guardar la posición exacta del toque, no solo la zona | Permite agregar los cuadrantes A–D u otras divisiones sin perder datos. |
| D-05 | La app propone jugador, complejo (K1/K2) y rotación | Reduce los toques por acción. |
| D-06 | El color lo define quién hizo la acción (azul a la izquierda, rojo a la derecha) | Lectura rápida, igual que las planillas en papel. |
| D-07 | Saque fuera o a la malla da punto automático, con corrección a ace | La mayoría de los saques que salen no los toca nadie; el caso raro se corrige con un toque. |
| D-08 | Ataque fuera o a la malla **pregunta** en vez de dar el punto | El balón puede tocar el bloqueo o la defensa y el punto es del atacante. |
| D-09 | Bloqueo que toca y sale = punto del atacante + error de bloqueo (B) | Permite ver qué bloqueador "regala" balones. |
| D-10 | Defensa que toca y sale = punto del atacante + error de defensa (0) | Se definió primero como solo punto del atacante y después se sumó la calificación de defensa. |
| D-11 | Misma escala (#, +, −, =) para recepción y defensa | Coincide con la planilla en papel (3, 2, 1, 0). |
| D-12 | Los comportamientos opcionales van en una pestaña de Configuración | Ya eran varios y no debían mezclarse con los datos de los equipos. |
| D-13 | Fórmulas provisionales de **eficacia** (puntos / intentos) | Las fórmulas oficiales todavía no se han definido. |

## 8. Pendientes y preguntas abiertas

**Por definir con el cuerpo técnico:**
- [ ] **Fórmulas de eficacia y eficiencia** oficiales. Se propuso eficiencia = (puntos − errores − bloqueados) / intentos, pero no está implementada ni confirmada.
- [ ] **Simbología de las planillas en papel:** qué significan las marcas "=" y "x" al final de algunas líneas.
- [ ] **Tabla de recepción del formato en Word:** las columnas "Receptor en 1 / 6 / 5…" ¿son por zona del receptor o por rotación? Hoy se muestra el total por jugador.
- [ ] Profundizar los **tipos de error** (por ahora A, S y B, más los agregados).

**Funciones no incluidas en el prototipo:**
- [ ] **Cambios de jugadores** durante el partido.
- [ ] **Rotado o flotado** en cada saque.
- [ ] Campo **"Mi equipo"**, para que el propio equipo quede siempre a la izquierda y en azul.
- [ ] **Cuadrantes A–D** para la versión avanzada.
- [ ] **Exportar** el partido (archivo o PDF) e **historial de varios partidos**, para ver tendencias entre partidos.
- [ ] **Sincronización** entre tablet, celular y portátil (primero por archivo, después en la nube).
- [ ] **Repaso con video:** alinear el video con la hora de cada acción para revisar y corregir.
- [ ] **Niveles de registro** (básico, estándar, completo) según lo que se alcance a anotar.

## 9. Limitaciones del prototipo

- Es un solo archivo HTML. Para la versión real se recomienda instalar Node.js y Git y usar un framework.
- Los datos viven solo en el navegador del dispositivo donde se registran.
- Todos los sets usan la misma alineación de números. Solo cambian el saque y la zona del armador por set.
- El partido de ejemplo (Santander vs Valle) es inventado y sirve solo para ver las planillas llenas.
