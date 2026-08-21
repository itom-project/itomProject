# Copilot instructions — itomProject

The tool-independent instructions for this repository live in
[`AGENTS.md`](../AGENTS.md). **Read and follow that file.**

Additional path-specific rules are provided in `.github/instructions/*.instructions.md`
and are applied automatically depending on the file you edit.

Quick reminders:

- `itomProject` is an umbrella repository; the code lives in the git submodules
  `itom`, `plugins`, `designerplugins` and `external`.
- Never edit generated files in the CMake binary directory.
- C++/Qt code, namespace `ito`, error handling via `ito::RetVal`, formatting via
  `.clang-format` (Microsoft style, 100 columns, 4 spaces).
- Files must end with a single newline and contain no trailing whitespace
  (`pre-commit`).
