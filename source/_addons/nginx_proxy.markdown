---
layout: page
title: "NGINX SSL proxy"
description: "NGINX Home Assistant SSL proxy."
date: 2017-04-30 13:28
sidebar: true
comments: false
sharing: true
footer: true
---

Sets up an SSL proxy with NGINX and redirect port 80 to 443. Make sure you have generated a certificate before you start this add-on.

In the `http` section of the `configuration.yaml` file remove `ssl_certificate` and `ssl_key` and don't enter the port in the `base_url` to avoid an HTTP 502 error.

```json
{
  "domain": "home.example.com",
  "certfile": "fullchain.pem",
  "keyfile": "privkey.pem",
  "hsts": "max-age=31536000; includeSubDomains",
  "customize": {
    "active": false,
    "default": "nginx_proxy_default*.conf",
    "servers": "nginx_proxy/*.conf"
  }
}
```

{% configuration %}
domain:
  description: The Domain to use for the proxy.
  required: true
  type: string
certfile:
  description: The certificate file to use in the `/ssl` directory.
  required: true
  type: string
keyfile:
  description: Private key file to use in the `/ssl` directory.
  required: true
  type: string
hsts:
  description: Value for the [`Strict-Transport-Security`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Strict-Transport-Security) HTTP header to send. If empty or `null`, the header is not sent.
  required: false
  type: string
customize:
  description: If true, additional NGINX configuration files for the default server and additional servers are read from files in the `/share` directory specified by the `default` and `servers` variables.
  required: false
  type: boolean
  default: false
{% endconfiguration %}

<p class='note'>
It is possible to deactivate port 80 if you need this for things like `emulated_hue`. Remove the host port from Network option of this add-on.
</p>
