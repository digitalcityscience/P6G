# Step 2 - Sionna simulation

Simulation person's part (geometry pipeline person provides the scene data
and has overview).

`sionna_rt_simulation.ipynb`: load a scene from `../scenes/` (or a
Colab/Drive-mounted path - both supported, see its §0), set up
transmitter/receiver arrays, compute paths, CIR/CFR, and a radio map for one
configuration. Parameter sweeps (positions, counts, materials) aren't
implemented yet - see the notebook's closing note.

Don't commit every raw sweep output here - see the root README for why.
