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