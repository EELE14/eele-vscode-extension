# EELE — VS Code Syntax Highlighting Extension

This is a minimal, production-ready VS Code extension that adds **syntax highlighting** for your custom language **EELE** (files ending with `.eele`). It uses a **TextMate grammar** and follows the language design you provided (keywords, built-ins, comments, strings, numbers, regex, emojis, events, etc.).

> Quick start: Open this folder in VS Code and press **F5** to launch the Extension Development Host. Open any `*.eele` file to see highlighting.

---

## 📁 File structure

```
-eele-vscode-extension/
  ├─ package.json
  ├─ README.md
  ├─ CHANGELOG.md
  ├─ language-configuration.json
  ├─ syntaxes/
  │   └─ eele.tmLanguage.json
  ├─ snippets/
  │   └─ eele.code-snippets
  └─ .vscodeignore
```

---

## package.json

```json
{
  "name": "eele-syntax",
  "displayName": "EELE Language Support",
  "description": "Syntax highlighting for the EELE language (.eele)",
  "version": "0.1.0",
  "publisher": "your-name-or-org",
  "license": "MIT",
  "engines": {
    "vscode": "^1.80.0"
  },
  "categories": ["Programming Languages"],
  "contributes": {
    "languages": [
      {
        "id": "eele",
        "aliases": ["EELE", "eele"],
        "extensions": [".eele"],
        "configuration": "./language-configuration.json"
      }
    ],
    "grammars": [
      {
        "language": "eele",
        "scopeName": "source.eele",
        "path": "./syntaxes/eele.tmLanguage.json"
      }
    ],
    "snippets": [
      {
        "language": "eele",
        "path": "./snippets/eele.code-snippets"
      }
    ]
  }
}
```

---

## language-configuration.json

```json
{
  "comments": {
    "lineComment": "//",
    "blockComment": ["/*", "*/"]
  },
  "brackets": [
    ["{", "}"],
    ["[", "]"],
    ["(", ")"]
  ],
  "autoClosingPairs": [
    { "open": "{", "close": "}" },
    { "open": "[", "close": "]" },
    { "open": "(", "close": ")" },
    { "open": '"', "close": '"', "notIn": ["string"] },
    { "open": "'", "close": "'", "notIn": ["string"] }
  ],
  "surroundingPairs": [
    ["{", "}"],
    ["[", "]"],
    ["(", ")"],
    ['"', '"'],
    ["'", "'"]
  ],
  "folding": {
    "markers": {
      "start": "^\n?\s*//\s*#region",
      "end": "^\n?\s*//\s*#endregion"
    }
  }
}
```

---

## syntaxes/eele.tmLanguage.json

TextMate grammar (Oniguruma regex). **Case-insensitive** matching is achieved with `(?i)` in patterns.

