---
name: itom-dataobject
description: Rules and patterns for working with ito::DataObject (creation, typed access, ROI, continuity, conversion to/from numpy). Use whenever C++ code in itom reads, writes or allocates DataObject instances.
---

# Working with `ito::DataObject`

`ito::DataObject` is itom's n-dimensional array type (similar to `cv::Mat`, but with
n dimensions, per-plane storage and axis meta information).

## Rules

1. **Never assume a data type.** Dispatch on `obj.getType()` (`ito::tUInt8`,
   `ito::tInt16`, `ito::tFloat32`, `ito::tFloat64`, `ito::tComplex64`, `ito::tRGBA32`,
   ...) or use the existing templated helper/`switch` pattern of the surrounding file.
2. **Never assume continuity.** A `DataObject` stores its data plane-wise and may carry
   a ROI. Iterate plane by plane:
   - number of planes: `obj.getNumPlanes()`
   - plane access: `obj.getCvPlaneMat(planeIdx)` (respects the ROI)
   - use `obj.getContinuousDataPtr()` only after checking `obj.getContinuous()`.
3. **Respect the ROI.** `getSize(dim)` returns the ROI size, `getOriginalSize(dim)` the
   allocated size. Use `getSize()` for all algorithmic work.
4. **Shallow copy semantics.** Assignment and copy construction share the data.
   Use `copyTo(dst, true)` for a deep copy and `convertTo(dst, type)` for a type change.
5. **Meta data must be preserved** when an algorithm produces an output of the same
   geometry: `dst.copyAxisTagsTo(...)`, `dst.copyTagMapTo(...)`, or use
   `ito::dObjHelper::` helpers where available.
6. **Validate inputs** with the `ito::dObjHelper::verify*` helpers (dimensions, type,
   size) and return `ito::RetVal(ito::retError, 0, tr("...").toLatin1().data())` on
   failure instead of throwing.
7. For 2D access prefer `obj.at<_Tp>(row, col)` only in non-critical code; in hot loops
   take the row pointer from the plane `cv::Mat`.

## Python bindings

Bindings are in `itom/Qitom/python/pythonDataObject.cpp`.

- Follow the existing reference counting exactly (`Py_INCREF`/`Py_DECREF`,
  `Py_RETURN_NONE`).
- Report errors via `PyErr_SetString(PyExc_...)` / the `PythonCommon` helpers and return
  `NULL`; never let an `ito::RetVal` error be silently dropped.
- Every new method/getter needs an entry in the corresponding `PyMethodDef` /
  `PyGetSetDef` table **and** a doc string, plus documentation in `itom/docs`.

## Tests

Cover changes with GoogleTest cases in `itom/itom_unittests/unittest_dataobject`
(target `unittest_dataobject`), and Python level behaviour in `itom/python_unittests`.
