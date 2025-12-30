# V17 OrbitalClass++ — Analog Apocalypse Edition

**Mars doesn't just win — it gaslights you first.**

A highly realistic, brutally difficult **Gymnasium** reinforcement learning environment simulating a crewed Mars surface habitat. This is not a toy problem — it integrates real physics, ISRU chains, 3D printing expansion, battery aging, dust storms, radiation, and (in V17) direct ports of failure modes from NASA's **CHAPEA** and **HI-SEAS** analog missions: water leaks, comms blackouts, and psychological sabotage.

Built from first principles with credible engineering parameters. The goal: achieve permanent human presence on Mars despite cascading failures and human factors.

## Features

- **Full ECLSS modeling**: O₂/CO₂, water recycling (partial), food from crops/fish/chickens
- **ISRU chain**: MOXIE-style O₂ production, Sabatier for CH₄/H₂O, pyrolysis to plastic feedstock
- **In-situ manufacturing**: Regolith mining → 3D printing → deploy solar panels or radiation shielding bags via risky EVAs
- **Power system**: Solar with dust degradation, large battery with realistic cycle aging and DoD stress
- **Environmental hazards**: Diurnal cycles, global dust storms, solar particle events
- **V17 Analog Failures**:
  - Random water leaks draining reserves
  - Comms blackouts causing isolation stress
  - Low crew mood → probabilistic **psych sabotage** (agent actions randomly nerfed)
  - Unreliable resupply windows every ~26 months
- **Human factors**: Crew health/mood dynamically affect performance and trigger despair termination
- Dense reward shaping + survival objective (10+ year missions possible... in theory)

## Installation

```bash
pip install gymnasium numpy pydantic
```

Copy the provided `OrbitalClassv17.py` (or save the code as such) into your project.

## Quick Start

```python
import gymnasium as gym
from OrbitalClassv17 import MarsHabitatEnv  # assuming saved as OrbitalClassv17.py

env = MarsHabitatEnv()
obs, info = env.reset(seed=42)

for _ in range(1000):
    action = env.action_space.sample()  # random policy (will die quickly)
    obs, reward, terminated, truncated, info = env.step(action)
    if terminated or truncated:
        break
```

Run the built-in demo (random policy, expect early death):

```bash
python OrbitalClassv17.py --sols 3650 --difficulty CHAPEA
```

## Spaces

- **Action Space**: `Box(0, 1, (28,))` — continuous fractions with pulse actions (>0.8) for discrete events
- **Observation Space**: `Box(-5, 5, (46,))` — normalized state variables including inventory, equipment health, and failure flags

### Action Descriptions

| Index | Description                                      |
|-------|--------------------------------------------------|
| 0     | Heater power fraction (0-1 → 0-15 kW)            |
| 1-9   | Core habitat systems power (granular, total 0-20 kW) |
| 10    | Crop lighting intensity (0-1)                    |
| 11    | Fish tank systems (0-1)                          |
| 12    | Chicken coop systems (0-1)                        |
| 13    | MOXIE (CO₂ → O₂) fraction (0-1)                  |
| 14    | Sabatier (CO₂ + H₂ → CH₄ + H₂O) fraction (0-1)   |
| 15    | Water recycling intensity (0-1) *(placeholder)*  |
| 16    | Atmospheric processor intensity (0-1) *(placeholder)* |
| 17    | Regolith mining rover power fraction (0-1)       |
| 18    | Pyrolysis (CH₄ → plastic) fraction (0-1)         |
| 19    | Printer enable fraction (0-1)                    |
| 20    | Queue solar_panel job (pulse >0.8)               |
| 21    | Queue regolith_bag job (pulse >0.8)              |
| 22    | Queue structure_brace job (pulse >0.8)           |
| 23    | Queue o2_filter job (pulse >0.8)                 |
| 24    | Queue tool_kit job (pulse >0.8)                  |
| 25    | Apply printed solar_panel (EVA pulse >0.8)       |
| 26    | Apply printed regolith_bag (EVA pulse >0.8)      |
| 27    | Repair printer with tool_kit (EVA pulse >0.8)    |

## Difficulty Modes

- `Nightmare`: High storm/SPE frequency, fast degradation
- `CHAPEA`: Realistic analog failure rates (default in demo)

Customize via `MarsConfig` dataclass.

## Known Issues / TODO

- Actions 15-16 (water recycling, atm processor) defined but not fully implemented
- Some printed parts (structure_brace, o2_filter) queued but no installation action yet
- Resupply adds spares but inventory linking needs fix

## Credits

Originally by Anonymous Mars Settler  
Upgraded to V17 by Grok (December 2025)  
Inspired by real Mars architecture studies and NASA's human factors research.

**Mars is hard. This environment makes sure you never forget it.** 🚀💀
