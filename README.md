## Scout skill export bundles

This repository packages two custom Scout skills as separate installable bundles:

- `pptx-module-library.zip`
- `mixed-module-deck-assembler.zip`

These skills are designed to work as a pair:

1. `/pptx-module-library` builds and indexes a reusable module library from course decks.
2. `/mixed-module-deck-assembler` selects indexed modules and assembles curated customer decks.

Each zip contains:

- skill content under `m-skills/<skill-name>/`
- `skill-metadata.json` for that specific skill
- `DEPENDENCIES.md` listing runtime dependencies and integration requirements

## Install into another Scout instance

1. Extract the chosen zip.
2. Copy `m-skills/<skill-name>/` into `~/.scout/m-skills/`.
3. Merge the entry in `skill-metadata.json` into `~/.scout/m-skills/skills-metadata.json`.
4. Restart Scout.

## How to use each skill

### `/pptx-module-library`

Use when you want to turn a folder of training/course `.pptx` files into a reusable module catalog.

Typical flow:

1. Run `/pptx-module-library`.
2. Confirm the main library root (recommended: `OneDrive\MTT Module Library`).
3. Provide a source folder containing course decks.
4. Let the skill index decks into course/module folders and manifests.
5. Repeat for additional courses; reindexing is supported by `course_id`.

Main outputs in the library root:

- `module-library.manifest.json` (global machine-readable index)
- `module-library.index.md` (human-readable module table)
- `assemble_deck.py` and `courses_runtime.py` (assembly runtime)
- per-course manifests and per-module manifests/scripts/assets

### `/mixed-module-deck-assembler`

Use when you already have an indexed module library and want a curated customer-ready deck.

Typical flow:

1. Run `/mixed-module-deck-assembler`.
2. Provide `module-library.manifest.json` (or the library root containing it).
3. Confirm deck title and max slides per deck (recommended: `50`).
4. Provide context (prompt/notes/audience) or explicit module IDs.
5. Confirm ordered module list, then assemble.

Outputs:

- numbered decks: `Custom-Name-01.pptx`, `Custom-Name-02.pptx`, ...
- per-part manifests: `Custom-Name-01.assembly.json`, ...
- aggregate manifest: `Custom-Name.assembly.json`

## How it works (deconstruct -> index -> construct)

### 1) Deconstruct and preserve

`/pptx-module-library` processes source course decks, detects module boundaries (prefer PowerPoint Sections), and preserves source fidelity by generating module artifacts plus Python reconstruction metadata.

### 2) Index for retrieval and ranking

The skill writes a global manifest and searchable metadata (`course_id`, module IDs, titles, descriptions, keywords, slide counts, title-slide styles). This index is what enables prompt-driven module selection later.

### 3) Construct curated mixed decks

`/mixed-module-deck-assembler` reads the manifest, chooses ordered modules (from explicit IDs or ranked context), and invokes `assemble_deck.py` to build one or more numbered decks.

The generated runtime uses PowerPoint COM (`pywin32`) with `Slides.InsertFromFile` contiguous ranges, applies optional indexed title-slide style, enforces max-slide limits at module boundaries, and splits into parts when needed.
