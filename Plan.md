# BrainX skill MVP 设计

## Table of Contents

1. [Overview and Scope](#1-overview-and-scope)
2. [Complete Current Skill List](#2-complete-current-skill-list)
3. [Skill Layer Design](#3-skill-layer-design)
4. [Reference Layer Illustration](#4-reference-layer-illustration)
5. [Skill 测试与迭代方法](#5-skill-测试与迭代方法)
6. [Test Case 规划](#6-test-case-规划)
7. [验收标准](#7-验收标准)
8. [后期严谨的版本管理](#8-后期严谨的版本管理)

## 1. Overview and Scope

- 需求：computational neuroscience 有关的科研人员需要使用 BrainX 的代码框架建模，现在普遍使用 AI agent 做 coding，所以这套 skill 需要教会 AI agent 使用 BrainX 的框架，并且根据 AI agent 的上下文性质，整个 skill 设计遵从渐进式披露的思想，以达到高效的 token使用，把信息分成 2 层：最核心且代表大方向的知识点放在 skill 层，把大方向之下发散知识点以及最完整样例代码归类 以 reference 的形式让agent去点击。

## 2. Complete Current Skill List

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

### 3.1 brainx-install

- Target size: Within 100-150 lines
- What it covers:
  - Concept:
    - BrainX meta-package
    - Python and pip requirements
    - CPU / GPU / TPU backend choice
    - CUDA install path
    - pinned BrainX release for reproducibility
  - General scripts:
    - ask-before-install environment checklist
    - quick install: `pip install -U BrainX`
    - CPU install
    - CUDA 12/13 install
    - TPU install
    - exact release pinning
    - import validation
    - JAX device validation
  - Script references:
    - `brainx-install-verify.py`
  - Common failures:
    - installing before identifying backend
    - unsupported Python version
    - old pip / setuptools / wheel
    - wrong CUDA/JAX wheel
    - installing only raw JAX when BrainCell/BrainUnit is needed
    - assuming GPU works without checking `jax.devices()`

### 3.2 brainx-general-guard

- Target size: Within 300-350 lines
- 这个需要组里大家一起给一个 list, 具体归纳 agent 常犯的错误。
- This is the global guardrail layer. It prevents the most damaging cross-framework mistakes.
- What it covers:
  - Concept:
    - BrainState mental model:
      - State
      - Module
      - Transform
    - BrainState transform is state-aware
    - raw JAX transform boundary
  - General Rules：
    1. Before writing unfamiliar BrainX code, open the relevant official BrainX example/API page.
    2. Copy import paths from official docs.
    3. Prefer existing library classes before custom implementation.
    4. Do not infer method names from PyTorch/JAX/BrainPy style.
    5. If unsure, write a small import/instantiation smoke test first.
  - General scripts:
    - stateful JIT guard example
    - stateful for-loop guard example
    - unit-required BrainCell example
  - Common failures:
    - writing raw NumPy simulation instead of BrainState / BrainCell
    - using raw `jax.jit` on mutable BrainState modules
    - using Python loops around transformed state updates
    - passing bare floats into BrainCell mechanisms
    - treating BrainCell as generic ODE code
    - writing custom ion/channel before checking built-in libraries
    - inventing BrainX APIs instead of opening official examples
  - Reference:
    - common failure wiki

### 3.3 brainunit-quantity-safety

- Target size: Within 200 lines, mirror:
  - https://brainx.chaobrain.com/brainunit/getting_started/quickstart.html
- Teach the fundamental infrastructure unit.
- What it covers:
  - Concept + General scripts:
    - Quantity = numeric value + physical unit
    - scalar quantity example
    - array quantity example
    - dimensional checking
    - unit conversion
    - `to_decimal()`
    - `in_unit()`
    - quantity attributes:
      - mantissa
      - unit
      - dim
      - shape
      - dtype
    - unit-aware math through `brainunit.math`
    - unit-aware JAX transforms
    - function-level unit contracts through `@check_units`
    - BrainCell requires explicit units for physical parameters
  - Reference markdowns:
    - None

### 3.4 brainstate-state-management

- Target size: Within 250 lines, mirror:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/01_state_and_pytrees.html
- What it covers:
  - Concept:
    - mutation happens through State objects
    - state update discipline
    - relationship between Module and State
    - core features of State + important notes
  - General scripts:
    - create, read, update a State, support with pytree
    - subclass state examples
    - collect ParamState
    - track states inside a module
    - LIF neuron example using:
      - HiddenState
      - ShortTermState
      - ParamState
  - Common failures:
    - mutating ordinary Python attributes instead of State
    - hiding model variables outside State
    - confusing ParamState with runtime hidden state
    - forgetting that transformed BrainState code tracks State reads/writes
  - Reference markdowns:
    - skill-transformation core
    - randomness and reproducibility skill

### 3.5 brainstate-module-building

- Target size: Within 300 lines
- What it covers:
  - Concept:
    - Module-tree of state
    - `brainstate.nn.Module` is the base class
    - reusability & composability
    - build one module, then combine and nest into module graph
    - parameter registration
    - `model.states(brainstate.ParamState)`
  - General scripts:
    - Layers in state
    - minimal Linear layer + forward pass with random input
    - prebuilt activation example, e.g. Sigmoid
    - nesting
    - nested module example
    - ResidualBlock example
    - HH model script HH Neuron model under Brain Dynamics
    - Sequential,.desc() layer construction,size inference
    - `->ComplexNet` example that puts layers and concepts together:
      - convolution
      - pooling
      - flatten
      - linear
    - full script example ModernCNN from tutorial
  - Reference markdowns:
    - Pre-built Activation Functions
    - Pre-built Basic Layers
    - parameters and regularization
    - Size Inference with Convolution
    - Size Inference with Pooling & Flatten
    - Skill for Deeplearning Training
    - Skill for randomness and reproducibility
    - Modern CNN script from “activation function and normalization https://brainx.chaobrain.com/brainstate/tutorials/core/04_activations_and_normalization.html”
  - Script references:
    - Explicit external script reference:
      - “Training Spiking Neural Network script” → https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/05_training_an_snn.html
  - Common failures:
    - writing loose functions instead of reusable Module classes
    - storing trainable parameters outside ParamState / registered parameters
    - building custom layers before checking prebuilt BrainState layers
    - missing shape inference rules
    - nesting modules without checking state collection

### 3.6 brainstate-transformations-core

- Target size: Within 150-200 lines, only the basic transformations, mirror:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/06_transformations_essentials.html
- The advanced transformations will be in reference.
- What it covers:
  - Concept:
    - BrainState-aware transformations
    - difference from raw JAX
    - state read/write tracking
    - `brainstate.transform.jit`, `grad`, `vmap`
    - composed transforms
    - raw JAX boundary
    - transform over modules and states
  - General scripts:
    - minimal state-aware JIT
    - forward pass wrapped by `brainstate.transform.jit`
    - collect ParamState
    - gradient over state collection
    - parameter update using gradient keys
    - minimal vmap over batch
    - composed train-step transform
  - Reference markdowns:
    - brainstate-control-flow patterns
    - transformation-grad expansion
    - transformation-jit expansion
    - transformation-vmap expansion
    - brainstate-transformed diagnostics
  - Common failures:
    - using raw `jax.jit` on mutable BrainState modules
    - differentiating positional arguments like plain JAX when the target is ParamState
    - compiling tiny operations instead of full steps
    - Python-looping over batches instead of using vmap
    - updating parameters outside the gradient key structure
    - using control-flow transforms without checking the control-flow reference

### 3.7 brainstate-randomness-reproducibility

- Target size: Within 150-200 lines, only the basic randomness, pick out the fundamentals from:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html
- The advanced RNG will be in reference.
- Fundamental to random trials and reproducible experiments.
- What it covers:
  - Concept:
    - Key Features
    - Automatic key splitting
    - NumPy compatibility
    - Stateful interface
    - JIT compatible
    - `brainstate.random` as the default RNG interface + script
    - reproducibility through `brainstate.random.seed(seed)` + script
    - global DEFAULT RandomState
    - common random functions:
      - random
      - rand
      - randn
    - stochastic module behavior
    - train-only randomness
    - `brainstate.environ.get('fit', False)`
    - Choice and Permutation & shuffle array
  - Advanced reference routing, ONLY included in markdown:
    - manual key control
    - custom RandomState
    - independent random streams
    - checkpoint RNG save/restore
    - temporary seed context
    - default_rng
    - clone_rng
    - probability distribution catalog
    - transformed randomness
    - parallel randomness
    - stochastic brain-dynamics workflows
  - Reference markdowns:
    - advanced randomness
  - Common failures:
    - manually creating/splitting raw JAX keys by default
    - forgetting to seed reproducible examples
    - using np.random in transformed BrainState code
    - reusing the same randomness across mapped trials
    - applying dropout/noise during evaluation

### 3.8 brainstate-deeplearning-training

- Target size: Within 300 lines, mirror:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html
- Concept:
  - what this skill is for
  - Route supervised BrainState training tasks here.
  - training loop structure
  - Assemble Module + ParamState + grad + jit into one train/eval loop.
  - model / loss / optimizer / metrics
  - Use the canonical four-part training stack: model, scalar loss, registered optimizer, accumulated metrics.
  - parameter collection
  - Train exactly ParamState; leave other states untouched.
  - gradient update
  - Differentiate loss, optionally clip gradients, then apply `optimizer.update(grads)`.
  - compiled training step
  - JIT the whole train step and let BrainState track state reads/writes.
  - metrics lifecycle
  - Evaluate through reset → update per batch → compute.
  - fit/eval environment note
  - Use `fit=True` for stochastic training behavior and `fit=False` for evaluation.
- Then mirror the scripts in:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html
- Reference markdowns:
  - brainstate-control-flow patterns
  - parameter-constraints regularization
  - skill transformation-core
  - skill randomness-reproduction
  - prebuilt-activation library
  - prebuilt-layer library
  - Optimizer library
- Script references:
  1. Image Classification with CNNs
     - Best normal supervised-training illustration: train/eval split, compiled train step, optimizer registration, loss, accuracy, validation loop.
     - https://brainx.chaobrain.com/brainstate/examples/deep_learning/image_classification.html
  2. Training Recurrent Neural Networks: Integrator Task
     - Best for temporal training: init_all_states, for_loop, sequence loss, L2 regularization, scheduler, recurrent state.
     - https://brainx.chaobrain.com/brainstate/examples/deep_learning/integrator_rnn.html
  3. Training a Spiking Neural Network
     - https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_training.html
     - Best for SNN-specific training: backpropagation through time, surrogate gradient, spk_fun=braintools.surrogate.ReluGrad(), for_loop, grad, optimizer update.

### 3.9 brainstate-brain-dynamics

- Target size: Within 300 lines, currently interpreted as a variation/sub-class of module building.
- What it covers:
  - Concept:
    - brain models as dynamical systems
    - state variables evolving over time, `update()`
    - element-wise update principle
    - architecture flow:
      - input
      - connection modules
      - dynamics modules
      - output
    - input / output / states / parameters
    - before-update hooks
    - after-update hooks
    - in_size
    - out_size
    - delay support
    - BrainPy-state SNN workflow boundary
    - BrainCell boundary
  - General scripts:
    - simulation time-step setup
    - minimal exponential decay Dynamics module
    - minimal LIF Dynamics module
    - simulation loop with `brainstate.transform.for_loop`
    - before-update hook
    - after-update hook
    - delay support pattern:
      - output_delay
      - prefetch_delay
    - BrainPy-state LIF population example
    - E/I SNN workflow route
    - SNN training route
  - Reference markdowns:
    - brain-dynamics-delay protocol
    - brain-dynamics-event-driven operators
    - brainstate-control-flow patterns
    - transformation-grad expansion
    - solver library with effects
  - Script references:
    - Example of simulating HH-cell
      - https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/hodgkin_huxley_neuron.html
    - Training a Spiking Neural Network
      - https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_training.html
      - Best for SNN-specific training: backpropagation through time, surrogate gradient, spk_fun=braintools.surrogate.ReluGrad(), for_loop, grad, optimizer update.
  - Common failures:
    - putting synaptic connectivity inside `Dynamics.update()`
    - forgetting to implement `update()`
    - using unitless time/current/voltage
    - using long Python loops instead of for_loop
    - forgetting time context inside step functions
    - manually managing delay buffers
    - treating BrainState brain dynamics as BrainCell morphology modeling

### 3.10 braincell-singlecell

- Target size: within 300 lines
- What it covers:
  - Concept:
    - SingleCompartment
    - point-neuron modeling
    - HH framework
    - membrane potential & capacitance
    - reversal potentials
    - channel conductances
    - external injected current
    - Ion
    - Channel
    - channel gating state
    - MixIons
    - BrainCell synapse classes:
      - AMPA
      - GABA
      - aNMDA
    - solver selection
  - General scripts:
    - minimal HH single cell
    - current-clamp simulation
    - direct cell.update(input_current)
    - use existing sodium ion & channel
    - add leak channel
    - Traub-Miles style HH cells with area-scaled conductance
    - MixIons for calcium-activated potassium current
    - vectorized FI curve
    - E/I network route
    - BrainCell synapse class route
    - solver guidance
  - Reference markdowns:
    - channel library
    - ion library
    - solver library with effects
    - skill randomness and reproduction
    - parameter and regularization
    - braincell-custom-ion-channel authoring
    - braincell-manual-morphology construction
  - Common failures:
    - using this skill when geometry matters
    - treating ion and channel as the same object
    - adding channel to wrong ion container
    - passing bare floats for biological quantities
    - using old add_elem style when current examples use add
    - putting leak current inside an ion container
    - forgetting init_state()
    - using raw Python loops instead of BrainState loop transforms
    - interpreting V_th as a conductance parameter
    - custom authoring before checking built-in ion/channel libraries

### 3.11 braincell-multicompartment

- Target size: within 350 lines
- What it covers:
  - Concept:
    - morphology
    - branch
    - soma / dendrite / axon
    - region
    - locset
    - paint
    - place
    - CV
    - CV policy
    - discretization
    - cable property
    - density mechanism
    - point mechanism
    - ion
    - channel
    - mechanism system
    - probe
    - clamp
    - solver
    - runtime topology
    - morphology loading
  - General scripts:
    - build Cell from existing morphology
    - load morphology from SWC
    - inspect topology
    - choose CV policy
    - paint cable properties
    - paint density mechanism
    - paint existing channel
    - place point mechanism
    - place current clamp
    - place state probe
    - use existing ion
    - use existing channel
    - use MixIons
    - use CVPerBranch
    - use DLambda
    - use MaxCVLen
    - add CableProperty
    - add clamp
    - add probe
    - sample probes
    - visualize topology
    - region and locset filter route
  - Reference markdowns:
    - braincell-custom-ion-channel authoring
    - braincell-manual-morphology construction
    - ion library
    - channel library
    - morphology-io-loading validation
    - filter function library
    - solver library + effect of different integrations
    - cv-policy reference
    - filter function library
  - Script references:
    - undecided
  - Common failures:
    - using single-compartment skill when geometry matters
    - confusing region painting with point placement
    - placing density mechanisms with place
    - painting point mechanisms with paint
    - skipping morphology validation
    - stripping units from cable parameters
    - using custom ion/channel before checking libraries
    - ignoring CV policy effects
    - forgetting cell.init_state()
    - expecting probes to own evolving state instead of sampling runtime state

### 3.12 brainx-accelerate

- 需要知道 AI 会出现的实际错误，现在根据 transformation 的内容写了 first version:
  - https://github.com/Wilsonnijc-bot/BrainX-skill/blob/main/skills/brainx-accelerate/SKILL.md
- Concept:
  - transform-friendly BrainX programs
  - correctness before speed
  - state/RNG safety
  - shape stability
  - warm runtime vs compile time
  - multi-device scale
  - Axis map:
    - N = neurons / synapses / features / compartments
    - T = time / recurrent steps
    - B = batch / trials / stimuli / subjects
    - E = ensemble / parameter sweep / seeds
    - P = trainable parameters / sensitivities
    - D = devices
  - BrainState transforms for stateful code.
  - JAX transforms only for pure functions.
  - jnp in transformed numerical paths.
  - np only for host preprocessing.
  - Explicit State, ParamState, RandomState.
  - Stable shapes and PyTrees.
  - Sparse/event representation preservation.
- General scripts / protocols:
  - rewrite N scalar loops into array state
  - rewrite T Python loops into scan / for_loop / while_loop
  - rewrite B loops into vmap
  - rewrite E sweeps into mapped states / params / RNG
  - rewrite finite differences into grad
  - choose JIT boundary
  - compose jit, scan, vmap, grad
  - handle randomness under vmap
  - decide when multi-device is appropriate
  - validation gates
  - acceleration audit response format
- Reference markdowns:
  - brainstate-control-flow patterns
  - transformation-grad expansion
  - transformation-jit expansion
  - transformation-vmap expansion
- Script references:
  - None
- Common failures:
  - sprinkling jit everywhere
  - compiling tiny helpers inside loops
  - using raw JAX transform around BrainState state
  - Python loops over time/batches/sweeps
  - finite-difference gradients
  - dynamic list history
  - full-history memory blowup
  - dynamic shapes
  - hidden mutable attributes
  - ambiguous state under vmap
  - RNG reuse
  - host sync in hot paths
  - accidental densification of sparse/event connectivity
  - claiming speedup from first-call compile time

## 4. Reference Layer Illustration

The reference layer below follows your uploaded Reference Markdown Plan. I changed the source format into literal HTML links, as requested.

### 4.1 Brain Dynamics Related References

#### brain-dynamics-delay protocol

- Description: Explains BrainState Brain Dynamics delay APIs and delay-buffer behavior.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/02_synaptic_delays.html

#### brain-dynamics-event-driven operators

- Description: Catalogs event-driven sparse spike operators and connectivity patterns for scalable SNNs.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/03_event_driven_operators.html

### 4.2 BrainCell Related References

#### braincell-custom-ion-channel authoring

- Description: Custom BrainCell ion/channel authoring notes moved out of the skill list into shared reference markdown.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/ions_channels.html
  - https://brainx.chaobrain.com/braincell/tutorials/channel.html
  - https://brainx.chaobrain.com/braincell/developer/extending.html

#### braincell-manual-morphology construction

- Description: Manual morphology construction and morphology-preparation notes moved out of the skill list into shared reference markdown.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/morphology.html

#### morphology-io-loading validation

- Description: Unified reference for morphology import paths, validation reports/options, NeuroMorpho caching, and checkpoints.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/file_formats/overview.html
  - https://brainx.chaobrain.com/braincell/file_formats/swc.html
  - https://brainx.chaobrain.com/braincell/file_formats/asc.html
  - https://brainx.chaobrain.com/braincell/file_formats/neuroml2.html
  - https://brainx.chaobrain.com/braincell/file_formats/neuromorpho.html
  - https://brainx.chaobrain.com/braincell/file_formats/checkpointing.html
  - https://brainx.chaobrain.com/braincell/concepts/morphology.html

#### probe reference

- Description: Documents BrainCell probes for recording runtime state, mechanism state, currents, and traces.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/tutorials/mech.html

#### topology-building-and visualization

- Description: Guides runtime topology inspection and visualization for multicompartment BrainCell cells.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/tutorials/vis.html
  - https://brainx.chaobrain.com/braincell/tutorials/filter.html

### 4.3 BrainState Related References

#### advanced randomness

- Description: Catalogs advanced BrainState RNG streams, stochastic layers, and transformed-randomness patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html

#### brainstate-control-flow patterns

- Description: Collects loop and branch patterns that remain valid under BrainState and JAX transformations.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/05_control_flow.html

#### parameter-constraints regularization

- Description: Conceptual workflow for ParamState vs nn.Param, constrained values, regularization penalties, Const, and training-loss integration.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/core/05_parameters_transforms_regularization.html

#### parameter-containers-transforms catalog

- Description: Catalog for Param, Const, and built-in parameter transforms; maps constraints to transform choices.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/parameters.html

#### regularization-catalog priors

- Description: Catalog for classical, structural, chained, and prior-distribution regularizers; maps modeling goals to reg= choices.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/regularization.html

#### transformation-grad expansion

- Description: Expands gradient and autodiff teaching for differentiable simulation and parameter fitting.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/02_autodiff.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/05_parameters_transforms_regularization.html

#### transformation-jit expansion

- Description: Expands BrainState-aware JIT compilation, state write-back, cache, and static-argument guidance.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/01_jit_and_compilation.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/06_transformations_essentials.html

#### transformation-vmap expansion

- Description: Expands BrainState vectorization, batching, state axes, sweeps, and stochastic vmap patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/03_vectorization.html
  - https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/04_advanced_batching.html

### 4.4 Diagnostics Related References

#### brainstate-transformed diagnostics

- Description: Collects runtime debugging, checking, and error-handling patterns for transformed BrainState code.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/06_error_handling_and_checks.html
  - https://brainx.chaobrain.com/brainstate/tutorials/transformations/07_debugging.html

#### common-failures index

- Description: Maps recurring BrainX failure patterns to the skill or reference that should handle them.
- Mirror Source URLs:
  - None — local cross-reference router.

### 4.5 Libraries Related References

#### channel library

- Description: Catalogs BrainCell channels and channel-modeling patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/braincell.channel.html
  - https://brainx.chaobrain.com/braincell/tutorials/channel.html

#### cv-policy reference

- Description: Catalogs BrainCell control-volume policies and discretization tradeoffs.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/concepts/discretization.html

#### filter-function library

- Description: Catalogs BrainCell region and locset filters for targeting mechanisms, probes, and clamps.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/filter.html

#### ion library

- Description: Catalogs BrainCell ion species and ion-related modeling patterns.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/braincell.ion.html

#### prebuilt-activation library

- Description: Catalogs BrainState activation and normalization components for module-building tasks.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/activation.html

#### prebuilt-layer library

- Description: Catalogs BrainState prebuilt layers so agents reuse existing components before writing custom modules.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/brainstate/apis/nn/linear.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/conv.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/normalization.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/pooling.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/padding.html
  - https://brainx.chaobrain.com/brainstate/apis/nn/dropout.html

#### solver library with effects

- Description: Catalogs BrainCell and BrainState solver and integration choices with modeling consequences.
- Mirror Source URLs:
  - https://brainx.chaobrain.com/braincell/apis/integration.html
  - https://brainx.chaobrain.com/braincell/integration/solvers.html
  - https://brainx.chaobrain.com/braincell/integration/advanced.html

## 5. Skill 测试与迭代方法

- 已经写成了 skill:
  - https://github.com/Wilsonnijc-bot/brainx-skill-creator/blob/main/skills/brainx-skill-creator/SKILL.md
- 流程分为五步：

### 5.1 选择测试案例

- 先找真实、有代表性的 BrainX 任务作为 test case。
- 例如：BrainState state 管理、Module 构建、BrainCell 单细胞模拟、多隔室建模、unit safety、training loop 等。
- 测试案例不能太简单，要比 BrainX tutorial 更加复杂一些。
- 运行前设定明确验收标准（预期）。
- 比如：
  - 应该使用哪个 BrainX package：BrainState、BrainCell、BrainUnit 等。
  - 应该使用哪些核心 API：Module、State、ParamState、BrainCell mechanisms、morphology selectors、quantity units 等。
  - 应该使用哪些 transformation：jit、grad、vmap、scan / for_loop 等。
  - 哪些做法是不合格的：比如用 NumPy 手写一个 HH model 来替代 BrainCell，或者把参数存在普通 Python attribute 里而不是 State 里。
- 这些验收标准要足够具体，能区分“真正用对 BrainX”和“只是提到了 BrainX”。
- 最终会把 expectations 一条一条列出来写成一个 eval.json。

### 5.2 做对照实验

- 每个 test case 都跑两次：
  - without skill / old skill：作为 baseline，看 agent 在没有新 skill 指导时会怎么做。
  - new skill：看新的 skill 是否能让 agent 更准确地选择 BrainX API、写出更接近可运行的代码，并避免泛化成普通 JAX/NumPy 实现。

### 5.3 让 agent 以 grader markdown 为框架参照 eval.json 去评判每一个验收标准

- 运行结束后，不靠主观感觉判断好坏，而是让 grader 按照预设 expectations 系统评分。
- grader 需要根据所有的 expectation 检查：
  - 是否用了正确的 BrainX package。
  - 是否用了正确的 API。
  - transformation、state、unit、module、morphology、mechanism 等概念是否用对。
- 最后输出结构化结果：哪些 expectation pass，哪些 fail，每个判断都要有 evidence。

### 5.4 根据失败案例改 skill

- 回到失败 transcript 和输出中，找出 agent 缺失的部分。
- 然后根据 BrainX 官方 HTML 文档核对正确 API 和概念，再修改 skill。
- 改进原则是：
  - 如果 agent 用了 generic code，skill 要更明确地强调 BrainX-native workflow。
  - 如果 agent hallucinate API，要补充正确 API 边界。
  - 如果 agent 选错 package，要强化 BrainState / BrainCell / BrainUnit 的分工。
  - 如果 agent 漏掉 transformation，要补充什么时候用 jit、grad、vmap、scan。
  - 如果 expectation 太弱，也要更新 expectation，让下次能抓住同类错误。

### 5.5 循环目标

- 这个循环会不断重复：
  - 选 test case → 写 expectations → baseline vs new skill → grader 评分 → 查 HTML 文档 → 改 skill → 再跑一轮。
- 最终目标不是让某一个案例通过，而是让 skill 稳定地训练 agent：在面对 BrainX 建模和仿真任务时，能自然选择正确的 BrainX API，写出清晰、可运行、符合文档的 BrainX-native 代码。

## 6. Test Case 规划

### 6.1 主测试：让科研组同学反馈

- 我认为最重要的测试不应该只由我自己判断，而应该让组里真正做建模的人参与反馈。
- 原因很简单：
  - 他们更懂 BrainX 代码，也更懂一个建模任务是否科学合理。
- 所以最终测试应该包括：
  - 让组里同学看 agent 生成的 BrainX 代码。
  - 让他们判断 API 是否自然、建模是否合理、结果是否可信。
  - 收集他们对失败点的反馈，再反向修改 skill。
- 每当他们用 skill 跑了一个任务的时候，可以简单记录：

0. 在做什么类型的任务使用了 skill，跑出来的理想结果应该是什么
1. 这个代码是否 BrainX-native？
2. 最大问题是什么？
3. 是否用了正确 package？
4. 建模假设是否合理？
5. 结果输出是否有科研意义？
6. 这个代码你是否愿意继续改？

### 6.2 个人测试路线 A：真实组内建模任务

- 第一类 test case 是直接使用组里同学正在做的建模任务。
- 做法是：
  - 请正在用 BrainX 建模的同学给我一个真实任务。
  - 我用同一个任务 prompt 去跑 agent。
  - 分别跑 baseline 和 new skill。
  - 然后把 agent 输出和同学自己的 BrainX 代码进行对照。
- 这里，同学自己的代码可以作为接近理想情况的 reference。
- 观察：
  - agent 是否选对 BrainX package。
  - agent 是否使用了正确的 BrainX API。
  - agent 是否遵循了真实 BrainX 建模 workflow。
  - agent 是否避免了 generic NumPy / JAX 替代。
  - agent 输出是否接近一个科研人员会继续修改和使用的版本。

### 6.3 个人测试路线 B：partial reproduction collaboration lab 论文

- 第二类 test case 是从 BrainX collaboration lab 的论文或项目中选取一部分，做 partial reproduction。
- 现在重点是 BrainCell，所以这类测试特别适合选择和 biophysical modeling、single-cell simulation、dendritic computation 有关的项目。
- 例如，可以从 Prof. Douglas Zhou lab 的 dendritic integration 项目中抽取一个小目标，而不是复现整篇论文。
- 可测试的 partial reproduction 目标包括：
  - 构建一个带 dendritic cable 的简化神经元模型。
  - 放置 excitatory / inhibitory synaptic inputs。
  - 模拟不同输入位置或时间差对 soma voltage 的影响。
  - 输出 voltage trace 或简单的 E-I integration comparison。
  - 让 agent 解释这个模型如何对应 BrainCell workflow。
- 这里的重点不是完整复现论文结果，而是测试 agent 能不能把论文中的建模需求转化为 BrainX-native implementation。

### 6.4 example Benchmark record

| Test case | Skills tested | Reference tested | Difficulty | Human review |
|---|---|---|---|---|
| Single-cell current clamp | BrainCell singlecell, BrainUnit | ion/channel library | medium | yes |
| Dendritic E/I integration | BrainCell multicompartment, BrainUnit | morphology, probe, filter | hard | yes |
| SNN training | BrainState brain dynamics, training, transform | control-flow, grad | hard | yes |
| CNN training | module, training, randomness | layer/activation library | medium | optional |
| Acceleration rewrite | accelerate, transform | jit/vmap/control-flow | hard | yes |

## 7. 验收标准

- 验收标准不按单个 skill 设置，而是按 test case 设置。
- 因为实际任务通常会同时触发多个 skill，例如 BrainCell、BrainState、BrainUnit、transformation、unit safety 等。
- 就像前面说的一样，每个 case 的验收重点（expectations）会被一一列举，如：
  1. 相关 skill 是否被正确唤起
  2. agent 是否选择了正确的 BrainX package 和 API
  3. 是否形成了 BrainX-native workflow，是否避免用 generic NumPy / JAX 替代核心建模逻辑。
- 验收的对象是“这一组 skill 在真实任务中是否协同产生了正确结果”。

## 8. 后期严谨的版本管理

- 每个 skill 和 reference markdown 都得绑定明确的版本状态，确保和 BrainX 本身代码迭代版本一致。
- 每次发布前必须记录：
  - 对应的 BrainX / BrainState / BrainCell / BrainUnit 版本；
  - 核对过的官方文档链接与日期；
  - 本次修改影响了哪些 skill、reference 和 test case。
- 当 BrainX 官方 API 或 example 更新时，相关 skill 可能需要重新回到 draft/tested 状态，重新修改。
