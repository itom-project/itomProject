# AGENTS.md — itomProject

Guidance for AI coding agents (GitHub Copilot, Claude, Codex, Cursor, ...) working in
this repository. Keep answers and code changes consistent with the rules below.

## 1. What this repository is

`itomProject` is the **umbrella repository** of the *itom* measurement and automation
software (C++/Qt + embedded Python). It contains almost no code itself; the real code
lives in git **submodules**:

| Submodule          | Content                                                                |
| ------------------ | ---------------------------------------------------------------------- |
| `itom`             | Core: `DataObject`, `PointCloud`, `shape`, `common`, `commonQt`, `AddInManager`, `Qitom` (GUI + Python bindings), `itomWidgets`, `QPropertyEditor`, docs, unittests |
| `plugins`          | Hardware and algorithm plugins (`DummyGrabber`, `DummyMotor`, `BasicFilters`, ...) |
| `designerplugins`  | Qt Designer widgets / plot plugins (`itom1DQwtPlot`, `itom2DQwtPlot`, ...) |
| `external`         | Optional pre-built third party dependencies (opt-in via `ENABLE_ITOM_EXTERNAL`) |

Each submodule is an **independent git repository**. A change that belongs to a
submodule must be committed *inside* that submodule; the superproject only records the
updated submodule pointer.

Module specific rules live next to the code:

- [`itom/AGENTS.md`](itom/AGENTS.md)
- [`plugins/AGENTS.md`](plugins/AGENTS.md)
- [`designerplugins/AGENTS.md`](designerplugins/AGENTS.md)

**Read the matching module file before changing files in that module.**

## 2. Build

The project is built with **CMake** (out-of-source). Typical local layout:

```
D:\itom2026\sources\itomProject   # this repository
D:\itom2026\build\itomProject     # binary directory (generated, never edit by hand)
```

Relevant top-level options (see `CMakeLists.txt`):

| Option                        | Default | Meaning                          |
| ----------------------------- | ------- | -------------------------------- |
| `BUILD_ITOM_CORE`             | ON      | core library + `qitom` executable |
| `BUILD_ITOM_DESIGNERPLUGINS`  | ON      | designer/plot plugins            |
| `BUILD_ITOM_PLUGINS`          | ON      | hardware/algorithm plugins       |
| `BUILD_ITOM_PRIVATEPLUGINS`   | OFF     | optional private plugin tree     |
| `ENABLE_ITOM_EXTERNAL`        | OFF     | use the `external` submodule     |

Configure/build (adapt generator and paths):

```powershell
cmake -S D:\itom2026\sources\itomProject -B D:\itom2026\build\itomProject
cmake --build D:\itom2026\build\itomProject --config Debug
```

Rules for agents:

- **Never** edit anything below the build directory (`CMakeCache.txt`, `*.vcxproj`,
  generated `moc_*`/`ui_*`/`qrc_*` files). Change the `CMakeLists.txt` / `*.ui` /
  `*.qrc` sources instead.
- Prefer building a **single target/submodule** over a full solution build; a full
  build of itom is very expensive.
- Do not add new third party dependencies without being asked.

## 3. Coding conventions

- **C++ with Qt.** Follow the surrounding style of the file you edit.
- Formatting is defined per submodule by `.clang-format`
  (`BasedOnStyle: Microsoft`, 4 spaces, `ColumnLimit: 100`, `PointerAlignment: Left`,
  no tabs). Only format the lines you actually touch — do **not** reformat whole files.
- Core types live in namespace `ito` (`ito::DataObject`, `ito::RetVal`, `ito::AddInBase`,
  `ito::Param`, ...).
- Error handling uses `ito::RetVal` (`ito::retOk`, `ito::retWarning`, `ito::retError`)
  instead of exceptions. Accumulate with `retval += ...` and check with
  `retval.containsError()`. Exceptions must not cross plugin/DLL boundaries.
- Public headers are exported via the module's `*_EXPORT` macro.
- Keep the code C++ standard and Qt version compatible with what the CMake files
  already require; do not silently raise them.
- Comments and identifiers are **English**. Doxygen-style comments (`//!`, `\brief`)
  where the file already uses them.

## 4. Python side

- itom embeds CPython. Python bindings for the core live in `itom/Qitom/python/`
  (e.g. `pythonDataObject.cpp`, `pythonPlugins.cpp`).
- When adding/changing a binding, also update the documentation in `itom/docs/` and, if
  applicable, the stub/`itom-packages` files.
- Python code in the repository targets Python 3.6+ syntax (`pyupgrade --py36-plus`).

## 5. Quality gates

`pre-commit` is configured (`.pre-commit-config.yaml`, also in each submodule):

- `check-yaml`, `end-of-file-fixer`, `trailing-whitespace`, `fix-byte-order-marker`
- `codespell` (ignore list: `.codespell_ignore_words.txt`)
- `pyupgrade --py36-plus`
- `sphinx-lint`

So: **files end with exactly one newline, no trailing whitespace, no BOM.**

Unit tests (GoogleTest) are in `itom/itom_unittests` and produce executables such as
`unittest_dataobject`, `unittest_commonlib`, `unittest_addinmanager`. Python tests are in
`itom/python_unittests`. Run the tests that cover the code you changed.

## 6. Working agreement for agents

1. Determine which submodule is affected and read its `AGENTS.md`.
2. Make the **minimal** change that solves the problem; no drive-by refactoring.
3. Preserve the existing public API of `itom` — plugins outside this repository depend
   on it. Deprecate instead of removing. If it is required to change the public API,
1. adapt the version number of the API. This is contained in `addInInterfaceVersion.h` (default API)
   and `designerPluginInterfaceVersion.h` (API subset for designer plugins only). These version
   files are relevant for checking if a plugin can be loaded with the current instance of itom.
   The compatibility check between plugins and core itom is only done by this API version,
   independent on the versions of the different libraries in the API. This means, if at least
   one library (like `dataObject`), that is exposed to the API, has a major version jump, the
   API major version must also be increased.
4. Never commit changes in a submodule and the superproject pointer in one step without
   saying so explicitly.
5. Do not touch license headers, `COPYING.txt` or `LGPL_EXCEPTION.txt`
   unless asked.
6. State clearly when you are unsure — do not invent APIs.

## 7. Used 3rd party libraries

itom or its plugins are using different 3rd party libraries. If a library from the following list
is used, make sure that the code is compatible to the indicated version range:

* CMake, 3.12 - 4.4, if possible even newer versions
* Qt, 5.11 - 6.11, Qt6 is preferred
* OpenCV, version 3.0 or higher (up to 5.x)
* Python, version 3.6 or higher (3.11 or higher preferred)
* Numpy, version 1.x and 2.x
* PointCloudLibrary, 1.5 - 1.15

It is allowed to use compiler pre-processors to exclude features if a specific, supported, version of the
3rd party library does not support this feature.

For Qt5, the minimum C++ compiler is C++11. For Qt6, the minimum C++ compiler is C++17.


