# Waline on Netlify

This repository deploys the `comment` function for `review-setting.netlify.app`.

## Runtime configuration

Set these environment variables in the Netlify project UI with the Functions
scope, then redeploy:

- `NODE_OPTIONS=--experimental-require-module`: AWS Lambda disables this Node
  feature by default; Waline 1.43.4 requires it for its ESM dependencies.
- `PG_HOST`: Neon connection hostname (the pooled endpoint is supported).
- `PG_PORT`: the port from the Neon connection settings.
- `PG_DB`: database name.
- `PG_USER`: database role.
- `PG_PASSWORD`: database password; store as a secret, never in Git.
- `PG_SSL`: enable SSL for the Neon connection.

Initialize an empty database using the official
[Waline PostgreSQL schema](https://github.com/walinejs/waline/blob/main/assets/waline.pgsql).
The tables are `wl_comment`, `wl_counter`, and `wl_users`.

## URLs

- Client `serverURL`: `https://review-setting.netlify.app/.netlify/functions/comment`
- Management: append `/ui`.
- Comments API: append `/api/comment?path=%2F`.
- View count API: append `/api/article?path=%2F`.

The `external_node_modules` and `included_files` settings in `netlify.toml`
preserve Waline/ThinkJS files loaded from disk at runtime. Bundling Waline into
one esbuild file silently loses its middleware and controllers and produces
plain-text `404 Not Found`. Source maps are excluded to keep the function
within Lambda's uncompressed package size limit.
