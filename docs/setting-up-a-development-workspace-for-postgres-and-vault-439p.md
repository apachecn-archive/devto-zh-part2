# 为 Postgres 和 vault 设置开发工作区

> 原文：<https://dev.to/baens/setting-up-a-development-workspace-for-postgres-and-vault-439p>

*这篇文章最初是在[blog.baens.net](https://blog.baens.net/posts/postgres-vault-dev-workspace/)T3 发表在我的博客上的*

我目前的工作使我开始越来越多地接触 docker。我一直在尝试的一件有趣的事情是使用 [Postgres](https://www.postgresql.org/) 作为我的数据库，使用 [Vault](https://www.vaultproject.io/) 进行秘密管理。我认为是时候展示一个基本的开发工作区是什么样子了，以帮助人们快速启动和运行。

我希望能把它归结为使事情运转所需的最小部分。这允许一个项目的新开发人员运行`docker-compose up`，这将带来一个工作开发环境。

我将首先指导您完成一些需要的设置脚本，然后将它们一起放在`docker-compose.yml`文件中。

# 使用 SSL 设置 Postgres

我必须满足的一个需求是，我需要一个打开 SSL 的运行 Postgres 实例。为了创建所需的 SSL 文件，我创建了这个脚本来创建所有必需的文件:

```
#!/bin/bash
# Creates SSL certificates need for postgres

OUTPUT_FOLDER="$(dirname "$0")/../run/ssl-files"

mkdir -p $OUTPUT_FOLDER

OUTPUT_FOLDER="$(cd "$(dirname "$OUTPUT_FOLDER")"; pwd)/$(basename "$OUTPUT_FOLDER")"

echo "SSL files output folder [$OUTPUT_FOLDER]"

rm -rf $OUTPUT_FOLDER/*

OPENSSL_COMMAND="docker run --rm -it -w /out -v $OUTPUT_FOLDER:/out svagi/openssl"

echo -e "\n\n### Create CA ###"
$OPENSSL_COMMAND req -new -x509 -nodes -newkey rsa -out ca.pem -keyout ca-key.pem -set_serial 01 -batch -subj "/CN=ssl-server"
$OPENSSL_COMMAND rsa -in ca-key.pem -out ca-key.pem

echo -e "\n\n### Create postgres server key ###"
$OPENSSL_COMMAND req -new -newkey rsa -keyout server-key.pem -out server-req.pem -passout pass:abcd -subj "/CN=postgres-ssl-server"
$OPENSSL_COMMAND rsa -in server-key.pem -out server-key.pem -passin pass:abcd
$OPENSSL_COMMAND x509 -req -in server-req.pem -CA ca.pem -CAkey ca-key.pem -set_serial 02 -out server-cert.pem

echo -e "\n\n### Create client key ###"
$OPENSSL_COMMAND req -new -newkey rsa -keyout client-key.pem -out client-req.pem -passout pass:abcd -subj "/CN=postgres-ssl-server"
$OPENSSL_COMMAND rsa -in client-key.pem -out client-key.pem -passin pass:abcd
$OPENSSL_COMMAND x509 -req -in client-req.pem -CA ca.pem -CAkey ca-key.pem -set_serial 03 -out client-cert.pem

chmod 0600 $OUTPUT_FOLDER/* 
```

Enter fullscreen mode Exit fullscreen mode

这个文件假设我们在一个子文件夹中，并将在它上面创建一个`run/ssl-files`文件夹，并将所有命令放在那里。为了使事情变得更容易和更可移植，我使用 Docker 作为引入 openssl 命令的工具，而不是将其安装在本地。

# 配置 Postgres

我的下一个脚本是正确配置 postgres。这将添加这些 SSL 文件，并确保只接受 SSL 连接。这个看起来是这样的:

```
#!/bin/sh

echo "Setting up postgres hba settings"
cat <<EOM > ${PGDATA}/pg_hba.conf
local     all  all       trust
hostnossl all  all  all  reject
hostssl   all  all  all  password EOM echo "Adding SSL settings to postgresql.conf file"
cat <<EOM >> ${PGDATA}/postgresql.conf
ssl = on
ssl_cert_file = '/var/ssl/server-cert.pem'
ssl_key_file = '/var/ssl/server-key.pem'
ssl_ca_file = '/var/ssl/ca.pem' EOM 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本打算在 docker 设置例程中运行，我们将在一分钟后讨论这个例程。`pg_hba.conf`文件是为了确保只接受 ssl 连接，而`postgresql.conf`文件是为了添加所需的 SSL 设置。

这个脚本将在我们启动 postgres 时运行，并将在我们的`docker-compose.yml`文件中进行配置。我们一会儿就会谈到这个问题。

# 播种金库

下一个脚本与 Vault 相关。此脚本将启动 vault，然后将 vault 配置为能够为我们的数据库创建机密。脚本是这样的:

```
#!/bin/sh
# Run a vault dev server and configure that instance for the database

# Capture the token defined in the environment and make it our dev server token so they match
export VAULT_DEV_ROOT_TOKEN_ID=$VAULT_TOKEN

# Start vault server and place in background
vault server -dev &

# Wait for server to start
sleep 2

echo "Enabling database plugin"
vault secrets enable -path=dbs database

echo "Adding postgres connection information"
vault write dbs/config/mydb \
  plugin_name=postgresql-database-plugin \
  connection_url='postgresql://{{username}}:{{password}}@database:5432/mydb' \
  allowed_roles=mydb-admin \
  username="admin" \
  password="secret" \
  verify_connection=false

echo "Adding admin role"
vault write dbs/roles/mydb-admin \
  db_name=mydb \
  default_ttl=5m \
  max_ttl=1h \
  creation_statements="CREATE ROLE \"{{name}}\" WITH LOGIN PASSWORD '{{password}}' \ VALID UNTIL '{{expiration}}'; \ GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO \"{{name}}\";"

# Wait for vault server to finish
wait 
```

Enter fullscreen mode Exit fullscreen mode

此脚本旨在 vault 容器启动时运行。这意味着该脚本负责确保服务器启动并保持运行。一旦完成，我们就使用`vault`命令写出我们的配置。在这一切都完成之后，我们让脚本与带有`wait`命令的`vault server`部分挂起。这是因为脚本实际上启动了服务器，当脚本结束时，docker 容器也将结束。

脚本中的第一部分是获取传递的变量`VAULT_TOKEN`,并将其作为我们的开发服务器令牌。现在，保险库凭证是你把东西从保险库拿出来的钥匙。为了确保我们知道密钥，我们告诉服务器使用哪个令牌，而不是执行生成随机密钥的默认行为。更简单的是，我们让 docker 容器通过`VAULT_TOKEN`给我们传递一个令牌，我们把它和`VAULT_DEV_ROOT_TOKEN_ID`绑定在一起，这样我们就不用定义两次了。需要特别说明的是，`VAULT_TOKEN`是客户端使用的，`VAULT_DEV_ROOT_TOKEN_ID`是服务器本身使用的。如果我们不提供这个 dev 令牌，服务器将生成一个随机令牌，这使得开发更加困难。这是我们的开发库实例和生产库实例之间的区别。在开发中，我们可以预先定义令牌，在生产中，这些令牌是为您随机生成的。

接下来有趣的是连接和角色设置。我想快速指出的一点是，路径遵循一种模式。名称`dbs`来自我们启用数据库插件的地方。然后有 3 个子路径，`config`、`roles`和`creds`。`config`路径是存储数据库配置的地方。接下来，我们添加到`roles`部分，它定义了哪些角色可以使用配置为我们的数据库创建凭证。然后还有一个`creds`键，它在被读取时实际创建凭证，稍后将详细介绍如何使用它。

重申一下，我们将配置存储在`dbs/config/mydb`中，将角色信息存储在`dbs/roles/mydb-admin`中。这将在`dbs/creds/mydb-admin`自动创建一个路径，我们稍后将使用它来获取我们的凭证。

postgres 证书的使用和管理完全值得一篇博文来讨论，但是让我快速地讨论一下这个问题。通过 Vault，我们可以创建临时角色，这些角色可以随意来去。当我们使用来自 Vault 的这些凭证来管理模式(例如，创建表)时，我们需要特别注意谁实际拥有这些表。当您创建一个表时，无论您当前的角色是谁，都将是该表的所有者。然后，除非正确设置了权限和所有权，否则您的下一个角色在实际访问该表时会遇到问题。我有一个与此相关的 [dba.stackexchange](https://dba.stackexchange.com/q/208649/16141) 问题，稍后会有一篇更长的博文。

# 坞站-复合设置

将所有这些绑定在一起的`docker-compose.yml`文件如下所示:

```
version: '3'
services:
  database:
    image: postgres:9.6
    volumes:
      - ./run/ssl-files:/var/ssl/
      - ./scripts/setup-postgres:/docker-entrypoint-initdb.d/setup.sh
    ports:
      - 5432:5432
    environment:
      - POSTGRES_USER=admin
      - POSTGRES_PASSWORD=secret
      - POSTGRES_DB=mydb
  vault:
      image: vault:0.10.1
      ports:
        - 8200:8200
      environment:
        - VAULT_TOKEN=secret-token
        - VAULT_ADDR=http://127.0.0.1:8200/
      volumes:
        - ./scripts/setup-and-run-vault:/setup-and-run.sh
      command: /setup-and-run.sh 
```

Enter fullscreen mode Exit fullscreen mode

让我们一行一行地来。

第 1 行和第 2 行只是普通 Docker 编写的文件格式。

接下来的 3 个部分是我们将运行的 3 种不同的服务。Postgres 第 3 - 13 行定义了数据库，第 14 - 23 行定义了保险库服务。

`database`部分是一个相当简单的 postgres docker 设置。我们将 SSL 文件添加到 docker 容器(第 6 行)和启动时运行的脚本(第 7 行)。我们还将端口暴露给我正在开发的其他应用程序，这些应用程序在运行时实际上会遇到这个问题(第 9 行)。最后两位是设置初始数据库的环境变量(第 11 - 13 行)。最后那些你可以随意调整的，这些只是我挑选的一些默认值。

`vault`段大部分是直线前进。在环境变量(从第 20 行开始)中，我们有两个定义，一个用于我们的令牌，另一个用于正在运行的 vault 实例的地址。第一个是我们用于开发服务器以及访问该服务器的令牌(记住:我们定义了令牌并在开发服务器的脚本中使用它)。需要定义下一个变量，因为默认情况下，vault 将尝试并使用 https。需要定义本地服务器看起来有点傻，但没关系。`volume`部分将我们的脚本从本地磁盘添加到容器中，然后我们覆盖默认命令来运行该脚本。

# 其实用这个

好了，我们现在有了所有这些疯狂的东西，我该如何使用它们呢？好吧，让我们来演示一下，在本地项目中运行，不连接任何代码。只是确保一切正常运行，并且我们可以证明它们正在运行。

首先，我该如何开始呢？井运行`docker-compose up`。这将启动整个系统。我们之前所做的播种等工作的要点是，一个命令将为您提供一个工作系统。如果通读所有输出，您会看到数据库已启动并初始化，vault 已启动并初始化。

接下来，让我们来看看如何连接到数据库。一个简单的方法是，我们实际上可以连接到正在运行的实例，并通过它执行`psql`。你可以通过`docker-compose exec database psql -U admin -d mydb`来实现。如果你运行了这个，你现在应该在`psql`提示里面，这个提示是连接到数据库。

然而，这并不是跳马的乐趣所在。如果我想尝试使用 vault 的整个管道创建凭证，然后使用这些凭证进入数据库，该怎么办？嗯，这有点复杂，但是这里有一个脚本可以完成这个过程:

```
VAULT_SETTINGS=$(docker-compose exec vault vault read --format=json dbs/creds/mydb-admin)
VAULT_USERNAME=$(echo $VAULT_SETTINGS | docker run --rm -i colstrom/jq -r '.data.username')
docker-compose exec database psql -U $VAULT_USERNAME -d mydb 
```

Enter fullscreen mode Exit fullscreen mode

第一部分是通过在一个`vault`容器中执行`vault read`来捕获一个 vault 令牌。这将创建一个类似如下的 JSON 对象:

```
{  "request_id":  "92b63ef7-9ed1-c669-cb36-16ac8110f7c2",  "lease_id":  "dbs/creds/mydb-admin/af6bcdaf-b20e-4927-6eff-c5c93aef3e2e",  "lease_duration":  300,  "renewable":  true,  "data":  {  "password":  "A1a-0684tz5r1zpz3vys",  "username":  "v-token-mydb-adm-29r3ryxwxs3ryrvy9010-1528801793"  },  "warnings":  null  } 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，这为您提供了一个用户名和密码，您可以用它来登录您的 postgres 实例。这是一个临时凭据，将在默认的 5 分钟后过期。

然后我们采用那个`VAULT_SETTINGS`(当然使用 Docker！)我们使用`jq`命令从 JSON 对象中提取用户名。注意如何将数据传输到 docker 容器中。这使得 docker 成为您工具箱中非常有用的工具，因为您现在可以像使用普通 Linux 命令一样使用它，并创建一个很好的数据管道。

然后，我们使用该用户名登录，我们可以通过运行`\c`来验证。

这就是你要的，一个 vault 和 postgres 开发工作区。如果你想下载并在需要时使用，我在 github 上有代码。