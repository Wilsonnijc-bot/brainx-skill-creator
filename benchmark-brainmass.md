# BrainMass Three-Region Network Benchmark

## Purpose

Build the smallest complete BrainMass network pipeline that demonstrates:

1. Local excitatory–inhibitory population dynamics.
2. Temporally correlated stochastic input.
3. Structural coupling among several regions.
4. Neural-to-BOLD forward modeling.
5. Basic functional-connectivity analysis.

This is an **API-composition test**, not a parameter-fitting or biologically validated whole-brain study.

## Prompt to the Agent

Create a simple three-region BrainMass simulation using `WilsonCowanStep`.

Use the oscillatory Wilson–Cowan parameters from the official model example, attach dimensionless `OUProcess` input to the excitatory populations, and connect the regions through a small structural-connectivity matrix using built-in diffusive coupling on `rE`.

Run the network with `brainmass.Simulator`, discard an initial transient, and record excitatory activity. Convert the neural activity into a toy BOLD signal using `HRFBold` with `GammaHRFKernel`. Use `brainunit.math` for array construction, finite-value checks, correlation, triangular indexing, and summary statistics.

Plot the excitatory time series and BOLD functional-connectivity matrix. Keep the implementation self-contained and do not add datasets, delays, parameter fitting, custom coupling, batching, or parameter sweeps.

The requested APIs match their intended roles: `Network` supplies built-in regional coupling, `Simulator` manages initialization and the compiled loop, and `HRFBold` performs the simplified convolution-based observation step.

## Configuration

### Global simulation

| Parameter              |                   Value | Reason                                                     |
| ---------------------- | ----------------------: | ---------------------------------------------------------- |
| Regions, `N`           |                     `3` | Smallest nontrivial network                                |
| Random seed            |                     `0` | Reproducible stochastic run                                |
| Integration step, `dt` |            `0.1 * u.ms` | Same step used in the official Wilson–Cowan example        |
| Duration               |         `12_000 * u.ms` | Long enough to see sustained noisy dynamics                |
| Transient              |          `2_000 * u.ms` | Removes initialization effects                             |
| Monitor                | `network.node.rE.value` | Excitatory population is the coupled and observed variable |

`Simulator` calculates the number of integration steps from `duration / dt`, initializes model states, runs the model through `brainstate.transform.for_loop`, and returns the monitored trajectories with a time axis.

### Wilson–Cowan node

```python
tau_E = 2.5 * u.ms
tau_I = 3.75 * u.ms

wEE = 16.0
wEI = 12.0
wIE = 15.0
wII = 3.0

method = "exp_euler"
```

These values come directly from the gallery configuration selected to place the E–I loop in an oscillatory regime. `rE` and `rI` are dimensionless population activities, while the time constants carry time units.

Do not expose every default model parameter in the test configuration. Leave the gains, thresholds, refractory parameter, and initializers at their documented defaults.

### Stochastic drive

```python
noise_E = brainmass.OUProcess(
    in_size=N,
    mean=1.0,
    sigma=0.15,
    tau=20.0 * u.ms,
)
```

| Parameter    | Meaning                                   |
| ------------ | ----------------------------------------- |
| `mean=1.0`   | Baseline excitatory drive                 |
| `sigma=0.15` | Moderate fluctuations around the baseline |
| `tau=20 ms`  | Temporal correlation of the fluctuations  |

Using a nonzero OU mean is a compact design choice: the gallery uses an excitatory drive near `1.0`, while `WilsonCowanStep` adds `noise_E` directly to `rE_inp`. The noise remains dimensionless because the Wilson–Cowan input is dimensionless. `OUProcess` may explicitly produce unitless output when `sigma` is unitless.

### Structural network

```python
conn = u.math.array([
    [0.0, 1.0, 0.3],
    [1.0, 0.0, 1.0],
    [0.3, 1.0, 0.0],
])
```

This represents a chain-like network:

```text
Region 1 ───── Region 2 ───── Region 3
    └──────── weak link ────────┘
```

Use:

```python
coupling = "diffusive"
coupled_var = "rE"
k = 0.1
```

Diffusive coupling computes weighted differences between source and target activity. `Network` handles this internally and feeds the resulting coupling current into the node’s first input. With no distance or speed parameters, coupling is instantaneous.

### BOLD observation

```python
hrf = brainmass.HRFBold(
    period=200.0 * u.ms,
    downsample_period=10.0 * u.ms,
    kernel=brainmass.GammaHRFKernel(
        duration=5.0 * u.second,
    ),
)
```

These deliberately shortened periods keep the example small and provide enough output points for a toy FC matrix. They should not be presented as a realistic clinical fMRI acquisition protocol.

`HRFBold` first averages the neural signal onto the intermediate downsampling grid, convolves it with the HRF kernel, and then decimates it to the requested final period. `GammaHRFKernel` supplies the closed-form gamma-shaped response.

## Architecture

