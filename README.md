# Model states for Filament

<p class="filament-hidden">
  <img src="https://raw.githubusercontent.com/maartenpaauw/model-states-for-filament-docs/main/assets/images/banner.jpg" alt="Pennant for Filament">
</p>

## Introduction

Model states for Filament is a powerful package that seamlessly integrates model states from Spatie Laravel model states
into the Filament UI. With this package, transitioning, listing and filtering states becomes an effortless task.

## Example

TODO: Tekst over belangrijkste features met praktijk voorbeeld.

**This package empowers you to perform such action with ease, all within Filament!**

### Demo video

TODO: Video

Check out this video demonstrating how straightforward it is to transition a state to another with Filament.

## Features

Model states for Filament comes packed with a range of features to enhance your experience:

TODO: Aanvullen van features.

- Spatie Laravel model states package support out-of-the-box.
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
 
Loading composer repositories with package information
Authentication required (model-states-for-filament.composer.sh):
Username: [your-email]
Password: [your-license-key:your-domain]
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

The package offers English and Dutch translations. You can publish the language files if needed:

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

You can see your credentials in your [Anystack.sh](https://anystack.sh/) account: Anystack -> Transactions -> View
details next to Model states for Filament.

> [!IMPORTANT]
> Make sure the `auth.json` file is in .gitignore to avoid leaking credentials into your git history.

If you are using [Laravel Forge](http://forge.laravel.com/), you don't need to create the `auth.json `file manually.
Instead, you can set the credentials on the Composer Package Authentication screen of your server.

## Setup

### Spatie

#### State Preparation

```php
<?php

namespace App\States;

use Maartenpaauw\SpatieLaravelModelStatesPlugin\Concerns\ProvidesSpatieStateToFilament;
use Maartenpaauw\SpatieLaravelModelStatesPlugin\Contracts\FilamentSpatieState;
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

> [!TIP]
> More information about state configuration can be found on the official
Spatie [documentation](https://spatie.be/docs/laravel-model-states/v2/working-with-states/01-configuring-states).

#### Transition Preparation

```php
<?php

namespace App\States;

use Maartenpaauw\SpatieLaravelModelStatesPlugin\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\SpatieLaravelModelStatesPlugin\Contracts\FilamentSpatieTransition;
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

> [!TIP]
> More information about transition configuration can be found on the official
Spatie [documentation](https://spatie.be/docs/laravel-model-states/v2/working-with-transitions/02-custom-transition-classes).

#### Optional Label, Color and Icon

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

```php
<?php

namespace App\States;

use Filament\Support\Colors\Color;
use Filament\Support\Contracts\HasColor;
use Filament\Support\Contracts\HasIcon;
use Filament\Support\Contracts\HasLabel;
use Maartenpaauw\SpatieLaravelModelStatesPlugin\Concerns\ProvidesSpatieTransitionToFilament;
use Maartenpaauw\SpatieLaravelModelStatesPlugin\Contracts\FilamentSpatieTransition;
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

### State Action

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateAction;

// ...

StateAction::make('fail')
    ->transitionTo(Failed::class);
```

> [!TIP]
> More information about actions can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/actions/overview).

### State Column

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateColumn;

// ...

StateColumn::make('state')
    ->badge();
```

> [!TIP]
> More information about columns can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/tables/columns/getting-started).

### State Entry

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateEntry;

// ...

StateEntry::make('state')
    ->badge();
```

> [!TIP]
> More information about entries can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/infolists/entries/getting-started).

### State Export Column

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateExportColumn;

// ...

StateExportColumn::make('state')
    ->label('State');
```

> [!TIP]
> More information about exports can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/actions/prebuilt-actions/export).

### State Group

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateGroup;

// ...

StateGroup::make('state')
    ->label('State');
```

> [!TIP]
> More information about groups can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/tables/grouping).

### State Select

> [!CAUTION]
> Using a state select in combination with the Spatie integration states aren't transitioned using the `Transition`
> classes and are directly saved to the database like regular fields. Only use this select when you are sure you are not
> relying on `Transition` classes and `StateChanged` events.

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateSelect;

// ...

StateSelect::make('state');
```

> [!TIP]
> More information about selects can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/forms/fields/select).

### State Select Filter

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateSelectFilter;

// ...

StateSelectFilter::make('state')
    ->multiple();
```

> [!TIP]
> More information about select filters can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/tables/filters/select).

### State Table Action

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateTableAction;

// ...

StateTableAction::make('fail')
    ->transitionTo(Failed::class);
```

> [!TIP]
> More information about table actions can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/tables/actions).

### State Tabs

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateTabs;

// ...

public function getTabs(): array
{
    StateTabs::make($this->getModel())->toArray();
}
```

> [!TIP]
> More information about state tabs can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/panels/resources/listing-records#using-tabs-to-filter-the-records).

### State Toggle Buttons

> [!CAUTION]
> Using a state toggle button in combination with the Spatie integration, states aren't transitioned using
> the `Transition` classes and are directly saved to the database like regular fields. Only use this toggle button when
> you are sure you are not relying on `Transition` classes and `StateChanged` events.

```php
use Maartenpaauw\SpatieLaravelModelStatesPlugin\StateToggleButtons;

// ...

StateToggleButtons::make('state');
```

> [!TIP]
> More information about toggle buttons can be found on the official
Filament [documentation](https://filamentphp.com/docs/3.x/forms/fields/toggle-buttons).

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
