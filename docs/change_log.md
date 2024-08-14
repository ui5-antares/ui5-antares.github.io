# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [1.96.31003] - 2024-08-14

[1.96.31003]: https://github.com/hasanciftci26/ui5-antares/pull/45

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.96.30003] - 2024-08-14

[1.96.30003]: https://github.com/hasanciftci26/ui5-antares/pull/46

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.96.25004] - 2024-08-14

[1.96.25004]: https://github.com/hasanciftci26/ui5-antares/pull/47

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.123.1004] - 2024-08-11

[1.123.1004]: https://github.com/hasanciftci26/ui5-antares/pull/34

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.120.15003] - 2024-08-11

[1.120.15003]: https://github.com/hasanciftci26/ui5-antares/pull/36

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.120.14003] - 2024-08-11

[1.120.14003]: https://github.com/hasanciftci26/ui5-antares/pull/37

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.120.13003] - 2024-08-11

[1.120.13003]: https://github.com/hasanciftci26/ui5-antares/pull/38

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.108.32003] - 2024-08-11

[1.108.32003]: https://github.com/hasanciftci26/ui5-antares/pull/39

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.108.31003] - 2024-08-11

[1.108.31003]: https://github.com/hasanciftci26/ui5-antares/pull/40

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.108.30003] - 2024-08-11

[1.108.30003]: https://github.com/hasanciftci26/ui5-antares/pull/41

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.108.23003] - 2024-08-11

[1.108.23003]: https://github.com/hasanciftci26/ui5-antares/pull/42

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.96.33003] - 2024-08-11

[1.96.33003]: https://github.com/hasanciftci26/ui5-antares/pull/43

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.96.32003] - 2024-08-11

[1.96.32003]: https://github.com/hasanciftci26/ui5-antares/pull/44

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.124.1003] - 2024-08-10

[1.124.1003]: https://github.com/hasanciftci26/ui5-antares/pull/28

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.123.2003] - 2024-08-10

[1.123.2003]: https://github.com/hasanciftci26/ui5-antares/pull/31

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.120.12003] - 2024-08-09

[1.120.12003]: https://github.com/hasanciftci26/ui5-antares/commit/ea1ff2bb74e7ac0e6464c1bd59195491e6598f57

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## [1.120.11003] - 2024-08-09

[1.120.11003]: https://github.com/hasanciftci26/ui5-antares/commit/0fe56eac538b4a8ed5f06176a471facfb77dd8dd

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.
- It is now possible to set initial filter values for standalone ValueHelpCL usages.
- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added.

## 1.120.1014 - 2024-08-09

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

## 1.124.999006 - 2024-08-09

### Changed

