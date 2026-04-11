# graphify-novel

Write. Don't track.

As your story grows, so does the overhead. Where was this character last seen, did you already establish that rule, which threads are still open? ```graphify-novel``` handles all of that so you can stay in the prose.

Give it a premise and it scaffolds your story bible. Feed it a chapter and it checks for contradictions, flags unresolved setups, and updates every character and thread automatically. Ask it a question and it traces connections across your full manuscript.

This skill uses [graphify](https://github.com/safishamsi/graphify) under the hood to extract a knowledge graph from your chapters and bible, mapping relationships between characters, locations, events, and themes across the full manuscript. This is what powers cross-chapter queries, structural hub detection, and the implicit connection hints in review.

---

## Prerequisites

- [graphify](https://github.com/safishamsi/graphify) skill installed

- If not already, the coding assistant will help install it for you when running graphify-novel

---

## Installation

```bash
npx skills add Anshler/graphify-novel
```

---

## Project structure

```
<your-project>/
  chapters/          ← manuscript files (preferably .txt or .md)
  draft/             ← work-in-progress (excluded from graph)
  static/            ← static files, images, etc (excluded from graph)
  bible/
    premise.md
    timeline.md
    characters/
    threads/
    world/
  graphify-out/      ← generated knowledge graph
  .graphifyignore
```

You can start with an empty project. But if it's an existing project, make sure your finished chapters and static files (images) are in `chapters/` and in-progress drafts are in `draft/`.

If you already had an existing bible/story-tracking structure, it won't be compatible. Move them outside the project then retrofit them into the new structure after you initialize bible.

---

## Usage

Run command from within your project folder in your AI assistant chat.

### Initialize a bible from a premise (fresh start)
```
/graphify-novel init "A disgraced archivist discovers the empire's founding records were forged, and the proof is hidden in a vault only the guilty can open."

/graphify-novel init --from premise.txt
```

### Initialize bible from existing chapters

```
/graphify-novel init --from-chapters

/graphify-novel init --from-chapters --batch 3
```
Scaffolds `bible/`, populates characters, threads, world files, and builds the initial knowledge graph.

`--from-chapters` is for porting an existing story. It sweeps all files in `chapters/` in batches (default: 5) using sub-agents, each working in a fresh context so chapter count is not limited by the context window. Use `--batch N` to override the batch size.

### Review a draft for consistency
```
/graphify-novel review ch03.md
/graphify-novel review ch03.md --intent "establish that Elara knows more than she admits"
/graphify-novel review --passage
```

Flags contradictions, continuity gaps, and thread opportunities. Never writes to the bible, findings are proposals only.

When ussing `--passage` the AI will prompt you to paste it in chat.

### Commit updates after writing
```
/graphify-novel update ch03.md
/graphify-novel update --manual
/graphify-novel update --lore "The Binding Pact was signed 40 years before the story begins"
```

Ideally, use it after you already reviewed the draft.

In `--manual` mode, the AI will hold conversation with you to identify the change.

### Check story state before starting a chapter
```
/graphify-novel status
```
Shows open threads, character states, unresolved setups (Chekhov's guns), and structural hubs from the knowledge graph.

### Manage plot threads
```
/graphify-novel thread new "The Heir's Secret"
/graphify-novel thread resolve binding-pact
/graphify-novel thread list
```

### Query cross-chapter relationships
```
/graphify-novel query "how is Elara connected to the Obsidian Throne?"
/graphify-novel query "what do ch.3 and ch.8 have in common thematically?" --dfs
/graphify-novel path "Elara" "The Black Fort"
```

---

## How it works

`bible/` is the source of truth, structured state files that track what is currently true about each character, thread, and world element.

`graphify-out/` is the relationship layer, a knowledge graph extracted from your full manuscript and bible. It surfaces implicit connections and structural patterns that direct file reads won't catch.

Neither replaces the other. The bible answers "where is Elara now?"; the graph answers "how did Elara and the Obsidian Throne end up connected?"

---

## License

MIT
