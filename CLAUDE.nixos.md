# Agent Instructions for [Jason Kuan/Senior Engineer]

## 👤 User Profile
- **Role:** Senior Software Engineer (Taiwan-based).
- **Environment:** NixOS (Immutable infrastructure), Neovim (Keyboard-centric workflow).
- **Preference:** High-precision, idiomatic code, and minimal bloat.

## 🛠 Tech Stack & Environment Rules
- **NixOS First:** Never suggest `sudo apt-get` or `pip install` globally. Always provide a `nix-shell` snippet or a Flake output.
- **Neovim Integration:** When discussing IDE features, focus on LSP servers, Lua configuration, and terminal-based workflows.
- **Pure Functions:** Prefer functional programming patterns and immutable data structures when applicable.
- **Package Management:** Use `bun` for JavaScript/TypeScript dependencies within this project.
- **Configuration Management:** Use `.json` or `.jsonc` for configuration files. Be mindful of JSON syntax (no trailing commas unless explicitly supported by the parser).

## 💻 Coding Standards
- **DRY & KISS:** Prioritize readability and maintainability. Avoid over-engineering.
- **Type Safety:** Always include types/interfaces (Rust, Go, C++, etc.) without being asked. For JavaScript, use JSDoc annotations for type checking where feasible.
- **Documentation:** Use JSDoc, Rustdoc, or similar standards for public APIs. Ensure all exported functions have clear descriptions.
- **Error Handling:**
    - Use specific error types where possible.
    - Handle async errors gracefully with `try/catch`.
    - Log errors with context to aid debugging.
- **Imports:**
    - Use ESM (`import`/`export`) syntax for JavaScript.
    - Group imports by type (Node.js built-ins, external libraries, internal modules).
    - Avoid circular dependencies.

## 🛠 Project Workflow
- **Standard Tooling:** Every project strictly includes `flake.nix` for environment orchestration and a `justfile` for task automation.
- **Workflow Integration:**
    - When adding dependencies, suggest the corresponding Nix flake input or attribute.
    - When suggesting commands (build, test, deploy), encapsulate them into `justfile` targets.
    - Assume the use of `nix develop` or `direnv` to enter the development shell.
- **Version Control:**
    - Commit messages should follow conventional commits (e.g., `feat:`, `fix:`, `chore:`).
    - **Security:** Do not commit sensitive files (`.env`, `.credentials.json`).
    - Use `.gitignore` to enforce exclusion of sensitive files.

## 🚀 Language-Specific Rules

### JavaScript / TypeScript
- **Style:**
    - Indentation: 2 spaces.
    - Semicolons: Always use semicolons.
    - Quotes: Single quotes for strings, double quotes for JSON.
    - Trailing Commas: Avoid in JSON, use in JS multiline arrays/objects (ES5+).
- **Structure:**
    - Plugins must export a named constant matching the purpose (PascalCase).
    - Use `async/await` for asynchronous operations.
    - Prefer `const` over `let`, avoid `var`.
- **Testing:**
    - Currently no automated tests. Implement unit tests using `bun test` if adding complex logic.

### Golang
- Follow standard project layouts. Use `Context` for concurrency control. Ensure rigorous error handling and idiomatic code (as per `effective go`).

### Python
- Prefer modern syntax (3.10+). Strict use of Type Hints is required. Address dependency management via Nix-compatible tools like `poetry2nix` or `mach-nix`.

### C++
- Focus on Modern C++ (C++17/20). Emphasize RAII and memory safety. Provide `CMakeLists.txt` or Nix derivations for build configurations.

### Bash
- Write robust scripts with `set -euo pipefail`. Prioritize readability and use `shellcheck`-compliant patterns.

## 💬 Communication Style
- **Concise:** No fluff. Get straight to the technical solution.
- **Technical Depth:** Assume I understand OS-level concepts (kernel, systemd, nix-store).
- **Language:** Default to Traditional Chinese (Taiwan) for explanations, but keep technical terms in English.

## 🚫 Constraints
- Avoid bloated frameworks if a lightweight alternative exists.
- Do not suggest GUI-based tools unless they are strictly necessary.
- If a task involves system configuration, assume it must be managed via `/etc/nixos/` or Home Manager.
- **Security:** NEVER commit secrets. Always check `.gitignore` and use environment variables or ignored config files.

## 📂 Repository Structure & Context
- **Root:** `~/.claude`
- **Configuration:** `CLAUDE.md` (global instructions), `settings.json`, `keybindings.json`
- **Commands:** `commands/` contains Markdown files defining custom slash commands.
- **Credentials:** `.credentials.json` stores authentication tokens. *Do not commit.*

## 🔍 Build & Test Instructions
- **Build:** No build step required (configuration files loaded at runtime).
- **Lint:** Use `bun` ecosystem tools if available (e.g., `biome` or `eslint`). Currently none configured.
- **Test:** Manual verification. Ensure `settings.json` and `keybindings.json` are valid JSON.

## 📝 Code Style Guidelines (Detailed)
1.  **Formatting:**
    - Use 2 spaces for indentation.
    - Max line length 100 characters where possible.
    - Use blank lines to separate logical blocks.
2.  **Naming:**
    - Variables/Functions: `camelCase`.
    - Classes/Components: `PascalCase`.
    - Constants: `UPPER_SNAKE_CASE`.
    - Filenames: `kebab-case.js` (or match export name if plugin convention requires).
3.  **Comments:**
    - Explain *why*, not *what*.
    - Use JSDoc for function signatures.
    - Keep comments up-to-date with code changes.
4.  **Imports:**
    - Sort imports: Built-in modules first, then external packages, then internal modules.
