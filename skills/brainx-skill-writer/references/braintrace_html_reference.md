# BrainTrace Reference Links

## Table of Contents

- [API Reference Pages](#api-reference-pages)
- [Quickstart Pages](#quickstart-pages)
- [Tutorial Pages](#tutorial-pages)
- [Advanced Pages](#advanced-pages)
- [Examples Documentation Page](#examples-documentation-page)
- [GitHub Examples Root](#github-examples-root)
- [Top-Level Spiking Neural Network Examples](#top-level-spiking-neural-network-examples)
- [Top-Level Rate-Based RNN Examples](#top-level-rate-based-rnn-examples)
- [Top-Level Supporting Files](#top-level-supporting-files)
- [pp-prop / ES-D-RTRL Example Series](#pp-prop--es-d-rtrl-example-series)
- [D-RTRL Example Series](#d-rtrl-example-series)

## API Reference Pages

- https://brainx.chaobrain.com/braintrace/apis/concepts.html — ETP Operators & Core Types. Reference page for the user-facing ETP operators that make weights participate in eligibility-trace online learning, plus the core input wrappers, eligibility-trace state, gradient utilities, and BrainTrace error types.

- https://brainx.chaobrain.com/braintrace/apis/primitives.html — Custom ETP Primitives. Reference page for registering custom ETP primitives: how to create an `ETPPrimitive`, attach the four ETP-specific trace rules, rely on auto-derived JAX rules, and control compiler behavior with `gradient_enabled`.

- https://brainx.chaobrain.com/braintrace/apis/compiler.html — Compiler, Executor & Diagnostics. Reference page for the lower-level compiler pipeline: graph compilation, module-info extraction, hidden-group discovery, hidden-parameter-operation relations, hidden perturbation construction, graph executors, diagnostics, and compilation reports.

- https://brainx.chaobrain.com/braintrace/apis/algorithms.html — Online-Learning Algorithms. Reference page for the one-call `braintrace.compile()` entry point and the online-learning algorithm hierarchy, including D-RTRL, pp-prop / ES-D-RTRL, and SNN-oriented algorithms such as EProp, OSTL, OTPE, OTTT, and OSTTP.

- https://brainx.chaobrain.com/braintrace/apis/nn.html — Neural-Network Layers. Reference page for `braintrace.nn` layers that mirror `brainstate.nn`-style modules while routing trainable operations through ETP primitives, including linear, embedding, convolutional, recurrent, LoRA, sparse, grouped, and readout layers.

## Quickstart Pages

- https://brainx.chaobrain.com/braintrace/quickstart/concepts.html — Core Concepts of BrainTrace. First conceptual entry point: explains online learning versus BPTT, eligibility traces, the four-layer BrainTrace architecture, primitive-based parameter selection, `braintrace.nn` modules, the 3-step online-learning workflow, and when to choose available algorithms.

- https://brainx.chaobrain.com/braintrace/quickstart/rnn_online_learning.html — RNN Online Learning with BrainTrace. Minimal RNN quickstart: defines the copying-memory task, builds a GRU with `braintrace.nn`, trains it using D-RTRL, compares against a BPTT baseline, and visualizes the loss.

- https://brainx.chaobrain.com/braintrace/quickstart/snn_online_learning.html — SNN Online Learning with BrainTrace. Minimal SNN quickstart: builds an SNN with `brainstate` neurons and `braintrace.nn` layers, trains it with ES-D-RTRL / pp-prop, and explains the practical difference between D-RTRL and ES-D-RTRL.

## Tutorial Pages

- https://brainx.chaobrain.com/braintrace/tutorials/etp_primitives.html — ETP Primitives Deep Dive. Explains how ETP primitives work as JAX custom primitives, why primitive identity replaces string matching, how built-in primitives such as matmul/conv/sparse/LoRA participate in online learning, how unit support and JAX rules work, and how to integrate a custom primitive.

- https://brainx.chaobrain.com/braintrace/tutorials/customizing_primitive_transforms.html — Customizing Primitive Transforms. Explains how to apply parameter transforms such as masks, sign constraints, standardization, or squashing inside ETP primitives while keeping gradients and eligibility traces attached to the raw trainable weight.

- https://brainx.chaobrain.com/braintrace/tutorials/hidden_states.html — Hidden State Management. Explains the hidden-state types used by BrainTrace models, how the compiler discovers read/write recurrent states, how related states are grouped, how to initialize/reset/batch states, and how hidden states interact with online-learning algorithms.

- https://brainx.chaobrain.com/braintrace/tutorials/graph_visualization.html — Graph Compilation & Visualization. Explains the compiled `ETraceGraph`, `learner.graph`, `learner.report`, and `show_graph()`: how to inspect hidden groups, weight-primitive-hidden relations, ETP-included parameters, and parameters excluded from online learning.

- https://brainx.chaobrain.com/braintrace/tutorials/batching.html — Batching Strategies. Explains the recommended `vmap` batching path, single-sample mode, multi-step data handling, state initialization under batching, and the structure of a full vmapped online-training loop.

## Advanced Pages

- https://brainx.chaobrain.com/braintrace/advanced/compiler_internals.html — Compiler Internals. Advanced compiler walkthrough: extracts `ModuleInfo`, discovers hidden groups, finds ETP relations, adds hidden perturbations, explains primitive identification by type identity, and shows how to debug compilation problems.

- https://brainx.chaobrain.com/braintrace/advanced/custom_algorithms.html — Custom Algorithm Development. Advanced algorithm-extension guide: explains the algorithm class hierarchy, D-RTRL and ES-D-RTRL math foundations, which methods to override, how to implement a clipped D-RTRL variant, how update flow works, and how to access/reset eligibility traces.

- https://brainx.chaobrain.com/braintrace/advanced/limitations.html — Limitations & Workarounds. Compatibility guide for BrainTrace model design: explains unsupported nested JAX subgraphs such as `cond`, `scan`, `while_loop`, and nested `vmap`, gives conditional-logic workarounds, documents shape constraints, the weight-to-weight-to-hidden invariant, and performance tradeoffs.

## Examples Documentation Page

- https://brainx.chaobrain.com/braintrace/examples/core_examples.html — Official Examples page. Summarizes the runnable example directory, explains the `0xx-` SNN and `1xx-` RNN filename conventions, points to the `pp_prop/` and `drtrl/` tutorial series, and describes the top-level scripts and support files.

## GitHub Examples Root

- https://github.com/chaobrain/braintrace/tree/main/examples — Root examples directory. Contains runnable top-level BrainTrace examples, the `pp_prop/` tutorial series, the `drtrl/` tutorial series, shared SNN model definitions, shared utilities, and tests.

## Top-Level Spiking Neural Network Examples

- https://github.com/chaobrain/braintrace/blob/main/examples/000-lif-snn-for-nmnist.py — Trains a recurrent LIF-delta SNN on N-MNIST using framed event inputs from `tonic` and the shared `OnlineTrainer` implementation based on `pp_prop`.

- https://github.com/chaobrain/braintrace/blob/main/examples/001-gif-snn-for-dms.py — Trains a GIF recurrent SNN on a delayed matching-to-sample task generated by `DMSDataset`, using the shared `OnlineTrainer` based on `pp_prop`.

- https://github.com/chaobrain/braintrace/blob/main/examples/002-coba-ei-rsnn.py — Trains an excitatory/inhibitory recurrent SNN on an evidence-accumulation task, with configurable CUBA or COBA synapses and online learning through ES-D-RTRL or D-RTRL.

- https://github.com/chaobrain/braintrace/blob/main/examples/003-snn-memory-and-speed-evaluation-all.py — Runs a synthetic benchmark over multiple sequence lengths and compares BPTT, ES-D-RTRL, and D-RTRL on training time and memory usage.

- https://github.com/chaobrain/braintrace/blob/main/examples/003-snn-memory-and-speed-evaluation-batched.py — Trains and benchmarks an SNN on DVSGesture with online algorithms running in `brainstate.mixin.Batching()` mode, reporting accuracy, speed, and memory.

- https://github.com/chaobrain/braintrace/blob/main/examples/003-snn-memory-and-speed-evaluation-vmap.py — Trains and benchmarks the same DVSGesture setup while keeping per-sample states under `vmap`, reporting accuracy, speed, and memory.

- https://github.com/chaobrain/braintrace/blob/main/examples/004-feedforward-conv-snn.py — Builds a feedforward convolutional SNN with `braintrace.nn.Conv2d`, LayerNorm, IF neurons, and `LeakyRateReadout`; defaults to N-MNIST training and also includes SHD loading plus online and BPTT trainers.

## Top-Level Rate-Based RNN Examples

- https://github.com/chaobrain/braintrace/blob/main/examples/100-gru-on-copying-task.py — Trains a GRU on the copying-memory task and compares online learning with `ParamDimVjpAlgorithm` against BPTT.

- https://github.com/chaobrain/braintrace/blob/main/examples/101-integrator-rnn.py — Trains a MiniGRU-based RNN to integrate white-noise input and compares online D-RTRL with BPTT while plotting predictions and training losses.

## Top-Level Supporting Files

- https://github.com/chaobrain/braintrace/blob/main/examples/README.md — Brief overview of the examples directory, filename conventions, direct-run command pattern, and extra Python dependencies.

- https://github.com/chaobrain/braintrace/blob/main/examples/snn_models.py — Shared SNN model and trainer definitions used by the top-level SNN examples, especially GIF-based models and online-training utilities.

- https://github.com/chaobrain/braintrace/blob/main/examples/utils.py — Shared command-line parser utilities used by the `003-*` benchmark scripts.

- https://github.com/chaobrain/braintrace/tree/main/examples/tests — Test directory for examples-level behavior; not a main tutorial workflow, but useful for checking example compatibility.

## pp-prop / ES-D-RTRL Example Series

- https://github.com/chaobrain/braintrace/tree/main/examples/pp_prop — Tutorial-linear example suite for `braintrace.pp_prop`, also referred to as ES-D-RTRL / `IODimVjpAlgorithm`; focuses on input-output-dimensional eligibility traces for SNN online learning.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/01-basics-lif-integrator.py — Basic pp-prop example: LIF recurrent SNN on Poisson-to-cumulative-rate regression.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/02-neurons-alif-dms.py — ALIF example: adaptive-threshold recurrent SNN trained on delayed match-to-sample.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/03-neurons-gif-working-memory.py — GIF example: generalized integrate-and-fire SNN with heterogeneous `tau_I2` on a working-memory recall task.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/04-neurons-coba-ei-rsnn.py — COBA/EI example: Dale-law excitatory/inhibitory recurrent SNN trained on small Poisson-MNIST.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/05-batching-vmap.py — Batching example using `brainstate.nn.Vmap(vmap_states='new')` so each sample has its own state and eligibility trace.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/06-batching-batched.py — Batching example using the batched ETP primitive path instead of external `vmap`.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/07-vjp-single-step.py — VJP-method example using `vjp_method='single-step'`, the default cheaper per-step mode.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/08-vjp-multi-step.py — VJP-method example using `vjp_method='multi-step'` for stronger temporal credit assignment.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/09-operator-sparse.py — Operator example for sparse recurrent connectivity, with a masked-dense fallback path.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/10-operator-lora.py — Operator example for low-rank recurrence using `braintrace.lora_matmul`.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/11-operator-conv.py — Operator example for a convolutional SNN using `braintrace.nn.Conv2d`.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/12-classification-neuromorphic.py — Flagship pp-prop classification example comparing pp-prop with BPTT on 10-class Poisson-MNIST.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/13-knob-decay-vs-rank.py — Algorithm-knob example sweeping `decay_or_rank` across float and integer settings.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/14-knob-vjp-method-contrast.py — VJP comparison example: single-step versus multi-step versus BPTT on delayed match-to-sample.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/README.md — README for the pp-prop example series; gives the run command, axis map, file-by-file summary, and tutorial cross-reference.

- https://github.com/chaobrain/braintrace/blob/main/examples/pp_prop/_shared.py — Shared SNN data generators, SNN cell wrappers, readout helper, online-training helpers, BPTT baseline helper, and plotting utility for the pp-prop examples.

## D-RTRL Example Series

- https://github.com/chaobrain/braintrace/tree/main/examples/drtrl — Tutorial-linear example suite for `braintrace.D_RTRL`; focuses on parameter-dimensional eligibility traces, batching modes, VJP methods, operator variants, classification, language modeling, and the `fast_solve` knob.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/01-basics-integrator.py — Minimal D-RTRL example: trains a vanilla RNN on noisy cumulative-sum regression and compares D-RTRL with BPTT from the same initialization.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/02-batching-vmap.py — D-RTRL batching example using the explicit per-sample state pattern: initialize states under a vmapped new-states scope, compile the graph per sample, then wrap the online model with `brainstate.nn.Vmap`.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/03-batching-batched.py — D-RTRL batching example using `brainstate.mixin.Batching`, where the algorithm sees the batch axis directly and compiles on a batched sample.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/04-vjp-single-step.py — D-RTRL VJP example using `vjp_method='single-step'` on the copying-memory task; cheapest mode and a good default, but biased over long dependencies.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/05-vjp-multi-step.py — D-RTRL VJP example using `vjp_method='multi-step'` on the copying-memory task; reduces single-step bias by computing VJP over a window at higher compute/memory cost.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/07-operator-lora.py — D-RTRL LoRA operator example: freezes a base recurrent weight through plain JAX matmul and trains only LoRA factors routed through `braintrace.lora_matmul` on sine-wave one-step prediction.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/08-operator-conv.py — D-RTRL convolution operator example: each timestep provides a row-like Poisson input, `braintrace.nn.Conv1d` extracts local features, MiniGRU integrates over time, and a linear head classifies.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/09-classification-mnist.py — Flagship D-RTRL classification example: treats MNIST images as 28 timesteps of 28 features, trains an LSTM classifier, and compares D-RTRL against BPTT with matched hyperparameters.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/10-char-lm-generation.py — Toy character-level language-modeling example: trains a MiniGRU on a short embedded corpus with D-RTRL and BPTT, then samples short continuations from each trained model.

- https://github.com/chaobrain/braintrace/blob/main/examples/drtrl/11-knob-fast-solve.py — D-RTRL performance-knob example comparing `fast_solve=True` and `fast_solve=False`, checking numerical equivalence of summed gradients and measuring the wall-clock speedup from the einsum fast path.
