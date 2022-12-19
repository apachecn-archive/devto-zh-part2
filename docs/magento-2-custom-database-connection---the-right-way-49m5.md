# Magento 2 自定义数据库连接-正确的方式

> 原文：<https://dev.to/jalogut/magento-2-custom-database-connection---the-right-way-49m5>

*这篇文章最初发表在我同事的博客上[https://blog.hauri.me/](https://blog.hauri.me/magento2-custom-database-connection.html)T3】*

在 Magento2 中添加一个定制的数据库连接初看起来很容易，但是做好它会有点复杂。在这里我将解释如何正确地做它。

通常，如果您想在 Magento 2 项目中添加一个新的数据库连接，您只需要做以下事情。至少我过去也是这样做的😅

1-用新的数据库连接编辑`app/etc/env.php`

```
 'db' => array (
        'connection' => array (
            //...
            'custom' => array (
                'host' => '<host>',
                'dbname' => '<database>',
                'username' => '<user>',
                'password' => '<password>',
                'active' => '1',
            ),
        ),
   ),
        'resource' => array(
        //...
        'custom' => array(
          'connection' => 'custom'
        )
   ) 
```

2-通过指定自定义资源连接，在您的`model/resource`或任何您想要的地方使用新连接:

*   在您的模型资源中

```
 public function __construct(Context $context, $connectionName = 'custom')
    {
        parent::__construct($context, $connectionName);
    } 
```

*   在任何其他地方

```
 /** @var Magento\Framework\App\ResourceConnection $this->resourceConnection **/
    $connection = $this->resourceConnection->getConnection('custom'); 
```

很简单，对吧？之后，您的模块就可以使用新的数据库连接了。然而，如果你想以正确的方式去做，那是不够的。否则，我们为什么要写这个帖子呢？

如果你只是这样做，你将面临两个问题:

1.  这个新的数据库连接将不可用于集成测试
2.  如果您的模块包含使用此数据库的安装脚本，全新安装将会崩溃。例如，如果您使用安装脚本在此数据库上创建/更改表。

克服这些问题的解决方案是将配置自动添加到`app/etc/env.php`中，与 Magento2 中默认添加`default_setup`的方式相同。这样，我们的定制数据库配置将始终可用于全新安装和集成测试。

好消息是 Magento 2 已经准备好允许其他模块扩展默认的`setup:install`命令，以便在设置时执行自定义动作。钥匙在`Magento\Setup\Model\ConfigOptionsListCollector::collectOptionsLists`

```
/**
 * Auto discover ConfigOptionsList class and collect them.
 * These classes should reside in <module>/Setup directories.
 */
public function collectOptionsLists()
{
    $optionsList = [];

    // go through modules
    foreach ($this->fullModuleList->getNames() as $moduleName) {
        $optionsClassName = str_replace('_', '\\', $moduleName) . '\Setup\ConfigOptionsList';
        if (class_exists($optionsClassName)) {
            $optionsClass = $this->objectManagerProvider->get()->create($optionsClassName);
            if ($optionsClass instanceof ConfigOptionsListInterface) {
                $optionsList[$moduleName] = $optionsClass;
            }
        }
    }
} 
```

正如您在前面的函数中看到的，这个`collectOptionsLists`加载了所有命名空间为`\Setup\ConfigOptionsList`的模块类。知道了这一点，我们需要创建我们自己的`ConfigOptionsList`,其中包含创建我们的定制数据库配置的逻辑。我们的班级如下:

`Vendorname\Modulename\SetupConfigOptionsList`

```
<?php
/**
 * ConfigOptionsList
 *
 */
namespace Vendorname\Modulename\Setup;

use Magento\Framework\Config\Data\ConfigData;
use Magento\Framework\Config\File\ConfigFilePool;
use Magento\Framework\Setup\ConfigOptionsListInterface;
use Magento\Framework\Setup\Option\TextConfigOption;
use Magento\Framework\App\DeploymentConfig;
use Magento\Framework\Config\ConfigOptionsListConstants;
use Magento\Setup\Exception as SetupException;
use Magento\Framework\Model\ResourceModel\Type\Db\ConnectionFactory;

class ConfigOptionsList implements ConfigOptionsListInterface
{
    const DB_CONNECTION_NAME = 'custom';
    const DB_CONNECTION_SETUP = self::DB_CONNECTION_NAME;
    const CONFIG_PATH_DB_CONNECTION = ConfigOptionsListConstants::CONFIG_PATH_DB_CONNECTIONS . '/' . self::DB_CONNECTION_NAME;
    const CONFIG_PATH_RESOURCE_SETUP = ConfigOptionsListConstants::CONFIG_PATH_RESOURCE . '/' . self::DB_CONNECTION_SETUP . '/connection';
    const OPTION_DB_HOST = self::DB_CONNECTION_NAME . '-db-host';
    const OPTION_DB_NAME = self::DB_CONNECTION_NAME . '-db-name';
    const OPTION_DB_USER = self::DB_CONNECTION_NAME . '-db-user';
    const OPTION_DB_PASSWORD = self::DB_CONNECTION_NAME . '-db-password';
    const OPTION_DB_ENGINE = self::DB_CONNECTION_NAME . '-db-engine';
    const OPTION_DB_INIT_STATEMENTS = self::DB_CONNECTION_NAME . '-db-init-statements';

    const OPTIONAL_OPTIONS = [
        ConfigOptionsListConstants::KEY_HOST => self::OPTION_DB_HOST,
        ConfigOptionsListConstants::KEY_NAME => self::OPTION_DB_NAME,
        ConfigOptionsListConstants::KEY_USER => self::OPTION_DB_USER,
        ConfigOptionsListConstants::KEY_PASSWORD => self::OPTION_DB_PASSWORD,
        ConfigOptionsListConstants::KEY_ENGINE => self::OPTION_DB_ENGINE,
        ConfigOptionsListConstants::KEY_INIT_STATEMENTS => self::OPTION_DB_INIT_STATEMENTS,
    ];

    protected $connectionFactory;

    public function __construct(
        ConnectionFactory $connectionFactory
    ) {
        $this->connectionFactory = $connectionFactory;
    }

    public function getOptions()
    {
        return [
            new TextConfigOption(
                self::OPTION_DB_HOST,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_HOST,
                'Database host (Connection name: Import)',
                'localhost'
            ),
            new TextConfigOption(
                self::OPTION_DB_NAME,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_NAME,
                'Database name (Connection name: Import)',
                'magento2_import'
            ),
            new TextConfigOption(
                self::OPTION_DB_USER,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_USER,
                'Database username (Connection name: Import)',
                'root'
            ),
            new TextConfigOption(
                self::OPTION_DB_PASSWORD,
                TextConfigOption::FRONTEND_WIZARD_PASSWORD,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_PASSWORD,
                'Database password (Connection name: Import)',
                ''
            ),
            new TextConfigOption(
                self::OPTION_DB_ENGINE,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_ENGINE,
                'Database engine (Connection name: Import)',
                'innodb'
            ),
            new TextConfigOption(
                self::OPTION_DB_INIT_STATEMENTS,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_INIT_STATEMENTS,
                'Database initial set of commands (Connection name: Import)',
                'SET NAMES utf8;'
            ),
        ];
    }

    public function createConfig(array $options, DeploymentConfig $deploymentConfig)
    {
        $configData = new ConfigData(ConfigFilePool::APP_ENV);

        $dbConfig = $this->getDbConfig($options, $deploymentConfig);
        foreach ($dbConfig as $configSubPath => $configValue) {
            $configData->set(self::CONFIG_PATH_DB_CONNECTION . '/' . $configSubPath, $configValue);
        }

        if ($deploymentConfig->get(self::CONFIG_PATH_RESOURCE_SETUP) === null) {
            $configData->set(self::CONFIG_PATH_RESOURCE_SETUP, self::DB_CONNECTION_NAME);
        }

        return [$configData];
    }

    protected function getDbConfig(array $options, DeploymentConfig $deploymentConfig) : array
    {
        $dbConfig = [];
        foreach (self::OPTIONAL_OPTIONS as $configSubPath => $option) {
            if ($options[$option] === null) {
                $options[$option] = $deploymentConfig->get(self::CONFIG_PATH_DB_CONNECTION . '/' . $configSubPath);
            }
            $dbConfig[$configSubPath] = $options[$option];
        }

        $activeConfigPath = self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_ACTIVE;
        $dbConfig[ConfigOptionsListConstants::KEY_ACTIVE] = $deploymentConfig->get($activeConfigPath)??'1';

        return $dbConfig;
    }

    public function validate(array $options, DeploymentConfig $deploymentConfig)
    {
        if (!$options[ConfigOptionsListConstants::INPUT_KEY_SKIP_DB_VALIDATION]) {
            try {
                $dbConfig = $this->getDbConfig($options, $deploymentConfig);
                $this->checkDatabaseConnection($dbConfig);
            } catch (\Exception $e) {
                $errors = [
                    sprintf('Error validating DB connection name: "%s"', self::DB_CONNECTION_NAME),
                    $e->getMessage()
                ];
                return $errors;
            }
        }
        return [];
    }

    /**
     * @param array $dbConfig
     * @return bool
     * @throws SetupException
     */
    protected function checkDatabaseConnection(array $dbConfig)
    {
        $connection = $this->connectionFactory->create($dbConfig);
        $dbName = $dbConfig[ConfigOptionsListConstants::KEY_NAME];

        $accessibleDbs = $connection
            ->query("SHOW DATABASES")
            ->fetchAll(\PDO::FETCH_COLUMN, 0); //@codingStandardsIgnoreLine
        if (in_array($dbName, $accessibleDbs)) {
            return true;
        }

        throw new SetupException(
            sprintf("Database '%s' does not exist or user does not have privileges to access this database.", $dbName)
        );
    }
} 
```

让我们来分解一下我们正在做的事情:

1-我们的类必须实现`ConfigOptionsListInterface`。检查这个接口，我们看到我们必须实现 3 个方法:

```
 interface ConfigOptionsListInterface
    {
        /**
         * Gets a list of input options so that user can provide required
         * information that will be used in deployment config file
         */
        public function getOptions();

        /**
         * Creates array of ConfigData objects from user input data.
         * Data in these objects will be stored in array form in deployment config file.
         */
        public function createConfig(array $options, DeploymentConfig $deploymentConfig);

        /**
         * Validates user input option values and returns error messages
         */
        public function validate(array $options, DeploymentConfig $deploymentConfig);
    } 
```

2-实现方法`getOptions()`。我们在这里指定执行`install:setup`时可用的新选项。对于新的数据库连接，我们将添加以下选项:

```
 --custom-db-host                Database host (Connection name: Custom)
    --custom-db-name                Database name (Connection name: Custom)
    --custom-db-user                Database username (Connection name: Custom)
    --custom-db-password            Database password (Connection name: Custom)
    --custom-db-engine              Database engine (Connection name: Custom)
    --custom-db-init-statements     Database initial set of commands (Connection name: Custom) 
```

每个选项必须是类型`TextConfigOption`，如下例所示:

```
 public function getOptions()
    {
        return [
            new TextConfigOption(
                self::OPTION_DB_HOST,
                TextConfigOption::FRONTEND_WIZARD_TEXT,
                self::CONFIG_PATH_DB_CONNECTION . '/' . ConfigOptionsListConstants::KEY_HOST,
                'Database host (Connection name: Import)',
                'localhost'
            ),
       //...
       ];
    } 
```

3-实现方法`validate()`。在这里，我们通过尝试连接到自定义数据库来检查数据库设置。如果无法建立连接，验证将返回错误。

```
 public function validate(array $options, DeploymentConfig $deploymentConfig)
    {
        if (!$options[ConfigOptionsListConstants::INPUT_KEY_SKIP_DB_VALIDATION]) {
            try {
                $dbConfig = $this->getDbConfig($options, $deploymentConfig);
                $this->checkDatabaseConnection($dbConfig);
            } catch (\Exception $e) {
                $errors = [
                    sprintf('Error validating DB connection name: "%s"', self::DB_CONNECTION_NAME),
                    $e->getMessage()
                ];
                return $errors;
            }
        }
        return [];
    } 
```

4-实现方法`createConfig()`。最后我们寻找的方法。在这里，我们将自定义数据库设置保存到`app/etc/env.php`配置文件中:

```
 public function createConfig(array $options, DeploymentConfig $deploymentConfig)
    {
        $configData = new ConfigData(ConfigFilePool::APP_ENV);

        $dbConfig = $this->getDbConfig($options, $deploymentConfig);
        foreach ($dbConfig as $configSubPath => $configValue) {
            $configData->set(self::CONFIG_PATH_DB_CONNECTION . '/' . $configSubPath, $configValue);
        }

        if ($deploymentConfig->get(self::CONFIG_PATH_RESOURCE_SETUP) === null) {
            $configData->set(self::CONFIG_PATH_RESOURCE_SETUP, self::DB_CONNECTION_NAME);
        }

        return [$configData];
    } 
```

我们完了。全新安装和集成测试现在将在安装过程中自动包括我们的自定义数据库设置。

执行`setup:install --help`将显示新的命令选项。同样，如果我们进行新的安装，我们会看到定制的数据库配置被自动添加到`app/etc/env.php`中。

这具有以下优点:

1.  现在，您可以添加影响自定义数据库的安装脚本，而不会导致全新安装崩溃。`env.php`现在在`setup:install`期间被编辑，所以在执行设置脚本之前，您的定制数据库配置已经准备好了。

2.  您可以运行使用自定义数据库的集成测试。为此，您需要用新选项编辑`dev/tests/integration/etc/install-config-mysql.php`

```
 return [
        //...
        'custom-db-host' => '<host>',
        'custom-db-user' => '<user>',
        'custom-db-password' => '<password>',
        'custom-db-name' => '<custom_database>_integration_tests',
    ]; 
```

最后一个提示。如果您想跳过手动创建代码的麻烦，您可以使用[magento 2-代码生成](https://github.com/staempfli/magento2-code-generator)工具来自动生成代码:

```
mg2-codegen template:generate customDBConnection 
```

仅此而已。我希望这将有助于您添加自定义数据库。对我来说，找到正确的方法并不容易，但看到 Magento2 为此做了适当的准备，这很棒。