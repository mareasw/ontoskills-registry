# OntoStore

Official skill registry for [OntoSkills](https://ontoskills.sh). Contains pre-compiled `.ttl` packages with per-skill embedding files.

## Structure

```text
ontostore/
  index.json                    # Registry index
  embeddings/
    tokenizer.json              # HuggingFace tokenizer for embedding model
  packages/
    <author>/<package>/
      package.json              # Package manifest
      <skill>/
        ontoskill.ttl           # Compiled skill ontology
        intents.json            # Pre-computed 384-dim embeddings
```

Current packages: `anthropics`, `coinbase`, `coreyhaines31`, `kotlin`, `mareasw`, `n8n-io`, `nextlevel-builder`, `obra`, `pbakaus`, `remotion-dev`, `vercel-labs`.

## User Workflow

```bash
# Search for skills
ontoskills search code review

# Install (3 resolution levels)
ontoskills install obra                           # All packages from that author
ontoskills install obra/superpowers               # All skills in that package
ontoskills install obra/superpowers/test-driven-development  # Single skill

# Enable/disable
ontoskills enable obra/superpowers/test-driven-development
ontoskills disable obra/superpowers/systematic-debugging

# Source import (requires ontocore)
ontoskills import-source https://github.com/user/skill-repo
```

Source imports land in `~/.ontoskills/skills/author/<slug>` and are compiled to `~/.ontoskills/ontologies/author/<slug>`.

## Index Format

```json
{
  "packages": [
    {
      "package_id": "mareasw/greeting",
      "trust_tier": "verified",
      "source_kind": "ontology",
      "manifest_path": "packages/mareasw/greeting/package.json"
    }
  ]
}
```

## Package Manifest

```json
{
  "package_id": "obra/superpowers",
  "version": "1.0.0",
  "trust_tier": "verified",
  "skills": [
    {
      "id": "test-driven-development",
      "path": "test-driven-development/ontoskill.ttl",
      "intents": ["write tests first", "practice TDD"],
      "aliases": ["tdd"]
    }
  ],
  "embedding_files": [
    "test-driven-development/intents.json"
  ]
}
```

## Resolution Rules

- Canonical runtime identity: `author/package/skill`
- Short IDs accepted as lookup convenience
- Ambiguous short IDs resolve with precedence: `local > verified > trusted > community`

## Activation Rules

- Install unit: package
- Activation unit: skill
- Enabling a skill auto-enables its `dependsOnSkill` dependencies
- Skills are enabled by default on install
