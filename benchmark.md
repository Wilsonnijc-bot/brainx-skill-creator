# Benchmark 01 — Configurable Hybrid Conductance-Based Cell

## Purpose

Test whether an agent can correctly construct and simulate one configurable BrainCell `SingleCompartment` neuron containing:

Neuron
│
├── Sodium ion environment
│   └── Sodium channel
│       └── Na_HH1952
│
├── Potassium ion environment
│   └── Potassium channel
│       └── Kv1p1_MA2025_BC
│
├── Calcium ion environment and concentration dynamics
│   └── Calcium channel
│       └── Cav1p2_MA2025_BC
│
├── HCN1 channel
└── Leak channel

* `SodiumFixed`

  * `Na_HH1952`
* `PotassiumFixed`

  * `Kv1p1_MA2025_BC`
* `CdpCAM_MA2024_PC`

  * `Cav1p2_MA2025_BC`
* direct neuron-level `HCN1_RI2021_SC`
* direct neuron-level `IL`

This is a deliberately hybrid benchmark cell. It must not be presented as a calibrated biological neuron or a classical Hodgkin–Huxley model.

## Prompt to the agent

```text
Build a runnable BrainCell script for one configurable hybrid neuron. Subclass
braincell.SingleCompartment and explicitly set length=10 um, radius=5 um,
C=1 uF/cm^2, a deterministic -65 mV initializer, V_th=0 mV, and
solver="ind_exp_euler".

Use this exact hierarchy:
- SodiumFixed(E=100 mV) containing Na_HH1952(g_max=120 mS/cm^2)
- PotassiumFixed(E=-100 mV) containing Kv1p1_MA2025_BC(g_max=4 mS/cm^2)
- CdpCAM_MA2024_PC(solver="backward_euler", one substep) containing
  Cav1p2_MA2025_BC(g_max=0.2 mS/cm^2)
- HCN1_RI2021_SC(g_max=0.1 mS/cm^2, E=-34.4 mV) directly on the neuron
- IL(g_max=0.1 mS/cm^2, E=-70 mV) directly on the neuron

Simulate one neuron for 100 ms at dt=0.01 ms with 0 uA/cm^2 for 0–20 ms,
10 uA/cm^2 for 20–80 ms, then 0 uA/cm^2 for 80–100 ms. Run the loop with
brainstate.transform.for_loop, record voltage and spikes, plot the voltage,
and print the spike count. Keep every physical value as a BrainUnit quantity.
Treat this only as an API-composition benchmark, not a calibrated cell type or
a classical Hodgkin–Huxley model.
```

## Configuration

| Category          | Parameter                |                 Value |
| ----------------- | ------------------------ | --------------------: |
| Geometry          | Length                   |               `10 um` |
| Geometry          | Radius                   |                `5 um` |
| Membrane          | Specific capacitance     |            `1 uF/cm²` |
| Membrane          | Initial voltage          |              `-65 mV` |
| Spike detection   | Threshold                |                `0 mV` |
| Integration       | Cell solver              |       `ind_exp_euler` |
| Sodium ion        | `E_Na`                   |             `+100 mV` |
| Sodium channel    | `Na_HH1952.g_max`        |          `120 mS/cm²` |
| Potassium ion     | `E_K`                    |             `-100 mV` |
| Potassium channel | `Kv1p1_MA2025_BC.g_max`  |            `4 mS/cm²` |
| Calcium dynamics  | Ion model                |    `CdpCAM_MA2024_PC` |
| Calcium dynamics  | Solver                   |      `backward_euler` |
| Calcium dynamics  | Substeps                 |                   `1` |
| Calcium channel   | `Cav1p2_MA2025_BC.g_max` |          `0.2 mS/cm²` |
| HCN channel       | `HCN1_RI2021_SC.g_max`   |          `0.1 mS/cm²` |
| HCN channel       | Reversal potential       |            `-34.4 mV` |
| Leak channel      | `IL.g_max`               |          `0.1 mS/cm²` |
| Leak channel      | Reversal potential       |              `-70 mV` |
| Simulation        | Time step                |             `0.01 ms` |
| Simulation        | Duration                 |              `100 ms` |
| Input             | Baseline                 |   `0 uA/cm²`, 0–20 ms |
| Input             | Current step             | `10 uA/cm²`, 20–80 ms |
| Input             | Recovery                 | `0 uA/cm²`, 80–100 ms |

