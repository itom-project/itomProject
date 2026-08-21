---
applyTo: "itom/**"
---

# itom core

You are editing the **itom core** submodule (independent git repository).
The authoritative rules are in [`itom/AGENTS.md`](../../itom/AGENTS.md) — read it before
making changes.

Key points:

- Layers: `DataObject` / `PointCloud` / `shape` (data), `common` + `commonQt`
  (helpers, `RetVal`, `Param`, `AddInInterface`), `AddInManager` (plugin loading),
  `Qitom` (application, GUI, embedded Python), `itomWidgets` + `QPropertyEditor` (UI).
- `itom/Qitom/python/*` implements the CPython bindings — follow the existing
  reference-counting and `PyErr_*` patterns exactly.
- The public API is consumed by out-of-tree plugins: do not break it, deprecate instead.
- Unit tests: `itom/itom_unittests` (GoogleTest), Python tests: `itom/python_unittests`.
