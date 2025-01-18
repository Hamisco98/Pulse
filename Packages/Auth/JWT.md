# JWT Authentication in ASP.NET Core

This guide covers the implementation and customization of JWT authentication in an ASP.NET Core project.

---

## 1. Install the Required Packages

Ensure you have the necessary NuGet packages installed for working with JWT:

- **Microsoft.AspNetCore.Authentication.JwtBearer**: Handles JWT authentication.
- **Microsoft.IdentityModel.Tokens**: Provides tools for signing and validating tokens.
- **System.IdentityModel.Tokens.Jwt**: Generates and reads JWT tokens.

Install these packages using the .NET CLI:

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```
```bash
dotnet add package Microsoft.IdentityModel.Tokens
```
```bash
dotnet add package System.IdentityModel.Tokens.Jwt
```

---

## 2. Configure JWT in the `Program.cs` Class

Set up JWT authentication in your `Program.cs` (or `Startup.cs` if using an older structure).

### Define JWT Options

- **Key**: The secret key used for signing tokens.
- **Issuer** and **Audience**: To validate the token’s origin.

Example:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// JWT Configuration
var key = Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]);
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = builder.Configuration["Jwt:Issuer"],
        ValidAudience = builder.Configuration["Jwt:Audience"],
        IssuerSigningKey = new SymmetricSecurityKey(key)
    };
});

builder.Services.AddAuthorization();
```

---

## 3. Validate the Logged-In User

Before generating a token, validate the user’s credentials (e.g., username and password). This can be done by:

1. Querying the database for the user.
2. Verifying the password hash.

Example:

```csharp
if (username == "admin" && password == "password") // Replace with actual validation
{
    // User is valid, proceed to generate a token
}
else
{
    // Return unauthorized response
    return Unauthorized();
}
```

---

## 4. Generate the JWT Token

Once the user is validated, generate a JWT token that includes claims about the user.

Example:

```csharp
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;

var claims = new[]
{
    new Claim(JwtRegisteredClaimNames.Sub, userId),
    new Claim(ClaimTypes.Role, "Admin"), // Add roles or other claims
    new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString())
};

var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"]));
var creds = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

var token = new JwtSecurityToken(
    issuer: builder.Configuration["Jwt:Issuer"],
    audience: builder.Configuration["Jwt:Audience"],
    claims: claims,
    expires: DateTime.UtcNow.AddHours(1),
    signingCredentials: creds
);

var tokenString = new JwtSecurityTokenHandler().WriteToken(token);
```

---

## 5. Token Validation Parameters

In the `AddJwtBearer` method, the `TokenValidationParameters` object allows customization of JWT authentication behavior.

### Core Token Validation Parameters

| Parameter                     | Description                                           | Default Value |
|-------------------------------|-------------------------------------------------------|---------------|
| `ValidateIssuer`              | Ensures the token was issued by a trusted issuer.    | `false`       |
| `ValidIssuer`                 | The expected issuer.                                 | N/A           |
| `ValidateAudience`            | Ensures the token is for a trusted audience.         | `false`       |
| `ValidAudience`               | The expected audience.                               | N/A           |
| `ValidateIssuerSigningKey`    | Ensures the signature matches the signing key.       | `false`       |
| `IssuerSigningKey`            | The key used to sign tokens.                         | N/A           |
| `ValidateLifetime`            | Ensures the token has not expired.                   | `false`       |
| `ClockSkew`                   | Allowed clock drift for expiration validation.       | 5 minutes     |

### Optional Token Parameters

| Parameter                   | Description                                         | Default Value |
|-----------------------------|-----------------------------------------------------|---------------|
| `RequireExpirationTime`     | Ensures the token includes an `exp` claim.         | `true`        |
| `RequireSignedTokens`       | Ensures that the token is signed.                  | `true`        |
| `ValidateActor`             | Ensures the token includes a valid `act` claim.    | `false`       |
| `RoleClaimType`             | Specifies the claim type for roles.                | `role`        |
| `NameClaimType`             | Specifies the claim type for the user's name.      | `name`        |
| `SaveSigninToken`           | Saves the token for later reference.               | `false`       |

### Advanced Options

| Parameter                   | Description                                         |
|-----------------------------|-----------------------------------------------------|
| `ValidAlgorithms`           | Specifies the allowed algorithms for signing.      |
| `TokenDecryptionKey`        | Specifies the key to decrypt encrypted tokens.     |
| `IssuerSigningKeyResolver`  | Custom mechanism to resolve signing keys.          |
| `AudienceValidator`         | Custom logic for audience validation.              |
| `IssuerValidator`           | Custom logic for issuer validation.                |

---

## 6. Example Configuration

Here’s an example of a complete configuration:

```csharp
options.TokenValidationParameters = new TokenValidationParameters
{
    ValidateIssuer = true,
    ValidateAudience = true,
    ValidateLifetime = true,
    ValidateIssuerSigningKey = true,
    ValidIssuer = builder.Configuration["Jwt:Issuer"],
    ValidAudience = builder.Configuration["Jwt:Audience"],
    IssuerSigningKey = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(builder.Configuration["Jwt:Key"])),
    ClockSkew = TimeSpan.Zero,
    RoleClaimType = ClaimTypes.Role,
    NameClaimType = ClaimTypes.Name,
    RequireExpirationTime = true,
    RequireSignedTokens = true
};
```

---

## 7. Common Scenarios

### Disable Audience Validation (e.g., Single-Tenant Apps)

```csharp
ValidateAudience = false
```

### Allow Clock Drift (e.g., Mobile Clients)

```csharp
ClockSkew = TimeSpan.FromMinutes(2)
```

### Custom Token Validation Logic

Use `IssuerValidator` or `AudienceValidator` for custom rules:

```csharp
IssuerValidator = (issuer, securityToken, validationParameters) =>
{
    return issuer == "ExpectedIssuer" ? issuer : throw new SecurityTokenInvalidIssuerException();
};
```

---

This document serves as a comprehensive reference for implementing and customizing JWT authentication in ASP.NET Core. If you need help with a specific step, feel free to ask!
