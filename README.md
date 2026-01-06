# OrbitLab — orbital mechanics & N‑body sandbox (with energy‑drift benchmarks)

![CI](https://github.com/slavasavelyev/orbitlab/actions/workflows/ci.yml/badge.svg)
![License](https://img.shields.io/badge/license-MIT-green)

OrbitLab is a small, **clean**, and **reproducible** physics sandbox:

- simulate 2‑body and N‑body gravity,
- compare numerical integrators (**Euler**, **RK4**, **Leapfrog / Velocity‑Verlet**),
- measure **energy drift** (a key sign of numerical stability),
- generate simple plots for trajectories and energy error.

It is designed to be easy to read and easy to run.

---

## Why this is interesting

In orbital problems, you can write code that "looks correct" but slowly destroys the orbit.
That happens because some integration methods do not preserve physics well.

OrbitLab helps you **see** it:

- Euler: fast, but energy often drifts a lot
- RK4: accurate per step, but still not symplectic
- Leapfrog (symplectic): often keeps energy bounded for long runs

---

## Quick start (3 commands)

### 1) Create and activate a virtual environment

**Windows (PowerShell):**
```bash
python -m venv .venv
.\\.venv\\Scripts\\Activate.ps1
```

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

### 2) Install dependencies

Minimal (just simulation):
```bash
pip install -r requirements.txt
```

With plots + dev tools:
```bash
pip install -r requirements.txt
pip install -r requirements-dev.txt
```

### 3) Run the demo

This will simulate a near-circular 2‑body orbit and create plots in `outputs/`:
```bash
python -m orbitlab.demo
```

You can also open the simple CLI help:
```bash
python -m orbitlab --help
```

---

## What you get after the demo

OrbitLab will create a folder:

- `outputs/trajectory.png` — orbit path
- `outputs/energy_drift.png` — how total energy changes over time
- `outputs/summary.json` — small machine-readable summary (numbers you can cite)

---

## Project structure

```
orbitlab/
  orbitlab/
    __main__.py      # CLI entry point: python -m orbitlab
    demo.py          # runs a safe default demo and saves plots
    scenarios.py     # ready-to-run initial conditions
    simulate.py      # simulation loop (stores trajectory)
    integrators.py   # Euler / RK4 / Leapfrog implementations
    nbody.py         # gravity acceleration (O(N^2), memory-safe)
    metrics.py       # energy, momentum, useful diagnostics
    constants.py     # G, tiny helpers
  tests/             # fast tests for correctness + invariants
  docs/              # short explanations (optional reading)
```

---

## How the physics is implemented (simple explanation)

### 1) Gravity model

For every body `i`, we compute acceleration from all other bodies `j`:

- direction: from `i` to `j`
- strength: proportional to `m_j`
- distance: scaled by `1 / r^3`

We also add a tiny number called **softening** to avoid division by zero when two bodies
get extremely close in a toy simulation.

### 2) Integrators (how we move the bodies)

Each integrator is a different rule for updating position and velocity each small step `dt`.

- **Euler**: simplest, but can break orbits quickly.
- **RK4**: more accurate per step, more expensive.
- **Leapfrog / Velocity‑Verlet**: symplectic (often best for long-term orbits).

### 3) Energy drift

Total energy = kinetic + potential.

If your simulation is stable, total energy should not explode.
In reality, numerical methods produce some error, so OrbitLab plots energy drift.

---

## How to run your own simulation

Example: 3-body random cluster, leapfrog:

```bash
python -m orbitlab run --scenario random3 --integrator leapfrog --dt 0.005 --steps 5000
```

Orbit + plots will be saved into `outputs/`.

---

## Testing and code quality

Run tests:
```bash
pytest
```

Format + lint:
```bash
ruff format .
ruff check .
```

---

## Notes (important)

- This is an educational project, not a spaceflight engine.
- Units are **dimensionless** by default (to keep the math simple).
- For real orbital work you would use careful units and validated ephemerides.

---

## License

MIT License. See `LICENSE`.
