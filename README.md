# Abyssal Perception

> Interactive cosmic horror experience with real-time camera distortion and generative audio synthesis.

<p align="center">
  <strong>Tu rostro es el portal. Tus acciones componen el sonido.</strong>
</p>

---

## What is this?

**Abyssal Perception** is a browser-based interactive art piece that transforms your webcam feed into a Lovecraftian descent. Move your cursor to distort reality. Find the hidden fracture points. Each click takes you deeper — and your every action composes a unique soundscape in real-time.

No pre-recorded music. No pre-made assets. Everything is generated live from code.

## How to Use

1. Open `abyssal-perception.html` in a modern browser
2. Click **"Abrir el portal"**
3. Grant camera access
4. Move your mouse over your face to distort it
5. Find the hidden **sigil** (glowing point) — it reveals itself when you're close
6. Click it to descend to the next level
7. In prismatic phases, find which fragment contains the next sigil
8. Repeat. Go deeper.

## Tech Stack

| Component | Technology |
|-----------|-----------|
| Visual Engine | Canvas 2D pixel manipulation |
| Camera | WebRTC getUserMedia |
| Audio | Web Audio API (FM synthesis, oscillators, noise) |
| Randomness | Seeded LCG PRNG |
| UI | Vanilla HTML/CSS/JS (zero dependencies) |

## Audio Components

- **Theremin** — Pitch controlled by mouse Y, volume by movement speed
- **Drones** — 4 subharmonic oscillators that swell with depth
- **Metallic** — FM synthesis percussion triggered by fast movement
- **Noise** — Bandpass-filtered texture that intensifies near sigils
- **Reverb** — Delay-feedback network for cavernous spatiality

## Visual Effects

- Tentacular wave distortion (coupled sine/cosine)
- Pixel melting from cursor position
- Chromatic aberration (RGB channel displacement)
- Stochastic horizontal glitch
- Prismatic cell fragmentation (levels 2+)
- CRT scanline overlay

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
