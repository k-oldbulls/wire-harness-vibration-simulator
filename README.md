# Wire Harness Vibration Simulator

Design work for a simulator that predicts how an installed wire harness moves under vehicle vibration, where it rubs, and how fast the rub wears through its covering. It extends my Requirements Specification 0.3 and Architecture Specification 0.2 with five new components:

- a harness physics engine (Cosserat rod model, three run modes)
- a materials and tribology library covering both bodies at every rub point
- a property calculator that computes bundle stiffness and damping from construction
- a wear and rub-point module that ranks contacts and predicts wear depth by layer
- 3D and augmented reality (AR) visual inspection

**[Open the interactive demo](https://k-oldbulls.github.io/wire-harness-vibration-simulator/)**

## What is here

| File | What it is |
| --- | --- |
| [Design Specification 0.3](docs/design-specification-0.3.md) ([PDF](docs/design-specification-0.3.pdf)) | The build-ready design: physics engine, materials and wear model, assessment ceilings, AR inspection, architecture amendments and the release roadmap |
| [Build Plan 0.1](docs/build-plan-0.1.md) ([PDF](docs/build-plan-0.1.pdf)) | State descriptors, benchmarks B-01 to B-12 with acceptance limits, and 28 dependency-ordered work packages that engineers or coding agents can pick up |
| [Glossary](docs/glossary.md) | The domain terms the two documents use, such as relative wear index, wear depth range and rerun request |
| [index.html](index.html) | The demo page, a single self-contained file |

## The demo

The demo is a simulator of the simulator. It runs a small stand-in model in the browser on invented data, and applies the rules the design specification sets:

- **3D view.** A dummy engine-bay harness with rub points colored by wear band. Drag to rotate, scroll to zoom, click a rub point to select it.
- **Design variants.** Baseline, move clip C3, edge protector on bracket K1, and velour tape at fastener F1. Each one reranks the rub points.
- **Rub points.** The relative wear index for each site with its range and band, rating-only sites ranked separately, and core rerun requests you can run or waive.
- **Wear depth.** Depth against test hours for both bodies, layer by layer, with breach times and a corrosion flag when bare substrate is exposed.
- **Materials.** Pair records with their property basis, and calculated bundle stiffness bounds.
- **Assessment gate.** A D1 request is released with its required statements. D2 and D3 are refused, with the ceiling chain that explains why.

Every number in the demo is made up. It shows how the system would behave, not how any real harness does.

## Status and scope

These are discussion drafts. They draw only on public information from TE Connectivity and the published literature cited in section 14 of the design specification, and they are not affiliated with or endorsed by TE Connectivity. Design Specification 0.3 went through five rounds of independent review before it was marked build-ready, and section 15 summarizes what changed.

© 2026 Keith Willett. All rights reserved.
