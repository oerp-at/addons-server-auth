# impersonate_login

## Purpose

Lets members of group "Impersonate Users" log in as another internal user ("Switch
Login" in the user menu) and switch back ("Back to Original User"). Every impersonation
is logged in `impersonate.log`; records and chatter messages written while impersonating
keep the real user.

## Models

- `impersonate.log`: user, impersonated partner, start/end; read/write for internal
  users, no create/unlink via ACL.
- `res.users`: `impersonate_login()` (button in user list), `back_to_origin_login()`,
  `action_impersonate_login()` (dialog with internal users).
- `base`: `_prepare_create_values()`/`write()` set `create_uid`/`write_uid` to the real
  user; abstract and transient models keep the impersonated user.
- `ir.http.session_info()`: `is_impersonate_user`, `impersonate_from_uid` for the user
  menu.
- `res.config.settings`: `restrict_impersonate_admin_settings` (system parameter
  `impersonate_login.restrict_impersonate_admin_settings`, read with `get_bool`).

## Rules

- Group `group_impersonate_login` links root and admin on install.
- Users with "Administration: Settings" cannot be impersonated when the restriction is
  on.

## Pitfalls

- 20.0: the session token is refreshed with `odoo.http.session.update_session_token()`
  (`odoo.service.security` is gone); config parameters use `get_bool`, no `get_param`.
- A running server that predates the install still serves the old session info; restart
  before checking the user menu.
