# Model states for Filament

<p class="filament-hidden">
  <img src="https://raw.githubusercontent.com/maartenpaauw/model-states-for-filament-docs/main/assets/images/banner.jpg" alt="Pennant for Filament">
</p>

## Introduction

Model states for Filament is a powerful plug-in that seamlessly integrates model states from Spatie Laravel model states
into the [Filament](https://filamentphp.com) UI. With this plug-in, transitioning, listing and filtering states becomes
an effortless task.

## Example

Consider a scenario where you're managing an e-commerce platform with various product states like `draft`, `published`,
and `archived`. With this plug-in, you can effortlessly list all products based on their states, apply filters to
quickly locate specific products in each state, and seamlessly transition between states directly within Filament's
intuitive interface. For instance, you can effortlessly move a product from `draft` to `published` with just a few
clicks, streamlining your workflow and enhancing productivity.

**This plugin leverages the robust capabilities of Filament PHP and support of Spatie Laravel model state
out-of-the-box, providing a seamless solution for managing model states with efficiency and ease.**

### Demo video

TODO: Video

Check out this video demonstrating how straightforward it is to transition a state to another with Filament.

## Features

Model states for Filament comes packed with a range of features to enhance your experience:

- Listing states.
- Filtering states.
- Transition to valid states using select or toggle buttons.
- Transition to valid states using actions.
- [Spatie Laravel model states](https://spatie.be/docs/laravel-model-states/v2/01-introduction) package support
  out-of-the-box.
- Compatible with dark mode.

## Screenshots

Take a glimpse of Model states for Filament in action:

TODO: Screenshot + onderschrift.

## Installation

**Thank you for choosing Model states for Filament!**

Here's a comprehensive guide to installing and utilizing this plugin. If you encounter any issues, have questions, need
support, or want to request a feature, please feel free to contact me
at [maartenpaauw@gmail.com](mailto:maartenpaauw@gmail.com).

### Requirements

- PHP `^8.1`
- Laravel `^10.0` or `^11.0`
- Filament `^3.2.39`
- Spatie Laravel model states `^2.7`

Additionally, make sure you have configured at least one Spatie Laravel model state in your project. For more
information, refer to the official
Spatie [documentation](https://spatie.be/docs/laravel-model-states/v2/01-introduction).

### Installation steps

#### Install with Composer

To begin, add the private registry to your `composer.json`:

```json
{
    "repositories": [
        {
            "type": "composer",
            "url": "https://model-states-for-filament.composer.sh"
        }
    ]
}
```

Once the repository is added, you can install Model states for Filament like any other composer package:

```shell
composer require maartenpaauw/model-states-for-filament
```

You will be prompted to provide your username and password. The username will be your email address and the password
will be equal to your license key, followed by a colon (`:`), followed by the domain you are activating. For example,
let's say we have the following licensee and license activation:

- Contact email: `john.doe@example.com`
- License key: `8c21df8f-6273-4932-b4ba-8bcc723ef500`
- Activation fingerprint: `example.com`

This will require you to enter the following information when prompted for your credentials:

```shell
Loading composer repositories with package information
Authentication required (model-states-for-filament.composer.sh):
Username: john.doe@example.com 
Password: 8c21df8f-6273-4932-b4ba-8bcc723ef500:example.com
```

To clarify, the license key and fingerprint should be separated by a colon (`:`).

#### Publishing

The plug-in offers English and Dutch translations. You can publish the language files if needed:

```shell
php artisan vendor:publish --tag="model-states-for-filament-translations"
```

### Deploying

It is not advised to store your `auth.json` file inside your project's version control repository. To store your
credentials on your deployment server you may create a Composer `auth.json` file in your project directory using the
following command:

```shell
composer config http-basic.model-states-for-filament.composer.sh your_account_email your_license_key:fingerprint_domain
```

You can see your credentials in your [Anystack.sh account](https://anystack.sh/): `Anystack -> Transactions -> View
details` next to Model states for Filament.

> [!IMPORTANT]
> Make sure the `auth.json` file is in `.gitignore` to avoid leaking credentials into your git history.

If you are using [Laravel Forge](http://forge.laravel.com/), you don't need to create the `auth.json `file manually.
Instead, you can set the credentials on the `Composer Package Authentication` screen of your server.

## Setup

In this paragraph we list the steps you need to follow to get up and running with the out-of-the-box supported Spatie
integration.

### Spatie

The Model states for Filament comes with support for Spatie Laravel model states out-of-the-box.

#### State Preparation

> [!TIP]
> More information about state configuration can be found on the official
> Spatie [documentation](https://spatie.be/docs/laravel-model-states/v2/working-with-states/01-configuring-states).

When using Spatie Laravel model states, there are multiple abstract state classes. These abstract state classes need
some modifications. We have to implement the `FilamentSpatieState` interface and use the `ProvidesSpatieStateToFilament`
trait.

Here is an example `PaymentState` abstract class with the modification in place.

```php
<?php

namespace App\States;

use Maartenpaauw\Filament\ModelStates\Concerns\ProvidesSpatieStateToFilament;
use Maartenpaauw\Filament\ModelStates\Contracts\FilamentSpatieState;
use Spatie\ModelStates\State;
use Spatie\ModelStates\StateConfig;

/**
 * @extends State<Payment>
 */
abstract class PaymentState extends State implements FilamentSpatieState
{
    use ProvidesSpatieStateToFilament;
    
    public static function config(): StateConfig
    {
        return parent::config()
            ->default(Pending::class)
            ->allowTransition(Pending::class, Paid::class)
            ->allowTransition(Pending::class, Failed::class, PendingToFailed::class);
    }
}
```

#### Transition Preparation

> [!TIP]
> More information about transition configuration can be found on the official Spatie
> [documentation](https://spatie.be/docs/laravel-model-states/v2/working-with-transitions/02-custom-transition-classes).

Spatie Laravel model states offers support for custom transition classes. All custom transition classes must implement
the `FilamentSpatieTransition` interface and use the `ProvidesSpatieTransitionToFilament` trait before they can be used
within Filament.

Here is an example `PendingToFailed` transition class with the modification is place.

```php
<?php

namespace App\States;

use Maartenpaauw\Filament\ModelStates\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\Filament\ModelStates\Contracts\FilamentSpatieTransition;
use Spatie\ModelStates\Transition;

/**
 * @implements FilamentSpatieTransition<Payment>
 */
final class PendingToFailed extends Transition implements FilamentSpatieTransition
{
    use ProvidesSpatieTransitionToFilament;
    
    public function __construct(
        private readonly Payment $payment,
    ) {
    }
    
    public function handle(): Payment
    {
        $this->payment->state = new Failed($this->payment);
        $this->payment->failed_at = now();

        $this->payment->save();

        return $this->payment;
    }
}
```

##### Additional Transition Data

Most of the time we need additional data before we can transition to a new state. With the `PendingToFailed` transition
in mind, it would be nice to store an error message explaining why the state was transitioned to failed. By adding
a `form` method to the transition class, there will be a form displayed when starting the transition.

Here is an example `PendingToFailed` transition class with the form is place. This transition will display a message
textarea when the `StateAction` button is clicked.

```php
<?php

namespace App\States;

use Maartenpaauw\Filament\ModelStates\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\Filament\ModelStates\Contracts\FilamentSpatieTransition;
use Spatie\ModelStates\Transition;

/**
 * @implements FilamentSpatieTransition<Payment>
 */
final class PendingToFailed extends Transition implements FilamentSpatieTransition
{
    use ProvidesSpatieTransitionToFilament;

    public function __construct(
        private readonly Payment $payment,
        private readonly string $message = '',
    ) {
    }

    public function handle(): Payment
    {
        $this->payment->state = new Failed($this->payment);
        $this->payment->failed_at = now();
        $this->payment->error_message = $this->message;

        $this->payment->save();

        return $this->payment;
    }

    public function form(): array | Closure | null
    {
        return [
            Textarea::make('message')
                ->required()
                ->minLength(1)
                ->maxLength(1000)
                ->rows(5)
                ->helperText(__('This message will be sent to the customer.')),
        ];
    }
}
```

> [!WARNING]
> Because the plug-in needs to create transition instances to determine if there is a `form`, all constructor properties
> need a default value.

By default, this plug-in will map the form component names to their constructor property names. With the
previous `PendingToFailed` in mind, the `message` textarea input will end up in constructor property `$message`. If you
wish to do any modifications before creating the transition instance, you can overwrite the static method `fill`.

For example prefixing the `message`:

```php
<?php

namespace App\States;

use Illuminate\Support\Arr;
use Maartenpaauw\Filament\ModelStates\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\Filament\ModelStates\Contracts\FilamentSpatieTransition;
use Spatie\ModelStates\Transition;

/**
 * @implements FilamentSpatieTransition<Payment>
 */
final class PendingToFailed extends Transition implements FilamentSpatieTransition
{
    use ProvidesSpatieTransitionToFilament;

    public function __construct(
        private readonly Payment $payment,
        private readonly string $message = '',
    ) {
    }

    public static function fill(Model $model, array $formData): SpatieTransition
    {
        return new self(
            payment: $model,
            message: 'The transition failed because: '.Arr::get($formData, 'message'),
        );
    }

    public function handle(): Payment
    {
        $this->payment->state = new Failed($this->payment);
        $this->payment->failed_at = now();
        $this->payment->error_message = $this->message;

        $this->payment->save();

        return $this->payment;
    }

    public function form(): array | Closure | null
    {
        return [
            Textarea::make('message')
                ->required()
                ->minLength(1)
                ->maxLength(1000)
                ->rows(5)
                ->helperText(__('This message will be sent to the customer.')),
        ];
    }
}
```

#### Optional Label, Color and Icon

By default, the name of the state class is used as a label (example `FailedState` will have the label `Failed`), it has
no color and no icon. If you want a different label, color or an icon; you have to implement the `HasLabel`, `HasColor`
or `HasIcon` interface.

Here is an example `Failed` state with `HasLabel`, `HasColor` and `HasIcon` implemented.

```php
<?php

namespace App\States;

use Filament\Support\Colors\Color;
use Filament\Support\Contracts\HasColor;
use Filament\Support\Contracts\HasIcon;
use Filament\Support\Contracts\HasLabel;

final class Failed extends PaymentState implements HasLabel, HasColor, HasIcon
{
    public function getLabel(): string
    {
        return __('Rejected');
    }

    public function getColor(): array
    {
        return Color::Red;
    }
    
    public function getIcon(): string
    {
        return 'heroicon-o-x-circle';
    }
}
```

By default, "Transition to" followed by the name of the destination state is used as the transition label. Like states,
it has no color or icon. If you want a different label, or if you want to use a color or icon; you have to implement
the `HasLabel`, `HasColor` or `HasIcon` interface.

Here is an example `PendingToFailed` transtition with `HasLabel`, `HasColor` and `HasIcon` implemented.

```php
<?php

namespace App\States;

use Filament\Support\Colors\Color;
use Filament\Support\Contracts\HasColor;
use Filament\Support\Contracts\HasIcon;
use Filament\Support\Contracts\HasLabel;
use Maartenpaauw\Filament\ModelStates\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\Filament\ModelStates\Contracts\FilamentSpatieTransition;
use Spatie\ModelStates\Transition;

/**
 * @implements FilamentSpatieTransition<Payment>
 */
final class PendingToFailed extends Transition implements FilamentSpatieTransition, HasLabel, HasColor, HasIcon
{
    use ProvidesSpatieTransitionToFilament;
    
    public function __construct(
        private readonly Payment $payment,
        private readonly string $message = '',
    ) {
    }
    
    public function handle(): Payment
    {
        $this->payment->state = new Failed($this->payment);
        $this->payment->failed_at = now();
        $this->payment->error_message = $this->message;

        $this->payment->save();

        return $this->payment;
    }
    
    public function getLabel(): string
    {
        return __('Mark as Failed');
    }

    public function getColor(): array
    {
        return Color::Red;
    }
    
    public function getIcon(): string
    {
        return 'heroicon-o-x-circle';
    }
    
    public function form(): array | Closure | null
    {
        return [
            Textarea::make('message')
                ->required()
                ->minLength(1)
                ->maxLength(1000)
                ->rows(5)
                ->helperText(__('This message will be sent to the customer.')),
        ];
    }
}
```

## Usage

In this paragraph all available components and their features are listed.

### State Column

The `StateColumn` will display the related model state within a table.

TODO: afbeelding van een tabel met state hier.

```php
use Maartenpaauw\Filament\ModelStates\StateColumn;

// ...

StateColumn::make('state')
    ->badge();
```

When using the `StateColumn` component, this plug-in will automatically generate a label for the current state. If you
want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateColumn` component is based on the `TextColumn` component, all the familiar `TextColumn` modifiers can
be used (e.g. `badge()`).

> [!TIP]
> More information about columns can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/tables/columns/getting-started).

### State Entry

The `StateEntry` will display the related model state within an infolist.

TODO: afbeelding van een infolist met state hier.

```php
use Maartenpaauw\Filament\ModelStates\StateEntry;

// ...

StateEntry::make('state')
    ->badge();
```

When using the `StateEntry` component, this plug-in will automatically generate a label for the current state. If you
want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateEntry` component is based on the `TextEntry` component, all the familiar `TextEntry` modifiers can be
used (e.g. `badge()`).

> [!TIP]
> More information about entries can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/infolists/entries/getting-started).

### State Action

The `StateAction` component will let you transition a state to another valid state. Basic transitions will only show a
confirmation dialogue, while advanced state transitions display an additional form before the transition can be done.

TODO: afbeelding van state action met invoervelden.

```php
use App\States\Failed;
use Maartenpaauw\Filament\ModelStates\StateAction;

// ...

StateAction::make('fail')
    ->transitionTo(Failed::class);
```

When using the `StateAction` component, this plug-in will automatically generate a label for the transition. By
default, "Transition to" followed by the name of the destination state is used as the transition label. If you
want to have a custom label, you can publish the language files and change the `transition_to_state` translation or, you
can implement the `HasLabel` interface.

Because the `StateAction` component is based on the `Action` component, all the familiar `Action` modifiers can be
used (e.g. `closeModalByClickingAway()`).

> [!TIP]
> More information about actions can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/actions/overview).

### State Export Column

The `StateExportColumn` will add a column to Excel or CSV export will the related model state as value.

TODO: afbeelding van een CSV-export met state hier.

```php
use Maartenpaauw\Filament\ModelStates\StateExportColumn;

// ...

StateExportColumn::make('state')
    ->label('State');
```

When using the `StateExportColumn` component, this plug-in will automatically generate a label for the current state. If
you want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateExportColumn` component is based on the `ExportColumn` component, all the familiar `ExportColumn`
modifiers can be used (e.g. `label()`).

> [!TIP]
> More information about exports can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/actions/prebuilt-actions/export).

### State Group

The `StateGroup` will add the functionality to group table records based on their status.

TODO: afbeelding van een gegroepeerde tabel hier invoegen.

```php
use Maartenpaauw\Filament\ModelStates\StateGroup;

// ...

StateGroup::make('state')
    ->label('State');
```

When using the `StateGroup` component, this plug-in will automatically generate a label for the current state. If
you want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateGroup` component is based on the `Group` component, all the familiar `Group`
modifiers can be used (e.g. `label()`).

> [!TIP]
> More information about groups can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/tables/grouping).

### State Select

> [!CAUTION]
> Using a state select in combination with the Spatie integration, states aren't transitioned using the `Transition`
> classes and are directly saved to the database like regular fields. Only use this select when you are sure you are not
> relying on `Transition` classes and `StateChanged` events.

The `StateSelect` is a form component which makes it possible to select valid state transitions, all other invalid state
transitions are disabled. This component is meant for basic state transitions, which does not require additional form
fields.

TODO: afbeelding van een state select met daarin disabled states.

```php
use Maartenpaauw\Filament\ModelStates\StateSelect;

// ...

StateSelect::make('state');
```

When using the `StateSelect` component, this plug-in will automatically list all states using their generated class name
label. If you want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateSelect` is based on the `Select` component, all the familiar `Select` modifiers can be used (
e.g. `native()`).

> [!TIP]
> More information about selects can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/forms/fields/select).

### State Select Filter

```php
use Maartenpaauw\Filament\ModelStates\StateSelectFilter;

// ...

StateSelectFilter::make('state')
    ->multiple();
```

> [!TIP]
> More information about select filters can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/tables/filters/select).

