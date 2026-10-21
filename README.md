# jwt-lesson

## Local PostgreSQL

Set a local database password in PowerShell, then start Docker Desktop and run
from this directory:

```powershell
$env:DB_PASSWORD = "choose-a-local-password"
docker compose up -d --wait
docker compose ps
```

Use these connection settings in the VS Code PostgreSQL extension:

| Setting | Value |
| --- | --- |
| Host | localhost |
| Port | 5432 |
| Database | jwt_lesson |
| Username | postgres |
| Password | The value of `DB_PASSWORD` |
| SSL | Disable (local development) |

The database credentials are for local development only. The application also
requires an admin password, a regular user password, and a JWT signing key. Set
them in the same PowerShell window before running the application:

```powershell
$env:DB_PASSWORD = "choose-the-same-local-password"
$env:ADMIN_PASSWORD = "choose-an-admin-password"
$env:USER_PASSWORD = "choose-a-user-password"
$env:JWT_SECRET = [Convert]::ToBase64String([Security.Cryptography.RandomNumberGenerator]::GetBytes(32))
mvn spring-boot:run
```

Set these values in the same PowerShell window used to run the application.
Use unique passwords and do not commit them or the generated JWT key. The
environment variables are required; the application has no built-in credentials
or signing key.

The HTTP security test requests use `adminPassword` and `userPassword`. For the
VS Code REST Client, create `http/http-client.private.env.json` locally (it is
git-ignored) with your test credentials:

```json
{
  "dev": {
    "adminPassword": "your-admin-password",
    "userPassword": "your-user-password"
  }
}
```

The `security` project uses a different database and credentials. Its Compose
service also binds port 5432, so stop that service before starting this one.

To stop this database while preserving its data:

```powershell
docker compose down
```

Hibernate creates the application tables when the Java application starts;
PostgreSQL must already be running. Keep the same `DB_PASSWORD` value while using
the local database volume.
