# Debugging

**Generate an admin token for the api service and store it in your lagoon `.env` file:**

```text
AUTH_SSH_ADMIN_TOKEN=<YOUR_TOKEN>
```

**Run all relevant docker containers:**

```text
docker-compose up api auth-ssh auth-server auth-database
```

**Try to connect with a ssh-client:**

```bash
ssh api@localhost -p 2020 login
```

