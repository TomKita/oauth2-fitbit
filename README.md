# FitBit Provider for OAuth 2.0 Client

[![Build Status](https://travis-ci.org/TomKita/oauth2-fitbit.svg)](https://travis-ci.org/TomKita/oauth2-fitbit)

This package provides Fitbit OAuth 2.0 support for the PHP League's [OAuth 2.0 Client](https://github.com/thephpleague/oauth2-client).

## Installation

To install, use composer:

```
composer require tomkita/oauth2-fitbit
```

## Usage

See below example but remember to use session_start()

### Authorization Code Flow

```php
$provider = new TomKita\OAuth2\Client\Provider\FitBit([
    'clientId'          => '229VL8',
    'clientSecret'      => '1ab24355af90770b820ae1c92faf3429s',
    'redirectUri'       => 'http://fitbit.ngrok.io'
]);

if (!isset($_GET['code'])) {

    // If we don't have an authorization code then get one
    $authUrl = $provider->getAuthorizationUrl();
    $_SESSION['oauth2state'] = $provider->getState();
    header('Location: '.$authUrl);
    exit;

// Check given state against previously stored one to mitigate CSRF attack
} elseif (empty($_GET['state']) || ($_GET['state'] !== $_SESSION['oauth2state'])) {

    unset($_SESSION['oauth2state']);
    exit('Invalid state');

} else {

    // Try to get an access token (using the authorization code grant)
    $token = $provider->getAccessToken('authorization_code', [
        'code' => $_GET['code']
    ]);

    // Optional: Now you have a token you get get the user's id
    try {

        // We got an access token, let's now get the user's details
        $user = $provider->getResourceOwner($token);
        echo $user->getId();

    } catch (Exception $e) {
        // Failed to get user details
    }

    // Use this to interact with an API on the users behalf
    echo $token->getToken();
}
```

## Testing

``` bash
$ ./vendor/bin/phpunit
```


## License

The MIT License (MIT). Please see [License File](https://github.com/tomkita/oauth2-fitbit/blob/master/LICENSE) for more information.
