# CORS Configuration Request for Iframe Embedding

Hi Team,

We're working on a white-label demo where we embed the Second Look Health app in an iframe for client branding purposes. We're encountering authentication issues due to cross-origin restrictions when hosting on GitHub Pages (https://deftio.github.io).

## Current Issue
- The iframe loads successfully
- Login attempts fail with 401 errors on `/get-current-user`
- Authentication cookies aren't being sent cross-origin

## Suggested Server-Side Changes

### 1. Update Cookie Settings
For all authentication cookies, please set:
```
Set-Cookie: auth_token=...; SameSite=None; Secure; Path=/
```
- `SameSite=None` allows cookies in cross-origin contexts
- `Secure` is required when using SameSite=None
- This enables cookies to work in iframes

### 2. Add CORS Headers
For API endpoints (especially `/get-current-user` and auth endpoints):
```
Access-Control-Allow-Origin: https://deftio.github.io
Access-Control-Allow-Credentials: true
Access-Control-Allow-Methods: GET, POST, PUT, DELETE, OPTIONS
Access-Control-Allow-Headers: Content-Type, Authorization, X-Requested-With
```

For development/testing, you could temporarily use:
```
Access-Control-Allow-Origin: *
```

### 3. Update X-Frame-Options
Either remove the X-Frame-Options header or set:
```
X-Frame-Options: SAMEORIGIN
```

Or better, use Content-Security-Policy:
```
Content-Security-Policy: frame-ancestors https://deftio.github.io https://*.github.io 'self';
```

### 4. Handle OPTIONS Preflight Requests
Ensure the server responds to OPTIONS requests with:
- 200 OK status
- The CORS headers above
- No authentication required for OPTIONS

## Alternative Solutions

If modifying cookies is problematic:

1. **Token-based auth**: Support authentication via URL parameters or postMessage
2. **Subdomain approach**: Host our wrapper on a subdomain like `demo.secondlookhealth.ai`
3. **Proxy endpoint**: Provide a proxy endpoint that handles auth differently for embedded contexts

## Testing
Once implemented, we can test with:
- https://deftio.github.io/slh-iframe/index2.html (direct iframe embed)
- https://deftio.github.io/slh-iframe/index3.html (popup auth flow)

Let me know if you need any clarification or have questions about the implementation.

Thanks!
Manu