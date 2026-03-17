# Abyssal Perception

> Interactive cosmic horror experience with real-time camera distortion and generative audio synthesis.

<p align="center">
  <strong>Tu rostro es el portal. Tus acciones componen el sonido.</strong>
</p>

<p align="center">
  <a href="https://dajimmy90.github.io/cthulhu-experience/abyssal-perception.html"><strong>&#9758; Abrir la experiencia en vivo</strong></a>
</p>

---

## What is this?

**Abyssal Perception** is a browser-based interactive art piece that transforms your webcam feed into a Lovecraftian descent. Move your cursor to distort reality. Find the hidden fracture points. Each click takes you deeper — and your every action composes a unique soundscape in real-time.

No pre-recorded music. No pre-made assets. Everything is generated live from code.

## How to Use

1. Open the [live experience](https://dajimmy90.github.io/cthulhu-experience/abyssal-perception.html) or `abyssal-perception.html` locally
2. Click **"Interceptar seal"**
3. Grant camera access
4. **Move** your cursor to warp the image and compose sound
5. **Hold click + drag** to sculpt pixels like clay (ZBrush-style smudge)
6. **Excavate** to uncover the hidden **sigil** — a light buried under the surface
7. Click the sigil to descend — each level brings a new scene type
8. Repeat. Go deeper. No two levels are ever the same.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Visual Engine | Canvas 2D pixel manipulation |
| Camera | WebRTC getUserMedia |
| Audio | Web Audio API (FM synthesis, oscillators, noise) |
| Randomness | Seeded LCG PRNG |
| UI | Vanilla HTML/CSS/JS (zero dependencies) |

## Audio Components (7 reactive layers)

| Layer | Controlled by |
|-------|--------------|
| **Theremin** | Cursor Y = pitch, velocity = volume, digging = wider vibrato |
| **Hover tone** | Always active — cursor X = freq, Y = filter cutoff |
| **4 Drones** | Total accumulated erosion + local erosion + level depth |
| **5 FM Metallic** | Cursor velocity + acceleration (lower threshold while digging) |
| **Bandpass noise** | Sigil proximity + erosion density under cursor |
| **Sub-bass rumble** | Total distortion accumulated across the entire canvas |
| **Granular crackle** | Hovering over already-eroded zones |
| **Resonant ping** | Each click — pitch from cursor Y, volume from local erosion |
| **Delay/reverb** | Feedback and delay time modulate with erosion depth |

## Scene Types (procedurally selected per level)

- **Excavation** — Base dig + smudge + monochrome tint
- **Fly Eye** — Hexagonal mosaic with different zooms per cell
- **Stained Glass** — Organic Voronoi tessellation with dark borders
- **White Eyes** — Face detection: eyes go white + flowing black tears
- **Abyss Mouth** — Face detection: forced smile opens into darkness
- **Corridor** — Deep perspective tunnel with image at vanishing point
- **Meltdown** — Everything melts downward based on brightness

## Visual Effects

- ZBrush-style pixel smudge/pull displacement field
- Chromatic aberration + color bleeding on eroded areas
- VHS tracking bands + horizontal sync tears
- CRT scanline overlay
- 8 procedural palettes (carmesi, sepia, ceniza, sangre, hueso, oxido, mercurio, vacio)

## Files

```
├── abyssal-perception.html      # Main experience (self-contained)
├── index.html                   # Pixel Sound Lab (initial prototype)
├── cosmic-terror-philosophy.md  # Algorithmic philosophy manifesto
├── ENGINEERING.md               # Technical documentation
└── README.md                    # This file
```

## Browser Support

Chrome 80+, Firefox 75+, Edge 80+, Safari 14.1+ (partial).
Requires webcam and JavaScript.

## Philosophy

See [cosmic-terror-philosophy.md](cosmic-terror-philosophy.md) for the full "Abyssal Perception" algorithmic philosophy — the generative aesthetic manifesto that guided this implementation.

## License

MIT

---

*Built with Web Audio API, Canvas 2D, and cosmic dread.*
