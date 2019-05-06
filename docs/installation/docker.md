# 将Monica安装到Docker上

可以使用 [Docker](https://www.docker.com) 和
[docker-compose](https://docs.docker.com/compose/) pull 或 build 来运行一个 Monica image。

开始前，你需要获得并编辑一个`.env` 文件。如果你已经克隆了 [Monica Git repo](https://github.com/monicahq/monica)，那么直接运行：

```sh
cp .env.example .env
```

创建 .env。如果没有克隆，可以用下面的命令从 github 上创建一个 .env：

```sh
curl -sS https://raw.githubusercontent.com/monicahq/monica/master/.env.example > .env
```

打开 `.env` 根据需要修改下面的配置：

- 将 `APP_KEY` 设为一个随机的32位字符的字符串。例如，如果你已经安装了 `pwgen`，可以复制粘贴 `pwgen -s 32 1` 的输出。
- 根据你的mailserver编辑 `MAIL_*`
- 编辑 `DB_*`指向你的数据库。如果你不想设置 db prefix，那么设置 `DB_PREFIX=` 而不是 `DB_PREFIX=''`，因为 docker 不会解析空字符串。

Note for macOS: you will need to stop Apache if you wish to have Monica available on port 80.

You can do this like so:

```sh
sudo /usr/sbin/apachectl stop
```

To start Apache up again use this command:

```sh
sudo /usr/sbin/apachectl start
```

现在使用下面的方法快速运行：

#### 使用 docker-compose 运行一个 pre-built image

This is the easiest and fastest way to try Monica! Use this process
if you want to download the newest image from Docker Hub and run it
with a pre-packaged MySQL database.

Start by fetching the latest `docker-compose.yml` and `.env` if you haven't done that already.

```sh
curl -sS https://raw.githubusercontent.com/monicahq/monica/master/docker-compose.yml > docker-compose.yml
curl -sS https://raw.githubusercontent.com/monicahq/monica/master/.env.example > .env
```

Edit the `docker-compose.yml` and change both the volumes on the monicahq service and the mysql service. Change the part before the `:` and point it to an existing, empty directory on your system. It is also be a good idea to change the webserver port from `80:80` to `3000:80`.

Edit `.env` to set `DB_HOST=mysql` (as `mysql` is the creative name of the MySQL container).

Start by downloading all the images and setup your new instance.

```sh
docker-compose pull
docker-compose up
```

Wait until all migrations are done and check if you can open up the login page by going to http://localhost:3000. If this looks ok, shut down the instance and add your first user account.

```sh
docker-compose run monicahq shell
php artisan setup:production
exit
```

Start your instance again with `docker-compose up` and login.

#### Use docker-compose to build and run your own image

Use this process if you want to modify Monica source code and build
your image to run.

Edit `.env` again to set `DB_HOST=mysql` (as `mysql` is the creative name of the MySQL container).

Then run:

```sh
docker-compose build
docker-compose up
```

#### Use Docker directly to run with your own database

Use this process if you're a developer and want complete control over
your Monica container.

Edit `.env` again to set the `DB_*` variables to match your database. Then run:

```sh
docker build -t monicahq/monicahq .
docker run --env-file .env -p 80:80 monicahq/monicahq    # to run MonicaHQ
# ...or...
docker run --env-file .env -it monicahq/monicahq shell   # to get a prompt
```

Note that uploaded files, like avatars, will disappear when you
restart the container. Map a volume to
`/var/www/monica/storage/app/public` if you want that data to persist
between runs. See `docker-compose.yml` for examples.

#### Other documents to read	

[Connecting to MySQL inside of a Docker container](/docs/installation/docker-mysql.md)
[Use mobile app with standalone server](/docs/installation/mobile.md)
