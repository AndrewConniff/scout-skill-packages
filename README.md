## Scout skill export bundles

This repository packages two custom Scout skills as separate installable bundles:

- `pptx-module-library.zip`
- `mixed-module-deck-assembler.zip`

Each zip contains:

- the skill folder content under `m-skills/<skill-name>/`
- `skill-metadata.json` for that specific skill
- `DEPENDENCIES.md` listing runtime dependencies and integration requirements

### Install into another Scout instance

1. Extract the chosen zip.
2. Copy `m-skills/<skill-name>/` into `~/.scout/m-skills/`.
3. Merge the entry in `skill-metadata.json` into `~/.scout/m-skills/skills-metadata.json`.
4. Restart Scout.

