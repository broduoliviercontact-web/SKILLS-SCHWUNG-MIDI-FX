# Schwung Module Skills for Claude Code

A collection of Claude Code skills for building, packaging, and releasing **Schwung MIDI FX modules** for the Ableton Move.

---

## What are these skills?

Skills are structured prompts that guide Claude through a specific task. Each one encodes hard-won knowledge about the Schwung API, Move hardware, packaging pitfalls, and release workflow — so you don't have to re-explain the context each time.

---

## How to install

Copy this folder into your module repo's `.claude/commands/` directory:

```bash
cp -r sssskkil/schwung   your-module-repo/.claude/commands/schwung
cp -r sssskkil/templates your-module-repo/.claude/commands/templates
cp    sssskkil/CLAUDE.md  your-module-repo/.claude/CLAUDE.md
cp    sssskkil/MODULES.md your-module-repo/.claude/MODULES.md
cp    sssskkil/API.md     your-module-repo/.claude/API.md
```

Once in place, skills are available as `/schwung:<skill-name>` and `/templates:<skill-name>` in Claude Code.

---

## Workflow — from idea to release

Skills are designed to be used in order:

```
1. /schwung:audit-open-source-midi-fx        ← understand the source material
2. /schwung:design-schwung-midi-fx-module     ← design the module
3. /templates:create-module-json              ← generate module.json
4. /schwung:implement-native-midi-fx          ← build the DSP engine (C)
5. /templates:create-dsp-c                    ← generate dsp.c
6. /schwung:build-move-ui-and-controls        ← design the Move UI
7. /templates:create-ui-js                    ← generate ui.js (full screen, opened manually)
8. /templates:create-ui-chain-js              ← generate ui_chain.js (chain slot — PRIMARY surface)
9. /templates:add-move-pads                   ← add pad LED interaction
10. /schwung:validate-module-for-catalogue    ← audit before release
11. /schwung:package-external-module          ← package and release
```

You don't need all steps — a pure-JS module skips steps 4–5, a module without custom UI skips 6–9.

---

## Skills reference

### `/schwung:` — Main workflow skills

| Skill | When to use |
|-------|------------|
| `audit-open-source-midi-fx` | Analyse an open-source MIDI FX project for porting to Schwung |
| `convert-open-source-midi-fx-to-schwung` | Port an audited project into a Schwung module |
| `design-schwung-midi-fx-module` | Design a new module from scratch (params, UI, engine plan) |
| `implement-native-midi-fx` | Implement the C DSP engine |
| `build-move-ui-and-controls` | Design the Move-facing UI and controls |
| `validate-module-for-catalogue` | Full pre-release audit — catches all known packaging pitfalls |
| `package-external-module` | Package and release to GitHub with Schwung installer support |
| `repo-bootstrap` | Set up a new module repo from scratch |

### `/templates:` — Code generators

| Template | Generates |
|----------|-----------|
| `create-module-json` | `module.json` manifest |
| `create-dsp-c` | Native C MIDI FX engine (`dsp.c`) |
| `create-ui-js` | Full-screen Move UI (`ui.js`) |
| `create-ui-chain-js` | Chain slot UI (`ui_chain.js`) |
| `add-move-pads` | Pad LED interaction for any UI |

### Reference docs (loaded automatically by Claude)

| File | Content |
|------|---------|
| `CLAUDE.md` | Project rules, guardrails, things to avoid |
| `MODULES.md` | Full Schwung DSP plugin API reference |
| `API.md` | Move JS host API (display, MIDI, LEDs, host functions) |
| `ARCHITECTURE.md` | Schwung system architecture |
| `BUILDING.md` | Build and deploy instructions |

---

## Key rules (enforced by all skills)

**Never write to `/tmp/`** — root filesystem is read-only or full on Move. Always use `/data/UserData/`.

**State goes through `set_param`/`get_param` only** — there is no `save_state`/`load_state` in the API. The chain host calls `set_param` on restore.

**`ui_chain.js` is the primary interaction surface** — when a module sits in a Signal Chain slot, `ui_chain.js` loads, not `ui.js`. Most users never open the full-screen view. Design and implement `ui_chain.js` first. `ui.js` is a bonus, not the default.

**LED in `ui_chain.js` uses `sharedSetLED`** — `move_midi_internal_send` silently drops in chain context.

**Schwung installer expects `<id>-module.tar.gz`** — release must include the unversioned filename as an asset, not just the versioned one.

---

## Example usage

```
/schwung:design-schwung-midi-fx-module a euclidean rhythm generator with 3 lanes (kick/snare/hat), X/Y position map, randomness, and sync to Move transport

/templates:create-module-json euclidean drum sequencer with map_x, map_y, density per lane, randomness, steps, bpm, sync

/schwung:validate-module-for-catalogue src/module.json
```

---

## Reference implementations

- **Branchage** — full module with DSP engine, `ui.js`, `ui_chain.js`, pad X/Y glow, jog navigation
- **Grilles** — same pattern, two-page chain UI with pad glow and playhead display

Both repos use these skills and contain working examples of every pattern documented here.
