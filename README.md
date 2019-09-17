# Laravel Airbrake

This is a Laravel service provider for the latest Airbrake PHP package https://github.com/airbrake/phpbrake

It is a modified fork of https://github.com/TheoKouzelis/laravel-airbrake to allow support for Laravel 6.0.  Thanks to Theo Kouzelis for doing the hard work.

The service provider will configure an instance of Airbrake\Notifier with an ID, key and environment name.

## Install
Require via composer.
```
composer require twotwentyseven/laravel-airbrake
```
Publish and fill out the config/airbrake.php file with your ID and key.
```
php artisan vendor:publish --provider="Twotwentyseven\LaravelAirbrake\ServiceProvider"
```

## Config
The variables projectId and projectKey are required. Leave the rest empty to use Airbrake's default values.
```
    'projectId'     => '',
    'projectKey'    => '',
    'environment'   => env('APP_ENV', 'production'),

    //leave the following options empty to use defaults

    'appVersion'    => '',
    'host'          => '',
    'rootDirectory' => '',
    'httpClient'    => '',
```

## Basic Usage
### >=5.6 Custom Channel
Add the custom "airbrake" channel (outlined below) to config/logging.php. Then add the "airbrake" channel to the stack channel.
```
//config/logging.php

    'channels' => [
        'stack' => [
            'driver' => 'stack',
            'channels' => ['single', 'airbrake'],
        ],

        'airbrake' => [
            'driver' => 'custom',
            'via' => Twotwentyseven\LaravelAirbrake\AirbrakeLogger::class,
            'level' => 'error',
        ],
    ]
```

### Exception Handler
To notify airbrake through the laravel exception handler as shown in the following code snippet. Inject or make a new instance
of a Airbrake\Notifier object then pass a exception to the notify function.

```
//app/Exceptions/Handler.php

/**
 * Report or log an exception.
 *
 * This is a great spot to send exceptions to Sentry, Bugsnag, etc.
 *
 * @param  \Exception  $exception
 * @return void
 */
public function report(Exception $exception)
{
    if ($this->shouldReport($exception)) {
        $airbrakeNotifier = \App::make('Airbrake\Notifier');
        $airbrakeNotifier->notify($exception);
    }

    parent::report($exception);
}
```
