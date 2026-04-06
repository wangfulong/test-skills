---
name: slide-workflow
description: End-to-end slide deck creation workflow. Processes user's raw materials into polished HTML presentations with AI-generated images. Orchestrates second-brain (workspace structure), slide (HTML generation), and image-workflow (illustrations). Use when user wants to create or iterate on slide presentations.
---

# Slide Workflow

End-to-end pipeline for creating and iterating slide decks. Orchestrates three sub-skills:

```
second-brain   →   slide   →   image-workflow (optional)
(structure)       (HTML)        (illustrations)
```

## Sub-Skills

- `rebyteai/second-brain` — Workspace structure convention (`raw/`, `output/`, `INDEX.md`)
- `rebyteai/slide` — HTML slide generation and iteration (aesthetics, CSS, data attributes)
- `rebyteai/image-workflow` — AI image generation (optional, for custom illustrations)

## Step 0: Ensure Sub-Skills Are Installed

```bash
ls ~/.skills/rebyteai-slide/SKILL.md ~/.skills/rebyteai-second-brain/SKILL.md 2>/dev/null
```

If missing, use `skill-installer` to install them. Do NOT proceed without the slide skill.

## Step 1: Understand & Organize (second-brain)

Follow the second-brain skill's INGEST pattern:

- If user uploaded files or provided references → save to `/code/raw/`
- Read all materials in `/code/raw/` to understand context
- Identify: presentation topic, target audience, key messages, desired tone

If user provides no raw materials (just a prompt), skip this step — go straight to slide generation.

## Step 2: Create or Update Decision

**BEFORE generating slides**, determine whether to create a new deck or update an existing one:

1. Read `/code/INDEX.md` (if exists)
2. Check `/code/slides/` for existing decks
3. **If an existing deck matches the topic → UPDATE that deck (reuse its slug)**
4. Else → CREATE new deck with a new slug

### Slug Rules

- kebab-case, derived from presentation title
- Examples: `ai-agent-intro`, `q4-review`, `product-launch-2026`
- **Stable across edits** — once a slug is created, never rename it
- Max 40 characters
- ASCII only (transliterate non-ASCII titles)

## Step 3: Generate or Edit Slides (slide skill)

### Create new deck

1. Use the slide skill to generate the HTML
2. Save to `/code/slides/{slug}/index.html`
3. Create the directory structure:
   ```
   /code/slides/{slug}/
     index.html        # The slide deck
     assets/            # Images, data files (if needed)
   ```
4. Output the same HTML as a `widget` code block in chat (for immediate preview)
5. Update `/code/INDEX.md`

### Update existing deck

1. Read `/code/slides/{slug}/index.html`
2. Locate the target `<section data-page="N">`
3. Edit specific pages using code editing tools (read → find section → edit → save)
4. **Preserve all `data-page` and `data-bp-id` attributes**
5. Output the updated HTML as a `widget` code block in chat
6. Update `/code/INDEX.md` (last updated date)

## Step 4: Illustrations (image-workflow, optional)

Only when slides need custom images (not stock photos or icons):

- Use the image-workflow skill to generate images
- Save generated images to `/code/slides/{slug}/assets/`
- Reference images in HTML with **relative paths**: `assets/image-name.png`
- Use descriptive filenames: `assets/market-growth-chart.png`, not `assets/img1.png`

## Step 5: Iterate

The core value loop — user gives feedback, agent refines:

1. User provides feedback (text instruction, or visual annotation from Slide Editor)
2. Agent reads `/code/slides/{slug}/index.html`
3. Edits specific pages using code editing tools
4. Can reference `/code/raw/` for additional context
5. Outputs updated `widget` in chat for preview
6. Updates `INDEX.md` after changes

Iteration should be **surgical**: edit only the pages the user mentioned. Never regenerate the entire deck unless explicitly asked.

## Step 6: Publish (output)

When user is satisfied and wants to share:

- **Deploy to URL**: `rebyte deploy` → shareable link
- **Download HTML**: user downloads the file directly
- **Fullscreen presentation**: open in new browser tab

Record published URLs or exported files in `/code/output/` if applicable.
