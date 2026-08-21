---
name: itom-plugin-development
description: Patterns for implementing and modifying itom plugins (AddInDataIO, AddInActuator, AddInAlgo), including parameter handling, threading and semaphores. Use when working under plugins/.
---

# itom plugin development

## Structure

Each plugin folder contains at least:

- `CMakeLists.txt` — target, Qt moc/uic, install rules
- `<Name>Interface` (derived from `ito::AddInInterfaceBase`) — metadata, mandatory and
  optional init parameters, `getAddInInst` / `closeThisInst`
- `<Name>` (derived from `ito::AddInDataIO`, `ito::AddInActuator` or `ito::AddInAlgo`)
- optional `dialog<Name>` / `dockWidget<Name>` UI classes
- `docs/` documentation

Reference implementations: `plugins/DummyGrabber`, `plugins/DummyMotor`,
`plugins/BasicFilters`. Skeletons: `itom/pluginTemplates`.

## Parameters

- Declare every parameter in the constructor by inserting `ito::Param` objects into
  `m_params`, with a meaningful documentation string and meta information
  (`ito::IntMeta`, `ito::DoubleMeta`, `ito::StringMeta`, ...).
- `getParam`/`setParam` must follow the existing pattern: `apiGetParamFromMapByKey`,
  `apiValidateParam`, then apply and finally `emit parametersChanged(m_params)`.
- Parameter keys are lowerCamelCase and must not change once released.

## Threading and semaphores

- Plugin instances live in their own thread; public slots are invoked via queued
  connections.
- Always start with `ItomSharedSemaphoreLocker locker(waitCond);` and, where the
  existing code does so, call `waitCond->returnValue = retval; waitCond->release();`
  before long running work is finished.
- Do not access GUI objects from the plugin thread; communicate via signals.
- Never let exceptions escape a plugin method — convert them to `ito::RetVal`.

## dataIO specifics

Implement `startDevice`, `stopDevice`, `acquire`, `getVal`, `copyVal`, `checkData`.
`getVal` hands out a shallow copy of the internal `m_data`, `copyVal` performs a deep
copy into the caller's `ito::DataObject`. Keep `m_data` allocated via `checkData()`.

## Actuator specifics

Implement `calib`, `setOrigin`, `getStatus`, `getPos`, `setPosAbs`, `setPosRel`,
plus `waitForDone`. Maintain `m_currentStatus`, `m_currentPos`, `m_targetPos` and emit
`actuatorStatusChanged` / `targetChanged`.

## Algorithm specifics

Register filters and widgets in `AddInAlgo::init` with `FilterDef` / `AlgoWidgetDef`
and provide the matching `*Params` function that declares mandatory, optional and
output parameters.

## Checklist before finishing

- [ ] `ito::RetVal` returned everywhere, no exceptions across the DLL boundary
- [ ] semaphore released on every code path
- [ ] parameters documented and validated
- [ ] plugin added to `plugins/CMakeLists.txt`
- [ ] documentation updated
- [ ] target builds
