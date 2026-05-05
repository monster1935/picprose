# About

This is a repo about postermaker. You can use it to make your poster especialy when you write your blogs.

Thanks for gezhaoyou/picprose. Your can just check the code from [gezhaoyou/picprose](https://github.com/gezhaoyou/picprose).

## Manual deployment

Build locally, upload the deploy package to the server, then restart the app with PM2.

### Local build

```bash
npm ci
npm run build

tar --exclude='.next/cache' -czf deploy.tar.gz \
	.next \
	public \
	package.json \
	package-lock.json \
	next.config.mjs \
	i18n.ts \
	middleware.ts \
	locales \
	tsconfig.json
```

Upload `deploy.tar.gz` to the server deployment directory:

```text
/www/picprose/deploy.tar.gz
```

### Server deploy

```bash
cd /www/picprose

cp .env.local /tmp/picprose.env.local.bak 2>/dev/null || true

tar -xzf deploy.tar.gz
rm -f deploy.tar.gz

mv /tmp/picprose.env.local.bak .env.local 2>/dev/null || true

npm ci --omit=dev

if pm2 describe picprose > /dev/null 2>&1; then
	pm2 reload picprose --update-env
else
	PORT=3000 pm2 start npm --name picprose -- run start
fi

pm2 save
```

Runtime environment variables should be stored on the server in:

```text
/www/picprose/.env.local
```

`NEXT_PUBLIC_*` variables are embedded into the browser bundle during `npm run build`. For GitHub Actions deployments, add `NEXT_PUBLIC_UNSPLASH_API_KEY` to repository Actions secrets so the CI build can include it.
