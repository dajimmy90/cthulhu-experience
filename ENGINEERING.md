# Abyssal Perception — Documentación de Ingeniería

> Terror cósmico interactivo con cámara en tiempo real, distorsión generativa de píxeles y síntesis de audio procedural.

---

## 1. Visión General del Sistema

```
┌─────────────────────────────────────────────────────────────┐
│                    ABYSSAL PERCEPTION                       │
│                                                             │
│  ┌──────────┐   ┌──────────────┐   ┌────────────────────┐  │
│  │  CÁMARA  │──▶│  DISTORSIÓN  │──▶│  CANVAS RENDERING  │  │
│  │  (WebRTC)│   │  ENGINE      │   │  (2D Context)      │  │
│  └──────────┘   └──────┬───────┘   └────────────────────┘  │
│                        │                                    │
│  ┌──────────┐   ┌──────▼───────┐   ┌────────────────────┐  │
│  │  MOUSE   │──▶│  GAME STATE  │──▶│  AUDIO ENGINE      │  │
│  │  INPUT   │   │  MACHINE     │   │  (Web Audio API)   │  │
│  └──────────┘   └──────────────┘   └────────────────────┘  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Stack Tecnológico

| Componente | Tecnología | Justificación |
|------------|-----------|---------------|
| Renderizado visual | Canvas 2D API | Acceso directo a píxeles vía `getImageData`/`putImageData` |
| Captura de video | WebRTC `getUserMedia` | Stream de cámara en tiempo real sin plugins |
| Síntesis de audio | Web Audio API | Grafo de nodos para síntesis FM, osciladores y filtros |
| Tipografía | Google Fonts (Special Elite, Lora, Poppins) | Estética de máquina de escribir para narrativa lovecraftiana |
| Aleatoriedad | LCG seeded PRNG | Reproducibilidad determinista para generación procedural |

### Archivos del Proyecto

```
pixel-sound-lab/
├── abyssal-perception.html     # Experiencia principal (autocontenida)
├── index.html                  # Pixel Sound Lab (prototipo inicial)
├── cosmic-terror-philosophy.md # Filosofía algorítmica del movimiento
├── ENGINEERING.md              # Este documento
├── .gitignore                  # Exclusiones de Git
└── README.md                   # Documentación pública del proyecto
```

---

## 2. Arquitectura de Componentes

### 2.1 Motor de Captura de Cámara

```javascript
// Solicitud de stream con preferencia por cámara frontal
navigator.mediaDevices.getUserMedia({
    video: { width: { ideal: 640 }, height: { ideal: 480 }, facingMode: 'user' }
})
```

**Flujo:**
1. El usuario otorga permisos de cámara al hacer click en "Abrir el portal"
2. El stream se asigna a un elemento `<video>` oculto (`display: none`)
3. En cada frame del render loop, se dibuja el video al canvas con `ctx.drawImage()`
4. Se aplica mirror horizontal (`scale(-1, 1)`) para efecto selfie natural
5. Si la cámara no está disponible, se genera un patrón de ruido sintético como fallback

**Resolución:** 640x480 capturados, escalados a pantalla completa vía canvas.

### 2.2 Motor de Distorsión de Píxeles

El motor opera pixel-a-pixel sobre `ImageData`, aplicando transformaciones de coordenadas source → destination.

#### Algoritmo Base (Fase Singular)

Para cada píxel `(x, y)` en el canvas de destino:

```
1. Calcular distancia al cursor: dist = √((x-mx)² + (y-my)²)
2. Calcular influencia: influence = max(0, 1 - dist/radius)²
3. Aplicar cadena de transformaciones al source:
   a. ONDA:        srcX += sin(y·freq + t·0.003) · amplitude · influence
   b. DERRETIR:    srcY += influence² · meltAmount · (40 + level·15)
   c. GLITCH:      srcX += random() · glitchAmount (probabilístico)
   d. TENTÁCULO:   srcX += sin(phase·2.3)·cos(phase·0.7) · amplitude
   e. CROMÁTICO:   R.srcX += offset, B.srcX -= offset (aberración)
