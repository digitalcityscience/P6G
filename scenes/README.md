# Scenes

Mitsuba XML exports from step 1 - this is what step 2 loads.

One folder per export, named for what's different about it, not just a
number:

```
scenes/
  <name>/
    <name>.xml
    meshes/
      *.ply
```

`.ply` and `.fbx` are tracked with Git LFS - see the repo's `.gitattributes`.
