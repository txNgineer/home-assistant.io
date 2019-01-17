---
layout: page
title: "Authentication Providers"
description: "Guide on configuring different auth providers."
date: 2018-08-23 09:40
redirect_from: /components/auth/
sidebar: true
comments: false
sharing: true
footer: true
---

When you log in, an _auth provider_ checks your credentials to make sure you are an authorized user.

<p class='note'>
The authentication system has been changed recently. Previously there was a single "_API password_" to log in, but you can now choose from several auth providers.<br/> <br/>

To make the transition from API passwords easier, we've added a _Legacy API Password_ auth provider. This is enabled by default if you have an API password configured so you will still be able to log in.

However, this feature is deprecated and will be removed in a future release so you should set up one of the newer authentication techniques.
</p>

## {% linkable_title Configuring auth providers %}

<p class='note warning'>
Home Assistant automatically configures the standard auth providers so you don't need to specify `auth_providers` in your `configuration.yaml` file unless you are configuring more than one. Specifying `auth_providers` will disable all auth providers that are not listed, so you could reduce your security or create difficulties logging in if it is not configured correctly.
</p>


Authentication providers are configured in your `configuration.yaml` under the `homeassistant:` block. You can supply more than one, for example:

```yaml
homeassistant:
  auth_providers:
   - type: homeassistant
   - type: legacy_api_password
```

## {% linkable_title Available auth providers %}

### {% linkable_title Home Assistant auth provider %}

This is the default auth provider. The first user created is designated as the _owner_ and can create other users.

User details are stored in the `[your config]/.storage`  directory. All passwords are stored hashed and with a salt, making it almost impossible for an attacker to figure out the password even if they have access to the file.

Users can be managed in Home Assistant by the owner. Go to the configuration panel and click on _Users_.

This is the entry in `configuration.yaml` for Home Assistant auth:

```yaml
homeassistant:
  auth_providers:
   - type: homeassistant
```

If you don't specify any `auth_providers` section in the `configuration.yaml` file then this provider will be set up automatically.

### {% linkable_title Trusted Networks %}

The Trusted Networks auth provider defines a range of IP addresses for which no authentication will be required (also known as "whitelisting"). For example, you can whitelist your local network so you won't be prompted for a password if you access Home Assistant from inside your home.

When you log in from one of these networks, you will be asked which user account to use and won't need to enter a password.

<p class='note info'>
The [multi-factor authentication module](/docs/authentication/multi-factor-auth/) will not participate in the login process if you using this auth provider.
</p>

Here is an example in `configuration.yaml` to set up Trusted Networks:

```yaml
homeassistant:
  auth_providers:
   - type: trusted_networks

# Temporary, this will be moved to be part of auth provider config
# https://github.com/home-assistant/home-assistant/issues/16149
http:
  trusted_networks:
    - 127.0.0.1
    - ::1
    - 192.168.0.0/24
    - fd00::/8
```

### {% linkable_title Legacy API password %}

<p class='note warning'>
This is a legacy feature for backwards compatibility and will be dropped in a future release. You should move to one of the other auth providers.
</p>

Activating this auth provider will allow you to authenticate with the API password set in the HTTP component.

```yaml
homeassistant:
  auth_providers:
   - type: legacy_api_password

http:
  api_password: !secret http_password
```

Activating this auth provider will also allow you to provide the API password using an authentication header to make requests against the Home Assistant API. This feature will be dropped in the future in favor of long-lived access tokens.

<p class='note warning'>
[Issue 16441](https://github.com/home-assistant/home-assistant/issues/16441): the legacy API password auth provider, won't be automatically configured if your API password is located in a package. This is because Home Assistant processes the `auth_provider` during the `core` section loading, which is earlier than the `packages` processing.
</p>
