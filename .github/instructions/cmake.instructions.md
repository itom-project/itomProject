---
applyTo: "**/CMakeLists.txt,**/*.cmake"
---

# CMake in itomProject

- Keep the existing macro/function style of the surrounding files; the shared helpers
  live in `itom/cmake`, `plugins/cmake` and `designerplugins/cmake`.
- Guard optional features with `option(...)` and keep the default consistent with the
  top-level `CMakeLists.txt`.
- Do not hard-code absolute paths, generator names or compiler flags for a single
  platform; itom is built on Windows and Linux. macOS is not officially supported, but
  try to implement code, such that it would be possible to run it under macOS.
- New source files must be added to the corresponding target's source list — the
  project does not use `file(GLOB)` for sources.
- Do not raise `cmake_minimum_required`, the C++ standard or the required Qt version
  without being explicitly asked.