```text
Configuration
    │
    ├── brainunit quantities
    │     ├── dt
    │     ├── duration
    │     ├── transient
    │     └── HRF periods
    │
    ├── u.math.array
    │     └── 3 × 3 structural connectivity
    │
    └── random seed
          │
          ▼
OUProcess
temporally correlated excitatory input
          │
          ▼
WilsonCowanStep
3 parallel E–I regional populations
          │
          ▼
brainmass.Network
diffusive coupling of regional rE states
          │
          ▼
brainmass.Simulator
state initialization + compiled time loop
          │
          ▼
neural activity: (time, 3)
          │
          ▼
HRFBold + GammaHRFKernel
          │
          ▼
BOLD activity: (slower time, 3)
          │
          ├── u.math.corrcoef(..., rowvar=False)
          ├── u.math.triu_indices(...)
          └── u.math.mean(...)
          │
          ▼
time-series plot + FC heatmap
```

For a `(time, region)` signal, `u.math.corrcoef(..., rowvar=False)` treats each column as one regional variable. `brainmass.viz.plot_functional_connectivity` can alternatively receive the raw time series and compute FC through `braintools.metric.functional_connectivity`.

## Acceptance Criteria

| Check              | Expected result                                           |
| ------------------ | --------------------------------------------------------- |
| Neural shape       | Second dimension equals `3`                               |
| BOLD shape         | Second dimension equals `3`                               |
| Temporal reduction | BOLD has substantially fewer samples than neural activity |
| Numerical validity | Neural activity, BOLD, and FC contain only finite values  |
| FC shape           | `(3, 3)`                                                  |
| FC symmetry        | `fc ≈ fc.T`                                               |
| FC diagonal        | Approximately `[1, 1, 1]`                                 |
| Reproducibility    | Same seed and configuration reproduce the same trajectory |

The upper-triangle indices should contain three unique regional pairs:

```text
Region 1–Region 2
Region 1–Region 3
Region 2–Region 3
```

Their mean is the single summary statistic reported by the toy test.

## Exact Ideal Code

```python
import brainmass
import brainstate
import brainunit as u
import matplotlib.pyplot as plt


# ------------------------------------------------------------
# Configuration
# ------------------------------------------------------------

N = 3
dt = 1.0 * u.ms
labels = ["Region 1", "Region 2", "Region 3"]

brainstate.environ.set(dt=dt)
brainstate.random.seed(0)


# ------------------------------------------------------------
# Structural connectivity
# ------------------------------------------------------------

# Dimensionless structural weights.
conn = u.math.array([
    [0.0, 1.0, 0.3],
    [1.0, 0.0, 1.0],
    [0.3, 1.0, 0.0],
])


# ------------------------------------------------------------
# Noisy Wilson-Cowan network
# ------------------------------------------------------------

node = brainmass.WilsonCowanStep(
    in_size=N,
    noise_E=brainmass.OUProcess(
        in_size=N,
        sigma=0.4,
        tau=20.0 * u.ms,
    ),
)

network = brainmass.Network(
    node,
    conn=conn,
    coupling="diffusive",
    coupled_var="rE",
    k=0.1,
)


# ------------------------------------------------------------
# Simulation
# ------------------------------------------------------------

result = brainmass.Simulator(network, dt=dt).run(
    6000.0 * u.ms,
    monitors=lambda model: model.node.rE.value,
    transient=500.0 * u.ms,
)

neural = result["output"]


# ------------------------------------------------------------
# Neural activity -> toy BOLD signal
# ------------------------------------------------------------

hrf = brainmass.HRFBold(
    period=200.0 * u.ms,            # short toy sampling period
    downsample_period=20.0 * u.ms,
    kernel=brainmass.GammaHRFKernel(),
)

bold = hrf(
    u.get_magnitude(neural),
    dt=dt,
)


# ------------------------------------------------------------
# Simple analysis with brainunit.math
# ------------------------------------------------------------

# Rows are observations; columns are regions.
fc = u.math.corrcoef(bold, rowvar=False)

assert neural.shape[1] == N
assert bold.shape[1] == N
assert bool(u.math.all(u.math.isfinite(neural)))
assert bool(u.math.all(u.math.isfinite(bold)))
assert bool(u.math.all(u.math.isfinite(fc)))

upper_triangle = u.math.triu_indices(N, k=1)
mean_fc = u.math.mean(fc[upper_triangle])

print("Neural shape:", neural.shape)
print("BOLD shape:", bold.shape)
print("BOLD functional connectivity:")
print(fc)
print("Mean off-diagonal FC:", float(mean_fc))


# ------------------------------------------------------------
# Visualization
# ------------------------------------------------------------

fig, axes = plt.subplots(1, 2, figsize=(10, 4))

brainmass.viz.plot_timeseries(
    neural,
    ts=result["ts"],
    labels=labels,
    ax=axes[0],
)
axes[0].set_title("Wilson-Cowan excitatory activity")
axes[0].set_ylabel("rE")

brainmass.viz.plot_connectivity(
    fc,
    labels=labels,
    ax=axes[1],
)
axes[1].set_title("BOLD functional connectivity")

plt.tight_layout()
plt.show()
```
