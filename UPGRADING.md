# Upgrading

## From v3 to v4

This guide will help you upgrade from Model States for Filament v3 to v4, which adds support for Filament v4 and
introduces several improvements.

> [!WARNING]
> Version 4.0.0 introduces breaking changes. Please review this guide carefully and test your upgrade in a development
> environment first.

**Backup:** Always back up your application before upgrading.

### Step 1: Upgrade Filament to v4

First, upgrade your Filament installation to v4 following
the [official upgrade guide](https://filamentphp.com/docs/4.x/support/upgrade-guide). Model States for Filament v4 is
**only compatible with Filament v4**.

### Step 2: Update Dependencies

Update your `composer.json` to require the new version:

```bash
composer require maartenpaauw/model-states-for-filament:^4.0
```

### Step 3: Update Transition Classes

The method for defining transition forms has been renamed from `form()` to `schema()`:

**Before (v3):**

```php
public function form(): array | Closure | null
{
    return [
        Textarea::make('reason')
            ->required()
            ->minLength(1)
            ->maxLength(1000)
            ->rows(5)
            ->helperText(__('This reason will be sent to the customer.')),
    ];
}
```

**After (v4):**

```php
public function schema(): array | Closure | null
{
    return [
        Textarea::make('reason')
            ->required()
            ->minLength(1)
            ->maxLength(1000)
            ->rows(5)
            ->helperText(__('This reason will be sent to the customer.')),
    ];
}
```

Find all your transition classes and update them.

### Step 4: Replace StateTableAction

`StateTableAction` has been removed and consolidated into `StateAction`. The `StateAction` component now works
seamlessly on both pages and tables.

**Before (v3):**

```php
// For pages
StateAction::make('cancel')
    ->transitionTo(CancelledState::class);

// For tables
StateTableAction::make('cancel')
    ->transitionTo(CancelledState::class);
```

**After (v4):**

```php
// Works for both pages and tables
StateAction::make('cancel')
    ->transitionTo(CancelledState::class);
```

Find and replace all instances:

- `use Maartenpaauw\Filament\ModelStates\StateTableAction;` → `use Maartenpaauw\Filament\ModelStates\StateAction;`
- `StateTableAction::make(...)` → `StateAction::make(...)`

### Step 5: Test Your Application

After completing the upgrade:

1. Clear your application cache:

   ```bash
   php artisan cache:clear
   php artisan config:clear
   php artisan view:clear
   ```

2. Test all state transitions in your application
3. Verify forms are working correctly with the new `schema()` method
4. Check that table actions still function properly
5. Verify that tooltips are displaying correctly (automatic feature)

### Troubleshooting

#### Common Issues

**Transition forms not displaying:** Ensure you've renamed `form()` to `schema()` in all transition classes.

**Table actions not working:** Replace `StateTableAction` with `StateAction`.

#### Getting Help

If you encounter issues during the upgrade:

1. Check that all breaking changes have been addressed
2. Review the changelog for additional context
3. Check the package documentation for the latest examples
4. Verify Filament v4 is properly installed
5. Clear all application caches

The upgrade process is straightforward, with most changes being simple method renames and import updates. The enhanced
features and Filament v4 compatibility make this upgrade highly recommended for all users.

# From v2 to v3

## Changes To `Operator` Enum Cases

### Changes

- Added
    - `Equal`
    - `NotEqual`
- Removed
    - `NotIn`

### Impact on Custom Drivers

If your custom driver relies on the `Operator` enum, update the following:

- **Replace `NotIn` usage** with alternative exclusion logic.
- **Ensure support for `Equal` and `NotEqual`** in operator handling.
- **Review switch cases and conditionals** to accommodate these changes.

Update your implementation accordingly to maintain compatibility.

# From v1 to v2

> [!TIP]
> If you don't use a custom `StateManager`, you can skip following this upgrade guide. This major release focuses on
> rewriting the state manager to make it easier to register a globally custom state manager.

## Replace a Custom Manager with a Custom Driver

This plug-in will now utilize the `Illuminate\Support\Manager` class to register custom drivers. By using
this class, we can register and use custom state drivers the Laravel way.

1. Remove the `Manager` interface from your custom-created state manager.
2. Implement the `Driver` interface on your custom-created state manager (from now on we'll call it a driver).
3. All previous manager methods are still there, except the static `make` method. This method is removed, and
   instantiating must be done via the regular constructor.
4. Update all other methods to receive a `Config $config` as the first parameter.
5. Rewrite your driver logic to use the given `$config`.
6. After rewriting the custom driver logic, register it by calling `extend()` on the `StateManager`
   facade inside a service provider.
7. Replace all your component `->manager(CustomDriver::class)` calls with `->stateDriver('custom-driver')`.
8. Bonus: If **all** your state components use the custom driver, you can remove all `->manager(...)` calls and simply
   configure the custom driver by adding `MODEL_STATES_DRIVER="custom-driver"` to your `.env` file.
