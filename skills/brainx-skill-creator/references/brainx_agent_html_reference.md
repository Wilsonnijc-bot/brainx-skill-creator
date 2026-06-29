# BrainX Agent HTML Reference
Purpose: help an agent choose which BrainX documentation page to open next.
Usage: match the user request to a section and description, then click the page link.
Scope: BrainCell Concepts, BrainCell Modeling Tutorials, BrainCell Examples, BrainCell File Formats and IO, BrainState Getting Started, and BrainState Core Tutorials.
Total HTML pages: 42.
Each teaching description is exactly ten words.

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