- The CHANGELOG.md file and the documentation in the README.md file have been moved to the [UI5 Antares](https://ui5-antares.github.io/) website.

## 1.120.1013 - 2024-07-16

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.

## 1.124.999005 - 2024-07-16

### Added

- It is now possible to attach a function that will be executed when the ValueHelpDialog is opened by ValueHelpCL class.

## 1.124.999004 - 2024-07-16

### Added

- It is now possible to set initial filter values for standalone ValueHelpCL usages.

## 1.120.1012 - 2024-07-16

### Added

- It is now possible to set initial filter values for standalone ValueHelpCL usages.

## 1.124.999003 - 2024-07-05

### Added

- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.
- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.
- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.
- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.
- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 
- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.
- The view dependent dialog bug fix for dialog loading in Entry classes.
- The missing parameters of the resetChanges in Entry class have been added. 

## 1.120.1011 - 2024-07-04

### Fixed

- The missing parameters of the resetChanges in Entry class have been added. 

## 1.120.1010 - 2024-07-03

### Fixed

- The view dependent dialog bug fix for dialog loading in Entry classes.

## 1.120.1009 - 2024-06-25

### Added

- It is now possible to attach a function that will be executed after the user selects a row from the ValueHelpDialog when the ValueHelpCL class is used as a standalone component.

## 1.120.1008 - 2024-06-21

### Fixed

- The missing dialog types ViewSettingsDialog, TableSelectDialog, SelectDialog, BusyDialog, ValueHelpDialog have been added to the FragmentCL class.

## 1.120.1007 - 2024-06-20

### Added

- It is now possible to set custom data for the auto-generated form elements using the **setFieldCustomData()** method of the Entry classes.
- It is now possible to set the `setTextInEditModeSource` property of the smartfield using the **setTextInEditModeSource()** method of the Entry classes.

## 1.120.1006 - 2024-06-14

### Added

- It is now possible to disable auto close after the submit is done in EntryCreateCL and EntryUpdateCL classes. To close and destroy the dialog manually, the **closeAndDestroyEntryDialog()** method can be utilized.

### Fixed

- The issue with label generation from the metadata for custom controls in the SMART form has been resolved. 

## 1.120.1005 - 2024-06-12

### Added

- **setAutoDestroyOnESC()** method has been added to the **FragmentCL** class. If this method is executed with **true** parameter, the dialog content will be destroyed automatically by the class.

## 1.120.1004 - 2024-06-11

### Added

- `viewDependent` parameter has been added to the **openAsync()** and **open()** methods of the **FragmentCL** class. If this parameter is set to true, dialog or popover is added as a dependent to the source view using the **addDependent()** method of the MVC View class.
- **closeAndDestroy()** method has been added to the **FragmentCL** class. With this method, it is no longer necessary to call **close()** and then **destroyFragmentContent()**. This method does both.

## 1.124.1002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.124.999002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.123.2002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.123.1003 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.15002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.14002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.13002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.12002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.11002 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.120.1003 - 2024-06-11

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.108.32002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.108.31002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.108.30002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.108.23002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.96.33002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.96.32002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.96.31002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.96.30002 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.96.25003 - 2024-06-10

### Added

- `filterCaseSensitive?` parameter added to the constructor of the ValueHelpCL to change the default value of case-sensitive search in the filterbar.

### Fixed

- ValidationLogicCL bug fix for falsy values such as false, 0, "" in the `value1` and `value2` properties. 

## 1.124.1001 - 2024-06-09

### Added

- Initial productive release.

## 1.123.2001 - 2024-06-09

### Added

- Initial productive release.

## 1.120.15001 - 2024-06-09

### Added

- Initial productive release.

## 1.120.14001 - 2024-06-09

### Added

- Initial productive release.

## 1.120.13001 - 2024-06-09

### Added

- Initial productive release.

## 1.120.12001 - 2024-06-09

### Added

- Initial productive release.

## 1.120.11001 - 2024-06-09

### Added

- Initial productive release.

## 1.96.33001 - 2024-06-09

### Added

- Initial productive release.

### Changed

- The sap.ui.model.odata.v2.Context class does not have the **delete()** method in the 1.96.33 SAPUI5 version. It is replaced with the **remove()** method of the sap.ui.model.odata.v2.ODataModel class. The **EntryDeleteCL** class still works the same. It is only a technical change.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.96.32001 - 2024-06-09

### Added

- Initial productive release.

### Changed

- The sap.ui.model.odata.v2.Context class does not have the **delete()** method in the 1.96.32 SAPUI5 version. It is replaced with the **remove()** method of the sap.ui.model.odata.v2.ODataModel class. The **EntryDeleteCL** class still works the same. It is only a technical change.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.96.31001 - 2024-06-09

### Added

- Initial productive release.

### Changed

- The sap.ui.model.odata.v2.Context class does not have the **delete()** method in the 1.96.31 SAPUI5 version. It is replaced with the **remove()** method of the sap.ui.model.odata.v2.ODataModel class. The **EntryDeleteCL** class still works the same. It is only a technical change.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.96.30001 - 2024-06-09

### Added

- Initial productive release.

### Changed

- The sap.ui.model.odata.v2.Context class does not have the **delete()** method in the 1.96.30 SAPUI5 version. It is replaced with the **remove()** method of the sap.ui.model.odata.v2.ODataModel class. The **EntryDeleteCL** class still works the same. It is only a technical change.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.108.32001 - 2024-06-09

### Added

- Initial productive release.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.108.31001 - 2024-06-09

### Added

- Initial productive release.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.124.999001 - 2024-06-09

### Added

- Initial productive release.

## 1.108.30001 - 2024-06-08

### Added

- Initial productive release.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.108.23001 - 2024-06-08

### Added

- Initial productive release.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.96.25002 - 2024-06-07

### Added

- **Form Grouping Feature:** It is now possible to have form groups in the generated form. Additionally, form groups are used as the sections of the sap.uxap.ObjectPageLayout when the object page is activated.
- **Object Page Feature:** It is now possible to decide to use either sap.m.Dialog or sap.uxap.ObjectPageLayout for Entry classes. 
- **Manual Submit Feature:** It is now possible to submit the changes manually. The default behavior is still auto-submit by the library.

### Changed

- The auto-generated sap.m.Dialog now is draggable and resizable.

## 1.120.1002 - 2024-06-07

### Added

- **Form Grouping Feature:** It is now possible to have form groups in the generated form. Additionally, form groups are used as the sections of the sap.uxap.ObjectPageLayout when the object page is activated.
- **Object Page Feature:** It is now possible to decide to use either sap.m.Dialog or sap.uxap.ObjectPageLayout for Entry classes. 
- **Manual Submit Feature:** It is now possible to submit the changes manually. The default behavior is still auto-submit by the library.

### Changed

- The auto-generated sap.m.Dialog now is draggable and resizable.

## 1.123.1002 - 2024-06-07

### Added

- **Form Grouping Feature:** It is now possible to have form groups in the generated form. Additionally, form groups are used as the sections of the sap.uxap.ObjectPageLayout when the object page is activated.
- **Object Page Feature:** It is now possible to decide to use either sap.m.Dialog or sap.uxap.ObjectPageLayout for Entry classes. 
- **Manual Submit Feature:** It is now possible to submit the changes manually. The default behavior is still auto-submit by the library.

### Changed

- The auto-generated sap.m.Dialog now is draggable and resizable.

## 1.120.1001 - 2024-05-30

### Added

- Initial productive release.

## 1.96.25001 - 2024-05-30

### Added

- Initial productive release.

### Changed

- The sap.ui.model.odata.v2.Context class does not have the **delete()** method in the 1.96.25 SAPUI5 version. It is replaced with the **remove()** method of the sap.ui.model.odata.v2.ODataModel class. The **EntryDeleteCL** class still works the same. It is only a technical change.

### Removed

- sap.m.ColorPalettePopover is not supported by the FragmentCL class in this version.

## 1.123.1001 - 2024-05-30

### Added

- Initial productive release.