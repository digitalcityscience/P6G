# Step 3 - Visualization

Shared.

Turns step 2 results (radio maps, coverage/path data) into figures for
presenting the work - plots, rendered scene images, whatever ends up in the
presentation.

**Status: nothing here yet, on purpose.** Step 2
(`sionna_rt_simulation.ipynb`) only just landed, hasn't been run against a
real scene yet, and doesn't have a sweep - there's no real result data to
build a visualization workflow against without guessing at shapes/formats.
Once step 2 is producing actual saved results, a reasonable starting point
is copying its rendering/plotting cells (scene render, CFR plot, radio map
render) here and specializing them for presentation-quality output - loading
*saved* results rather than recomputing the simulation each time, since this
step is about polishing outputs, not running them.
