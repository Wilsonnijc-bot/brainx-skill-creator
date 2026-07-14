# QuaIF E–I Network Benchmark

## Purpose

Test whether an agent can turn a high-level E–I network specification into a
minimal, valid BrainPy-state simulation with correct recurrent wiring, delays,
units, transforms, and biologically interpretable activity.

## Prompt to the Agent

Build a small, configurable BrainPy-state E–I network with one mixed `QuaIF`
population: the first `n_exc` neurons are excitatory and the rest are
inhibitory. Add two recurrent paths, E → all and I → all, using
`align_pre_projection`, `Expon`, `EventLinear`, and current-based `CUBA`.

Delay `population.prefetch("V")` with `.delay.at(delay)`, recover spikes with
`population.get_spike(delayed_v)`, then slice the E and I presynaptic groups.
Build explicit weights as Bernoulli masks times synaptic weights. Their shapes
must be `(n_exc, n_exc + n_inh)` and `(n_inh, n_exc + n_inh)`; keep E weights
positive and I weights negative.

Make population sizes, connection probability, E/I weights, E/I delays,
synaptic time constant, external input, time step, duration, and seed
configurable. Use this baseline: `n_exc=800`, `n_inh=200`, `prob=0.1`,
`delay=1.5 * u.ms`, `tau_syn=2.0 * u.ms`, `dt=0.1 * u.ms`,
`duration=1000.0 * u.ms`, and `external_input=5.0 * u.mA`. Set
`JE = 1 / u.math.sqrt(prob * n_exc) * u.mS` and `JI = -4 * JE`.

In each update call `self.E()`, `self.I()`, then `self.N(external_input)`, and
return the spikes. Simulate with `brainstate.transform.for_loop`. Report only
runtime and mean E/I firing rates, and draw one raster with the E/I boundary.
Also allow simple sweeps over size, probability, delay, E/I weight, and input.
Keep the solution to one network class and one simulation loop; omit training,
plasticity, optimization, extra mechanisms, and heavy analysis scaffolding.

---

## Configuration

```python
num_exc = 800
num_inh = 200

prob = 0.1

delay = 1.5 * u.ms
tau_syn = 2.0 * u.ms

dt = 0.1 * u.ms
duration = 1000.0 * u.ms

Ib = 5.0 * u.mA
```

Use a 4:1 excitatory-to-inhibitory population ratio.

The excitatory weight follows the original scaling:

```python
JE = 1 / u.math.sqrt(prob * num_exc) * u.mS
```

Because the inhibitory population is four times smaller, use inhibition approximately four times stronger:

```python
JI = -4.0 * JE
```

This keeps expected excitatory and inhibitory recurrent input approximately balanced when their firing rates are similar:

```text
prob × num_exc × JE ≈ prob × num_inh × abs(JI)
```

---

## Exact Architecture

```text
QuaIF population
├── first num_exc neurons: excitatory
├── last num_inh neurons: inhibitory
│
├── delayed E spikes
│   └── Expon(num_exc)
│       └── EventLinear(num_exc → all)
│           └── CUBA
│
└── delayed I spikes
    └── Expon(num_inh)
        └── EventLinear(num_inh → all)
            └── CUBA
```

`align_pre_projection` places the exponential synaptic state in presynaptic space:

```python
syn=brainpy.state.Expon(num_exc, tau=tau_syn)
```

The communication step then maps the filtered presynaptic activity to all neurons:

```python
comm=brainstate.nn.EventLinear(
    num_exc,
    total_num,
    weight=W_e,
)
```

---

## Connectivity

`EventLinear` does not create fixed-probability connectivity itself. Construct its weight matrix using a Bernoulli mask:

```python
W_e = (
    brainstate.random.bernoulli(
        prob,
        size=(num_exc, total_num),
    )
    * JE
)
```

The inhibitory matrix is constructed in the same way:

```python
W_i = (
    brainstate.random.bernoulli(
        prob,
        size=(num_inh, total_num),
    )
    * JI
)
```
---

## Delay

The built-in neuron exposes membrane voltage `V`, so delay `V` and then regenerate spikes from the delayed voltage:

```python
self.N.prefetch("V").delay.at(delay)
```

For excitation:

```python
lambda v: (self.N.get_spike(v) != 0.0)[:self.n_exc]
```

For inhibition:

```python
lambda v: (self.N.get_spike(v) != 0.0)[self.n_exc:]
```

Because the projection fetches its own delayed input, call it without an argument:

```python
self.E()
self.I()
```

---

## Exact Ideal Code

