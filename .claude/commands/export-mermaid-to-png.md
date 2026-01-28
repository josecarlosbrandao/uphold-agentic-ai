---
allowed-tools: Bash(npx:*)
description: Export mermaid diagram to PNG
disable-model-invocation: true
---

## Arguments

- `$FILE`: The `.mmd` file containing the mermaid diagram to be exported.
- `$RESULT`: The `.mmd` file containing the mermaid diagram to be exported.

## Your task

1. Check if $ARGUMENTS exists and is a `.mmd` file
2. Check if $RESULT contains `.png` extension. If not, make sure to add it so in the next step we ensure that output will be a PNG file
3. Export mermaid diagram to PNG format using the command below.
```bash
npx -p @mermaid-js/mermaid-cli mmdc -i $ARGUMENTS -o $RESULT -w 2400 -b white
```