### State Table Action

The `StateTableAction` component will let you transition a state to another valid state. Basic transitions will only
show a confirmation dialogue, while advanced state transitions display an additional form before the transition can be
done.

TODO: afbeelding van state action met invoervelden.

```php
use App\States\Failed;
use Maartenpaauw\Filament\ModelStates\StateTableAction;

// ...

StateTableAction::make('fail')
    ->transitionTo(Failed::class);
```

When using the `StateTableAction` component, this plug-in will automatically generate a label for the transition. By
default, "Transition to" followed by the name of the destination state is used as the transition label. If you
want to have a custom label, you can publish the language files and change the `transition_to_state` translation or, you
can implement the `HasLabel` interface.

Because the `StateTableAction` component is based on the `Action` component, all the familiar `Action` modifiers can be
used (e.g. `closeModalByClickingAway()`).

> [!TIP]
> More information about table actions can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/tables/actions).

### State Tabs

The `StateTabs` component can be used to filter records using tabs. Besides the state tabs, there will also be an "All"
tab to list all records. The state tabs are alphabetically ordered.

TODO: afbeelding van de state tabs.

```php
use Maartenpaauw\Filament\ModelStates\StateTabs;

// ...

public function getTabs(): array
{
    StateTabs::make($this->getModel())->toArray();
}
```

