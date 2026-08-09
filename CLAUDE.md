# AI Development Guidelines for itom Project

> **Note:** This file (CLAUDE.md) is automatically recognized by GitHub Copilot and other AI assistants.  
> It defines requirements, constraints, and best practices for AI-assisted development on the itom project.

---

## 1. Project Overview

**Project:** itom - Software Suite for Optical Metrology  
**Repository:** https://github.com/itom-project/itomProject  
**License:** LGPL v2+ with ITO LGPL Exception  
**Primary Language:** C++ (Qt-based) with Python integration  
**Build System:** CMake

---

## 2. Compatibility Requirements

### 2.1 Qt Framework

**Required Compatibility Range:** Qt 5.6 - 6.11

**Important Considerations:**
- Use `#if QT_VERSION >= QT_VERSION_CHECK(x, y, z)` for version-specific code
- Avoid Qt 6-only APIs without fallback for Qt 5
- Test code paths for both Qt 5 and Qt 6 when possible
- Prefer APIs available in both versions

**Common Qt Version Checks:**
```cpp
#if QT_VERSION >= QT_VERSION_CHECK(5, 10, 0)
	// Qt 5.10+ or Qt 6.x feature
#else
	// Fallback for Qt 5.6-5.9
#endif

#if QT_VERSION >= QT_VERSION_CHECK(6, 0, 0)
	// Qt 6 only feature
#else
	// Qt 5 feature
#endif
```

**Deprecated Qt 5 APIs to avoid:**
- `QString::null` → use `QString()`
- `QRegExp` → prefer `QRegularExpression` (available since Qt 5.0)
- `QLinkedList` → use `QList` or `std::list`

### 2.2 Python

**Required Compatibility Range:** Python 3.6 - 3.13

**Important Considerations:**
- Use Python C API compatible with Python 3.6+
- Avoid f-strings in documentation examples (use `.format()` or `%` for 3.6 compatibility)
- Check for Python version-specific features:

**Python Version Checks:**
```cpp
// In C++ code using Python C API
#if PY_VERSION_HEX >= 0x03070000
	// Python 3.7+
#endif

#if PY_VERSION_HEX >= 0x030B0000
	// Python 3.11+
#endif
```

```python
# In Python code
import sys

if sys.version_info >= (3, 7):
	# Python 3.7+ feature
else:
	# Fallback for 3.6
```

**Python 3.6 Compatibility Notes:**
- No f-strings (use `.format()`)
- No `asyncio.run()` (use `loop.run_until_complete()`)
- Type hints available but more limited

**Python 3.7+ Features to guard:**
- `asyncio.run()`
- `dataclasses` (use fallback or backport)
- `importlib.resources` (use `pkg_resources` for 3.6)

**Python 3.8+ Features to guard:**
- Walrus operator `:=`
- Positional-only parameters `/`

### 2.3 OpenCV

**Required Compatibility Range:** OpenCV 3.x - 5.x

**Important Considerations:**
- Use `CV_VERSION_MAJOR` macro for version checks
- OpenCV 5 removed C API - use compatibility macros in `itom/DataObject/dataobj.h`
- `cv::Exception` constructor changed in OpenCV 5 - use `ITOM_CV_EXCEPTION` macro

**OpenCV Version Checks:**
```cpp
#if CV_VERSION_MAJOR >= 5
	// OpenCV 5.x specific code
#elif CV_VERSION_MAJOR >= 4
	// OpenCV 4.x specific code
#else
	// OpenCV 3.x specific code
#endif
```

**Compatibility Macros (already defined in itom):**
```cpp
// Use ITOM_CV_EXCEPTION instead of cv::Exception constructor
throw ITOM_CV_EXCEPTION(code, msg, func, __FILE__, __LINE__);

// cv::error() is compatible across versions when using cv::Error::Code enum
cv::error(cv::Error::StsAssert, "message", "", __FILE__, __LINE__);
```