4. Clamp coordenadas a [0, width-1] × [0, height-1]
5. Aplicar color shift cósmico (verde bioluminiscente + ultravioleta)
6. Aplicar viñeta basada en distancia al centro
```

**Complejidad:** O(W × H) por frame. Para 1920×1080 = ~2M píxeles/frame.

#### Transformaciones Detalladas

| Efecto | Fórmula | Parámetros |
|--------|---------|------------|
| **Onda** | `sin(y·f + t·0.003) · A · influence` | f: 0.015+level·0.003, A: 5+intensity·30 |
| **Derretir** | `influence² · melt · (40 + level·15)` | Desplazamiento vertical + oscilación horizontal |
| **Glitch** | `random()·60·glitch` (p < glitch·0.15) | Desplazamiento horizontal estocástico |
| **Tentáculo** | `sin(φ·2.3)·cos(φ·0.7) · A` | φ = t·0.001 + dist·0.01, estilo lovecraftiano |
| **Cromático** | R offset +N, B offset -N | N = chromatic·(10+level·5)·influence |

#### Fase Prismática

Divide el canvas en N celdas (4 a 20, según nivel):

```
cols = ceil(√count)
rows = ceil(count / cols)
cellW = canvasWidth / cols
cellH = canvasHeight / rows
```

Cada celda recibe:
- `distortSeed`: semilla única para fase de distorsión
- `rotAngle`: ángulo de rotación local (-0.15 a +0.15 rad)
- `hueShift`: desplazamiento de tono (-30 a +30)
- `meltDir`: dirección de derretimiento (±1)

Una celda aleatoria (determinada por seed) contiene el sigilo activo.

### 2.3 Sistema de Sigilo (Punto de Fractura)

El sigilo es el punto oculto que el usuario debe encontrar para descender al siguiente nivel.

**Generación:**
```javascript
setSeed(state.seed + state.level * 137);  // Determinista por nivel
sigil.x = margin + seededRandom() * (width - margin·2);
sigil.y = margin + seededRandom() * (height - margin·2);
sigil.radius = max(15, 35 - level·2);     // Se reduce con profundidad
```

**Visibilidad:**
- Invisible cuando el mouse está lejos (> 200px)
- Se revela gradualmente con proximidad: `alpha = proximity · 0.3 · pulse`
- Renderizado con `globalCompositeOperation: 'screen'`
- Gradiente radial: verde bioluminiscente → azul cósmico → transparente
- Símbolo hexagonal rotante superpuesto

**Detección de click:**
```javascript
dist < sigil.radius * 2.5  // Zona de tolerancia aumentada para UX
```

### 2.4 Máquina de Estados del Juego

```
              click "Abrir el portal"
    INTRO ─────────────────────────────▶ SINGULAR (Nivel I)
                                              │
                                     encontrar sigilo
                                              │
                                              ▼
                                        PRISMÁTICA (Nivel II)
                                              │
                                     encontrar sigilo
                                              │
                                              ▼
                                        PRISMÁTICA (Nivel III)
                                              │
                                             ...
                                        (∞ iteraciones)
```

**Transición entre niveles:**
1. Click en sigilo detectado → `triggerImpact()` (audio)
2. Overlay de transición (800ms fade-in)
3. Texto "PROFUNDIDAD [numeral romano]" con efecto glitch
4. Regeneración de sigilos y celdas prismáticas
5. Incremento de parámetros base de distorsión
6. Overlay fade-out → nuevo nivel activo

**Escalado de dificultad por nivel:**

| Nivel | Celdas | Radio sigilo | Intensidad | Melt | Glitch |
|-------|--------|-------------|------------|------|--------|
| I | 1 (singular) | 33px | 0.38 | 0.26 | 0.15 |
| II | 8 | 31.5px | 0.46 | 0.32 | 0.20 |
| III | 12 | 30px | 0.54 | 0.38 | 0.25 |
| V | 20 | 27px | 0.70 | 0.50 | 0.35 |
| X | 20 | 20px | 1.00 | 0.80 | 0.60 |

---

## 3. Motor de Audio Generativo

### 3.1 Grafo de Señal

```
                    ┌─────────────┐
                    │  THEREMIN    │──┐
                    │  (sine+vib) │  │
                    └─────────────┘  │
                                     │    ┌──────────┐    ┌────────────┐
                    ┌─────────────┐  ├──▶│  MASTER  │──▶│  SPEAKERS  │
                    │  DRONES ×4  │──┤   │  GAIN    │    │            │
                    │  (saw+tri)  │  │   │  (0.35)  │    └────────────┘
                    └─────────────┘  │   └────┬─────┘
                                     │        │
                    ┌─────────────┐  │   ┌────▼─────┐
                    │  METALLIC×4 │──┤   │  DELAY   │──┐
                    │  (FM synth) │  │   │  (0.4s)  │  │
                    └─────────────┘  │   └──────────┘  │
                                     │        ▲        │
                    ┌─────────────┐  │   ┌────┴─────┐  │
                    │  NOISE      │──┘   │ FEEDBACK │◀─┘
                    │  (bandpass) │       │  (0.3)   │
                    └─────────────┘       └──────────┘
```

### 3.2 Componentes de Síntesis

#### Theremin (Control por mouse)

```
Oscillator (sine, 220Hz base)
    ↑ modulado por
Vibrato (sine, 5Hz) → GainNode (depth: 8Hz)
    ↓