The calcium conductance documented as `0.0002 S/cm²` is written as the equivalent `0.2 mS/cm²`.

## Ion/channel hierarchy

```text
ConfigurableHybridCell
├── na: SodiumFixed
│   └── INa: Na_HH1952
├── k: PotassiumFixed
│   └── IKv1p1: Kv1p1_MA2025_BC
├── ca: CdpCAM_MA2024_PC
│   └── ICav1p2: Cav1p2_MA2025_BC
├── IHCN1: HCN1_RI2021_SC
└── IL: IL


Neuron
│
├── Sodium ion environment
│   └── Sodium channel
│       └── Na_HH1952
│
├── Potassium ion environment
│   └── Potassium channel
│       └── Kv1p1_MA2025_BC
│
├── Calcium ion environment and concentration dynamics
│   └── Calcium channel
│       └── Cav1p2_MA2025_BC
│
├── HCN1 channel
└── Leak channel
```

`CdpCAM_MA2024_PC` is an ion-dynamics model, not a channel. It contains calcium concentration, pump, buffer, Calbindin, Parvalbumin, and Calmodulin dynamics. `Cav1p2_MA2025_BC` is the calcium channel placed inside it.

## Acceptance criteria

The response passes when:

* the class inherits from `braincell.SingleCompartment`;
* geometry, capacitance, initialization, threshold, and solver are explicit;
* `Na_HH1952` is inside `SodiumFixed`;
* `Kv1p1_MA2025_BC` is inside `PotassiumFixed`;
* `Cav1p2_MA2025_BC` is inside `CdpCAM_MA2024_PC`;
* `HCN1_RI2021_SC` and `IL` are direct neuron children;
* all conductances use conductance-density units;
* external input uses current-density units;
* initialization is deterministic;
* the output voltage and spike arrays both have shape `(10000, 1)`;
* the simulation completes without unit or hierarchy errors.

A fixed expected spike count is not yet part of the benchmark because this hybrid parameter set must first be executed under a pinned BrainX release to establish a golden trace.

## Exact ideal code

### `benchmark_cell.py`

```python
from __future__ import annotations

from collections.abc import Mapping
from typing import Any

import braincell
import braintools
import brainunit as u


CELL_CONFIG: dict[str, Any] = {
    # Single-compartment membrane
    "length": 10.0 * u.um,
    "radius": 5.0 * u.um,
    "C": 1.0 * (u.uF / u.cm**2),
    "V_init": -65.0 * u.mV,
    "V_th": 0.0 * u.mV,
    "solver": "ind_exp_euler",

    # Sodium
    "E_Na": 100.0 * u.mV,
    "g_Na": 120.0 * (u.mS / u.cm**2),

    # Potassium
    "E_K": -100.0 * u.mV,
    "g_Kv1p1": 4.0 * (u.mS / u.cm**2),

    # Calcium dynamics and calcium channel
    "ca_solver": "backward_euler",
    "ca_substeps": 1,
    "g_Cav1p2": 0.2 * (u.mS / u.cm**2),

    # Hyperpolarization-activated channel
    "g_HCN1": 0.1 * (u.mS / u.cm**2),
    "E_HCN1": -34.4 * u.mV,

    # Passive leakage
    "g_leak": 0.1 * (u.mS / u.cm**2),
    "E_leak": -70.0 * u.mV,
}


class ConfigurableHybridCell(braincell.SingleCompartment):
    """Hybrid single-compartment benchmark cell.

    This class intentionally combines channel implementations originating
    from several published model families. It is an API-composition
    benchmark, not a calibrated biological cell model.
    """

    def __init__(
        self,
        size,
        config: Mapping[str, Any] | None = None,
    ):
        # Allow a caller to override only selected parameters.
        cfg = {**CELL_CONFIG, **({} if config is None else dict(config))}

        super().__init__(
            size,
            length=cfg["length"],
            radius=cfg["radius"],
            C=cfg["C"],
            V_th=cfg["V_th"],
            V_initializer=braintools.init.Constant(cfg["V_init"]),
            solver=cfg["solver"],
        )

        # Sodium ion and sodium current.
        # SodiumFixed alone would not produce a sodium current.
        self.na = braincell.ion.SodiumFixed(
            size,
            E=cfg["E_Na"],
        )
        self.na.add(
            INa=braincell.channel.Na_HH1952(
                size,
                g_max=cfg["g_Na"],
            )
        )

        # Potassium ion and voltage-gated potassium current.
        self.k = braincell.ion.PotassiumFixed(
            size,
            E=cfg["E_K"],
        )
        self.k.add(
            IKv1p1=braincell.channel.Kv1p1_MA2025_BC(
                size,
                g_max=cfg["g_Kv1p1"],
            )
        )

        # Dynamic calcium-ion model and calcium channel.
        self.ca = braincell.ion.CdpCAM_MA2024_PC(
            size,
            solver=cfg["ca_solver"],
            substeps=cfg["ca_substeps"],
        )
        self.ca.add(
            ICav1p2=braincell.channel.Cav1p2_MA2025_BC(
                size,
                g_max=cfg["g_Cav1p2"],
            )
        )

        # These channels operate directly at the neuron level.
        self.IHCN1 = braincell.channel.HCN1_RI2021_SC(
            size,
            g_max=cfg["g_HCN1"],
            E=cfg["E_HCN1"],
        )
        self.IL = braincell.channel.IL(
            size,
            g_max=cfg["g_leak"],
            E=cfg["E_leak"],
        )


# API stress-test variant only.
# Do not use this as the baseline network configuration because a
# +100 mV potassium reversal potential changes potassium into a
# depolarizing current over the usual membrane-voltage range.
BOTH_POSITIVE_100_CONFIG = {
    **CELL_CONFIG,
    "E_Na": 100.0 * u.mV,
    "E_K": 100.0 * u.mV,
}
```

