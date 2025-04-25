# Learn how to integrate PlanetScale with a sample Laravel application

This sample application demonstrates how to connect to a PlanetScale MySQL database, run migrations, seed the database, and display the data.

For the full tutorial, see the [Laravel PlanetScale documentation](https://planetscale.comhttps://planetscale.com/docs/tutorials/connect-laravel-app).

## Prerequisites

- [PHP](https://www.php.net/manual/en/install.php) &mdash; This tutorial uses `v8.1`
- [Composer](https://getcomposer.org/)
- A [free PlanetScale account](https://auth.planetscale.com/sign-up)
- [PlanetScale CLI](https://github.com/planetscale/cli) &mdash;You can also follow this tutorial using just the PlanetScale admin dashboard, but the CLI will make setup quicker. For dashboard instructions, see [the full tutorial](https://planetscale.comhttps://planetscale.com/docs/tutorials/connect-laravel-app).

## Set up the Laravel app

1. Clone the starter Laravel application:

   ```bash
   git clone https://github.com/planetscale/planetscale-laravel-mysql.git
   ```

2. Enter into the folder and install the dependencies:

   ```bash
   cd planetscale-laravel-mysql
   composer install
   ```

   You may need to run `composer update` if you haven't updated in a while.

3. Copy the `.env.example` file into `.env` and generate the app key:

   ```bash
   cp .env.example .env
   php artisan key:generate
   ```

## Set up the database

If this is your first time in the dashboard, you'll be prompted to [create an organization](https://planetscale.com/docs/onboarding/create-an-organization#creating-a-new-organization) and go through the database creation walkthrough. Otherwise, click "**New database**" > "**Create new database**".

- **Name** — You can use any name with lowercase, alphanumeric characters, or underscores. We also permit dashes, but don't recommend them, as they may need to be escaped in some instances.
- **Region** — Choose the [region](https://planetscale.com/docs/concepts/regions#available-regions) closest to you or your application. It's important to note if you intend to make this branch a production branch, you will not be able to change the region later, so choose the region with this in mind.
- **Storage option** — Choose a storage option. You can choose between network-attached storage or [Metal](/metal) for storage. For more information, see the [plans](plans documentation).
- **Cluster size** — Select the [desired cluster size](https://planetscale.com/docs/concepts/planetscale-plans) for your database.

Finally, click "**Create database**".

## Connect to the Laravel app

First, you need to generate a database username and password so that you can use it to connect to your application.

You'll be presented with this option after creating your database. You can also access the password creation page by clicking "**Connect**" -> "**Create password**".

Click "Laravel" as the framework, then copy the contents of the `.env` tab and paste them into your own `.env` file in your Laravel application. The structure will look like this:

```bash
DB_CONNECTION=mysql
DB_HOST=<ACCESS HOST URL>
DB_PORT=3306
DB_DATABASE=<DATABASE_NAME>
DB_USERNAME=<USERNAME>
DB_PASSWORD=<PASSWORD>
MYSQL_ATTR_SSL_CA=/etc/ssl/cert.pem
```

The `MYSQL_ATTR_SSL_CA` value is platform-dependent. Please refer to our documentation around [how to connect to PlanetScale securely](https://planetscale.com/docs/concepts/secure-connections#ca-root-configuration) for the platform you're using.

## Run migrations and seeder


Laravel uses foreign key constraints by default. PlanetScale, however, has foreign key constraint support turned off by default. For this tutorial, we're keeping the Laravel defaults, so you need to enable [foreign key constraint](https://planetscale.com/docs/concepts/foreign-key-constraints) support in your database settings page. Click the checkbox next to "Allow foreign key constraints" and press "Save database settings".

Let's migrate and seed the database now.

1. In the root of the Laravel project, run the following to migrate and seed the database:

   ```bash
   php artisan migrate --seed
   ```

2. Start the application:

   ```bash
   php artisan serve
   ```

You can view the application at [http://localhost:8000](http://localhost:8000).

3. Refresh your Laravel homepage and you'll see a list of users.

![Laravel PlanetScale starter app homepage](./laravel-users.png)

## Add data manually

If you want to continue to play around with adding data on the fly, you have a few options:

- PlanetScale [dashboard console](https://planetscale.com/docs/concepts/web-console)
- [Laravel Tinker](hhttps://laravel.comhttps://planetscale.com/docs/12.x/artisan#tinker)
- [PlanetScale CLI shell](https://planetscale.com/docs/reference/shell)
- Your favorite MySQL client (for a list of tested MySQL clients, review our article on [how to connect MySQL GUI applications](https://planetscale.com/docs/tutorials/connect-mysql-gui))

The first option is covered below.

### Add data in PlanetScale dashboard console

PlanetScale has a [built-in console](https://planetscale.com/docs/concepts/web-console) where you can run MySQL commands against your branches.

By default, web console access to production branches is disabled to prevent accidental deletion. From your database's dashboard page, click on the "**Settings**" tab, check the box labelled "**Allow web console access to production branches**", and click "**Save database settings**".

To access it, click "**Console**" > select your branch > "**Connect**".

From here, you can run MySQL queries and DDL against your database branch.

1. Add a record to the `users` table:

   ```sql
   UPDATE users
   SET email = 'cyrus@planetscale.com'
   WHERE id=1;
   ```

2. Refresh the Laravel homepage to see the new record. You can also verify it was added in the console with:

   ```sql
   SELECT * FROM users;
   ```

![PlanetScale web console](./laravel-web-console.png)

## What's next?

Once you're done with initial development, you can enable [safe migrations](https://planetscale.com/docs/concepts/safe-migrations) to protect from accidental schema changes and enable zero-downtime deployments.

To learn more about PlanetScale, take a look at the following resources:

- [PlanetScale workflow](https://planetscale.com/docs/concepts/planetscale-workflow) — Quick overview of the PlanetScale workflow: branching, non-blocking schema changes, deploy requests, and reverting a schema change.
- [PlanetScale branching](https://planetscale.com/docs/concepts/branching) — Learn how to utilize branching to ship schema changes with no locking or downtime.
- [PlanetScale CLI](https://planetscale.com/docs/reference/planetscale-cli) — Power up your workflow with the PlanetScale CLI. Every single action you just performed in this quickstart (and much more) can also be done with the CLI.
