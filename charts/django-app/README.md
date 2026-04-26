# django-app chart

This chart deploys the Django application and connects it to an external PostgreSQL database such as AWS RDS.

## Homework setup

Keep only non-secret database settings in the GitOps repo:

```yaml
externalDatabase:
	host: "django-db.c3m480c421j3.eu-central-1.rds.amazonaws.com"
	port: "5432"
	name: "app"

secret:
	create: false
	name: django-app-secret
```

Create the Kubernetes Secret manually in the cluster before deploying the chart:

```bash
kubectl create secret generic django-app-secret \
	--from-literal=SECRET_KEY='replace-me' \
	--from-literal=DB_USER='replace-me' \
	--from-literal=DB_PASSWORD='replace-me'
```

The Secret must contain these exact keys:

- `SECRET_KEY`
- `DB_USER`
- `DB_PASSWORD`

Deploy the chart with the non-secret values:

```bash
helm upgrade --install django-app ./charts/django-app \
	--set externalDatabase.host=django-db.c3m480c421j3.eu-central-1.rds.amazonaws.com \
	--set externalDatabase.port=5432 \
	--set externalDatabase.name=app
```

## Optional local mode

If you want Helm to create the Secret for a temporary environment, enable it explicitly and provide the values:

```yaml
secret:
	create: true
	name: django-app-secret
	data:
		SECRET_KEY: "replace-me"
		DB_USER: "replace-me"
		DB_PASSWORD: "replace-me"
```
