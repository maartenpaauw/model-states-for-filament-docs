# Changelog

All notable changes to `model-states-for-filament` will be documented in this file.

## 2.5.0 - 2025-01-12

### Added

- Relationship support for state tabs.
- Relationship support for hide state filter.
- Relationship support for state select filter.
- Relationship support for state action.
- Relationship support for state table action.
- Relationship support for state bulk action.

## 2.4.0 - 2024-12-06

### Added

- Display the state description as state group description.

## 2.3.2 - 2024-08-21

### Added

- Use override attribute when overriding or implementing methods.

## 2.3.1 - 2024-08-20

### Changed

- Simplify Spatie query logic with `whereIn` and `whereNotIn`.

## 2.3.0 - 2024-08-11

### Added

- State sorting functionality.

## 2.2.0 - 2024-08-05

### Added

- State bulk action component.

## 2.1.0 - 2024-08-03

### Added

- Display the state description as state column tooltip.
- Display the state description as state entry tooltip.

### Fixed

- Add default name to hide state filter.

## 2.0.2 - 2024-07-28

### Fixed

- Make trait getters protected.

## 2.0.1 - 2024-07-19

### Fixed

- Handle form closures correctly.
- Do not use form data when validating transition.

## 2.0.0 - 2024-07-18

### Added

- Configure the default state driver globally via an `.env` variable.

### Changed

- Renamed the interface `Manager` to `Driver` and passed `Config` to each method as the first parameter.
- Utilized the class `Illuminate\Support\Manager` to register custom drivers.
- Renamed the method `manager()` to `stateDriver()` for defining component-specific custom state drivers.

## 1.4.0 - 2024-06-01

### Added

- Add state select column component.

## 1.3.1 - 2024-05-21

### Fixed

- Retrieve Spatie state cast using casts instead of attributes.

## 1.3.0 - 2024-04-20

### Added

- Add support for `HasDescription` interface.
- Add state radio form component.

## 1.2.0 - 2024-04-17

### Added

- Add operator enum to determine how the scope should behave.
- Add table filter to hide specific state.

## 1.1.0 - 2024-04-15

### Added

- Extends `State` and `Transition` interface from `HasDescription`.

## 1.0.1 - 2024-04-14

### Fixed

- Use `getAttribute()` instead of `getRelationshipTitleAttribute()` to receive filter attribute.

## 1.0.0 - 2024-04-14

Initial release