GainNode (volumen dinámico) → Master

Pitch:  100 + (1 - mouseY/height) · 600 + level·50 Hz
Volume: min(0.4, mouseSpeed · 0.003) · thereminVol
```

**Mapeo:** El eje Y controla la frecuencia (arriba = agudo, abajo = grave). La velocidad del mouse controla el volumen — quietud = silencio, movimiento = revelación.

#### Drones (Paisaje sonoro ambiental)

4 osciladores con frecuencias fundamentales de la serie subarmónica:

| Drone | Frecuencia | Tipo | Filtro LP |
|-------|-----------|------|-----------|
| 0 | 55 Hz (A1) | Sawtooth | 200 Hz |
| 1 | 82.5 Hz (E2) | Triangle | 250 Hz |
| 2 | 82.41 Hz (E2) | Sawtooth | 300 Hz |
| 3 | 36.71 Hz (D1) | Triangle | 350 Hz |

**Modulación:** Cada drone tiene envolvente sinusoidal lenta:
```
volume = droneBase · (sin(t · 0.001 · (1 + i·0.3)) · 0.5 + 0.5)
detune = sin(t · 0.0003 · (i+1)) · (5 + level·3) cents
```

El detune creciente con el nivel crea batimientos (beats) cada vez más perturbadores.

#### Metálico (Síntesis FM por percusión)

4 pares carrier/modulator con ratios inarmónicos:

| Par | Carrier | Modulator | Ratio | Carácter |
|-----|---------|-----------|-------|----------|
| 0 | 200 Hz | 280 Hz | 1.4 | Campana baja |
| 1 | 350 Hz | 595 Hz | 1.7 | Gong |
| 2 | 500 Hz | 1000 Hz | 2.0 | Metal agudo |
| 3 | 650 Hz | 1495 Hz | 2.3 | Choque industrial |

**Trigger:** Velocidad de mouse > threshold (3, 5, 7, 9 px/frame respectivamente).
**Envolvente de impacto:** Attack instantáneo → decay exponencial 0.8-1.4s.

**En click (correcto o incorrecto):**
```
All carriers: gain = 0.2 → 0.001 en 0.8s (exponential)
All modulators: modIndex = 300+level·100 → 1 en 1.0s
Noise: burst 0.25 → 0.01 en 0.5s, filter sweep 3000→200 Hz
```

#### Ruido (Textura estocástica)

```
BufferSource (white noise, 2s looped)
    → BiquadFilter (bandpass, Q=5)
    → GainNode → Master

Frequency: 400 + proximity·2000 + mouseSpeed·10 Hz
Volume:    0.02 + sigilProximity·0.1 + level·0.01
```

El ruido bandpass actúa como "detector de proximidad" — se intensifica cuando el mouse se acerca al sigilo, creando tensión subconsciente.

### 3.3 Reverberación

Implementada como delay con feedback (sin convolution para mantener el archivo autocontenido):

```
Signal → Delay (400ms) → Feedback Gain (0.3) → Lowpass (2000Hz) → Delay (loop)
                                                                  → Master
