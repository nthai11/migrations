# Migration scripts for ReportPortal

## Description

In this repository, you will find the migration scripts for ReportPortal.
These scripts are utilized to update the database schema and generate an index
template for OpenSearch.

## Usage

### Enabling OpenSearch support

If you want to use OpenSearch instead of Elasticsearch, you need to set
the OS_HOST, OS_PORT and OS_PROTOCOL environment variables.

### Update to latest revision

```sh
docker-compose run --rm migrations
```

### Downgrade to previous revision

```sh
docker-compose run --rm migrations down
```

### Downgrade to N revisions back

```sh
docker-compose run --rm migrations down N
```

## Environment variables

| Variable | Description |
|----------|-------------|
|POSTGRES_SSLMODE|SSL mode for Postgres connection|
|POSTGRES_USER|Postgres user|
|POSTGRES_PORT|Postgres port|
|POSTGRES_PASSWORD|Postgres password|
|POSTGRES_SERVER|Postgres server|
|POSTGRES_DB|Postgres database|

## Develop authorization compatibility

Migration `1001_migrate_auth_integrations` moves authentication integrations
to the shared `integration` model and removes the legacy
`oauth_registration*` tables and built-in `ad`, `ldap`, and `saml` integration
types. The current `service-authorization` develop branch still reads those
legacy objects, so migration `1005_restore_legacy_auth_integration_types`
temporarily restores them for the TMS-enabled develop stack.

Do not edit or delete migration `1005` after it has been published. Remove the
compatibility layer with a new forward migration only after all of the
following are true:

- `service-authorization` no longer maps the `oauth_registration*` tables.
- SAML and LDAP configuration no longer looks up the removed built-in types.
- OAuth, SAML, and LDAP configuration is read from the new integration model.
- The root Compose file points to that compatible authorization revision.

The cleanup migration must drop the three legacy tables and delete only the
compatibility integration types restored by migration `1005`.