```json
{
  "$schema": "https://raw.githubusercontent.com/martinring/tmlanguage/master/tmlanguage.json",
  "name": "EELE",
  "scopeName": "source.eele",
  "patterns": [
    { "include": "#comments" },
    { "include": "#strings-timespec" },
    { "include": "#strings" },
    { "include": "#regex" },
    { "include": "#numbers" },
    { "include": "#booleans-null" },
    { "include": "#discord-context" },
    { "include": "#keywords-declarations" },
    { "include": "#keywords-control" },
    { "include": "#events" },
    { "include": "#discord-builtins" },
    { "include": "#stdlib-builtins" },
    { "include": "#operators" },
    { "include": "#emoji" },
    { "include": "#identifiers" }
  ],
  "repository": {
    "comments": {
      "patterns": [
        { "name": "comment.line.double-slash.eele", "match": "//.*$" },
        { "name": "comment.block.eele", "begin": "/\\*", "end": "\\*/" }
      ]
    },

    "strings-timespec": {
      "patterns": [
        {
          "name": "constant.other.timespec.eele",
          "match": "\"(\\d+)(s|m|h|d)\""
        }
      ]
    },

    "strings": {
      "patterns": [
        {
          "name": "string.quoted.double.eele",
          "begin": "\"",
          "end": "\"",
          "patterns": [
            { "name": "constant.character.escape.eele", "match": "\\\\[\\\\\"'nrvtbf]" }
          ]
        }
      ]
    },

    "regex": {
      "patterns": [
        {
          "name": "string.regexp.eele",
          "begin": "/",
          "end": "/",
          "patterns": [
            { "match": "\\\\/", "name": "constant.character.escape.eele" },
            { "match": "\\\\.", "name": "constant.character.escape.eele" }
          ]
        }
      ]
    },

    "numbers": {
      "patterns": [
        { "name": "constant.numeric.eele", "match": "(?<![\\w.])(?:\\d+)(?:\\.\\d+)?(?![\\w.])" }
      ]
    },

    "booleans-null": {
      "patterns": [
        { "name": "constant.language.boolean.eele", "match": "(?i)\\b(TRUE|FALSE)\\b" },
        { "name": "constant.language.null.eele", "match": "(?i)\\bNULL\\b" }
      ]
    },

    "discord-context": {
      "patterns": [
        {
          "name": "variable.language.discord.eele",
          "match": "(?i)\\b(AUTHOR|CHANNEL|MESSAGE|GUILD|USER)\\b(?:\\.[A-Z_]+)*"
        }
      ]
    },

    "keywords-declarations": {
      "patterns": [
        { "name": "keyword.declaration.bot.eele", "match": "(?i)\\bBOT\\b" },
        { "name": "keyword.declaration.var.eele", "match": "(?i)\\b(LET|CONST)\\b" },
        { "name": "keyword.declaration.func.eele", "match": "(?i)\\bFUNC\\b" },
        { "name": "keyword.other.module.eele", "match": "(?i)\\b(IMPORT|INCLUDE|EXPORT)\\b" }
      ]
    },

    "keywords-control": {
      "patterns": [
        { "name": "keyword.control.conditional.eele", "match": "(?i)\\b(IF|ELSEIF|ELSE)\\b" },
        { "name": "keyword.control.loop.eele", "match": "(?i)\\b(WHILE|LOOP|FOR|IN|BREAK|CONTINUE)\\b" },
        { "name": "keyword.control.return.eele", "match": "(?i)\\bRETURN\\b" }
      ]
    },

    "events": {
      "patterns": [
        { "name": "support.type.event.eele", "match": "(?i)\\bON\\b" },
        { "name": "support.constant.event.eele", "match": "(?i)\\b(READY|MESSAGE|MATCH|REACTION_ADD|REACTION_REMOVE|MEMBER_JOIN|MEMBER_LEAVE|ERROR|SCHEDULE|EVERY|AT)\\b" }
      ]
    },

    "discord-builtins": {
      "patterns": [
        { "name": "support.function.discord.eele", "match": "(?i)\\b(SAY|REPLY|DM|REACT|WAIT|SET_STATUS|FETCH|SEND|PUSH|POP|APPEND|REMOVE|ADD_ROLE|REMOVE_ROLE|BAN|UNBAN|TIMEOUT)\\b" },
        { "name": "support.constant.fetch.eele", "match": "(?i)\\b(USER|CHANNEL|BY_ID|BY_NAME|AS|TO|WITH|ROLE|REASON|FOR)\\b" }
      ]
    },

    "stdlib-builtins": {
      "patterns": [
        { "name": "support.function.builtin.eele", "match": "(?i)\\b(LEN|NOW|RANDOM|RANDOM_INT|FORMAT_DATETIME|ENV|JSON_PARSE|JSON_STRINGIFY|PRINT|LOG|ASSERT)\\b" }
      ]
    },

    "operators": {
      "patterns": [
        { "name": "keyword.operator.logical.eele", "match": "(?i)\\b(AND|OR|NOT)\\b" },
        { "name": "keyword.operator.comparison.eele", "match": "==|!=|>=|<=|>|<" },
        { "name": "keyword.operator.arithmetic.eele", "match": "\\+|\\-|\\*|/|%" },
        { "name": "punctuation.section.braces.begin.eele", "match": "\\{" },
        { "name": "punctuation.section.braces.end.eele", "match": "\\}" },
        { "name": "punctuation.section.group.begin.eele", "match": "\\(" },
        { "name": "punctuation.section.group.end.eele", "match": "\\)" },
        { "name": "punctuation.definition.list.begin.eele", "match": "\\[" },
        { "name": "punctuation.definition.list.end.eele", "match": "\\]" },
        { "name": "punctuation.separator.comma.eele", "match": "," },
        { "name": "punctuation.separator.colon.eele", "match": ":" }
      ]
    },

    "emoji": {
      "patterns": [
        { "name": "constant.character.emoji.named.eele", "match": ":[A-Za-z0-9_+\-]+:" },
        { "name": "constant.character.emoji.unicode.eele", "match": "[\uD800-\uDBFF][\uDC00-\uDFFF]" }
      ]
    },

    "identifiers": {
      "patterns": [
        { "name": "entity.name.function.call.eele", "match": "\\b[A-Za-z_][A-Za-z0-9_]*\\s*(?=\\()" },
        { "name": "variable.other.readwrite.eele", "match": "\\b[A-Za-z_][A-Za-z0-9_]*\\b" }
      ]
    }
  }
}
```

