---
title: 'Tutorial: PHP app with MySQL' 
description: Learn how to get a PHP app working in Azure, with connection to a MySQL database in Azure. Laravel is used in the tutorial.

ms.assetid: 14feb4f3-5095-496e-9a40-690e1414bd73
ms.devlang: php
ms.topic: tutorial
ms.date: 06/13/2022
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli, devdivchpfy22
zone_pivot_groups: app-service-platform-windows-linux
---

# Tutorial: Build a PHP and MySQL app in Azure App Service

::: zone pivot="platform-windows"  

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service using the Windows operating system. This tutorial shows how to create a PHP app in Azure and connect it to a MySQL database. When you're finished, you'll have a [Laravel](https://laravel.com/) app running on Azure App Service on Windows.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md) provides a highly scalable, self-patching web hosting service using the Linux operating system. This tutorial shows how to create a PHP app in Azure and connect it to a MySQL database. When you're finished, you'll have a [Laravel](https://laravel.com/) app running on Azure App Service on Linux.

::: zone-end

:::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Screenshot of the Azure app example titled Task List showing new tasks added.":::

In this tutorial, you learn how to:

> [!div class="checklist"]
> * Create a MySQL database in Azure
> * Connect a PHP app to MySQL
> * Deploy the app to Azure
> * Update the data model and redeploy the app
> * Stream diagnostic logs from Azure
> * Manage the app in the Azure portal

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

To complete this tutorial:

- [Install Git](https://git-scm.com/)
- [Install PHP 7.4](https://php.net/downloads.php)
- [Install Composer](https://getcomposer.org/doc/00-intro.md)
- [Install and start MySQL](https://dev.mysql.com/doc/refman/5.7/en/installing.html)
- <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> to run commands in any shell to provision and configure Azure resources.

## Prepare local MySQL

In this step, you create a database in your local MySQL server for your use in this tutorial.

### Connect to local MySQL server

In a local terminal window, connect to your local MySQL server. You can use this terminal window to run all the commands in this tutorial.

```terminal
mysql -u root -p
```

If you're prompted for a password, enter the password for the `root` account. If you don't remember your root account password, see [MySQL: How to Reset the Root Password](https://dev.mysql.com/doc/refman/5.7/en/resetting-permissions.html).

If your command runs successfully, then your MySQL server is running. If not, ensure that your local MySQL server is started by following the [MySQL post-installation steps](https://dev.mysql.com/doc/refman/5.7/en/postinstallation.html).

### Create a database locally

1. At the `mysql` prompt, create a database.

    ```sql 
    CREATE DATABASE sampledb;
    ```

1. Exit your server connection by typing `quit`.

    ```sql
    quit
    ```

<a name="step2"></a>

## Create a PHP app locally
In this step, you get a Laravel sample application, configure its database connection, and run it locally. 

### Clone the sample

1. `cd` to a working directory.

1. Clone the sample repository and change to the repository root.

    ```terminal
    git clone https://github.com/Azure-Samples/laravel-tasks
    cd laravel-tasks
    ```

1. Install the required packages.

    ```terminal
    composer install
    ```

### Configure MySQL connection

In the repository root, create a file named *.env*. Copy the following variables into the *.env* file. Replace the _&lt;root_password>_ placeholder with the MySQL root user's password.

```txt
APP_ENV=local
APP_DEBUG=true
APP_KEY=

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_DATABASE=sampledb
DB_USERNAME=root
DB_PASSWORD=<root_password>
```

For information on how Laravel uses the _.env_ file, see [Laravel Environment Configuration](https://laravel.com/docs/8.x#environment-based-configuration).

### Run the sample locally

1. Run [Laravel database migrations](https://laravel.com/docs/8.x/migrations) to create the tables the application needs. To see which tables are created in the migrations, look in the _database/migrations_ directory in the Git repository.

    ```terminal
    php artisan migrate
    ```

1. Generate a new Laravel application key.

    ```terminal
    php artisan key:generate
    ```

1. Run the application.

    ```terminal
    php artisan serve
    ```

1. Go to `http://localhost:8000` in a browser. Add a few tasks in the page.

    ![PHP connects successfully to MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

1. To stop PHP, enter `Ctrl + C` in the terminal.

## Deploy Laravel sample to App Service

### Deploy sample code

::: zone pivot="platform-windows"  

1. In the root directory of the respository, add a file called *.deployment*. This file tells App Service to run a custom deployment script during build automation. Copy the following text into it as its content:

    ```
    [config]
    command = bash deploy.sh  
    ```

    > [!NOTE]
    > The deployment process installs [Composer](https://getcomposer.org/) packages at the end. App Service on Windows does not run these automations during default deployment, so this sample repository has two additional files in its root directory to enable it:
    >
    > - `deploy.sh` - The custom deployment script. If you review the file, you see that it runs `php composer.phar install`.
    > - `composer.phar` - The Composer package manager.
    >
    > You can use this approach to add any step to your [Git-based](deploy-local-git.md) or [ZIP](deploy-zip.md) deployment to App Service. For more information, see [Custom Deployment Script](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).
    >
    

::: zone-end

1. From the command line, sign in to Azure using the [`az login`](/cli/azure#az_login) command.

    ```azurecli
    az login
    ```

1. Deploy the code in your local folder using the  [`az webapp up`](/cli/azure/webapp#az_webapp_up) command. Replace *\<app-name>* with a unique name for your app. 

    ::: zone pivot="platform-windows"  
    
    ```azurecli
    az webapp up --resource-group myResourceGroup --name <app-name> --location "West Europe" --sku FREE --runtime "php|7.4" --os-type=windows
    ```
    
    ::: zone-end
    
    ::: zone pivot="platform-linux"
    
    ```azurecli
    az webapp up --resource-group myResourceGroup --name <app-name> --location "West Europe" --sku FREE --runtime "php|7.4" --os-type=linux
    ```
    
    ::: zone-end

    [!include [az webapp up command note](../../includes/app-service-web-az-webapp-up-note.md)]


### Configure Laravel environment variables

Laravel needs an application key in App Service. You can configure it with app settings.

1. Use `php artisan` to generate a new application key without saving it to _.env_.

    ```terminal
    php artisan key:generate --show
    ```

1. Set the application key in the App Service app by using the [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) command. Replace the placeholders _&lt;app-name>_ and _&lt;outputofphpartisankey:generate>_.

    ```azurecli-interactive
    az webapp config appsettings set --settings APP_KEY="<output_of_php_artisan_key:generate>" APP_DEBUG="true"
    ```

    `APP_DEBUG="true"` tells Laravel to return debugging information when the deployed app encounters errors. When running a production application, set it to `false`, which is more secure.

### Set the virtual application path

::: zone pivot="platform-windows"  

Set the virtual application path for the app. This step is required because the [Laravel application lifecycle](https://laravel.com/docs/8.x/lifecycle#lifecycle-overview) begins in the _public_ directory instead of the application's root directory. Other PHP frameworks whose lifecycle start in the root directory can work without manual configuration of the virtual application path.

Set the virtual application path by using the [`az resource update`](/cli/azure/resource#az-resource-update) command. Replace the _&lt;app-name>_ placeholder.

```azurecli-interactive
az resource update --name web --resource-group myResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<app_name> --set properties.virtualApplications[0].physicalPath="site\wwwroot\public" --api-version 2015-06-01
```

By default, Azure App Service points the root virtual application path (_/_) to the root directory of the deployed application files (_sites\wwwroot_).

::: zone-end

::: zone pivot="platform-linux"

[Laravel application lifecycle](https://laravel.com/docs/8.x/lifecycle#lifecycle-overview) begins in the _public_ directory instead of the application's root directory. The default PHP Docker image for App Service uses Apache, and it doesn't let you customize the `DocumentRoot` for Laravel. But you can use `.htaccess` to rewrite all requests to point to _/public_ instead of the root directory. In the repository root, an `.htaccess` is added already for this purpose. With it, your Laravel application is ready to be deployed.

For more information, see [Change site root](configure-language-php.md#change-site-root).

::: zone-end

If you browse to `https://<app-name>.azurewebsites.net` now and see a `Whoops, looks like something went wrong` message, then you have configured your App Service app properly and it's running in Azure. It just doesn't have database connectivity yet. In the next step, you create a MySQL database in [Azure Database for MySQL](../mysql/index.yml).

## Create MySQL in Azure

1. Create a MySQL server in Azure with the [`az mysql server create`](/cli/azure/mysql/server#az-mysql-server-create) command.

    In the following command, substitute a unique server name for the *\<mysql-server-name>* placeholder, a user name for the *\<admin-user>*, and a password for the *\<admin-password>*  placeholder. The server name is used as part of your MySQL endpoint (`https://<mysql-server-name>.mysql.database.azure.com`), so the name needs to be unique across all servers in Azure. For details on selecting MySQL DB SKU, see [Create an Azure Database for MySQL server](../mysql/quickstart-create-mysql-server-database-using-azure-cli.md#create-an-azure-database-for-mysql-server).

    ```azurecli-interactive
    az mysql server create --resource-group myResourceGroup --name <mysql-server-name> --location "West Europe" --admin-user <admin-user> --admin-password <admin-password> --sku-name B_Gen5_1
    ```

1. Create a database called `sampledb` by using the [`az mysql db create`](/cli/azure/mysql/db#az-mysql-db-create) command.

    ```azurecli-interactive
    az mysql db create --resource-group myResourceGroup --server-name <mysql-server-name> --name sampledb    
    ```

## Connect the app to the database

Configure the connection between your app and the SQL database by using the [az webapp connection create mysql](/cli/azure/webapp/connection/create#az-webapp-connection-create-mysql) command. `--target-resource-group` is the resource group that contains the MySQL database.

::: zone pivot="platform-windows"  

```azurecli-interactive
az webapp connection create mysql --resource-group myResourceGroup --name <app-name> --target-resource-group myResourceGroup --server <mysql-server-name> --database sampledb --connection my_laravel_db --client-type php
```

::: zone-end

::: zone pivot="platform-linux"  

```azurecli-interactive
az webapp connection create mysql --resource-group myResourceGroup --name <app-name> --target-resource-group myResourceGroup --server <mysql-server-name> --database sampledb --connection my_laravel_db
```

::: zone-end

When prompted, provide the administrator username and password for the MySQL database. 

> [!NOTE]
> The CLI command does everything the app needs to successfully connect to the database, including:
>
> - In your App Service app, adds [six app settings](../service-connector/how-to-integrate-mysql.md#php-mysqli-secret--connection-string) with the names `AZURE_MYSQL_<setting>`, which your code can use for its database connection. If the app setting names are already in use, the `AZURE_MYSQL_<connection-name>_<setting>` format is used instead.
> - In your MySQL database server, allows Azure services to access the MySQL database server.

## Generate the database schema

<a name="devconfig"></a>

1. Allow access to the Azure database from your local computer by using the [az mysql server firewall-rule create](/cli/azure/mysql/server/firewall-rule#az-mysql-server-firewall-rule-create) and replacing *\<your-ip-address>* with [your local IPv4 IP address](https://www.whatsmyip.org/).

    ```azurecli-interactive
    az mysql server firewall-rule create --name AllowLocalClient --server <mysql-server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
    ```

    > [!TIP]
    > Once the firewall rule for your local computer is enabled, you can connect to the server like any MySQL server with the `mysql` client. For example:
    > ```terminal
    > mysql -u <admin-user>@<mysql-server-name> -h <mysql-server-name>.mysql.database.azure.com -P 3306 -p
    > ```

1. Generate the environment variables from the [service connector you created earlier](#connect-the-app-to-the-database) by running the [`az webapp connection list-configuration`](/cli/azure/webapp/connection/create#az-webapp-connection-create-mysql) command.

    ::: zone pivot="platform-windows"  

    ```powershell
    $Settings = az webapp connection list-configuration --resource-group myResourceGroup --name <app-name> --connection my_laravel_db --query configurations | ConvertFrom-Json
    foreach ($s in $Settings) { New-Item -Path Env:$($s.name) -Value $s.value}
    ```
    
    > [!TIP]
    > These commands are equivalent to setting the database variables manually like this:
    >
    > ```powershell
    > New-Item -Path Env:AZURE_MYSQL_DBNAME -Value ...
    > New-Item -Path Env:AZURE_MYSQL_HOST -Value ...
    > New-Item -Path Env:AZURE_MYSQL_PORT -Value ...
    > New-Item -Path Env:AZURE_MYSQL_FLAG -Value ...
    > New-Item -Path Env:AZURE_MYSQL_USERNAME -Value ...
    > New-Item -Path Env:AZURE_MYSQL_PASSWORD -Value ...
    > ``` 
    ::: zone-end
    
    ::: zone pivot="platform-linux"
    
    ```bash
    export $(az webapp connection list-configuration --resource-group myResourceGroup --name <app-name> --connection my_laravel_db --query "configurations[].[name,value] | [*].join('=',@)" --output tsv)
    ```

    > [!TIP]
    > The [JMESPath query](https://jmespath.org/) in `--query` and the `--output tsv` formatting let you feed the output directly into the `export` command. It's equivalent to setting the database variables manually like this:
    >
    > ```powershell
    > export AZURE_MYSQL_DBNAME=...
    > export AZURE_MYSQL_HOST=...
    > export AZURE_MYSQL_PORT=...
    > export AZURE_MYSQL_FLAG=...
    > export AZURE_MYSQL_USERNAME=...
    > export AZURE_MYSQL_PASSWORD=...
    > ``` 

    <!--     export $(az webapp connection list-configuration -g myResourceGroup -n <app-name> --connection my-laravel-db | jq -r '.configurations[] | "\(.name)=\(.value)"')
     -->    
    ::: zone-end

1. Open _config/database.php_ and find the `mysql` section. It's already set up to retrieve connection secrets from environment variables.

    ```php
    'mysql' => [
        'driver'    => 'mysql',
        'url' => env('DATABASE_URL'),
        'host' => env('DB_HOST', '127.0.0.1'),
        'port' => env('DB_PORT', '3306'),
        'database'  => env('DB_DATABASE', 'forge'),
        'username'  => env('DB_USERNAME', 'forge'),
        'password'  => env('DB_PASSWORD', ''),
        ...
    ],
    ```

    Change the default environment variables to the ones that the service connector created:

    ```php
    'mysql' => [
        'driver' => 'mysql',
        'url' => env('DATABASE_URL'),
        'host' => env('AZURE_MYSQL_HOST', '127.0.0.1'),
        'port' => env('AZURE_MYSQL_PORT', '3306'),
        'database' => env('AZURE_MYSQL_DBNAME', 'forge'),
        'username' => env('AZURE_MYSQL_USERNAME', 'forge'),
        'password' => env('AZURE_MYSQL_PASSWORD', ''),
        ...
    ],
    ```

    > [!TIP]
    > PHP uses the [getenv](https://www.php.net/manual/en/function.getenv.php) method to access the settings. the Laravel code uses an [env](https://laravel.com/docs/8.x/helpers#method-env) wrapper over the PHP `getenv`. 

1. By default, Azure Database for MySQL enforces TLS connections from clients. To connect to your MySQL database in Azure, you must use the [_.pem_ certificate supplied by Azure Database for MySQL](../mysql/single-server/how-to-configure-ssl.md). The certificate `BaltimoreCyberTrustRoot.crt.pem` is provided in the sample repository for convenience in this tutorial. At the bottom of the `mysql` section in _config/database.php_, change the `options` parameter to the following code:

    ```php
    'options' => extension_loaded('pdo_mysql') ? array_filter([
        PDO::MYSQL_ATTR_SSL_KEY    => '/ssl/BaltimoreCyberTrustRoot.crt.pem',
    ]) : [],
    ```

1. Your sample app is now configured to connect to the Azure MySQL database. Run Laravel database migrations again to create the tables and run the sample app.

    ```bash
    php artisan migrate
    php artisan serve
    ```

1. Go to `http://localhost:8000`. If the page loads without errors, the PHP application is connecting to the MySQL database in Azure.

1. Add a few tasks in the page.

    ![PHP connects successfully to Azure Database for MySQL](./media/tutorial-php-mysql-app/mysql-connect-success.png)

1. To stop PHP, enter `Ctrl + C` in the terminal.

## Deploy changes to Azure

1. Deploy your code changes by running `az webapp up` again.

    ::: zone pivot="platform-windows"  
    
    ```azurecli
    az webapp up --os-type=windows
    ```
    
    ::: zone-end
    
    ::: zone pivot="platform-linux"
    
    ```azurecli
    az webapp up --runtime "php|7.4" --os-type=linux
    ```
    
    > [!NOTE]
    > `--runtime` is still needed for deployment with `az webapp up`. Otherwise, the runtime is detected to be Node.js due to the presence of *package.json*.

    ::: zone-end

1. Browse to `http://<app-name>.azurewebsites.net` and add a few tasks to the list.

    :::image type="content" source="./media/tutorial-php-mysql-app/php-mysql-in-azure.png" alt-text="Screenshot of the Azure app example titled Task List showing new tasks added.":::

Congratulations, you're running a data-driven PHP app in Azure App Service.

## Stream diagnostic logs

While the PHP application runs in Azure App Service, you can get the console logs piped to your terminal. That way, you can get the same diagnostic messages to help you debug application errors.

To start log streaming, use the [`az webapp log tail`](/cli/azure/webapp/log#az-webapp-log-tail) command.

```azurecli-interactive
az webapp log tail
```

Once log streaming has started, refresh the Azure app in the browser to get some web traffic. You can now see console logs piped to the terminal. If you don't see console logs immediately, check again in 30 seconds.

To stop log streaming at any time, enter `Ctrl`+`C`.

::: zone pivot="platform-linux"

> [!NOTE]
> You can also inspect the log files from the browser at `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

::: zone-end

> [!TIP]
> A PHP application can use the standard [error_log()](https://php.net/manual/function.error-log.php) to output to the console. The sample application uses this approach in _app/Http/routes.php_.
>
> As a web framework, [Laravel uses Monolog](https://laravel.com/docs/8.x/logging) as the logging provider. To see how to get Monolog to output messages to the console, see [PHP: How to use monolog to log to console (php://out)](https://stackoverflow.com/questions/25787258/php-how-to-use-monolog-to-log-to-console-php-out).
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>

## Next steps

In this tutorial, you learned how to:

> [!div class="checklist"]
> * Create a MySQL database in Azure
> * Connect a PHP app to MySQL
> * Deploy the app to Azure
> * Update the data model and redeploy the app
> * Stream diagnostic logs from Azure
> * Manage the app in the Azure portal

Advance to the next tutorial to learn how to map a custom DNS name to the app.

> [!div class="nextstepaction"]
> [Tutorial: Map custom DNS name to your app](app-service-web-tutorial-custom-domain.md)

Or, check out other resources:

> [!div class="nextstepaction"]
> [Configure PHP app](configure-language-php.md)
