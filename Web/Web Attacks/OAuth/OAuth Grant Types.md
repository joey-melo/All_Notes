## OAuth scopes

For any OAuth grant type, the client application has to specify which data it wants to access and what kind of operations it wants to perform. It does this using the `scope` parameter of the authorization request it sends to the OAuth service.

For basic OAuth, the scopes for which a client application can request access are unique to each OAuth service. As the name of the scope is just an arbitrary text string, the format can vary dramatically between providers. Some even use a full URI as the scope name, similar to a REST API endpoint. For example, when requesting read access to a user's contact list, the scope name might take any of the following forms depending on the OAuth service being used:

```
scope=contacts
scope=contacts.read
scope=contact-list-r
scope=https://oauth-authorization-server.com/auth/scopes/user/contacts.readonly
```

## Authorization code grant type


![[oauth-authorization-code-flow.jpg]]

#### 1. Authorization request

The client application sends a request to the OAuth service's `/authorization` endpoint asking for permission to access specific user data. Note that the endpoint mapping may vary between providers - our labs use the endpoint `/auth` for this purpose. However, you should always be able to identify the endpoint based on the parameters used in the request.

```http
GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=code&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1
Host: oauth-authorization-server.com
```

#### 2. User login and consent

When the authorization server receives the initial request, it will redirect the user to a login page, where they will be prompted to log in to their account with the OAuth provider. For example, this is often their social media account.

#### 3. Authorization code grant

If the user consents to the requested access, their browser will be redirected to the `/callback` endpoint that was specified in the `redirect_uri` parameter of the authorization request. The resulting `GET` request will contain the authorization code as a query parameter. Depending on the configuration, it may also send the `state` parameter with the same value as in the authorization request.

```http
GET /callback?code=a1b2c3d4e5f6g7h8&state=ae13d489bd00e3c24 HTTP/1.1
Host: client-app.com
```

#### 4. Access token request

Once the client application receives the authorization code, it needs to exchange it for an access token. To do this, it sends a server-to-server `POST` request to the OAuth service's `/token` endpoint. All communication from this point on takes place in a secure back-channel and, therefore, cannot usually be observed or controlled by an attacker.

```http
POST /token HTTP/1.1
Host: oauth-authorization-server.com

client_id=12345&client_secret=SECRET&redirect_uri=https://client-app.com/callback&grant_type=authorization_code&code=a1b2c3d4e5f6g7h8
```

#### 5. Access token grant

The OAuth service will validate the access token request. If everything is as expected, the server responds by granting the client application an access token with the requested scope.

```json
{
    "access_token": "z0y9x8w7v6u5",
    "token_type": "Bearer",
    "expires_in": 3600,
    "scope": "openid profile"
}
```

#### 6. API call

Now the client application has the access code, it can finally fetch the user's data from the resource server. To do this, it makes an API call to the OAuth service's `/userinfo` endpoint. The access token is submitted in the `Authorization: Bearer` header to prove that the client application has permission to access this data.

```http
GET /userinfo HTTP/1.1
Host: oauth-resource-server.com
Authorization: Bearer z0y9x8w7v6u5
```

#### 7. Resource grant

The resource server should verify that the token is valid and that it belongs to the current client application. If so, it will respond by sending the requested resource i.e. the user's data based on the scope of the access token.

```json
{
    "username":"carlos",
    "email":"carlos@carlos-montoya.net"
}
```

## Implicit grant type

![[oauth-implicit-flow.jpg]]

#### 1. Authorization request

The implicit flow starts in much the same way as the authorization code flow. The only major difference is that the `response_type` parameter must be set to `token`.

```http
GET /authorization?client_id=12345&redirect_uri=https://client-app.com/callback&response_type=token&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1
Host: oauth-authorization-server.com
```

#### 2. User login and consent

The user logs in and decides whether to consent to the requested permissions or not. This process is exactly the same as for the authorization code flow.

#### 3. Access token grant

If the user gives their consent to the requested access, this is where things start to differ. The OAuth service will redirect the user's browser to the `redirect_uri` specified in the authorization request. However, instead of sending a query parameter containing an authorization code, it will send the access token and other token-specific data as a URL fragment.

```http
GET /callback#access_token=z0y9x8w7v6u5&token_type=Bearer&expires_in=5000&scope=openid%20profile&state=ae13d489bd00e3c24 HTTP/1.1
Host: client-app.com
```

#### 4. API call

Once the client application has successfully extracted the access token from the URL fragment, it can use it to make API calls to the OAuth service's `/userinfo` endpoint. Unlike in the authorization code flow, this also happens via the browser.

```http
GET /userinfo HTTP/1.1
Host: oauth-resource-server.com
Authorization: Bearer z0y9x8w7v6u5
```

#### 5. Resource grant

The resource server should verify that the token is valid and that it belongs to the current client application. If so, it will respond by sending the requested resource i.e. the user's data based on the scope associated with the access token.

```json
{
    "username":"carlos",
    "email":"carlos@carlos-montoya.net"
}
```