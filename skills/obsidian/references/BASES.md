# Obsidian Bases (`.base`) Reference

Use Bases to replace hand-maintained index/MOC notes and static source tables with **live, auto-updating views** driven by the frontmatter the workflow already writes (`status`, `tags`, `sources`, `created`, `updated`). A `.base` index never goes stale, which directly improves vault recall.

Adapted from the Obsidian Bases documentation (https://help.obsidian.md/bases/syntax).

## When to generate a base

- One base per domain folder under `20_Permanent/`, or one per source cluster, as a dynamic index instead of (or alongside) a static MOC.
- A "recently updated" or "needs review" base over `status` to surface maintenance work.
- A source-coverage base that lists notes by `sources` to complement `SOURCE-REGISTER.md`.
- An opinion timeline over `40_Opinions/` that lists user opinions by `subject` and `expressed` date, filtered to `status == "current"` or grouped to show how a stance evolved (see the example below).

Place index bases under `00_Meta/` or the relevant domain folder. Embed them in a human-facing MOC note with `![[Domain Index.base]]`.

## File format

A `.base` file is valid YAML with these top-level keys (all optional except `views`):

```yaml
# Global filters apply to every view
filters:
  and:
    - 'status != "archived"'
    - file.hasTag("permanent")

# Computed properties usable in any view
formulas:
  age_days: '(now() - file.ctime).days'
  source_count: 'sources.length'

# Display-name overrides
properties:
  status:
    displayName: Status
  formula.age_days:
    displayName: "Age (days)"

# One or more views
views:
  - type: table          # table | cards | list | map
    name: "All Permanent Notes"
    limit: 50             # optional
    groupBy:              # optional
      property: status
      direction: ASC
    filters:              # optional, view-specific (same syntax as global)
      and:
        - 'status == "evergreen"'
    order:                # columns / fields to show, in order
      - file.name
      - status
      - tags
      - formula.age_days
    summaries:            # optional aggregates
      formula.age_days: Average
```

## Filters

A filter is either a single quoted expression or an object with exactly one of `and`, `or`, `not` containing a list (which may nest):

```yaml
filters:
  or:
    - file.hasTag("paper")
    - and:
        - file.hasTag("code")
        - 'status == "evergreen"'
    - not:
        - file.inFolder("10_Fleeting")
```

Operators: `==`, `!=`, `>`, `<`, `>=`, `<=`, `&&`, `||`, `!`.

## Property sources

- **Note properties** (frontmatter): `status`, `note.author`, or bare `author`.
- **File properties**: `file.name`, `file.basename`, `file.path`, `file.folder`, `file.ext`, `file.size`, `file.ctime`, `file.mtime`, `file.tags`, `file.links`, `file.backlinks`, `file.embeds`.
- **Formula properties**: `formula.<name>`, defined in `formulas`.

Helper methods on `file`: `file.hasTag("x")`, `file.inFolder("x")`, `file.hasLink("Note")`.

## Formulas

```yaml
formulas:
  has_grounding: 'if(sources, "✅", "⚠️ none")'
  age_days: '(now() - file.ctime).days'
  stale: 'if((now() - file.mtime).days > 180, "review", "")'
```

Key functions: `date(str)`, `now()`, `today()`, `if(cond, a, b?)`, `duration(str)`, `link(path, display?)`, `file(path)`, `min()`, `max()`, `number()`.

**Duration pitfall:** subtracting two dates yields a Duration, not a number. Access a numeric field (`.days`, `.hours`, ...) before applying `.round()` etc.

```yaml
"(now() - file.ctime).days.round(0)"   # CORRECT
"(now() - file.ctime).round(0)"        # WRONG — Duration has no .round()
```

**Null guard:** properties may be absent. Wrap in `if()`:

```yaml
'if(due, (date(due) - today()).days, "")'   # safe
'(date(due) - today()).days'                 # crashes when due is empty
```

## Default summaries

`Average`, `Min`, `Max`, `Sum`, `Range`, `Median`, `Stddev`, `Earliest`, `Latest`, `Checked`, `Unchecked`, `Empty`, `Filled`, `Unique`.

## YAML quoting rules

- Wrap a formula that contains double quotes in single quotes: `'if(done, "Yes", "No")'`.
- Quote any display string containing `:`, `{`, `}`, `[`, `]`, `,`, `&`, `*`, `#`, `?`, `|`, `<`, `>`, `=`, `!`, `%`, `@`, `` ` ``.
- Every `formula.X` referenced in `order`/`properties` must be defined in `formulas`.

## Example: permanent-note index

```yaml
filters:
  and:
    - file.inFolder("20_Permanent")
    - 'file.ext == "md"'

formulas:
  grounded: 'if(sources, "✅", "⚠️")'
  age_days: '(now() - file.ctime).days'

properties:
  formula.grounded:
    displayName: "Grounded"
  formula.age_days:
    displayName: "Age (days)"

views:
  - type: table
    name: "Permanent Notes"
    groupBy:
      property: status
      direction: ASC
    order:
      - file.name
      - status
      - tags
      - formula.grounded
      - updated
    summaries:
      formula.age_days: Average

  - type: table
    name: "Ungrounded / Needs Review"
    filters:
      not:
        - 'sources'
    order:
      - file.name
      - status
      - updated
```

## Example: opinion timeline

Lists user opinions newest-first, with a second view for only the currently held stances. Assumes the [OPINION-FORMAT.md](../OPINION-FORMAT.md) frontmatter (`type: opinion`, `subject`, `expressed`, `polarity`, `status`).

```yaml
filters:
  and:
    - file.inFolder("40_Opinions")
    - 'type == "opinion"'

views:
  - type: table
    name: "Opinion Timeline"
    order:
      - expressed
      - subject
      - polarity
      - status
      - file.name

  - type: table
    name: "Currently Held"
    filters:
      and:
        - 'status == "current"'
    groupBy:
      property: subject
      direction: ASC
    order:
      - subject
      - stance
      - polarity
      - expressed
```

## Validation

After writing a `.base`: confirm it is valid YAML, every referenced property/formula exists, Duration math accesses a field before rounding, and (when a vault is live) the view renders via the Obsidian CLI — see OBSIDIAN-CLI.md.

## References

- https://help.obsidian.md/bases/syntax
- https://help.obsidian.md/bases/functions
- https://help.obsidian.md/bases/views