If you wish to not include the "All" tab mentioned above, you can chain the `includeAllTab(false)` method.

```php
use Maartenpaauw\Filament\ModelStates\StateTabs;

// ...

public function getTabs(): array
{
    StateTabs::make($this->getModel())
        ->includeAllTab(false)
        ->toArray();
}
```

> [!TIP]
> More information about state tabs can be found on the official Filament
> [documentation](https://filamentphp.com/docs/3.x/panels/resources/listing-records#using-tabs-to-filter-the-records).

### State Toggle Buttons

> [!CAUTION]
> Using a state toggle button in combination with the Spatie integration, states aren't transitioned using
> the `Transition` classes and are directly saved to the database like regular fields. Only use this toggle button when
> you are sure you are not relying on `Transition` classes and `StateChanged` events.

Like the `StateSelect`, the `StateToggleButtons` is a form component which makes it possible to select valid state
transitions, all other invalid state transitions are disabled. This component is meant for basic state transitions,
which does not require additional form fields.

TODO: afbeelding van een state select met daarin disabled states.

```php
use Maartenpaauw\Filament\ModelStates\StateToggleButtons;

// ...

StateToggleButtons::make('state');
```

When using the `StateToggleButtons` component, this plug-in will automatically list all states using their generated
class name label. If you want to have a custom label, you can implement the `HasLabel` interface.

Because the `StateToggleButtons` is based on the `ToggleButtons` component, all the familiar `ToggleButtons` modifiers
can be used (e.g. `inline()`).

> [!TIP]
> More information about toggle buttons can be found on the official
> Filament [documentation](https://filamentphp.com/docs/3.x/forms/fields/toggle-buttons).

## Advanced

### Custom State Manager

Do you want to use a different model state manager than Spatie Laravel model state? This is totally possible. You have
to create your own manager by creating a new class that implements the `Manager` interface.

```php
<?php

declare(strict_types=1);

namespace Maartenpaauw\Filament\ModelStates\Contracts;

use Illuminate\Contracts\Validation\ValidationRule;
use Illuminate\Database\Eloquent\Scope;
use Illuminate\Support\Collection;

interface Manager
{
    public static function make(Config $config): self;

    public function currentState(): State;

    public function defaultState(): ?State;

    /**
     * @return Collection<string, State>
     */
    public function allStates(): Collection;

    public function transformState(mixed $state): State;

    public function getTransition(PendingTransition $pendingTransition): Transition;

    public function isValidPendingTransition(PendingTransition $pendingTransition): bool;

    public function isInvalidPendingTransition(PendingTransition $pendingTransition): bool;

    public function executePendingTransition(PendingTransition $pendingTransition): void;

    public function scope(State $state): Scope;

    public function validationRule(bool $required = true): ValidationRule;
}
```

After creating a custom manager, you can specify per component which manager to use. For example when using the state
action, you can chain the `->manager(...)` method after making the `StateAction`.

```php
use App\States\CustomManager;
use App\States\Failed;
use Maartenpaauw\Filament\ModelStates\StateAction;

// ...

StateAction::make('fail')
    ->manager(CustomManager::class)
    ->transitionTo(Failed::class);
```

## Need Assistance?

Questions, bugs, feature requests, or suggestions? Feel free to contact me at maartenpaauw@gmail.com. Your feedback is
invaluable.

## Licensing Information

### Single License

The single license permits the use of Model states for Filament in a single project hosted on a single domain or
subdomain. It's suitable for personal websites or client-specific websites.

For SaaS applications, you'll require an unlimited license.

### Unlimited License

The unlimited license allows the usage of Model states for Filament on multiple domains, subdomains, and even in SaaS
applications.

### Code Distribution

Please note that the licenses for Model states for Filament prohibit the public distribution of its source code. Hence,
you cannot build and distribute applications using Model states for Filament's source code on open-source platforms.

### Questions About Licensing?

If you're uncertain about which license is appropriate for your needs, don't hesitate to reach out. Contact me at
maartenpaauw@gmail.com, and I'll be glad to assist you.
