# Payload owns the database schema and migrations, not drizzle-kit

Payload's PostgreSQL adapter is built on Drizzle. It generates the schema from our collection definitions, and `payload migrate:create` writes Drizzle-based migration files, which we commit. In production, `prodMigrations` applies them when the app starts. In development, the adapter updates the local database to match the code automatically.

We don't run drizzle-kit ourselves, because two tools managing the same tables would drift apart. When a query needs more than Payload's Local API, we use Drizzle through `payload.db.drizzle`, with types from `payload generate:db-schema`. Custom tables, columns or indexes are added through the adapter's schema hooks, so Payload's migrations include them.
