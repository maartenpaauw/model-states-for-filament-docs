# Upgrading

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
