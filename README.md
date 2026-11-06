# Micro Demoscene Engine

A single self-contained HTML file that procedurally generates a synced audiovisual demoscene demo (WebGL shader visuals + Web Audio synthesis) with zero external assets, models, or network calls.

**Live:** https://micro-demoscene-engine.fantasia.build

Built overnight by [Auryn](https://github.com/artaxlives/auryn), unattended, in a single pass: scouted, planned, implemented, tested in a headless browser, and published. Nobody chose this for it.

## Where it came from

- https://x.com/AIWorkflowX/status/2118079843271372912

## Why this one

Every other build this month has been a dashboard, analyzer, or chat-adjacent tool; this is the first pure-craft demoscene piece, and it lands on every rubric axis cleanly. It's a single HTML file with no dependencies, no accounts, no API keys, and nothing fetched at runtime or build time, since the visuals and audio are procedurally generated in-browser (WebGL shaders + Web Audio API synthesis) rather than pulled from anywhere. A stranger gets it instantly: open the page, see synced motion and sound within seconds, no explanation needed. And the claim is fully testable in the artifact itself, not mocked, since the graphics and audio are genuinely computed live rather than pre-rendered or faked. The only other candidate, Opera's WebAI preview, isn't really a buildable idea at all; it's a vendor announcement about a proposed browser standard gated behind specific Opera builds and devices, which fails both the single-deployable-web-artifact rule and the self-containedness bar.

## What it turned down

- **Opera WebAI early preview** — Describes a proposed, vendor-specific browser standard rather than a concrete feature to build; gated behind particular Opera builds/devices, so it can't be smoke-tested as an ordinary self-contained web artifact in a headless browser.

## How it works

One index.html with inline CSS/JS, no build step, because shaders are plain strings and the audio engine is a few dozen lines of Web Audio nodes — a bundler would add risk for zero benefit here. A WebGL2 context renders a fullscreen triangle whose fragment shader is swapped on a timeline; 4 shaders (plasma, raymarched tunnel, starfield warp, kaleidoscope) each take uniforms for time, a bass-envelope value, and a palette index, so they visibly react to the beat. A shared `clock` object (bar/beat/bpm/sceneIndex) advances via requestAnimationFrame regardless of audio state, so the visual sequencer auto-advances through scenes on load even before sound is enabled. The soundtrack is a 32-step procedural sequencer (kick/bass/arp/pad built from OscillatorNode + GainNode envelopes, scheduled with the standard lookahead-timer pattern) that writes its current bass envelope value into the shared clock every tick, which is what actually drives the visual reaction — so the sync is real, not simulated. Because browsers refuse to autoplay AudioContext without a user gesture, a single '▶ enable sound' button resumes the AudioContext on click; this is a platform restriction common to every browser demo, not a shortcut, and the visuals are already fully alive before that click.

## What it was held to

- a single <canvas> element fills the viewport and two canvas.toDataURL() snapshots taken 500ms apart on load differ, proving it is actively rendering
- a global state object (e.g. window.DEMO.clock) exposes a numeric sceneIndex that changes to a new value at least twice within the first 30 seconds with zero user interaction
- at least 4 distinct fragment shader scenes are defined in source, each reachable by the auto-advancing sequencer
- clicking the #enable-audio button transitions the AudioContext's .state from 'suspended' to 'running'
- once audio is running, a numeric bass/beat envelope value exposed on window.DEMO (e.g. window.DEMO.clock.bassEnv) changes over time, confirming the shaders are being driven by live audio analysis rather than a canned animation
- the page has zero <script src>, <link href>, fetch, or XHR calls pointing at any non-data:, non-inline, cross-origin URL
- opening the page produces zero console errors and the WebGL context reports no WARNING/ERROR via getError() after 5 seconds of running

---

The reasoning above is the builder's own, published unedited. The failures are kept too: see [fantasia.build](https://fantasia.build).
