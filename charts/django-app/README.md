# django-app chart

This chart deploys the Django application and connects it to an external PostgreSQL database such as AWS RDS.

## Required values

Set these values after the infrastructure repo creates the RDS instance:

- `externalDatabase.host`: use the Terraform `db_endpoint` output.
- `externalDatabase.port`: PostgreSQL port, usually `5432`.
- `externalDatabase.name`: application database name.

Sensitive values must come from a Kubernetes Secret exposed to the container as `SECRET_KEY`, `DB_USER`, and `DB_PASSWORD`.

By default, the chart renders a Secret named `django-app-secret`. Supply its values with Helm overrides or a values file:

```yaml
secret:
  create: true
  name: django-app-secret
  data:
    SECRET_KEY: "replace-me"
    DB_USER: "replace-me"
    DB_PASSWORD: "replace-me"
```

If you already manage the Secret outside this chart, disable secret creation and point the Deployment at the existing Secret name:

```yaml
secret:
  create: false
  name: django-app-secret
```

The existing Secret must contain these exact keys:

- `SECRET_KEY`
- `DB_USER`
- `DB_PASSWORD`

## Example

```bash
helm upgrade --install django-app ./charts/django-app \
  --set externalDatabase.host=mydb.abcdefghijkl.eu-central-1.rds.amazonaws.com \
  --set externalDatabase.port=5432 \
  --set externalDatabase.name=app_db \
  --set secret.data.SECRET_KEY=replace-me \
  --set secret.data.DB_USER=replace-me \
  --set secret.data.DB_PASSWORD=replace-me
```