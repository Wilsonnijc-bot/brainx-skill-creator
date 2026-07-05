# BrainX Agent HTML Reference
Purpose: help an agent choose which BrainX documentation page to open next.
Usage: match the user request to a section and description, then click the page link.
Scope: BrainX Ecosystem Getting Started, BrainUnit Getting Started, BrainCell Concepts, BrainCell Modeling Tutorials, BrainCell Examples, BrainCell File Formats and IO, BrainState Getting Started, BrainState Core Tutorials, BrainState Transformation Tutorials, BrainState Brain Dynamics Tutorials, BrainState Deep Learning Examples, BrainState Brain Dynamics Examples, and Braintools Optimization.
Total HTML pages: 64.
Each teaching description is exactly ten words.

## BrainX Ecosystem Getting Started
- [Installing the Ecosystem](https://brainx.chaobrain.com/summ/install.html) — Covers requirements, BrainX package installation, accelerator extras, verification, and troubleshooting.

## BrainUnit Getting Started
- [Overview](https://brainx.chaobrain.com/brainunit/) — Introduces physical units, quantities, JAX integration, installation, and quickstart workflows.

## BrainCell Concepts
- [Architecture](https://brainx.chaobrain.com/braincell/concepts/architecture.html) — Explains declaration layers, discretization, runtime graphs, and JAX execution flow.
- [Units](https://brainx.chaobrain.com/braincell/concepts/units.html) — Explains physical units, conversions, dimensional checks, and quantity safety rules.
- [Cells](https://brainx.chaobrain.com/braincell/concepts/cells.html) — Compares point neurons and morphologically detailed cells sharing HH machinery.
- [Morphology](https://brainx.chaobrain.com/braincell/concepts/morphology.html) — Defines neuronal branch geometry, topology, typed cables, and import sources.
- [Mechanisms](https://brainx.chaobrain.com/braincell/concepts/mechanisms.html) — Defines density and point mechanisms using declarative paint place semantics.
- [Ions & Channels](https://brainx.chaobrain.com/braincell/concepts/ions_channels.html) — Explains ion species, channels, reversal potentials, and current coupling logic.
- [Regions & Locsets](https://brainx.chaobrain.com/braincell/concepts/regions_locsets.html) — Teaches region and locset selectors for targeting mechanisms spatially precisely.
- [Discretization](https://brainx.chaobrain.com/braincell/concepts/discretization.html) — Explains control volumes, CV policies, and multicompartment discretization tradeoffs clearly.
- [Integration](https://brainx.chaobrain.com/braincell/concepts/integration.html) — Explains differential equations, solver choices, stiffness, and differentiable integration protocols.

## BrainCell Modeling Tutorials
- [Overview](https://brainx.chaobrain.com/braincell/tutorials/overview.html) — Maps a path from point HH cells to multicompartment modeling.
- [Single Compartment Neurons](https://brainx.chaobrain.com/braincell/tutorials/single_compartment.html) — Explains HHTypedNeuron foundations and single compartment neuron model implementation workflows.
- [Channels](https://brainx.chaobrain.com/braincell/tutorials/channel.html) — Teaches existing, custom, HH, and Markov ion channel modeling patterns.
- [Ions](https://brainx.chaobrain.com/braincell/tutorials/ion.html) — Explains ion species, MixIons, Nernst templates, and calcium dynamics modeling.
- [Morphology in BrainCell](https://brainx.chaobrain.com/braincell/tutorials/morphology.html) — Builds neuron morphologies from branches, points, topology, and geometry rules.
- [Region and Locset Filters](https://brainx.chaobrain.com/braincell/tutorials/filter.html) — Shows region and locset filters for targeted spatial mechanism placement.
- [Mechanisms in BrainCell](https://brainx.chaobrain.com/braincell/tutorials/mech.html) — Explains density and point mechanisms, clamps, probes, paint, place workflows.
- [Cell in BrainCell](https://brainx.chaobrain.com/braincell/tutorials/cell.html) — Turns morphologies into cells using CV policies, paint, place APIs.
- [Point Tree Visualization](https://brainx.chaobrain.com/braincell/tutorials/vis.html) — Visualizes NodeTree, CV, and branch topology with runtime coloring options.

## BrainCell Examples
- [Your First Hodgkin Huxley Neuron](https://brainx.chaobrain.com/braincell/examples/hh_neuron_basics.html) — Builds and simulates one HH neuron, then plots spikes traces.
- [Calcium Channel Gating](https://brainx.chaobrain.com/braincell/examples/calcium_channel_gating.html) — Compares low and high threshold calcium channel steady state gating.
- [Frequency Current Curve](https://brainx.chaobrain.com/braincell/examples/fi_curve.html) — Sweeps input current to measure firing rate excitability curves systematically.
- [Channel Ablation](https://brainx.chaobrain.com/braincell/examples/channel_ablation.html) — Ablates potassium current to reveal its repolarization role clearly visually.
- [Spike Frequency Adaptation](https://brainx.chaobrain.com/braincell/examples/spike_frequency_adaptation.html) — Demonstrates spike frequency adaptation through calcium activated potassium AHP currents.
- [T Current Rebound](https://brainx.chaobrain.com/braincell/examples/t_current_rebound.html) — Shows T type calcium current causing post inhibitory rebound bursting.
- [Integration Methods](https://brainx.chaobrain.com/braincell/examples/integration_methods.html) — Compares integration methods for Hodgkin Huxley membrane potential dynamics traces.
- [Thalamic Neurons](https://brainx.chaobrain.com/braincell/examples/thalamic_neurons.html) — Builds four thalamic neuron types and simulates electrophysiological responses patterns.
- [EI Network](https://brainx.chaobrain.com/braincell/examples/ei_network.html) — Builds excitatory inhibitory HH networks and analyzes spike dynamics collectively.

## BrainCell File Formats and IO
- [IO Overview](https://brainx.chaobrain.com/braincell/file_formats/overview.html) — Overviews morphology loading, validation reports, reader options, and checkpoints workflow.
- [SWC](https://brainx.chaobrain.com/braincell/file_formats/swc.html) — Explains SWC morphology loading, structure identifiers, and validation options settings.
- [Neurolucida ASC](https://brainx.chaobrain.com/braincell/file_formats/asc.html) — Loads Neurolucida ASC files with metadata, spines, and reports validation.
- [NeuroML2](https://brainx.chaobrain.com/braincell/file_formats/neuroml2.html) — Imports NeuroML2 morphologies while requiring mechanisms be declared separately later.
- [NeuroMorpho Org](https://brainx.chaobrain.com/braincell/file_formats/neuromorpho.html) — Searches, downloads, caches, and parses NeuroMorpho reconstructed neurons into morphologies.
- [Checkpointing](https://brainx.chaobrain.com/braincell/file_formats/checkpointing.html) — Saves and reloads processed morphologies and branches as checkpoints files.


## BrainState Getting Started
- [Thinking in BrainState](https://brainx.chaobrain.com/brainstate/getting_started/thinking_in_brainstate.html) — Introduces State, Module, Transform mindset for writing stateful BrainState programs.

## BrainState Core Tutorials
- [State Management](https://brainx.chaobrain.com/brainstate/tutorials/core/01_state_and_pytrees.html) — Explains state objects, PyTrees, tracing, splitting, and value updates safely.
- [Module System Protocol](https://brainx.chaobrain.com/brainstate/tutorials/core/02_modules_and_graph.html) — Introduces Modules, graph nodes, state traversal, and composition protocols patterns.
- [Basic Neural Network Layers](https://brainx.chaobrain.com/brainstate/tutorials/core/03_common_layers.html) — Builds neural networks with linear, convolution, pooling, and containers layers.
- [Activation Functions and Normalization](https://brainx.chaobrain.com/brainstate/tutorials/core/04_activations_and_normalization.html) — Covers activation functions, normalization layers, and feature standardization module usage.
- [Parameters Transforms and Regularization](https://brainx.chaobrain.com/brainstate/tutorials/core/05_parameters_transforms_regularization.html) — Explains parameters, constraints, transformations, and regularization for learnable models training.
- [Transformations Essentials](https://brainx.chaobrain.com/brainstate/tutorials/core/06_transformations_essentials.html) — Teaches state aware transforms for JIT, gradients, mapping, control flow.
- [Training and Metrics](https://brainx.chaobrain.com/brainstate/tutorials/core/07_training_and_metrics.html) — Shows training loops, optimizers, losses, metrics, and evaluation patterns workflows.
- [Random Number Generation](https://brainx.chaobrain.com/brainstate/tutorials/core/08_randomness.html) — Explains reproducible random keys, streams, seeding, and stochastic modules behavior.

## BrainState Transformation Tutorials
- [JIT and Compilation](https://brainx.chaobrain.com/brainstate/tutorials/transformations/01_jit_and_compilation.html) — Teaches stateful JIT compilation and BrainState versus JAX API choices.
- [Automatic Differentiation](https://brainx.chaobrain.com/brainstate/tutorials/transformations/02_autodiff.html) — Explains gradients over arguments, states, Jacobians, Hessians, and training loops.
- [Vectorization](https://brainx.chaobrain.com/brainstate/tutorials/transformations/03_vectorization.html) — Teaches vmap mapping axes, batched states, and vectorized module execution.
- [Advanced Batching and Parallelism](https://brainx.chaobrain.com/brainstate/tutorials/transformations/04_advanced_batching.html) — Covers nested batching, pmap, sharding, parallel mapping, and device execution.
- [Control Flow](https://brainx.chaobrain.com/brainstate/tutorials/transformations/05_control_flow.html) — Explains compiled conditionals, loops, scans, checkpointed scans, and state updates.
- [Error Handling and Runtime Checks](https://brainx.chaobrain.com/brainstate/tutorials/transformations/06_error_handling_and_checks.html) — Shows runtime checks, assertions, NaN debugging, and transformed error handling.
- [Debugging Transformed Code](https://brainx.chaobrain.com/brainstate/tutorials/transformations/07_debugging.html) — Teaches debugging transformed code with breakpoints, callbacks, and inspection tools.
- [IR Optimization and Code Generation](https://brainx.chaobrain.com/brainstate/tutorials/transformations/08_ir_optimization_and_codegen.html) — Introduces IR optimization concepts, lowering, code generation, and compilation inspection.

## BrainState Brain Dynamics Tutorials
- [Dynamics and Integration](https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/01_dynamics_and_integration.html) — Defines Dynamics modules, integration loops, update hooks, sizes, and delays.
- [Delay Protocol](https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/02_synaptic_delays.html) — Explains delay buffers, registration, interpolation, access patterns, and synaptic timing.
- [Event-Driven Operators](https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/03_event_driven_operators.html) — Introduces event-driven operators for sparse spiking communication and efficient updates.
- [Building a Spiking Neural Network](https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/04_building_an_snn.html) — Builds spiking neural networks with populations, synapses, monitors, and simulation.
- [Training a Spiking Neural Network](https://brainx.chaobrain.com/brainstate/tutorials/brain_dynamics/05_training_an_snn.html) — Trains spiking networks using surrogate gradients, losses, optimizers, and metrics.

## BrainState Deep Learning Examples
- [Image Classification with CNNs](https://brainx.chaobrain.com/brainstate/examples/deep_learning/image_classification.html) — Builds CNN image classifiers with datasets, training loops, evaluation, visualizations.
- [Variational Autoencoder on MNIST](https://brainx.chaobrain.com/brainstate/examples/deep_learning/variational_autoencoder.html) — Builds MNIST variational autoencoders with encoders, decoders, losses, latent visualizations.
- [Training Recurrent Neural Networks](https://brainx.chaobrain.com/brainstate/examples/deep_learning/integrator_rnn.html) — Trains recurrent networks on integrator tasks using sequences and metrics.

## Braintools Optimization
- [Optimization](https://brainx.chaobrain.com/braintools/optim/index.html) — Indexes optimizer tutorials, schedulers, Optax bridges, SciPy, and Nevergrad solvers.

## BrainState Brain Dynamics Examples
- [Hodgkin-Huxley Neuron Model](https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/hodgkin_huxley_neuron.html) — Implements Hodgkin Huxley neurons with channels, currents, integration, and plotting.
- [Simulating Spiking Neural Networks](https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_simulation.html) — Simulates spiking networks with LIF populations, connections, monitors, and dynamics.
- [Training Spiking Neural Networks](https://brainx.chaobrain.com/brainstate/examples/brain_dynamics/snn_training.html) — Trains spiking networks with surrogate gradients, classification data, and evaluation.
