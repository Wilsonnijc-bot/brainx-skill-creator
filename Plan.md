# BrainX Skill 设计与开发计划

## 目录

1. [设计目标与整体原则](#1-设计目标与整体原则)
2. [Current Skill List](#2-current-skill-list)
3. [Skill Layer Design](#3-skill-layer-design)
4. [Reference Layer Illustration](#4-reference-layer-illustration)
5. [Skill 测试与迭代方法](#5-skill-测试与迭代方法)
6. [Test Case 规划](#6-test-case-规划)
7. [版本与发布策略](#7-版本与发布策略)

## 1. 设计目标与整体原则

需求：computational neuroscience 有关的科研人员需要使用 BrainX 的代码框架建模，现在普遍使用 AI agent 做 coding，所以这套 skill 需要教会 AI agent 使用 BrainX 的框架。

根据 AI agent 的上下文性质，整个 skill 设计遵从渐进式披露的思想，以达到高效的 token使用，把信息分成 2 层：

- skill 层：最核心且代表大方向的知识点。
- reference 层：大方向之下发散知识点以及最完整样例代码，让 agent 去点击。

## 2. Current Skill List

```text
braincell-multicompartment/
braincell-singlecell/
brainstate-brain-dynamics/
brainstate-deeplearning-training/
brainstate-module-building/
brainstate-randomness-reproducibility/
brainstate-state-management/
brainstate-transformations-core/
brainunit-quantity-safety/
brainx-accelerate/
brainx-general-guard/
brainx-install/
```

## 3. Skill Layer Design

### 3.1 `brainx-install`

#### Scope

- **Purpose:** Teach BrainX installation, backend selection, and validation.
- **Target size:** Within 100-150 lines.

#### Core Concepts

- BrainX meta-package.
- Python and pip requirements.
- CPU / GPU / TPU backend choice.
- CUDA install path.
- pinned BrainX release for reproducibility.

#### General Scripts

1. ask-before-install environment checklist.
2. quick install: `pip install -U BrainX`.
3. CPU install.
4. CUDA 12/13 install.
5. TPU install.
6. exact release pinning.
7. import validation.
8. JAX device validation.

#### Script References

- `brainx-install-verify.py`

#### Common Failures

- installing before identifying backend.
- unsupported Python version.
- old pip / setuptools / wheel.
- wrong CUDA/JAX wheel.
- installing only raw JAX when BrainCell/BrainUnit is needed.
- assuming GPU works without checking `jax.devices()`.

### 3.2 `brainx-general-guard`

#### Scope

- **Purpose:** Provide the global guardrail layer for damaging cross-framework mistakes.
- **Target size:** Within 300-350 lines.
- **Note:** 这个需要组里大家一起给一个 list, 具体归纳 agent 常犯的错误。

#### Core Concepts

- This is the global guardrail layer. It prevents the most damaging cross-framework mistakes.
- BrainState mental model: State, Module, Transform.
- BrainState transform is state-aware.
- raw JAX transform boundary.

#### General Scripts

1. Before writing unfamiliar BrainX code, open the relevant official BrainX example/API page.
2. Copy import paths from official docs.
3. Prefer existing library classes before custom implementation.
4. Do not infer method names from PyTorch/JAX/BrainPy style.
5. If unsure, write a small import/instantiation smoke test first.
6. stateful JIT guard example.
7. stateful for-loop guard example.
8. unit-required BrainCell example.

#### Reference Routing

- common failure wiki

#### Common Failures

- writing raw NumPy simulation instead of BrainState / BrainCell.
- using raw `jax.jit` on mutable BrainState modules.
- using Python loops around transformed state updates.
- passing bare floats into BrainCell mechanisms.
- treating BrainCell as generic ODE code.
- writing custom ion/channel before checking built-in libraries.
- inventing BrainX APIs instead of opening official examples.

### 3.3 `brainunit-quantity-safety`

#### Scope

- **Purpose:** Teach the fundamental infrastructure unit.
- **Target size:** Within 200 lines.
- **Source mirrored:** https://brainx.chaobrain.com/brainunit/getting_started/quickstart.html

#### Core Concepts

- Quantity = numeric value + physical unit.
- dimensional checking.
- unit conversion.
- `to_decimal()` and `in_unit()`.
- quantity attributes: mantissa, unit, dim, shape, dtype.
- unit-aware math through `brainunit.math`.
- unit-aware JAX transforms.
- function-level unit contracts through `@check_units`.
- BrainCell requires explicit units for physical parameters.

#### General Scripts

1. scalar quantity example.
2. array quantity example.

### 3.4 `brainstate-state-management`

#### Scope

- **Purpose:** Teach BrainState State usage and state update discipline.
- **Target size:** Within 250 lines.
- **Source mirrored:** https://brainx.chaobrain.com/brainstate/tutorials/core/01_state_and_pytrees.html

#### Core Concepts

- mutation happens through State objects.
- state update discipline.
- relationship between Module and State.
- core features of State + important notes.
- LIF neuron example using HiddenState, ShortTermState, ParamState.

#### General Scripts

1. create, read, update a State, support with pytree.
2. subclass state examples.
3. collect ParamState.
4. track states inside a module.
5. LIF neuron example.

#### Reference Routing

- skill-transformation core.
- randomness and reproducibility skill.

#### Common Failures

- mutating ordinary Python attributes instead of State.
- hiding model variables outside State.
- confusing ParamState with runtime hidden state.
- forgetting that transformed BrainState code tracks State reads/writes.

### 3.5 `brainstate-module-building`

#### Scope

- **Purpose:** Teach reusable BrainState Module construction and module composition.
- **Target size:** Within 300 lines.

#### Core Concepts

- Module-tree of state.
- `brainstate.nn.Module` is the base class.
- reusability & composability.
- build one module, then combine and nest into module graph.
- parameter registration.
- `model.states(brainstate.ParamState)`.

#### General Scripts

1. Layers in state.
2. minimal Linear layer + forward pass with random input.
3. prebuilt activation example, e.g. Sigmoid.
4. nesting and nested module example.
5. ResidualBlock example.
6. HH model script HH Neuron model under Brain Dynamics.
7. Sequential,.desc() layer construction,size inference.
8. `->ComplexNet` example with convolution, pooling, flatten, linear.
9. full script example ModernCNN from tutorial.

#### Reference Routing

- Pre-built Activation Functions.
- Pre-built Basic Layers.
- parameters and regularization.
- Size Inference with Convolution.
- Size Inference with Pooling & Flatten.
- Skill for Deeplearning Training.
- Skill for randomness and reproducibility.
- Modern CNN script from “activation function and normalization”: https://brainx.chaobrain.com/brainstate/tutorials/core/04_activations_and_normalization.html

#### Script References

- “Training Spiking Neural Network script” → https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/05_training_an_snn.html

#### Common Failures

- writing loose functions instead of reusable Module classes.
- storing trainable parameters outside ParamState / registered parameters.
- building custom layers before checking prebuilt BrainState layers.
- missing shape inference rules.
- nesting modules without checking state collection.

### 3.6 `brainstate-transformations-core`

#### Scope

- **Purpose:** Teach basic BrainState-aware transformations.
- **Target size:** Within 150-200 lines.
- **Source mirrored:** https://brainx.chaobrain.com/brainstate/tutorials/core/06_transformations_essentials.html

#### Core Concepts

- BrainState-aware transformations.
- difference from raw JAX.
- state read/write tracking.
- `brainstate.transform.jit`, `grad`, `vmap`.
- composed transforms.
- raw JAX boundary.
- transform over modules and states.

#### General Scripts

1. minimal state-aware JIT.
2. forward pass wrapped by `brainstate.transform.jit`.
3. collect ParamState.
4. gradient over state collection.
5. parameter update using gradient keys.
6. minimal vmap over batch.
7. composed train-step transform.

#### Reference Routing

- brainstate-control-flow patterns.
- transformation-grad expansion.
- transformation-jit expansion.
- transformation-vmap expansion.
- brainstate-transformed diagnostics.

#### Common Failures

- using raw `jax.jit` on mutable BrainState modules.
- differentiating positional arguments like plain JAX when the target is ParamState.
- compiling tiny operations instead of full steps.
- Python-looping over batches instead of using vmap.
- updating parameters outside the gradient key structure.
- using control-flow transforms without checking the control-flow reference.

### 3.7 `brainstate-randomness-reproducibility`

#### Scope

- **Purpose:** Teach basic BrainState randomness for random trials and reproducible experiments.
- **Target size:** Within 150-200 lines.
- **Source mirrored:** https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html

#### Core Concepts

- Key Features.
- Automatic key splitting.
- NumPy compatibility.
- Stateful interface.
- JIT compatible.
- `brainstate.random` as the default RNG interface + script.
- reproducibility through `brainstate.random.seed(seed)` + script.
- global DEFAULT RandomState.
- common random functions: random, rand, randn.
- stochastic module behavior.
- train-only randomness.
- `brainstate.environ.get('fit', False)`.
- Choice and Permutation & shuffle array.

#### General Scripts

1. `brainstate.random` default RNG interface script.
2. `brainstate.random.seed(seed)` reproducibility script.

#### Reference Routing

- advanced randomness.
- manual key control.
- custom RandomState.
- independent random streams.
- checkpoint RNG save/restore.
- temporary seed context.
- default_rng.
- clone_rng.
- probability distribution catalog.
- transformed randomness.
- parallel randomness.
- stochastic brain-dynamics workflows.

#### Common Failures

- manually creating/splitting raw JAX keys by default.
- forgetting to seed reproducible examples.
- using np.random in transformed BrainState code.
- reusing the same randomness across mapped trials.
- applying dropout/noise during evaluation.

### 3.8 `brainstate-deeplearning-training`

#### Scope

- **Purpose:** Route supervised BrainState training tasks here.
- **Target size:** Within 300 lines.
- **Source mirrored:** https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html

#### Core Concepts

- what this skill is for.
- training loop structure.
- Assemble Module + ParamState + grad + jit into one train/eval loop.
- model / loss / optimizer / metrics.
- Use the canonical four-part training stack: model, scalar loss, registered optimizer, accumulated metrics.
- parameter collection.
- Train exactly ParamState; leave other states untouched.
- gradient update.
- Differentiate loss, optionally clip gradients, then apply `optimizer.update(grads)`.
- compiled training step.
- JIT the whole train step and let BrainState track state reads/writes.
- metrics lifecycle.
- Evaluate through reset → update per batch → compute.
- fit/eval environment note.
- Use `fit=True` for stochastic training behavior and `fit=False` for evaluation.

#### General Scripts

1. Mirror the scripts in https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html.

#### Reference Routing

- brainstate-control-flow patterns.
- parameter-constraints regularization.
- skill transformation-core.
- skill randomness-reproduction.
- prebuilt-activation library.
- prebuilt-layer library.
- Optimizer library.

#### Script References

- Image Classification with CNNs: Best normal supervised-training illustration: train/eval split, compiled train step, optimizer registration, loss, accuracy, validation loop. Source: https://brainx.chaobrain.com/brainstate/examples/deep_learning/image_classification.html
- Training Recurrent Neural Networks: Integrator Task: Best for temporal training: init_all_states, for_loop, sequence loss, L2 regularization, scheduler, recurrent state. Source: https://brainx.chaobrain.com/brainstate/examples/deep_learning/integrator_rnn.html
- Training a Spiking Neural Network: Best for SNN-specific training: backpropagation through time, surrogate gradient, spk_fun=braintools.surrogate.ReluGrad(), for_loop, grad, optimizer update. Source: https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_training.html

### 3.9 `brainstate-brain-dynamics`

#### Scope

- **Purpose:** Teach BrainState brain dynamics workflows for dynamical systems and SNN routes.
- **Target size:** Within 300 lines.

#### Core Concepts

- brain models as dynamical systems.
- state variables evolving over time, `update()`.
- element-wise update principle.
- architecture flow: input, connection modules, dynamics modules, output.
- input / output / states / parameters.
- before-update hooks.
- after-update hooks.
- in_size.
- out_size.
- delay support.
- BrainPy-state SNN workflow boundary.
- BrainCell boundary.

#### General Scripts

1. simulation time-step setup.
2. minimal exponential decay Dynamics module.
3. minimal LIF Dynamics module.
4. simulation loop with `brainstate.transform.for_loop`.
5. before-update hook.
6. after-update hook.
7. delay support pattern: output_delay, prefetch_delay.
8. BrainPy-state LIF population example.
9. E/I SNN workflow route.
10. SNN training route.

#### Reference Routing

- brain-dynamics-delay protocol.
- brain-dynamics-event-driven operators.
- brainstate-control-flow patterns.
- transformation-grad expansion.
- solver library with effects.

#### Script References

- Example of simulating HH-cell. Source: https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/hodgkin_huxley_neuron.html
- Training a Spiking Neural Network: Best for SNN-specific training: backpropagation through time, surrogate gradient, spk_fun=braintools.surrogate.ReluGrad(), for_loop, grad, optimizer update. Source: https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_training.html

#### Common Failures

- putting synaptic connectivity inside `Dynamics.update()`.
- forgetting to implement `update()`.
- using unitless time/current/voltage.
- using long Python loops instead of for_loop.
- forgetting time context inside step functions.
- manually managing delay buffers.
- treating BrainState brain dynamics as BrainCell morphology modeling.

### 3.10 `braincell-singlecell`

#### Scope

- **Purpose:** Teach BrainCell single-compartment point-neuron modeling.
- **Target size:** within 300 lines.

#### Core Concepts

- SingleCompartment.
- point-neuron modeling.
- HH framework.
- membrane potential & capacitance.
- reversal potentials.
- channel conductances.
- external injected current.
- Ion.
- Channel.
- channel gating state.
- MixIons.
- BrainCell synapse classes: AMPA, GABA, aNMDA.
- solver selection.

#### General Scripts

1. minimal HH single cell.
2. current-clamp simulation.
3. direct cell.update(input_current).
4. use existing sodium ion & channel.
5. add leak channel.
6. Traub-Miles style HH cells with area-scaled conductance.
7. MixIons for calcium-activated potassium current.
8. vectorized FI curve.
9. E/I network route.
10. BrainCell synapse class route.
11. solver guidance.

#### Reference Routing

- channel library.
- ion library.
- solver library with effects.
- skill randomness and reproduction.
- parameter and regularization.
- braincell-custom-ion-channel authoring.
- braincell-manual-morphology construction.

#### Common Failures

- using this skill when geometry matters.
- treating ion and channel as the same object.
- adding channel to wrong ion container.
- passing bare floats for biological quantities.
- using old add_elem style when current examples use add.
- putting leak current inside an ion container.
- forgetting init_state().
- using raw Python loops instead of BrainState loop transforms.
- interpreting V_th as a conductance parameter.
- custom authoring before checking built-in ion/channel libraries.

### 3.11 `braincell-multicompartment`

#### Scope

- **Purpose:** Teach BrainCell multicompartment modeling with morphology, mechanisms, probes, and clamps.
- **Target size:** within 350 lines.

#### Core Concepts

- morphology.
- branch.
- soma / dendrite / axon.
- region.
- locset.
- paint.
- place.
- CV.
- CV policy.
- discretization.
- cable property.
- density mechanism.
- point mechanism.
- ion.
- channel.
- mechanism system.
- probe.
- clamp.
- solver.
- runtime topology.
- morphology loading.

#### General Scripts

1. build Cell from existing morphology.
2. load morphology from SWC.
3. inspect topology.
4. choose CV policy.
5. paint cable properties.
6. paint density mechanism.
7. paint existing channel.
8. place point mechanism.
9. place current clamp.
10. place state probe.
11. use existing ion.
12. use existing channel.
13. use MixIons.
14. use CVPerBranch.
15. use DLambda.
16. use MaxCVLen.
17. add CableProperty.
18. add clamp.
19. add probe.
20. sample probes.
21. visualize topology.
22. region and locset filter route.

#### Reference Routing

- braincell-custom-ion-channel authoring.
- braincell-manual-morphology construction.
- ion library.
- channel library.
- morphology-io-loading validation.
- filter function library.
- solver library + effect of different integrations.
- cv-policy reference.
- filter function library.

#### Common Failures

- using single-compartment skill when geometry matters.
- confusing region painting with point placement.
- placing density mechanisms with place.
- painting point mechanisms with paint.
- skipping morphology validation.
- stripping units from cable parameters.
- using custom ion/channel before checking libraries.
- ignoring CV policy effects.
- forgetting cell.init_state().
- expecting probes to own evolving state instead of sampling runtime state.

### 3.12 `brainx-accelerate`

#### Scope

- **Purpose:** Teach transform-friendly BrainX acceleration patterns and audit guidance.
- **Version status:** first version ready.
- **Source mirrored:** https://github.com/Wilsonnijc-bot/BrainX-skill/blob/main/skills/brainx-accelerate/SKILL.md
- **Note:** 需要知道 AI 会出现的实际错误，现在根据 transformation 的内容写了 first version.

#### Core Concepts

- transform-friendly BrainX programs.
- correctness before speed.
- state/RNG safety.
- shape stability.
- warm runtime vs compile time.
- multi-device scale.
- Axis map: N = neurons / synapses / features / compartments; T = time / recurrent steps; B = batch / trials / stimuli / subjects; E = ensemble / parameter sweep / seeds; P = trainable parameters / sensitivities; D = devices.
- BrainState transforms for stateful code.
- JAX transforms only for pure functions.
- jnp in transformed numerical paths.
- np only for host preprocessing.
- Explicit State, ParamState, RandomState.
- Stable shapes and PyTrees.
- Sparse/event representation preservation.

#### General Scripts

1. rewrite N scalar loops into array state.
2. rewrite T Python loops into scan / for_loop / while_loop.
3. rewrite B loops into vmap.
4. rewrite E sweeps into mapped states / params / RNG.
5. rewrite finite differences into grad.
6. choose JIT boundary.
7. compose jit, scan, vmap, grad.
8. handle randomness under vmap.
9. decide when multi-device is appropriate.
10. validation gates.
11. acceleration audit response format.

#### Reference Routing

- brainstate-control-flow patterns.
- transformation-grad expansion.
- transformation-jit expansion.
- transformation-vmap expansion.

#### Common Failures

- sprinkling jit everywhere.
- compiling tiny helpers inside loops.
- using raw JAX transform around BrainState state.
- Python loops over time/batches/sweeps.
- finite-difference gradients.
- dynamic list history.
- full-history memory blowup.
- dynamic shapes.
- hidden mutable attributes.
- ambiguous state under vmap.
- RNG reuse.
- host sync in hot paths.
- accidental densification of sparse/event connectivity.
- claiming speedup from first-call compile time.

## 4. Reference Layer Illustration

The reference layer below follows your uploaded Reference Markdown Plan. Source mirrors use literal HTML links.

### 4.1 Brain Dynamics related references

### brain-dynamics-delay protocol

- Description: Explains BrainState Brain Dynamics delay APIs and delay-buffer behavior.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/02_synaptic_delays.html

### brain-dynamics-event-driven operators

- Description: Catalogs event-driven sparse spike operators and connectivity patterns for scalable SNNs.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/03_event_driven_operators.html

### 4.2 BrainCell related references

### braincell-custom-ion-channel authoring

- Description: Custom BrainCell ion/channel authoring notes moved out of the skill list into shared reference markdown.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/ions_channels.html
  - https://brainx.chaobrain.com/braincell/tutorials/channel.html
  - https://brainx.chaobrain.com/braincell/developer/extending.html

### braincell-manual-morphology construction

- Description: Manual morphology construction and morphology-preparation notes moved out of the skill list into shared reference markdown.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/morphology.html

### morphology-io-loading validation

- Description: Unified reference for morphology import paths, validation reports/options, NeuroMorpho caching, and checkpoints.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/file_formats/overview.html
  - https://brainx.chaobrain.com/braincell/file_formats/swc.html
  - https://brainx.chaobrain.com/braincell/file_formats/asc.html
  - https://brainx.chaobrain.com/braincell/file_formats/neuroml2.html
  - https://brainx.chaobrain.com/braincell/file_formats/neuromorpho.html
  - https://brainx.chaobrain.com/braincell/file_formats/checkpointing.html
  - https://brainx.chaobrain.com/braincell/concepts/morphology.html

### probe reference

- Description: Documents BrainCell probes for recording runtime state, mechanism state, currents, and traces.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/tutorials/mech.html

### topology-building-and visualization

- Description: Guides runtime topology inspection and visualization for multicompartment BrainCell cells.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/tutorials/vis.html
  - https://brainx.chaobrain.com/braincell/tutorials/filter.html

### 4.3 BrainState related references

### advanced randomness

- Description: Catalogs advanced BrainState RNG streams, stochastic layers, and transformed-randomness patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html

### brainstate-control-flow patterns

- Description: Collects loop and branch patterns that remain valid under BrainState and JAX transformations.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/05_control_flow.html

### parameter-constraints regularization

- Description: Conceptual workflow for ParamState vs nn.Param, constrained values, regularization penalties, Const, and training-loss integration.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/05_parameters_transforms_regularization.html

### parameter-containers-transforms catalog

- Description: Catalog for Param, Const, and built-in parameter transforms; maps constraints to transform choices.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/parameters.html

### regularization-catalog priors

- Description: Catalog for classical, structural, chained, and prior-distribution regularizers; maps modeling goals to reg= choices.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/regularization.html

### transformation-grad expansion

- Description: Expands gradient and autodiff teaching for differentiable simulation and parameter fitting.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/02_autodiff.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/05_parameters_transforms_regularization.html

### transformation-jit expansion

- Description: Expands BrainState-aware JIT compilation, state write-back, cache, and static-argument guidance.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/01_jit_and_compilation.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/06_transformations_essentials.html

### transformation-vmap expansion

- Description: Expands BrainState vectorization, batching, state axes, sweeps, and stochastic vmap patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/03_vectorization.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/04_advanced_batching.html

### 4.4 Diagnostics related references

### brainstate-transformed diagnostics

- Description: Collects runtime debugging, checking, and error-handling patterns for transformed BrainState code.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/06_error_handling_and_checks.html
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/07_debugging.html

### 4.5 Libraries related references

### channel library

- Description: Catalogs BrainCell channels and channel-modeling patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/braincell.channel.html
  - https://brainx.chaobrain.com/braincell/tutorials/channel.html

### cv-policy reference

- Description: Catalogs BrainCell control-volume policies and discretization tradeoffs.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/discretization.html

### filter-function library

- Description: Catalogs BrainCell region and locset filters for targeting mechanisms, probes, and clamps.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/filter.html

### ion library

- Description: Catalogs BrainCell ion species and ion-related modeling patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/braincell.ion.html

### prebuilt-activation library

- Description: Catalogs BrainState activation and normalization components for module-building tasks.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/activation.html

### prebuilt-layer library

- Description: Catalogs BrainState prebuilt layers so agents reuse existing components before writing custom modules.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/linear.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/conv.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/normalization.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/pooling.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/padding.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/dropout.html

### solver library with effects

- Description: Catalogs BrainCell and BrainState solver and integration choices with modeling consequences.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/integration.html
  - https://brainx.chaobrain.com/braincell/integration/solvers.html
  - https://brainx.chaobrain.com/braincell/integration/advanced.html

### 4.6 Local Index and Policy related references

### common-failures index

- Description: Maps recurring BrainX failure patterns to the skill or reference that should handle them.

## 5. Skill 测试与迭代方法

已经写成了 skill: brainx-skill-creator SKILL.md - https://github.com/Wilsonnijc-bot/brainx-skill-creator/blob/main/skills/brainx-skill-creator/SKILL.md

流程分为五步。

### 5.1 选择测试案例

- 先找真实、有代表性的 BrainX 任务作为 test case。
- 例如：BrainState state 管理、Module 构建、BrainCell 单细胞模拟、多隔室建模、unit safety、training loop 等。
- 测试案例不能太简单，要比 BrainX tutorial 更加复杂一些。
- 运行前设定明确验收标准（预期）。

比如：

- 应该使用哪个 BrainX package：BrainState、BrainCell、BrainUnit 等。
- 应该使用哪些核心 API：Module、State、ParamState、BrainCell mechanisms、morphology selectors、quantity units 等。
- 应该使用哪些 transformation：jit、grad、vmap、scan / for_loop 等。
- 哪些做法是不合格的：比如用 NumPy 手写一个 HH model 来替代 BrainCell，或者把参数存在普通 Python attribute 里而不是 State 里。

这些验收标准要足够具体，能区分“真正用对 BrainX”和“只是提到了 BrainX”。最终会把 expectations 一条一条列出来写成一个 eval.json。

### 5.2 做对照实验

每个 test case 都跑两次：

- without skill / old skill：作为 baseline，看 agent 在没有新 skill 指导时会怎么做。
- new skill：看新的 skill 是否能让 agent 更准确地选择 BrainX API、写出更接近可运行的代码，并避免泛化成普通 JAX/NumPy 实现。

### 5.3 让 agent 以 grader markdown 为框架参照 eval.json 去评判每一个验收标准

- 运行结束后，不靠主观感觉判断好坏，而是让 grader 按照预设 expectations 系统评分。
- grader 需要根据所有的 expectation 检查：是否用了正确的 BrainX package、是否用了正确的 API、transformation、state、unit、module、morphology、mechanism 等概念是否用对。
- 最后输出结构化结果：哪些 expectation pass，哪些 fail，每个判断都要有 evidence。

### 5.4 根据失败案例改 skill

- 回到失败 transcript 和输出中，找出 agent 缺失的部分。
- 然后根据 BrainX 官方 HTML 文档核对正确 API 和概念，再修改 skill。

改进原则是：

- 如果 agent 用了 generic code，skill 要更明确地强调 BrainX-native workflow。
- 如果 agent hallucinate API，要补充正确 API 边界。
- 如果 agent 选错 package，要强化 BrainState / BrainCell / BrainUnit 的分工。
- 如果 agent 漏掉 transformation，要补充什么时候用 jit、grad、vmap、scan。
- 如果 expectation 太弱，也要更新 expectation，让下次能抓住同类错误。

### 5.5 循环目标

这个循环会不断重复：

- 选 test case → 写 expectations → baseline vs new skill → grader 评分 → 查 HTML 文档 → 改 skill → 再跑一轮。

最终目标不是让某一个案例通过，而是让 skill 稳定地训练 agent：在面对 BrainX 建模和仿真任务时，能自然选择正确的 BrainX API，写出清晰、可运行、符合文档的 BrainX-native 代码。

### 5.6 验收标准

- 验收标准不按单个 skill 设置，而是按 test case 设置。
- 因为实际任务通常会同时触发多个 skill，例如 BrainCell、BrainState、BrainUnit、transformation、unit safety 等。
- 验收的对象是“这一组 skill 在真实任务中是否协同产生了正确结果”。

每个 case 的验收重点（expectations）会被一一列举，如：

1. 相关 skill 是否被正确唤起。
2. agent 是否选择了正确的 BrainX package 和 API。
3. 是否形成了 BrainX-native workflow，是否避免用 generic NumPy / JAX 替代核心建模逻辑。

## 6. Test Case 规划

### 6.1 主测试：让科研组同学反馈

我认为最重要的测试不应该只由我自己判断，而应该让组里真正做建模的人参与反馈。

原因很简单：他们更懂 BrainX 代码，也更懂一个建模任务是否科学合理。

所以最终测试应该包括：

- 让组里同学看 agent 生成的 BrainX 代码。
- 让他们判断 API 是否自然、建模是否合理、结果是否可信。
- 收集他们对失败点的反馈，再反向修改 skill。

每当他们用 skill 跑了一个任务的时候，可以简单记录：

0. 在做什么类型的任务使用了 skill，跑出来的理想结果应该是什么
1. 这个代码是否 BrainX-native？
2. 最大问题是什么？
3. 是否用了正确 package？
4. 建模假设是否合理？
5. 结果输出是否有科研意义？
6. 这个代码你是否愿意继续改？

### 6.2 个人测试路线 A：真实组内建模任务

第一类 test case 是直接使用组里同学正在做的建模任务。

做法是：

- 请正在用 BrainX 建模的同学给我一个真实任务。
- 我用同一个任务 prompt 去跑 agent。
- 分别跑 baseline 和 new skill。
- 然后把 agent 输出和同学自己的 BrainX 代码进行对照。

这里，同学自己的代码可以作为接近理想情况的 reference。

观察：

- agent 是否选对 BrainX package。
- agent 是否使用了正确的 BrainX API。
- agent 是否遵循了真实 BrainX 建模 workflow。
- agent 是否避免了 generic NumPy / JAX 替代。
- agent 输出是否接近一个科研人员会继续修改和使用的版本。

### 6.3 个人测试路线 B：partial reproduction collaboration lab 论文

第二类 test case 是从 BrainX collaboration lab 的论文或项目中选取一部分，做 partial reproduction。

现在重点是 BrainCell，所以这类测试特别适合选择和 biophysical modeling、single-cell simulation、dendritic computation 有关的项目。例如，可以从 Prof. Douglas Zhou lab 的 dendritic integration 项目中抽取一个小目标，而不是复现整篇论文。

可测试的 partial reproduction 目标包括：

- 构建一个带 dendritic cable 的简化神经元模型。
- 放置 excitatory / inhibitory synaptic inputs。
- 模拟不同输入位置或时间差对 soma voltage 的影响。
- 输出 voltage trace 或简单的 E-I integration comparison。
- 让 agent 解释这个模型如何对应 BrainCell workflow。

这里的重点不是完整复现论文结果，而是测试 agent 能不能把论文中的建模需求转化为 BrainX-native implementation。

### 6.4 example Benchmark record

| Test case | Skills tested | Reference tested | Difficulty | Human review |
|---|---|---|---|---|
| Single-cell current clamp | BrainCell singlecell, BrainUnit | ion/channel library | medium | yes |
| Dendritic E/I integration | BrainCell multicompartment, BrainUnit | morphology, probe, filter | hard | yes |
| SNN training | BrainState brain dynamics, training, transform | control-flow, grad | hard | yes |
| CNN training | module, training, randomness | layer/activation library | medium | optional |
| Acceleration rewrite | accelerate, transform | jit/vmap/control-flow | hard | yes |

### 6.5 Accelerate Skill 专项 Benchmark

Accelerate skill 需要单独设置 benchmark，因为它的目标不是让 agent 重新写一个 BrainX 模型，而是让 agent 识别已有实现中的性能瓶颈，并把普通 BrainX 代码改写成 transformation-aware 的高性能实现。

#### Baseline 设置

让 agent 在看不到任何 transformation 相关 skill 的情况下完成同一个任务。

观察重点：

- 是否写出低效 Python loop。
- 是否出现重复计算、手动 batch 处理、手动参数 sweep。
- 是否用 generic NumPy / JAX 代替 BrainState transformation。
- 是否缺少明确的 jit、vmap、scan / for_loop、grad 重写思路。

#### New Skill 设置

让 agent 使用 accelerate + transformation skill bundle 浏览 workspace，并对同一个任务进行加速改写。

观察重点：

- 是否能识别原始代码中的性能瓶颈。
- 是否主动使用 BrainState 的 jit、vmap、scan / for_loop、grad 等 transformation 重写核心计算。
- 是否把时间循环、batch 维度、参数 sweep、训练梯度等结构映射到合适的 transformation。
- 是否保持 BrainX-native 代码结构，而不是只写 generic JAX 加速代码。

#### 对比重点

- agent 是否从普通实现转向 BrainState transformation workflow。
- 是否避免继续使用低效 Python loop 作为核心计算。
- 是否把加速边界放在完整 step / simulation / train step 上，而不是零散 helper function。
- 是否保持 State、ParamState、RandomState 的 BrainState 语义。
- 生成的代码是否更接近可编译、可扩展、适合大规模仿真的实现。

#### 验收标准

Accelerate skill 的验收标准不是“有没有提到加速”，而是看它是否真的让 agent 把一个普通 BrainX 实现改写成结构正确、transformation 使用合理、性能意识明确的 BrainX-native 高性能实现。

## 7. 版本与发布策略

- 每个 skill 和 reference markdown 都得绑定明确的版本状态，确保和 BrainX 本身代码迭代版本一致。
- 每次发布前必须记录对应的 BrainX / BrainState / BrainCell / BrainUnit 版本。
- 每次发布前必须记录核对过的官方文档链接与日期。
- 每次发布前必须记录本次修改影响了哪些 skill、reference 和 test case。
- 当 BrainX 官方 API 或 example 更新时，相关 skill 可能需要重新回到 draft/tested 状态，重新修改。