```python
import time

import brainunit as u
import matplotlib.pyplot as plt

import brainpy
import brainstate
import braintools


class EINet(brainstate.nn.Module):
    def __init__(
        self,
        n_exc,
        n_inh,
        prob,
        JE,
        JI,
        delay,
        tau_syn=2.0 * u.ms,
    ):
        super().__init__()

        self.n_exc = n_exc
        self.n_inh = n_inh
        self.num = n_exc + n_inh

        # QuaIF neuron population.
        self.N = brainpy.state.QuaIF(
            self.num,
            R=1.0 * u.ohm,
            tau=10.0 * u.ms,
            V_rest=-65.0 * u.mV,
            V_c=-50.0 * u.mV,
            V_th=-30.0 * u.mV,
            V_reset=-68.0 * u.mV,
            c=0.07 / u.mV,
            V_initializer=braintools.init.Normal(
                -60.0,
                5.0,
                unit=u.mV,
            ),
            spk_reset="soft",
        )

        # Fixed random E→all and I→all weight matrices.
        W_e = (
            brainstate.random.bernoulli(
                prob,
                size=(n_exc, self.num),
            )
            * JE
        )

        W_i = (
            brainstate.random.bernoulli(
                prob,
                size=(n_inh, self.num),
            )
            * JI
        )

        # Excitatory projection:
        # delayed V -> delayed E spikes -> Expon -> EventLinear -> CUBA.
        self.E = brainpy.state.align_pre_projection(
            self.N.prefetch("V").delay.at(delay),
            lambda v: (
                self.N.get_spike(v) != 0.0
            )[:self.n_exc],
            syn=brainpy.state.Expon(
                self.n_exc,
                tau=tau_syn,
            ),
            comm=brainstate.nn.EventLinear(
                self.n_exc,
                self.num,
                weight=W_e,
            ),
            out=brainpy.state.CUBA(),
            post=self.N,
        )

        # Inhibitory projection:
        # delayed V -> delayed I spikes -> Expon -> EventLinear -> CUBA.
        self.I = brainpy.state.align_pre_projection(
            self.N.prefetch("V").delay.at(delay),
            lambda v: (
                self.N.get_spike(v) != 0.0
            )[self.n_exc:],
            syn=brainpy.state.Expon(
                self.n_inh,
                tau=tau_syn,
            ),
            comm=brainstate.nn.EventLinear(
                self.n_inh,
                self.num,
                weight=W_i,
            ),
            out=brainpy.state.CUBA(),
            post=self.N,
        )

    def update(self, t, inp):
        with brainstate.environ.context(t=t):
            # Projections fetch the delayed spikes themselves.
            self.E()
            self.I()

            self.N(inp)

            return self.N.get_spike()


# ---------------------------------------------------------------------
# Configuration
# ---------------------------------------------------------------------

brainstate.environ.set(dt=0.1 * u.ms)
brainstate.random.seed(42)

num_exc = 800
num_inh = 200

prob = 0.1
delay = 1.5 * u.ms
tau_syn = 2.0 * u.ms

duration = 1000.0 * u.ms
Ib = 5.0 * u.mA

# Balanced-current scaling.
JE = 1.0 / u.math.sqrt(prob * num_exc) * u.mS
JI = -4.0 * JE


# ---------------------------------------------------------------------
# Network
# ---------------------------------------------------------------------

net = EINet(
    num_exc,
    num_inh,
    prob=prob,
    JE=JE,
    JI=JI,
    delay=delay,
    tau_syn=tau_syn,
)

brainstate.nn.init_all_states(net)


# ---------------------------------------------------------------------
# Simulation
# ---------------------------------------------------------------------

times = u.math.arange(
    0.0 * u.ms,
    duration,
    brainstate.environ.get_dt(),
)

start = time.perf_counter()

spikes = brainstate.transform.for_loop(
    lambda t: net.update(t, Ib),
    times,
    pbar=brainstate.transform.ProgressBar(10),
)

# Ensure JAX execution has completed before stopping the timer.
spikes.block_until_ready()

elapsed = time.perf_counter() - start


# ---------------------------------------------------------------------
# Minimal benchmark output
# ---------------------------------------------------------------------

duration_seconds = float(duration.to_decimal(u.second))

rate_e = (
    float(u.math.sum(spikes[:, :num_exc]))
    / num_exc
    / duration_seconds
)

rate_i = (
    float(u.math.sum(spikes[:, num_exc:]))
    / num_inh
    / duration_seconds
)

print(f"Runtime: {elapsed:.4f} seconds")
print(f"Excitatory mean rate: {rate_e:.2f} Hz")
print(f"Inhibitory mean rate: {rate_i:.2f} Hz")


# ---------------------------------------------------------------------
# Visualization
# ---------------------------------------------------------------------

t_indices, n_indices = u.math.where(spikes)

plt.scatter(
    times[t_indices].to_decimal(u.ms),
    n_indices,
    s=1,
)

plt.axhline(
    num_exc,
    linestyle="--",
    linewidth=1,
    label="E/I boundary",
)

plt.xlabel("Time (ms)")
plt.ylabel("Neuron index")
plt.legend()
plt.show()
```

---

## Minimal Benchmark Variants

Run the same code with only one parameter changed at a time.

### Network size

```python
(200 E, 50 I)
(400 E, 100 I)
(800 E, 200 I)
(1600 E, 400 I)
```

### Delay

```python
0.0 * u.ms
1.0 * u.ms
2.0 * u.ms
5.0 * u.ms
```

### Connection probability

```python
0.05
0.10
0.20
```

### Required output

For every run, record only:

```text
network size
connection probability
delay
runtime
mean E firing rate
mean I firing rate
```

---

## Biological Sanity Check

The baseline is acceptable when:

* Both E and I populations spike.
* Activity continues throughout the simulation.
* The raster is not dominated by population-wide simultaneous spikes.
* Removing or weakening the inhibitory projection clearly increases network activity.
* Increasing inhibition clearly reduces network activity.

The exact firing rate does not need to match a particular experiment. The benchmark only needs to retain recognizable recurrent excitation, inhibitory stabilization, and delayed spike transmission.