```

Esto crea un espacio acústico con reverb tail de ~1.5s, sugiriendo un espacio cavernoso/subterráneo.

### 3.4 Composición Reactiva

El sistema no tiene música pregrabada. Las acciones del usuario son la partitura:

| Acción del Usuario | Respuesta Sonora |
|-------------------|------------------|
| Mouse quieto | Silencio + drones mínimos |
| Mouse lento | Theremin suave, drones moderados |
| Mouse rápido | Theremin fuerte + metálicos activados |
| Acercarse al sigilo | Ruido bandpass creciente |
| Click correcto | Impacto FM + noise burst + transición |
| Click incorrecto | Impacto FM solo (castigo sonoro) |
| Nivel más profundo | Drones más fuertes, detune mayor, más capas |

---

## 4. Generador de Números Pseudoaleatorios

Se usa un **Linear Congruential Generator (LCG)** para aleatoriedad determinista:

```javascript
// Park-Miller LCG
function seededRandom() {
    rngState = (rngState * 16807) % 2147483647;
    return (rngState - 1) / 2147483646;
}
```

**Propiedades:**
- Período: 2³¹ - 2 (~2.1 billones)
- Distribución uniforme en [0, 1)
- Determinista: misma semilla → misma secuencia
- Se re-semilla en cada generación de nivel: `seed + level * 137`

El multiplicador 137 (primo) evita correlación entre niveles consecutivos.

---

## 5. Interfaz de Usuario

### 5.1 Pantalla de Intro

- Símbolo Unicode ⚈ con animación de pulso CSS (4s ease-in-out)
- Tipografía Special Elite para estética de máquina de escribir
- Texto narrativo en Lora italic para tono literario
- Botón con efecto shimmer CSS (gradiente animado)
- Fade-out de 1.5s con `transition: opacity`

### 5.2 HUD (Heads-Up Display)

Información mínima no intrusiva:
- Esquina superior izquierda: título + profundidad
- Esquina superior derecha: nivel (numeral romano) + fase
- `pointer-events: none` para no interferir con interacción

### 5.3 Cursor Personalizado

```css
cursor: none;  /* Ocultar cursor nativo */
#cursor {
    border: 1px solid var(--bioluminescent);
    mix-blend-mode: difference;
    transition: transform 0.1s;
}
#cursor.near-sigil {
    transform: scale(2.5);        /* Expand near sigil */
    border-color: var(--blood);   /* Red warning */
}
```

### 5.4 Panel de Control (Sidebar)

Accesible vía botón ⚙ en esquina inferior derecha:
- **Distorsión:** Intensidad, Derretir, Glitch, Cromático (0-100)
- **Audio:** Theremin, Metálico, Drones (0-100)
- **Seed:** Semilla numérica para reproducibilidad

Todos los controles actualizan en tiempo real sin reiniciar la experiencia.

---

## 6. Rendimiento

### Optimizaciones Implementadas

| Técnica | Impacto |
|---------|---------|
| `willReadFrequently: true` en canvas context | Optimiza acceso repetido a ImageData |
| Cálculos de distancia sin sqrt cuando es posible | Reduce operaciones por píxel |
| Clamp de coordenadas con Math.max/min | Evita acceso fuera de bounds |
| Scanlines como overlay simple (no por píxel) | Efecto CRT con mínimo costo |
| Audio con rampas exponenciales | Evita clicks/pops en transiciones |

### Cuellos de Botella Conocidos

1. **Fase prismática:** Crea N offscreen canvases por frame. Para N>12, puede caer bajo 30fps en hardware bajo.
2. **Manipulación pixel-a-pixel:** ~2M iteraciones/frame en 1080p. Podría optimizarse con WebGL shaders.
3. **Audio nodes:** ~15 nodos activos simultáneamente. Dentro del límite de Web Audio API.

### Mejoras Futuras

- Migrar distorsión a WebGL fragment shaders (60fps garantizados)
- Implementar Web Workers para procesamiento de píxeles off-thread
- Usar OffscreenCanvas para fase prismática
- Añadir compresión dinámica al master bus de audio

---

## 7. Paleta de Color

Derivada de bioluminiscencia oceánica y radiación cósmica:

| Variable CSS | Hex | Uso |
|-------------|-----|-----|
| `--void` | `#050508` | Fondo principal |
| `--abyss` | `#0a0a12` | Fondo secundario |
| `--deep-green` | `#1a3a2a` | Acentos UI, bordes |
| `--bioluminescent` | `#2aff8a` | Sigilo, cursor, highlights |
| `--cosmic-blue` | `#4a2aff` | Gradientes de sigilo |
| `--ultraviolet` | `#7b2aff` | Efectos de profundidad |
| `--amber-decay` | `#cc8844` | Degradación de realidad |
| `--flesh` | `#d4a574` | Tonos de piel distorsionados |
| `--blood` | `#8b1a1a` | Cursor near-sigil, peligro |

---

## 8. Compatibilidad

| Navegador | Soporte | Notas |
|-----------|---------|-------|
| Chrome 80+ | Completo | Referencia principal |
| Firefox 75+ | Completo | — |
| Safari 14.1+ | Parcial | `webkitAudioContext` fallback incluido |
| Edge 80+ | Completo | Basado en Chromium |
| Mobile Chrome | Completo | Touch events implementados |
| Mobile Safari | Parcial | Requiere gesto para audio + cámara |

**Requisitos:**
- Cámara web (o se usa fallback de ruido)
- Permisos de cámara otorgados por el usuario
- JavaScript habilitado
- Conexión para Google Fonts (o fallback a system fonts)

---

## 9. Fundamento Filosófico

Ver [cosmic-terror-philosophy.md](cosmic-terror-philosophy.md) para la filosofía algorítmica completa "Abyssal Perception".

**Principios clave:**
1. **La realidad como membrana** — La cámara captura consenso; el algoritmo lo disuelve
2. **Descenso voluntario** — El usuario elige activamente profundizar (click en sigilo)
3. **Composición involuntaria** — Las acciones del usuario son la partitura musical
4. **Complejidad acumulativa** — Cada nivel hereda y amplifica el anterior
5. **Terror como estética** — No jump-scares, sino erosión gradual de lo familiar

---

*Documento generado el 2026-03-16. Proyecto: Modelos de Lenguaje para Procesos Creativos.*
