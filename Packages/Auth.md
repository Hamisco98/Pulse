# Authentication and Authorization Mechanisms

## 1. Introduction to Authentication and Authorization
Authentication and Authorization are essential components in building secure applications. Authentication verifies the identity of a user or system, while Authorization determines what actions or resources the authenticated entity can access.

This document provides a comprehensive guide on various mechanisms used for authentication and authorization, catering to beginners, intermediate developers, and professionals. 

---

## 2. Mechanisms Overview

### 2.1 JSON Web Tokens (JWT)
#### **Overview**
JWT is a compact, URL-safe means of representing claims between two parties. It's commonly used for stateless authentication.

#### **Use Case**
Authentication in APIs, microservices, and Single Page Applications (SPAs).

#### **Features**
- Stateless
- Encodes claims (e.g., user roles, permissions) in the token.
- Signed using HMAC or RSA.

#### **Code Example**

```csharp
// Generating JWT in C#
var tokenHandler = new JwtSecurityTokenHandler();
var key = Encoding.ASCII.GetBytes("YourSecretKey");
var tokenDescriptor = new SecurityTokenDescriptor
{
    Subject = new ClaimsIdentity(new[] { new Claim("id", "user1") }),
    Expires = DateTime.UtcNow.AddHours(1),
    SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
};
var token = tokenHandler.CreateToken(tokenDescriptor);
string jwt = tokenHandler.WriteToken(token);
```

---

### 2.2 OAuth 2.0
#### **Overview**
OAuth 2.0 is an authorization framework that allows third-party applications to access resources without exposing user credentials.

#### **Use Case**
Logging in with Google, Facebook, or GitHub.

#### **How it Works**
- **Authorization Code Grant**: Used for web apps.
- **Implicit Grant**: For SPAs.
- **Client Credentials Grant**: For server-to-server communication.

#### **Code Example**

```http
POST /token HTTP/1.1
Host: authserver.com
Content-Type: application/x-www-form-urlencoded

client_id=CLIENT_ID&client_secret=CLIENT_SECRET&grant_type=authorization_code&code=AUTH_CODE&redirect_uri=CALLBACK_URL
```

---

### 2.3 Session-Based Authentication
#### **Overview**
Session-based authentication relies on storing user session data on the server. A session ID is stored in a secure cookie.

#### **Use Case**
Traditional web applications with server-rendered pages.

#### **How it Differs from JWT**
- Requires server-side state.
- More secure for certain use cases due to centralized control.

#### **Code Example**
```csharp
// ASP.NET Core Session Example
services.AddSession();

// Storing data
HttpContext.Session.SetString("User", "User1");

// Retrieving data
var user = HttpContext.Session.GetString("User");
```

---

### 2.4 API Keys
#### **Overview**
API keys are simple tokens issued to a user or application for accessing APIs.

#### **Use Case**
Public APIs, machine-to-machine communication.

#### **Example**
```http
GET /data HTTP/1.1
Authorization: ApiKey 12345
```

---

### 2.5 SAML (Security Assertion Markup Language)
#### **Overview**
SAML is an XML-based protocol for exchanging authentication and authorization data.

#### **Use Case**
Enterprise Single Sign-On (SSO).

#### **Code Example**
Configuration typically involves metadata exchange between Identity Providers (IdPs) and Service Providers (SPs).

---

### 2.6 Paseto (Platform-Agnostic Security Tokens)
#### **Overview**
Paseto is a secure-by-default token format designed to avoid the common pitfalls of JWT.

#### **Use Case**
Authentication where security is a priority.

---

### 2.7 Mutual TLS (mTLS)
#### **Overview**
Uses client certificates to authenticate clients during a TLS handshake.

#### **Use Case**
Financial or healthcare APIs with high-security requirements.

---

### 2.8 OpenID Connect (OIDC)
#### **Overview**
OIDC is an identity layer built on OAuth 2.0, primarily used for user authentication.

#### **Use Case**
Logging into applications with accounts from providers like Google.

---

## 3. Access Tokens vs. Refresh Tokens

### 3.1 Access Tokens
- **Purpose**: Access protected resources.
- **Lifetime**: Short-lived (15 minutes to 1 hour).
- **Format**: Usually JWT.
- **Example Usage**:

```http
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

### 3.2 Refresh Tokens
- **Purpose**: Obtain a new access token.
- **Lifetime**: Long-lived (days or weeks).
- **Storage**: Securely in HTTP-only cookies.

#### Example Workflow:
1. User logs in and receives both tokens.
2. Access token is sent with API requests.
3. Upon expiration, the refresh token is used to get a new access token.

---

## 4. Security Best Practices
- Store tokens securely (e.g., HTTP-only cookies).
- Use HTTPS.
- Validate tokens on the server.
- Rotate and revoke tokens as needed.

---

## 5. Advanced Topics
### 5.1 Token Revocation
- JWTs cannot be revoked once issued; use a token blacklist for server-side control.
- Refresh tokens can be invalidated to prevent further access.

### 5.2 Rotating Refresh Tokens
- Each token refresh returns a new refresh token.
- Enhances security by limiting token reuse.

---