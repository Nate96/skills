---
name: vlt
description: A place for notes, resources, and journal
---

Located at `{dir path}`
Follow a bullet journal process. 

The `README.md` is where information on the journal is located

## Log Types
1. future log -> future_log.md
2. Monthly Log 
3. Weekly Log
4. Daily Log

All Journal entries are located in Journal/ dir

---

There are sqlite files in Vault. Utilize sqlite cli if need to look at data

## Helpful links 
1. https://sqlite.org/cli.html
2. https://bulletjournal.com/

---

## Commands

### save {Title}

Invoked as `/vlt save {Title}`. Saves the agent's previous response (the one before this
command) into today's journal entry. If `Title` is missing, ask for it with `ask_user`.

1. Resolve today's file: `Journal/YYYY-MM-DD Weekday.md` (e.g. `2026-10-09 Friday.md`).
   Create it if it does not exist.
2. Copy the previous response verbatim. Drop trailing "Next step" lines that only make
   sense in chat. Keep code blocks, lists, and links.
3. Build the section:

   ```markdown
   ## From Agent: {Title}

   {previous response}

   ---
   ```

4. Insert position:
   * If the file starts with YAML front matter (`---` on line 1 through the next `---`),
     insert directly below the closing `---`.
   * Otherwise insert at the top of the file.
   * Leave one blank line between the new section and existing content.
5. Enforce a `80` character line limit. Re-wrap prose; never break code blocks.
6. Verify by re-reading the top of the file.
7. Reply with one line: the file path and the heading added.
