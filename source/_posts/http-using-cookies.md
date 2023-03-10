---
title: http using cookies
date: 2023-03-06 09:09:35
tags:
  - http
  - cookies
categories: http
---

An HTTP cookie (web cookie, browser cookie) is a small piece of data that a server sends to a user's web browser. The browser may store the cookie and send it back to the same server with later request. Typically, an HTTP cookie is used to tell if two requests come from the same browser -- keeping a user logged in, for example. It remembers the stateful information for the stateless HTTP protocol.

Cookies are mainly used for three purposes:

1. Session management: logins, shopping carts, game scores, or anything else the server should remember.

2. Personalization: user preferences, themes, and other settings.

3. Tracking: recording and analyzing user behavior.

<!-- more -->

## Creating cookies

After receiving an HTTP request, a server can send one or more `Set-Cookie` headers with the response. The browser usually stores the cookie and sends it with the requests made to the same server inside a `Cookie` HTTP header.

### The `Set-Cookie` and `Cookie` headers

The `Set-Cookie` HTTP response header sends cookies from the server to the user agent. A simple cookie is set like this:

```http
Set-Cookie: <cookie-name>=<cookie-value>
```

This instructs the server sending headers to tell the client to store a pair of cookies:

```http
HTTP/2.0 200 OK
Content-Type: text/html
Set-Cookie: yummy_cookie=choco
Set-Cookie: tasty_cookie=strawberry

[page content]
```

Then, with every subsequent request to the server, the browser sends all previously stored cookies back to the server using the `Cookie` header.

```http
GET /sample_page.html HTTP/2.0
Host: www.example.org
Cookie: yummy_cookie=choco; tasty_cookie=strawberry
```

## `Set-Cookie`

### Syntax

```http
Set-Cookie: <cookie-name>=<cookie-value>
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>
Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date>
Set-Cookie: <cookie-name>=<cookie-value>; HttpOnly
Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<number>
Set-Cookie: <cookie-name>=<cookie-value>; Partitioned
Set-Cookie: <cookie-name>=<cookie-value>; Path=<path-value>
Set-Cookie: <cookie-name>=<cookie-value>; Secure

Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Strict
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=Lax
Set-Cookie: <cookie-name>=<cookie-value>; SameSite=None; Secure

// Multiple attributes are also possible, for example:
Set-Cookie: <cookie-name>=<cookie-value>; Domain=<domain-value>; Secure; HttpOnly
```

### Attributes

- `<cookie-name>=<cookie-value>`

  Defines the cookie name and its value. A cookie definition begins with a name-value pair.

  A `<cookie-name>` can contain any US-ASCII characters except for: the control character, space, or a tab. It also must not contain separator characters like the following: `( ) < > @ , ; : \ " / [ ] ? = { }`.

  A `<cookie-value>` can optionally be wrapped in double quotes and include any US-ASCII character excluding a control character, Whitespace, double quotes, comma, simicolon, and backslash.

  Encoding: Many implementations perform URL encoding on cookie values. However, this is not required by the RFC specification. The URL encoding does help to satisfy the requirement of the character allowed for `<cookie-value>`.

  > Note: Some `<cookie-name>` have a specific semantic:
  >
  > `__Secure-` prefix: Cookies with names starting with `__Secure-` (dash is part of the prefix) must be set with the `secure` flag from a secure page (HTTPS).
  >
  > `__Host-` prefix: Cookies with names starting with `__Host-` must be set with the `secure` flag, must be from a secure page (HTTPS), must not have a domin specified (and therefore, are not sent to subdomains), and the path must be `/`.

- `domain=<domain-value>` (Optional)

  Defines the host to which the cookie will be sent.

  Only the current domain can be set as the value, or a domain of a higher order, unless it is a public suffix. Setting the domain will make the cookie available to it, as well as to all its subdomains.

  If omitted, this attribute defaults to the hosts of the current document URL, not including subdomains.

  Contrary to earlier specifications, leading dots in domain names (`.example.com`) are ignored.

  Multiple host/domain values are not allowed, but if a domain is specified, then subdomains are always included.

- `Expires=<date>` (Optional)

  Indicates the maximum lifetime of the cookie as an HTTP-date timestamp. See [`Date`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Date) for the required formatting.

  If unspecified, the cookie becomes a session cookie. A session finishes when the client shuts down, after which the session cookie is removed.

  > Warning: Many web browsers have a session restore feature that will save all tabs and restore them the next time the browser is used. Session cookies will also be restored, as if the browser was never closed.

  When an `Expires` date is set, the deadline is relative to the client the cookie is being set on, not the server.

- `HttpOnly` (Optional)

  Forbids Javascript from accessing the cookie, for example, through the `Document.cookie` property. Note that a cookie that has been created with `HttpOnly` will still be sent with JavaScript-initiated requests, for example, when calling `XMLHttpRequest.send()` or `fetch()`. This mitigates attacks against cross-site scripting.

- `Max-Age=<number>` (Optional)

  Indicates the number of seconds until the cookie expires. A zero or negative number will expire the cookie immediately. If both `Expires` and `Max-Age` are set, `Max-Age` has precedence.

