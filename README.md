# Longdo Map — Claude Code Skill

A Claude Code skill that gives AI assistants full knowledge of the [Longdo Map API v3](https://map.longdo.com/docs3/) — Thailand's leading map platform.

When active, Claude understands how to:
- Initialize maps and configure layers
- Add markers, popups, shapes (polyline, polygon, circle)
- Use the Search API (suggest, reverse geocode, nearby POI)
- Handle map events
- Integrate with React, Vue, or vanilla JavaScript

---

## Install

### Personal (all your projects)
```bash
git clone https://github.com/YOUR_USERNAME/longdo-map-skill ~/.claude/skills/longdo-map
```

### Project-specific
```bash
git clone https://github.com/YOUR_USERNAME/longdo-map-skill .claude/skills/longdo-map
```

That's it. Claude Code picks up skills automatically — no restart needed.

---

## Usage

The skill activates automatically when you mention Longdo Map, `longdo.Map`, `แผนที่ longdo`, or similar terms in your message.

You can also invoke it manually:
```
/longdo-map
```

**Example prompts that trigger this skill:**
- "เพิ่ม marker บน longdo map ยังไง"
- "How do I initialize a Longdo Map in React?"
- "I'm getting `longdo is not defined`, how do I fix it?"
- "Add a search box to my Longdo Map page"
- "Draw a route between two points on the map"

---

## Files

| File | Purpose |
|------|---------|
| `SKILL.md` | Main skill — quick reference with code examples |
| `api-reference.md` | Full API reference (all constructor options, event names, layer constants) |

---

## Requirements

- Claude Code CLI (`claude` command)
- A Longdo Map API key from [map.longdo.com](https://map.longdo.com/)

---

## Contributing

PRs welcome for:
- Additional framework integrations (Next.js, Nuxt, Svelte)
- More code examples
- Corrections to API details

---

## License

MIT