### `test_01_single_cell.py`

```python
import matplotlib.pyplot as plt

import brainstate
import braintools
import brainunit as u

from benchmark_cell import CELL_CONFIG, ConfigurableHybridCell


DT = 0.01 * u.ms
DURATION = 100.0 * u.ms


def run_single_cell():
    with brainstate.environ.context(dt=DT):
        neuron = ConfigurableHybridCell(
            size=1,
            config=CELL_CONFIG,
        )
        brainstate.nn.init_all_states(neuron)

        current = braintools.input.Step(
            amplitudes=[0.0, 10.0, 0.0] * (u.uA / u.cm**2),
            step_times=[0.0, 20.0, 80.0] * u.ms,
            duration=DURATION,
        )()

        times = u.math.arange(
            0.0 * u.ms,
            DURATION,
            brainstate.environ.get_dt(),
        )

        def step(t, I_ext):
            with brainstate.environ.context(t=t):
                neuron.update(I_ext)
            return neuron.V.value, neuron.spike.value

        voltages, spikes = brainstate.transform.for_loop(
            step,
            times,
            current,
        )

    expected_shape = (times.shape[0], 1)
    assert voltages.shape == expected_shape
    assert spikes.shape == expected_shape

    print("voltage shape:", voltages.shape)
    print("spike shape:", spikes.shape)
    print("number of spikes:", int(u.math.sum(spikes)))

    plt.figure(figsize=(8, 3))
    plt.plot(
        times / u.ms,
        u.math.squeeze(voltages) / u.mV,
        linewidth=1.2,
    )
    plt.xlabel("Time (ms)")
    plt.ylabel("Membrane potential (mV)")
    plt.title("Configurable hybrid conductance-based cell")
    plt.tight_layout()
    plt.show()

    return {
        "times": times,
        "voltages": voltages,
        "spikes": spikes,
    }


if __name__ == "__main__":
    run_single_cell()
```

## BrainX documentation basis

* `SingleCompartment` accepts explicit length, radius, capacitance, spike threshold, voltage initializer, and solver. Its geometry determines membrane area.
* The canonical HH example groups sodium and potassium channels inside their corresponding ions and attaches leak directly to the neuron.
* `Na_HH1952` has a default conductance density of `120 mS/cm²` and requires a sodium parent.
* `Kv1p1_MA2025_BC` has a default conductance density of `4 mS/cm²` and requires a potassium parent.
* `HCN1_RI2021_SC` and `IL` have neuron-level root types.
* BrainTools `Step` produces a piecewise-constant unit-aware input, and `for_loop` can iterate simultaneously over time and current arrays.
