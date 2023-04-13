# Security

A tools to secure your application in symfony:

- [Secure session (provided by default)](https://symfony.com/doc/5.4/session.html)
- [CSRF protection (provided by default)](https://symfony.com/doc/5.4/security/csrf.html)

**Keywords:**
`authentication`,`authorization`

## Component to install

```bash
$ composer require symfony/security-bundle
```

With `symfony flex` that command will creates a `security.yaml` configuration file

```yaml
# config/packages/security.yaml
security:
  enable_authenticator_manager: true
  # https://symfony.com/doc/current/security.html#registering-the-user-hashing-passwords
  password_hashers:
    Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface: "auto"
  # https://symfony.com/doc/current/security.html#where-do-users-come-from-user-providers
  providers:
    users_in_memory: { memory: null }
  firewalls:
    dev:
      pattern: ^/(_(profiler|wdt)|css|images|js)/
      security: false
    main:
      lazy: true
      provider: users_in_memory

      # activate different ways to authenticate
      # https://symfony.com/doc/current/security.html#firewalls-authentication

      # https://symfony.com/doc/current/security/impersonating_user.html
      # switch_user: true

  # Easy way to control access for large sections of your site
  # Note: Only the *first* access control that matches will be used
  access_control:
    # - { path: ^/admin, roles: ROLE_ADMIN }
    # - { path: ^/profile, roles: ROLE_USER }
```
**Notes:**
> `providers`: Each part of your firewall need a kind of provider to load user from any storage
>
>  `firewalls`: Allow you to secure the part of your url
> 
> `access_control`:


## User
- An `User` class should implement `UserInterface` interface
- The easiest way to generate a class is to use `maker bundle`

**Command for generate this class:**
```bash
$ php bin/console make:migration
$ php bin/console doctrine:migration:migrate
```

These command above will generate the config file below

```yaml
# config/packages/security.yaml
security:
    # ...
    providers:
        app_user_provider:
            entity:
                class: App\Entity\User
                property: email
```

>**User provider:** This user provider allows the security system to load user from any storage (e.g: database) based on user identifier (e.g: email or username).
> The security system load user from two places:
>
> - `Load the User based on an identifier` during the login
> - `Reload the User from the session` in each request, unless your firewall is `stateless`


**Some user provider bundled with symfony:**
- Entity User Provider from `Doctrine`
- LDAP User Provider from `LDAP`
- Memory User Provider from `config file`
- Chain User Provider merge two or more user providers into a new user provider

> **NB:**
> 
> All user provider follow this pattern for their service ID
> `security.user.provider.concrete.<your-provider-name>` where `<your-provider-name>` is the configuration key, e.g. `app_user_provider`


**Registering the User: Hashing Passwords**
- The security bundle provides password hashing and verification functionality.
- The `User` class should implements the interface `PasswordAuthenticatedUserInterface`
  
```php
// src/Entity/User.php

// ...
use Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface;

class User implements UserInterface, PasswordAuthenticatedUserInterface
{
    // ...

    /**
     * @return string the hashed password for this user
     */
    public function getPassword(): string
    {
        return $this->password;
    }
}

```

- Configure which password hasher should be used for this class

```yaml
# config/packages/security.yaml
security:
    # ...
    password_hashers:
        # Use native password hasher, which auto-selects and migrates the best
        # possible hashing algorithm (which currently is "bcrypt")
        Symfony\Component\Security\Core\User\PasswordAuthenticatedUserInterface: 'auto'
```
- Then you can inject the password hashere service