---

## snippets/eele.code-snippets

A few handy snippets to speed up writing EELE scripts.

```json
{
  "onMessage": {
    "prefix": "onmsg",
    "body": [
      "ON MESSAGE \"$1\" {",
      "  $0",
      "}"
    ],
    "description": "ON MESSAGE event"
  },
  "ready": {
    "prefix": "ready",
    "body": [
      "ON READY {",
      "  SAY \"Bereit! 👋\"",
      "}"
    ],
    "description": "ON READY event"
  },
  "if": {
    "prefix": "if",
    "body": [
      "IF $1 {",
      "  $0",
      "} ELSE {",
      "  SAY \"...\"",
      "}"
    ],
    "description": "IF/ELSE"
  }
}
```

---

## README.md

````markdown
# EELE Language Support for VS Code

Syntax highlighting for the EELE language (`.eele`).

## Features
- Keywords, events, Discord built-ins, stdlib built-ins
- Strings, numbers, booleans, null
- Regex literals (`/pattern/`), emojis (`:name:` and unicode surrogates)
- Comments (`//` and `/* ... */`)
- Basic operators and punctuation

## Getting Started (Development)
1. Open this folder in VS Code.
2. Press **F5** to start the Extension Development Host.
3. Open any `*.eele` file and verify highlighting.

## Permanent Install (optional)
- Build a VSIX with `vsce package` and install via **Extensions > ... > Install from VSIX**.

## File Association
If VS Code doesn’t automatically pick up `.eele`, add this to your `settings.json`:
```json
"files.associations": { "*.eele": "eele" }
````

````

---

## CHANGELOG.md
```markdown
# Changelog

## 0.1.0
- Initial release with TextMate grammar for EELE (.eele)
````

---

## .vscodeignore

```text
**/.vscode/**
**/.git/**
**/.github/**
**/node_modules/**
**/*.vsix
```

---

## How to run it

1. Create a folder (e.g., `eele-vscode-extension`) and copy these files into it with the same structure.
2. Open the folder in VS Code.
3. Press **F5** to launch an Extension Development Host.
4. Create a file `example.eele` and paste:

   ```
   BOT "MeinBot"

   ON READY {
     SAY "Bereit! 👋"
   }

   ON MESSAGE "!ping" {
     SAY "pong!"
   }
   ```
5. You should see proper highlighting.

> Optional: Install globally by packaging with `vsce` (Node.js required).