**OpenCV API Changes to be aware of:**
- OpenCV 5: `cv::Exception` requires `cv::Error::Code` enum
- OpenCV 5: Removed C API macros (`CV_*` → `cv::Error::*`)
- OpenCV 4: Removed `cv::flann` deprecated functions
- OpenCV 3: Some legacy APIs still available

---

## 3. Documentation Standards

### 3.1 Language

**Rule:** All Markdown documentation files MUST be written in English.

**Applies to:**
- `*.md` files (README, guides, API docs, etc.)
- Code comments intended for documentation generation
- Commit messages for documentation changes

**Exceptions:**
- User-facing UI strings (can be translated)
- Inline code comments (can be in German if legacy)
- Issues/discussions (contributor's choice)

**Rationale:** English is the lingua franca for international collaboration and AI processing.

### 3.2 Markdown Style

**File Structure:**
```markdown
# Title (H1 - only one per document)

Brief introduction paragraph.

## Section (H2)

Content...

### Subsection (H3)

Content...
```

**Code Blocks:**
- Always specify language: \`\`\`cpp, \`\`\`python, \`\`\`bash
- Include file paths in comments when relevant
- Keep examples concise and runnable

**Links:**
- Use relative links for internal documentation
- Use absolute URLs for external resources
- Check that links are valid

### 3.3 Code Documentation

**C++ Comments:**
```cpp
/**
 * @brief Brief description (one line)
 * 
 * Detailed description if needed.
 * 
 * @param paramName Description of parameter
 * @return Description of return value
 * @throws cv::Exception if error occurs
 * @sa RelatedFunction, RelatedClass
 */
```

**Python Docstrings:**
```python
def function_name(param1, param2):
	"""Brief description.

	Longer description if needed.

	Args:
		param1: Description
		param2: Description

	Returns:
		Description of return value

	Raises:
		ValueError: Description of when this is raised
	"""
```

---

## 4. Coding Standards

### 4.1 C++ Style

**Naming Conventions:**
- Classes: `PascalCase` (e.g., `DataObject`, `PythonEngine`)
- Methods: `camelCase` (e.g., `getData()`, `setPixel()`)
- Member variables: `m_camelCase` (e.g., `m_size`, `m_pData`)
- Constants: `UPPER_CASE` or `kPascalCase`
- Namespaces: `lowercase` (e.g., `ito`)

**Header Guards:**
```cpp
#ifndef MODULENAME_H
#define MODULENAME_H

// content

#endif // MODULENAME_H
```

**Include Order:**
1. Corresponding header (for .cpp files)
2. Project headers
3. Qt headers
4. System headers
5. Third-party library headers

**Example:**
```cpp
#include "myclass.h"

#include "../common/typeDefs.h"
#include "DataObject/dataobj.h"

#include <QObject>
#include <QString>

#include <opencv2/opencv.hpp>

#include <vector>
#include <memory>
```

### 4.2 Python Style

**Follow PEP 8 with these notes:**
- Indentation: 4 spaces (consistent with itom codebase)
- Line length: 100 characters (slightly more relaxed than PEP 8's 79)
- Use `snake_case` for functions and variables
- Use `PascalCase` for classes

**Type Hints (Optional for 3.6 compatibility):**
```python
# Acceptable for 3.6+
def process_data(data: list) -> dict:
	pass

# Or without type hints for maximum compatibility
def process_data(data):
	"""
	Args:
		data (list): Input data

	Returns:
		dict: Processed results
	"""
	pass
```

### 4.3 Error Handling

**C++ Exception Handling:**
```cpp
try {
	// Code that may throw
} catch (const cv::Exception& exc) {
	// Handle OpenCV exceptions
} catch (const std::exception& e) {
	// Handle standard exceptions
}
```

**Python Exception Handling:**
```python
try:
	# Code that may raise
except ValueError as e:
	# Handle specific exception
except Exception as e:
	# Handle general exception
	pass
```

**Prefer exceptions over error codes** for exceptional conditions.

---

## 5. Version Control

### 5.1 Commit Messages

**Format:**
```
Short summary (50 chars or less)

Longer explanation if needed (wrap at 72 characters).
Explain what changed and why, not how.

Refs: #123 (issue number if applicable)
```

**Commit Message Guidelines:**
- Use imperative mood ("Add feature" not "Added feature")
- Capitalize first letter
- No period at the end of summary
- Reference issues when relevant

### 5.2 Branch Naming

**Convention:**
- `feature/short-description` - New features
- `bugfix/short-description` - Bug fixes
- `refactor/short-description` - Code refactoring
- `docs/short-description` - Documentation only

---

## 6. Testing Requirements

### 6.1 Code Changes

**Every code change should:**
- Compile without warnings on supported compilers (MSVC, GCC, Clang)
- Be tested with at least one Qt version (5.x and 6.x if possible)
- Not break existing functionality
- Include inline documentation for public APIs

### 6.2 Build Verification

**Before finalizing changes:**
```cpp
// Use get_errors tool to check specific files
// Use run_build tool to verify full build
```

**Test different configurations if applicable:**
- Debug vs Release
- Qt 5 vs Qt 6
- OpenCV 3 vs 4 vs 5
- Python 3.6 vs 3.11+

---

## 7. AI Assistant Instructions

### 7.1 General Guidelines

**When writing code:**
1. ALWAYS check compatibility requirements (Section 2)
2. ALWAYS write documentation in English (Section 3.1)
3. ALWAYS use compatibility macros for OpenCV (Section 2.3)
4. ALWAYS verify build before completion (Section 6.2)

**When creating documentation:**
1. Use English language
2. Follow markdown style guide (Section 3.2)
3. Include code examples with proper language tags
4. Verify links are valid

**When modifying existing code:**
1. Maintain existing style and conventions
2. Add version checks if introducing new APIs
3. Update documentation if changing public APIs
4. Test compatibility with target versions

### 7.2 Prohibited Patterns

❌ **DO NOT:**
- Use Qt 6-only APIs without Qt 5 fallback
- Use Python 3.7+ features without version checks
- Use OpenCV 5 APIs without compatibility wrappers
- Write documentation in languages other than English
- Break existing compatibility ranges
- Ignore compiler warnings
- Leave code in non-compilable state

✅ **DO:**
- Add version checks for new features
- Use compatibility macros (ITOM_CV_EXCEPTION, etc.)
- Write portable code
- Test on multiple Qt/Python/OpenCV versions when possible
- Update documentation when changing APIs
- Run build verification before completion

### 7.3 Common Tasks

**Adding Qt version-specific code:**
```cpp
#if QT_VERSION >= QT_VERSION_CHECK(6, 0, 0)
	// Qt 6 code
#else
	// Qt 5 code
#endif
```

**Adding Python version-specific code:**
```cpp
#if PY_VERSION_HEX >= 0x030B0000  // 3.11
	// Python 3.11+ API
#else
	// Older Python API
#endif
```

**Adding OpenCV version-specific code:**
```cpp
#if CV_VERSION_MAJOR >= 5
	// OpenCV 5 code
#else
	// OpenCV 3/4 code
#endif
```

---

## 8. Project-Specific Patterns

### 8.1 Data Object Usage

**itom's core data structure:**
```cpp
#include "DataObject/dataobj.h"

ito::DataObject obj(100, 100, ito::tUInt8);
// Use DataObject API
```

### 8.2 Python Integration

**Calling Python from C++:**
```cpp
PyGILState_STATE gstate = PyGILState_Ensure();
try {
	// Python API calls
} catch (...) {
	PyGILState_Release(gstate);
	throw;
}
PyGILState_Release(gstate);
```

**Always lock GIL before Python API calls.**

### 8.3 Qt Signals/Slots

**Modern Qt5+ syntax preferred:**
```cpp
connect(sender, &SenderClass::signalName,
		receiver, &ReceiverClass::slotName);
```

**String-based syntax for compatibility:**
```cpp
connect(sender, SIGNAL(signalName()),
		receiver, SLOT(slotName()));
```

Use modern syntax where possible, but string-based is acceptable for complex scenarios.

---

## 9. Build System (CMake)

### 9.1 Overview

**Build System:** CMake (cross-platform)  
**Main CMakeLists.txt:** Located at project root  
**Module CMakeLists.txt:** Each major component has its own CMakeLists.txt

### 9.2 Adding New Files

**When creating new .h/.cpp files, you MUST add them to the appropriate CMakeLists.txt**

**Example: Adding files to itom/Qitom/python/**

1. **Find the correct CMakeLists.txt:**
   - Python module files: `itom/Qitom/CMakeLists.txt`
   - DataObject files: `itom/DataObject/CMakeLists.txt`
   - Common files: `common/CMakeLists.txt`

2. **Add header files** to the headers list:
```cmake
set(QITOM_HEADERS
    # ... existing headers ...
    ${CMAKE_CURRENT_SOURCE_DIR}/python/yourNewFile.h
)
```

3. **Add source files** to the sources list:
```cmake
set(QITOM_SOURCES
    # ... existing sources ...
    ${CMAKE_CURRENT_SOURCE_DIR}/python/yourNewFile.cpp
)
```

### 9.3 File Organization in CMakeLists.txt

**Keep files alphabetically sorted or logically grouped:**
```cmake
# Good: Grouped by functionality
${CMAKE_CURRENT_SOURCE_DIR}/python/pythonJedi.h
${CMAKE_CURRENT_SOURCE_DIR}/python/pythonJediRunner.h
${CMAKE_CURRENT_SOURCE_DIR}/python/languageServerBackend.h  # New LSP files
${CMAKE_CURRENT_SOURCE_DIR}/python/lspClient.h
${CMAKE_CURRENT_SOURCE_DIR}/python/zubanLspBackend.h
```

### 9.4 Verification

**After adding files to CMakeLists.txt:**
1. Run CMake configuration: `cmake ..` or use IDE CMake tools
2. Check for CMake errors
3. Build the project to verify
4. All new files should compile without errors

**Common mistakes:**
- ❌ Forgetting to add both .h and .cpp files
- ❌ Using absolute paths instead of `${CMAKE_CURRENT_SOURCE_DIR}`
- ❌ Adding files to the wrong CMakeLists.txt
- ❌ Typos in file paths

### 9.5 CMake Variables

**Useful CMake variables in itom:**
- `CMAKE_CURRENT_SOURCE_DIR` - Current CMakeLists.txt directory
- `ITOM_SOURCE_DIR` - Root itom directory
- `QT_VERSION_MAJOR` - Qt major version (5 or 6)
- `PYTHON_VERSION_STRING` - Python version

**Version-specific code in CMakeLists.txt:**
```cmake
if(QT_VERSION_MAJOR EQUAL 5)
    # Qt 5 specific
else()
    # Qt 6 specific
endif()
```

---

## 10. External Dependencies

**Core Dependencies (must be compatible):**
- Qt: 5.6 - 6.11
- Python: 3.6 - 3.13
- OpenCV: 3.x - 5.x
- NumPy: (Python package, version managed by Python environment)
- Jedi: (Python package for autocomplete)

**Optional Dependencies:**
- PCL (Point Cloud Library)
- Various scientific Python packages

**When adding new dependencies:**
- Check compatibility with all supported versions
- Document minimum version requirements
- Provide fallback or graceful degradation if possible

---

## 11. Additional Resources

**Project Documentation:**
- Main docs: https://itom-project.github.io/
- API Reference: (generated from code)

**External Resources:**
- Qt Documentation: https://doc.qt.io/
- Python C API: https://docs.python.org/3/c-api/
- OpenCV Documentation: https://docs.opencv.org/
- LSP Specification: https://microsoft.github.io/language-server-protocol/

---

## 12. Changelog

**Version 1.1 (August 2026):**
- Added CMake build system section
- Added guidance for adding new files to CMakeLists.txt
- Updated project overview to mention CMake

**Version 1.0 (August 2026):**
- Initial guidelines document
- Defined compatibility requirements
- Added documentation standards
- Added coding conventions

---

**This document should be referenced by AI assistants for all itom project work.**

**Last Updated:** August 2026
