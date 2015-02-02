# jwt-auth-bundle

JWT Authentication bundle for Symphony


##Demo

Check the usage demo: https://github.com/auth0/jwt-auth-bundle/tree/master/example

##Usage

###1. Install dependencies

We recommend using [Composer](http://getcomposer.org/doc/01-basic-usage.md) to install the library.

Modify your `composer.json` to add the following dependencies and run `composer update`.

~~~js
{
    "require": {
        "firebase/php-jwt": "dev-master",
        "adoy/oauth2": "dev-master",
        "auth0/jwt-auth-bundle": "1.0.0"
    }
}
~~~

###2. Add the bundle to your AppKernell.php file

~~~php

class AppKernel extends Kernel
{
    public function registerBundles()
    {
        $bundles = array(
            
            ...
            
            new \Auth0\JWTAuthBundle\Auth0JWTAuthBundle(),
            
            ...
            
        );
        
        ...
        
        return $bundles;
    }
   
~~~

###3. Configure your Auth0 app data

Modify the file /app/config/config.yml

~~~yml
auth0_symfony_jwt:
    domain:        yourdomain.auth0.com
    client_id:     YOURCLIENTID
    client_secret: YOURCLIENTSECRET
~~~

###4. Setup your User and UserProvider

Create your User and UserProvider.

The UserProvider must implements the JWTUserProviderInterface (see /source/AppBundle/Security/A0UserProvider). This class should implement 2 methods:
- loadUserByJWT: This method receives the decoded JWT (but overloaded with the encoded token on the token attribute) and should return a User.
- getAnonymousUser: This method should return an anonymous user that represents an unauthenticated one (usually represented by the role *IS_AUTHENTICATED_ANONYMOUSLY*).
*Both methods can throw an AuthenticationException exception in case that the user is not found, in the case of the loadUserByJWT method, or you don't want to handle unauthenticated users on your app, in the case of the getAnonymousUser method.*

The configure your services on /app/config/services.yml

~~~yml
services:
    a0_user_provider:
        class: AppBundle\Security\A0UserProvider
        arguments: ["@auth0_symfony_jwt.auth0_service"]
~~~

###5. Setup the SecurityProvider

Modify the file /app/config/security.yml:

- define your user provider
- define your secured area that want to authenticate using JWT
- define the access_control section with the roles needed for each route

~~~yml
security:
    providers:
        a0:
            id:
                a0_user_provider

    firewalls:
        secured_area:
            pattern: ^/api
            stateless: true
            simple_preauth:
                authenticator: auth0_symfony_jwt.jwt_authenticator

    access_control:
        - { path: ^/api/login, roles: IS_AUTHENTICATED_ANONYMOUSLY }
        - { path: ^/api, roles: ROLE_OAUTH_USER }
~~~


## Issue Reporting

If you have found a bug or if you have a feature request, please report them at this repository issues section. Please do not report security vulnerabilities on the public GitHub issue tracker. The [Responsible Disclosure Program](https://auth0.com/whitehat) details the procedure for disclosing security issues.