- `Partitioned` (Optional, Experimental)

  Indicates that the cookie should be stored using partitioned storage. See [Cookies Having Independent Partitioned State (CHIPS)](https://developer.mozilla.org/en-US/docs/Web/Privacy/Partitioned_cookies) for more details.

- `Path=<path-value>` (Optional)

  Indicates the path that must exist in the requested URL for the browser to send the `Cookie` header.

  The forward slash (`/`) character is interpreted as directory seperator, and subdirectories are matched as well. For example, for `Path=/docs`:

  - the request paths `/docs`, `/docs/`, `/docs/Web/`, `/docs/Web/HTTP` will all match.

  - the request paths `/`, `/docsets`, `/fr/docs` will not match.

- `SameSite=<samesite-value>` (Optional)

  Controls whether or not a cookie is sent with cross-site requests, providing some protection against cross-site request forgery attacks(CSRF).

  The possible attribute values are:

  `Strict`

  means that the browser sends the cookie only for same-site requests, that is, requests originating from the same-site that set the cookie. If a request originate from a different domain or scheme (even with the same domain), no cookies with the `SameSite=Strict` attribute are sent.

  `Lax`

  means that the cookie is not sent on cross-site requests, such as on requests to load images or frames, but is sent when a user is navigating to the origin site from an external site (for example, when following a link). This is the default behavior if the `SameSite` attribute is not specified.

  `None`

  means that the browser sends the cookie with both cross-site and same-site requests. The `Secure` attribute must also be set when setting this value, like so `SameSite=None; Secure`.

  > Note: Standards related to the [SameSite Cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite) recently changed, such that:
  >
  > 1. The cookie-sending behavior if `SameSite` is not specified is `SameSite=Lax`. Previously, cookies were sent for all requests by default (`SameSite=None`).
  >
  > 2. Cookies with `SameSite=None` must now also specify the `Secure` attribute (in other words, they require a secure context).
  >
  > 3. Cookies from the same domain no longer considered to be from the same site if sent using a different scheme (`http:` or `https:`).

- `Secure` (Optional)

  Indicates that the cookie will sent to server only when a request is made with the `https:` scheme (**except on localhost**), and therefore, is more resistant to man-in-the-middle attacks.

  > Note: Do not assume that `Secure` prevents all access to sensitive information in cookies (session keys, login details, etc.). Cookies with this attribute can still be read/modified either with access to the client's hard disk or from JavaScript if the `HttpOnly` cookie attribute is not set.
  >
  > Insecure sites (`http:`) cannot set cookies with the `Secure` attribute (since Chrome 52 and Firefox 52). For Firefox, the `https:` requirements are ignored when the `Secure` attribute is set by localhost (since Firefox 75).

### Examples

#### Session Cookie

Session Cookies are removed when the client shuts down. Cookies are session cookies if they do not specify the `Expires` or `Max-Age` attribute.

```http
Set-Cookie: sessionId=38afes7a8
```

#### Permanent Cookie

Permanent cookies are removed at a specific date (`Expires`) or after a spcific length of time (`Max-Age`) and not when the client is closed.

```http
Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT
```

```http
Set-Cookie: id=a3fWa; Max-Age=2592000
```

#### Invalid domains

A cookie for a domain that does not include the server that set it should be reject by the user agent.

The following cookie will be rejected if set by a server hosted on `originalcompany.com`:

```http
Set-Cookie: qwerty=219ffwef9w0f; Domain=somecompany.co.uk
```

A cookie for a subdomain of the serving domain will be rejected.

The following cookie will be rejected if set by a server hosted on `example.com`:

```http
Set-Cookie: sessionId=e8bb43229de9; Domain=foo.example.com
```

#### Cookie prefixes

Cookie names prefixed with `__Secure-` or `__Host-` can be used only if they are set with `Secure` attribute from a secure (HTTPS) origin.

In addition, cookies with the `__Host-` prefix must have a path of `/` (meaning any path at the host) and must not have a `Domain` attribute.

> Warning: For clients that don't implement cookie prefixes, you can not count on these additional assurances, and prefixed cookies will always be accepted.

```http
// Both accepted when from a secure origin (HTTPS)
Set-Cookie: __Secure-ID=123; Secure; Domain=example.com
Set-Cookie: __Host-ID=123; Secure; Path=/

// Rejected due to missing Secure attribute
Set-Cookie: __Secure-id=1

// Rejected due to the missing Path=/ attribute
Set-Cookie: __Host-id=1; Secure

// Rejected due to setting domain
Set-Cookie: __Host-id=1; Secure; Path=/; Domain=example.com
```

#### Partitioned cookie

```http
Set-Cookie: __Host-example=34d8g; SameSite=None; Secure; Path=/; Partitioned
```

> Note: Partitioned cookie must be set with `Secure` and `Path=/`. In addition, it is recommended to use the `__Host-` prefix when setting partitioned cookies to make them bound to the hostname and not the registrable domain.

> References
>
> [MDN-Using HTTP cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Cookies)
>
> [MDN-Set-Cookie](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie)
>
> [MDN-SameSite cookies](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Set-Cookie/SameSite)
