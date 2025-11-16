# Markdown lint instructions

Purpose
- Provide consistent, maintainable Markdown across this repository.
- Based on patterns and rules from: https://github.com/updownpress/markdown-lint/tree/master

Quick setup (project)
1. Add a linter as a dev dependency:
    - npm:
      npm install --save-dev markdownlint-cli
    - or run via npx without installing:
      npx markdownlint "**/*.md"

Configuration
- Place a JSON config at the repo root named `.markdownlint.json`.
- Example configuration (tweak rules to taste):
{
  "default": true,
  "MD013": false,
  "MD033": false,
  "MD041": true,
  "MD022": true,
  "MD004": {
     "style": "dash"
  },
  "MD007": {
     "indent": 2
  },
  "MD029": {
     "style": "ordered"
  },
  "MD026": false
}

Common rules to consider
- MD004: Unordered list style (dash vs asterisk)
- MD007: Unordered list indentation
- MD013: Line length — set to repo convention or disable
- MD022 / MD032: Headings should be surrounded by blank lines
- MD041: First line should be a top-level heading
- MD029: Ordered list item prefix
- MD033: Allow raw HTML if required (enable/disable)

Editor integration
- VS Code: Install "markdownlint" by David Anson (`marketplace: DavidAnson.vscode-markdownlint`).
- It will read `.markdownlint.json` automatically. Enable format on save or run fixes manually.

Running locally
- Lint all Markdown files:
  npx markdownlint "**/*.md"
- Lint a single file:
  npx markdownlint README.md

CI integration (GitHub Actions)
- Example workflow `.github/workflows/markdown-lint.yml`:
name: markdown-lint
on: [push, pull_request]
jobs:
  lint:
     runs-on: ubuntu-latest
     steps:
        - uses: actions/checkout@v4
        - name: Use Node
          uses: actions/setup-node@v4
          with:
             node-version: '18'
        - name: Install markdownlint
          run: npm ci --omit=dev || npm install --no-save markdownlint-cli
        - name: Run markdownlint
          run: npx markdownlint "**/*.md"

Notes & troubleshooting
- The CLI reports file:line:rule. Fix issues or adjust `.markdownlint.json`.
- If the repo uses a different linter or custom rule set (see reference), adapt commands/config accordingly.
- For auto-fix capabilities consider additional tools or editor extensions; markdownlint-cli itself focuses on reporting.

References
- Primary reference: https://github.com/updownpress/markdown-lint/tree/master
- markdownlint (core): https://github.com/DavidAnson/markdownlint