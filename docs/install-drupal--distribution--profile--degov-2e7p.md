# 安装 Drupal 发行版(概要文件)degov

> 原文：<https://dev.to/patrickschanen/install-drupal--distribution--profile--degov-2e7p>

### 作曲家安装，作曲家更新，drush updb

```
 demo@prod ~/d/d/p/w/html> 
composer create-project degov/degov-project . --dev
You are using the deprecated option "dev". Dev packages are installed by default now.
Installing degov/degov-project (2.1.14)
  - Installing degov/degov-project (2.1.14): Loading from cache
Created project in .
Loading composer repositories with package information
Installing dependencies (including require-dev) from lock file
Package operations: 221 installs, 0 updates, 0 removals
  - Installing mnsami/composer-custom-directory-installer (1.1.1): Loading from cache
  - Installing composer/installers (v1.5.0): Loading from cache
  - Installing cweagans/composer-patches (1.6.5): Loading from cache
No patches supplied.
Gathering patches for dependencies. This might take a minute.
  - Installing symfony/polyfill-ctype (v1.9.0): Loading from cache
  - Installing symfony/yaml (v3.4.14): Loading from cache
  - Installing symfony/finder (v3.4.14): Loading from cache
  - Installing composer/semver (1.4.2): Loading from cache
  - Installing oomphinc/composer-installers-extender (v1.1.2): Loading from cache
  - Installing drupal-composer/drupal-scaffold (2.5.4): Loading from cache
  - Installing drupal/console-extend-plugin (0.9.2): Loading from cache
  - Installing paragonie/random_compat (v2.0.17): Loading from cache
  - Installing symfony/polyfill-php70 (v1.9.0): Loading from cache
  - Installing symfony/polyfill-mbstring (v1.9.0): Loading from cache
  - Installing symfony/http-foundation (v3.4.14): Loading from cache
  - Installing symfony/event-dispatcher (v3.4.14): Loading from cache
  - Installing psr/log (1.0.2): Loading from cache
  - Installing symfony/debug (v3.4.14): Loading from cache
  - Installing symfony/http-kernel (v3.4.14): Loading from cache
  - Installing asm89/stack-cors (1.2.0): Loading from cache
  - Installing symfony/console (v3.4.14): Loading from cache
  - Installing consolidation/log (1.0.6): Loading from cache
  - Installing zenopopovici/doubletaptogo (0.3): Cloning master
  - Installing psr/cache (1.0.1): Loading from cache
  - Installing predis/predis (v1.1.1): Loading from cache
  - Installing phpfastcache/phpssdb (1.0.1): Loading from cache
  - Installing phpfastcache/phpfastcache (5.0.21): Loading from cache
  - Installing mashape/unirest-php (v3.0.4): Loading from cache
  - Installing raiym/instagram-php-scraper (v0.8.24): Loading from cache
  - Installing php-coord/php-coord (2.1): Loading from cache
  - Installing phansys/getid3 (2.1.x-dev 25fb540): Cloning 25fb540dd9
  - Installing madcoda/php-youtube-api (v1.2.4): Loading from cache
  - Installing leaflet/leaflet (1.1.0): Cloning a3a7e04
  - Installing kenwheeler/slick (1.7.1): Cloning master
  - Installing heiseonline/shariff (2.0.4): Loading from cache
  - Installing enyo/dropzone (4.2.0): Cloning origin/master
  - Installing webmozart/assert (1.3.0): Loading from cache
  - Installing webmozart/path-util (2.3.0): Loading from cache
  - Installing webflo/drupal-finder (1.1.0): Loading from cache
  - Installing symfony/polyfill-php72 (v1.9.0): Loading from cache
  - Installing symfony/var-dumper (v4.1.3): Loading from cache
  - Installing symfony/process (v3.4.14): Loading from cache
  - Installing symfony/filesystem (v3.4.14): Loading from cache
  - Installing symfony/config (v3.4.14): Loading from cache
  - Installing sebastian/version (2.0.1): Loading from cache
  - Installing nikic/php-parser (v4.0.3): Loading from cache
  - Installing jakub-onderka/php-console-color (0.1): Loading from cache
  - Installing jakub-onderka/php-console-highlighter (v0.3.2): Loading from cache
  - Installing dnoegel/php-xdg-base-dir (0.1): Loading from cache
  - Installing psy/psysh (v0.9.7): Loading from cache
  - Installing psr/container (1.0.0): Loading from cache
  - Installing container-interop/container-interop (1.2.0): Loading from cache
  - Installing league/container (2.4.1): Loading from cache
  - Installing dflydev/dot-access-data (v1.1.0): Loading from cache
  - Installing grasmash/yaml-expander (1.4.0): Loading from cache
  - Installing consolidation/site-alias (1.1.1): Loading from cache
  - Installing consolidation/output-formatters (3.2.1): Loading from cache
  - Installing grasmash/expander (1.0.0): Loading from cache
  - Installing consolidation/config (1.1.0): Loading from cache
  - Installing consolidation/annotated-command (2.8.4): Loading from cache
  - Installing consolidation/robo (1.3.0): Loading from cache
  - Installing twig/twig (v1.35.4): Loading from cache
  - Installing chi-teck/drupal-code-generator (1.26.0): Loading from cache
  - Installing drush/drush (dev-master 1844496): Cloning 1844496e1f
  - Installing zendframework/zend-stdlib (3.2.0): Loading from cache
  - Installing zendframework/zend-escaper (2.6.0): Loading from cache
  - Installing zendframework/zend-feed (2.10.3): Loading from cache
  - Installing psr/http-message (1.0.1): Loading from cache
  - Installing zendframework/zend-diactoros (1.8.5): Loading from cache
  - Installing symfony/translation (v3.4.14): Loading from cache
  - Installing symfony/validator (v3.4.14): Loading from cache
  - Installing symfony/serializer (v3.4.14): Loading from cache
  - Installing symfony/routing (v3.4.14): Loading from cache
  - Installing symfony/psr-http-message-bridge (v1.0.2): Loading from cache
  - Installing symfony/polyfill-iconv (v1.9.0): Loading from cache
  - Installing symfony/dependency-injection (v3.4.14): Loading from cache
  - Installing symfony/class-loader (v3.4.14): Loading from cache
  - Installing symfony-cmf/routing (1.4.1): Loading from cache
  - Installing stack/builder (v1.0.5): Loading from cache
  - Installing masterminds/html5 (2.3.0): Loading from cache
  - Installing guzzlehttp/psr7 (1.4.2): Loading from cache
  - Installing guzzlehttp/promises (v1.3.1): Loading from cache
  - Installing guzzlehttp/guzzle (6.3.3): Loading from cache
  - Installing doctrine/lexer (v1.0.1): Loading from cache
  - Installing egulias/email-validator (1.2.14): Loading from cache
  - Installing easyrdf/easyrdf (0.9.1): Loading from cache
  - Installing doctrine/annotations (v1.6.0): Loading from cache
  - Installing doctrine/reflection (v1.0.0): Loading from cache
  - Installing doctrine/event-manager (v1.0.0): Loading from cache
  - Installing doctrine/collections (v1.5.0): Loading from cache
  - Installing doctrine/cache (v1.7.1): Loading from cache
  - Installing doctrine/persistence (v1.0.0): Loading from cache
  - Installing doctrine/inflector (v1.3.0): Loading from cache
  - Installing doctrine/common (v2.9.0): Loading from cache
  - Installing drupal/core (8.5.6): Loading from cache
  - Applying patches for drupal/core
    https://www.drupal.org/files/issues/1356278-408--8.5.x-real.patch (1356276 - Allow profiles to provide a base/parent profile and load them in the correct order)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/1356278-408--8.5.x-real.patch
    https://www.drupal.org/files/issues/2880374-remove-experimental-warnings-6.patch (2880374 - Experimental modules should not have warnings after being installed)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2880374-remove-experimental-warnings-6.patch
    https://www.drupal.org/files/issues/2869592-remove-update-warning-7.patch (2869592 - Disabled update module shouldn't produce a status report warning)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2869592-remove-update-warning-7.patch
    https://www.drupal.org/files/issues/2885441-2.patch (2885441 - EntityReferenceAutocompleteWidget should define its size setting as an integer)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2885441-2.patch
    https://www.drupal.org/files/issues/2018-06-07/2815221-102.patch (2815221 - Add quickedit to the latest-revision route)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2018-06-07/2815221-102.patch
    https://www.drupal.org/files/issues/2018-07-04/2921661-86-8.6.x.patch (2921661 (follow-up): Add support to migrate multilingual revisions)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2018-07-04/2921661-86-8.6.x.patch
    https://www.drupal.org/files/issues/2877383-56.patch (2877383 - Add action support to Media module)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2877383-56.patch
    https://www.drupal.org/files/issues/2670730-81-89-8.5.0-rc1.patch (2670730 - Provide a delete action for each content entity type)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2670730-81-89-8.5.0-rc1.patch

  - Installing drupal/workbench_moderation (1.3.0): Loading from cache
  - Installing drupal/workbench_access (1.0.0-alpha6): Loading from cache
  - Installing drupal/workbench (1.0.0): Loading from cache
  - Installing drupal/webform (5.0.0-rc18): Loading from cache
  - Installing drupal/viewsreference (1.2.0): Loading from cache
  - Installing drupal/ultimate_cron (2.0.0-alpha4): Loading from cache
  - Installing drupal/twig_field_value (1.1.0): Loading from cache
  - Installing drupal/token (1.3.0): Loading from cache
  - Installing drupal/system_stream_wrapper (1.0.0-alpha2): Loading from cache
  - Installing swiftmailer/swiftmailer (v5.4.12): Loading from cache
  - Installing html2text/html2text (4.0.1): Loading from cache
  - Installing drupal/mailsystem (4.1.0): Loading from cache
  - Installing drupal/swiftmailer (1.0.0-beta2): Loading from cache
  - Installing phayes/geophp (1.2): Loading from cache
  - Installing drupal/geofield (1.0.0-beta4): Loading from cache
  - Installing drupal/styled_google_map (1.2.0): Loading from cache
  - Installing drupal/spamspan (dev-1.x 279061d): Cloning 279061db73
  - Installing drupal/sitemap (1.4.0): Loading from cache
  - Installing drupal/simplenews (1.0.0-alpha3): Loading from cache
  - Installing drupal/simple_sitemap (2.12.0): Loading from cache
  - Installing drupal/shariff (1.3.0): Loading from cache
  - Installing drupal/search_api (1.9.0): Loading from cache
  - Installing drupal/search_api_sorts (1.0.0-beta1): Loading from cache
  - Installing solarium/solarium (3.8.1): Loading from cache
  - Installing drupal/search_api_solr (1.2.0): Loading from cache
  - Installing drupal/inline_entity_form (1.0.0-rc1): Loading from cache
  - Installing drupal/scheduled_updates (1.0.0-alpha7): Loading from cache
  - Installing drupal/role_delegation (1.0.0-alpha1): Loading from cache
  - Installing drupal/restrict_ip (1.3.0): Loading from cache
  - Applying patches for drupal/restrict_ip
    https://www.drupal.org/files/issues/2905569_check_user_authentication.patch (2905569 - Avoid redirect loop for authenticated users)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2905569_check_user_authentication.patch

  - Installing drupal/redirect (1.2.0): Loading from cache
  - Installing drupal/matomo (1.7.0): Loading from cache
  - Installing drupal/piwik (1.4.0): Loading from cache
  - Installing drupal/ctools (3.0.0): Loading from cache
  - Installing drupal/password_policy (3.0.0-alpha4): Loading from cache
  - Installing drupal/entity_reference_revisions (1.5.0): Loading from cache
  - Installing drupal/paragraphs (1.3.0): Loading from cache
  - Installing drupal/paragraphs_edit (dev-2.x 1551330): Cloning 1551330f27
  - Installing drupal/open_readspeaker (1.4.0): Loading from cache
  - Installing drupal/module_status (1.0.0-alpha3): Loading from cache
  - Installing drupal/metatag (1.5.0): Loading from cache
  - Installing drupal/memcache_storage (1.0.0-beta2): Loading from cache
  - Installing drupal/media_entity_generic (1.0.0-alpha1): Loading from cache
  - Installing drupal/linkit (4.3.0): Loading from cache
  - Installing phpdocumentor/reflection-common (1.0.1): Loading from cache
  - Installing phpdocumentor/type-resolver (0.4.0): Loading from cache
  - Installing phpdocumentor/reflection-docblock (4.3.0): Loading from cache
  - Installing drupal/pathauto (1.2.0): Loading from cache
  - Installing drupal/contact_storage (1.0.0-beta9): Loading from cache
  - Installing drupal/acquia_connector (1.14.0): Loading from cache
  - Installing drupal/lightning_core (2.8.0): Loading from cache
  - Installing sunra/php-simple-html-dom-parser (v1.5.2): Loading from cache
  - Installing ezyang/htmlpurifier (v4.10.0): Loading from cache
  - Installing caxy/php-htmldiff (v0.1.7): Loading from cache
  - Installing mkalkbrenner/php-htmldiff-advanced (0.0.8): Loading from cache
  - Installing drupal/diff (1.0.0-rc2): Loading from cache
  - Installing drupal/lightning_workflow (1.2.0): Loading from cache
  - Installing drupal/views_infinite_scroll (1.5.0): Loading from cache
  - Installing drupal/video_embed_field (2.0.0): Loading from cache
  - Installing j7mbo/twitter-api-php (1.0.6): Loading from cache
  - Installing drupal/media_entity_twitter (2.0.0-alpha2): Loading from cache
  - Installing drupal/media_entity_instagram (2.0.0-alpha1): Loading from cache
  - Applying patches for drupal/media_entity_instagram
    https://www.drupal.org/files/issues/2917454-2.patch (2917454 - Field formatter test is broken)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2917454-2.patch

  - Installing drupal/crop (2.0.0-rc1): Loading from cache
  - Installing drupal/image_widget_crop (2.2.0): Loading from cache
  - Installing drupal/embed (1.0.0): Loading from cache
  - Installing drupal/entity_embed (1.0.0-beta2): Loading from cache
  - Applying patches for drupal/entity_embed
    https://www.drupal.org/files/issues/2832504-2.patch (2832504 - Send the CKEditor instance ID to the embed.preview route)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2832504-2.patch

  - Installing drupal/entity_browser (2.0.0-alpha2): Loading from cache
  - Applying patches for drupal/entity_browser
    https://www.drupal.org/files/issues/entity-browser-view-context-2865928-14.patch (2865928 - The View widget should filter based on field settings)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/entity-browser-view-context-2865928-14.patch
    https://www.drupal.org/files/issues/2877751-27-8.x-2.x.patch (2877751 - Inform users how many items they can add to a field that uses an entity browser)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2877751-27-8.x-2.x.patch

  - Installing drupal/dropzonejs (2.0.0-alpha3): Loading from cache
  - Installing bower-asset/dropzone (v5.5.0): Loading from cache
  - Installing bower-asset/cropper (v2.3.4): Loading from cache
  - Installing drupal/lightning_media (2.3.0): Loading from cache
  - Installing drupal/panels (4.3.0): Loading from cache
  - Applying patches for drupal/panels
    https://www.drupal.org/files/issues/panels-ipe-2878684-3.patch (2878684 - Use String.match to correlate regions when switching Layouts in Panels IPE)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/panels-ipe-2878684-3.patch
    https://www.drupal.org/files/issues/panels-ipe-propogate-errors-2825034-5.patch (2825034 - Form error messages do not appear in IPE)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/panels-ipe-propogate-errors-2825034-5.patch

  - Installing drupal/panelizer (4.1.0): Loading from cache
  - Installing drupal/entity_block (1.0.0-beta1): Loading from cache
  - Installing drupal/lightning_layout (1.3.0): Loading from cache
  - Installing league/event (2.1.2): Loading from cache
  - Installing lcobucci/jwt (3.2.4): Loading from cache
  - Installing defuse/php-encryption (v2.2.1): Loading from cache
  - Installing league/oauth2-server (6.1.1): Loading from cache
  - Installing drupal/consumers (1.0.0): Loading from cache
  - Installing drupal/simple_oauth (3.8.0): Loading from cache
  - Installing drupal/schemata (1.0.0-alpha3): Loading from cache
  - Installing drupal/openapi (1.0.0-beta1): Loading from cache
  - Installing drupal/jsonapi (1.23.0): Loading from cache
  - Installing drupal/lightning_api (2.5.0): Loading from cache
  - Installing drupal/imagick (1.2.0): Loading from cache
  - Installing igorw/get-in (v1.0.3): Loading from cache
  - Installing egeloen/http-adapter (0.8.0): Loading from cache
  - Installing willdurand/geocoder (v3.3.2): Loading from cache
  - Installing drupal/geocoder (2.0.0-beta5): Loading from cache
  - Installing drupal/field_group (dev-3.x 072bf21): Cloning 072bf212d8
  - Installing drupal/config_update (1.5.0): Loading from cache
  - Installing drupal/features (3.7.0): Loading from cache
  - Installing drupal/facets (1.1.0): Loading from cache
  - Installing drupal/eu_cookie_compliance (1.2.0): Loading from cache
  - Installing drupal/entity_reference_integrity (1.0.0-rc4): Loading from cache
  - Installing drupal/entity_reference_display (1.2.0): Loading from cache
  - Installing drupal/easy_breadcrumb (1.8.0): Loading from cache
  - Installing drupal/date_popup (1.0.0): Loading from cache
  - Installing symfony/dom-crawler (v3.4.14): Loading from cache
  - Installing symfony/css-selector (v3.4.14): Loading from cache
  - Installing stecman/symfony-console-completion (0.8.0): Loading from cache
  - Installing drupal/console-en (1.8.0): Loading from cache
  - Installing dflydev/placeholder-resolver (v1.0.2): Loading from cache
  - Installing dflydev/dot-access-configuration (v1.0.2): Loading from cache
  - Installing drupal/console-core (1.8.0): Loading from cache
  - Installing alchemy/zippy (0.4.3): Loading from cache
  - Installing drupal/console (1.8.0): Loading from cache
  - Installing drupal/config_rewrite (1.1.0): Loading from cache
  - Installing drupal/config_replace (1.2.0): Loading from cache
  - Installing drupal/views_templates (1.0.0-alpha1): Loading from cache
  - Installing drupal/calendar (dev-1.x 1ef5b86): Cloning 1ef5b86b11
  - Installing drupal/block_field (1.0.0-alpha7): Loading from cache
  - Installing drupal/admin_toolbar (1.24.0): Loading from cache
  - Installing drupal/admin_links_access_filter (1.0.0-alpha3): Loading from cache
  - Installing commerceguys/addressing (v1.0.0): Loading from cache
  - Installing drupal/address (1.4.0): Loading from cache
  - Installing drupal/bootstrap (3.12.0): Loading from cache
  - Installing degov/degov_base_theme (dev-8.x-2.x-dev e14421d): Cloning e14421d3e6
  - Installing degov/degov (2.0.13): Loading from cache
paragonie/random_compat suggests installing ext-libsodium (Provides a modern crypto API that can be used to generate random bytes.)
symfony/http-kernel suggests installing symfony/browser-kit
symfony/console suggests installing symfony/lock
predis/predis suggests installing ext-phpiredis (Allows faster serialization and deserialization of the Redis protocol)
phpfastcache/phpfastcache suggests installing ext-predis (*)
phpfastcache/phpfastcache suggests installing ext-redis (*)
phpfastcache/phpfastcache suggests installing ext-sqlite (*)
phansys/getid3 suggests installing ext-dba (DBA extension is required in order to use DBA database as cache storage.)
phansys/getid3 suggests installing ext-rar (RAR extension is required for RAR archive module.)
phansys/getid3 suggests installing ext-sqlite (SQLite extension is required in order to use SQLite database as cache storage.)
psy/psysh suggests installing ext-pdo-sqlite (The doc command requires SQLite to work.)
psy/psysh suggests installing hoa/console (A pure PHP readline implementation. You'll want this if your PHP install doesn't already support readline or libedit.)
consolidation/robo suggests installing henrikbjorn/lurker (For monitoring filesystem changes in taskWatch)
consolidation/robo suggests installing natxet/CssMin (For minifying CSS files in taskMinify)
consolidation/robo suggests installing patchwork/jsqueeze (For minifying JS files in taskMinify)
consolidation/robo suggests installing pear/archive_tar (Allows tar archives to be created and extracted in taskPack and taskExtract, respectively.)
zendframework/zend-feed suggests installing zendframework/zend-cache (Zend\Cache component, for optionally caching feeds between requests)
zendframework/zend-feed suggests installing zendframework/zend-db (Zend\Db component, for use with PubSubHubbub)
zendframework/zend-feed suggests installing zendframework/zend-http (Zend\Http for PubSubHubbub, and optionally for use with Zend\Feed\Reader)
zendframework/zend-feed suggests installing zendframework/zend-servicemanager (Zend\ServiceManager component, for easily extending ExtensionManager implementations)
zendframework/zend-feed suggests installing zendframework/zend-validator (Zend\Validator component, for validating email addresses used in Atom feeds and entries when using the Writer subcomponent)
symfony/validator suggests installing psr/cache-implementation (For using the metadata cache.)
symfony/validator suggests installing symfony/expression-language (For using the Expression validator)
symfony/validator suggests installing symfony/intl
symfony/validator suggests installing symfony/property-access (For accessing properties within comparison constraints)
symfony/serializer suggests installing psr/cache-implementation (For using the metadata cache.)
symfony/serializer suggests installing symfony/property-access (For using the ObjectNormalizer.)
symfony/serializer suggests installing symfony/property-info (To deserialize relations.)
symfony/routing suggests installing symfony/expression-language (For using expression matching)
symfony/dependency-injection suggests installing symfony/expression-language (For using expressions in service container configuration)
symfony/dependency-injection suggests installing symfony/proxy-manager-bridge (Generate service proxies to lazy load them)
symfony/class-loader suggests installing symfony/polyfill-apcu (For using ApcClassLoader on HHVM)
easyrdf/easyrdf suggests installing ml/json-ld (~1.0)
doctrine/cache suggests installing alcaeus/mongo-php-adapter (Required to use legacy MongoDB driver)
solarium/solarium suggests installing minimalcode/search (Query builder compatible with Solarium, allows simplified solr-query handling)
drupal/search_api_solr suggests installing drupal/search_api_solr_multilingual (Highly recommended if installation is multilingual or uses a language different than English.)
lcobucci/jwt suggests installing mdanter/ecc (Required to use Elliptic Curves based algorithms.)
egeloen/http-adapter suggests installing ext-http (Allows you to use the PECL adapter)
egeloen/http-adapter suggests installing guzzle/guzzle (Allows you to use the Guzzle 3 adapter)
egeloen/http-adapter suggests installing kriswallsmith/buzz (Allows you to use the Buzz adapter)
egeloen/http-adapter suggests installing nategood/httpful (Allows you to use the httpful adapter)
egeloen/http-adapter suggests installing symfony/stopwatch (Allows you to use the stopwatch http adapter and event subscriber)
egeloen/http-adapter suggests installing zendframework/zend-http (Allows you to use the Zend 2 adapter)
egeloen/http-adapter suggests installing zendframework/zendframework1 (Allows you to use the Zend 1 adapter)
willdurand/geocoder suggests installing ext-geoip (Enabling the geoip extension allows you to use the MaxMindProvider.)
willdurand/geocoder suggests installing geoip/geoip (If you are going to use the MaxMindBinaryProvider (conflict with geoip extension).)
willdurand/geocoder suggests installing geoip2/geoip2 (If you are going to use the GeoIP2DatabaseProvider.)
willdurand/geocoder suggests installing symfony/stopwatch (If you want to use the TimedGeocoder)
alchemy/zippy suggests installing guzzle/guzzle (To use the GuzzleTeleporter with Guzzle 3)
drupal/console suggests installing symfony/thanks (Thank your favorite PHP projects on Github using the CLI!)
drupal/console suggests installing vlucas/phpdotenv (Loads environment variables from .env to getenv(), $_ENV and $_SERVER automagically.)
Package phansys/getid3 is abandoned, you should avoid using it. Use james-heinrich/getid3 instead.
Package egeloen/http-adapter is abandoned, you should avoid using it. Use php-http/httplug instead.
Generating autoload files
> DrupalComposer\DrupalScaffold\Plugin::scaffold
  - .csslintrc (https://cgit.drupalcode.org/drupal/plain/.csslintrc?h=8.5.6): Downloading (100%)         
  - .editorconfig (https://cgit.drupalcode.org/drupal/plain/.editorconfig?h=8.5.6): Downloading (100%)         
  - .eslintignore (https://cgit.drupalcode.org/drupal/plain/.eslintignore?h=8.5.6): Downloading (100%)         
  - .eslintrc.json (https://cgit.drupalcode.org/drupal/plain/.eslintrc.json?h=8.5.6): Downloading (100%)         
  - .gitattributes (https://cgit.drupalcode.org/drupal/plain/.gitattributes?h=8.5.6): Downloading (100%)         
  - .ht.router.php (https://cgit.drupalcode.org/drupal/plain/.ht.router.php?h=8.5.6): Downloading (100%)         
  - .htaccess (https://cgit.drupalcode.org/drupal/plain/.htaccess?h=8.5.6): Downloading (100%)         
  - index.php (https://cgit.drupalcode.org/drupal/plain/index.php?h=8.5.6): Downloading (100%)         
  - robots.txt (https://cgit.drupalcode.org/drupal/plain/robots.txt?h=8.5.6): Downloading (100%)         
  - sites/default/default.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/default/default.services.yml?h=8.5.6): Downloading (connectinDownloading (100%)         
  - sites/default/default.settings.php (https://cgit.drupalcode.org/drupal/plain/sites/default/default.settings.php?h=8.5.6): Downloading (connectinDownloading (100%)         
  - sites/development.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/development.services.yml?h=8.5.6): Downloading (100%)         
  - sites/example.settings.local.php (https://cgit.drupalcode.org/drupal/plain/sites/example.settings.local.php?h=8.5.6): Downloading (connecting...Downloading (100%)         
  - sites/example.sites.php (https://cgit.drupalcode.org/drupal/plain/sites/example.sites.php?h=8.5.6): Downloading (100%)         
  - update.php (https://cgit.drupalcode.org/drupal/plain/update.php?h=8.5.6): Downloading (100%)         
  - web.config (https://cgit.drupalcode.org/drupal/plain/web.config?h=8.5.6): Downloading (100%)         
demo@prod ~/d/d/p/w/html> ll
insgesamt 548K
drwxrwxr-x  2 demo demo  136  5\. Okt 13:07 bin/
-rw-rw-r--  1 demo demo  376 17\. Aug 15:01 CHANGELOG.md
-rw-rw-r--  1 demo demo 3,5K 17\. Aug 15:01 composer.json
-rw-rw-r--  1 demo demo 496K 17\. Aug 15:01 composer.lock
drwxrwxr-x  3 demo demo   18 17\. Aug 15:01 config/
drwxrwxr-x  8 demo demo  327  5\. Okt 13:07 docroot/
-rw-rw-r--  1 demo demo  35K 17\. Aug 15:01 LICENSE
-rw-rw-r--  1 demo demo  385 17\. Aug 15:01 README.md
drwxrwxr-x 61 demo demo 4,0K  5\. Okt 13:07 vendor/
demo@prod ~/d/d/p/w/html> composer update
Removing package drupal/core so that it can be re-installed and re-patched.
  - Removing drupal/core (8.5.6)
Deleting docroot/core - deleted
Removing package drupal/entity_browser so that it can be re-installed and re-patched.
  - Removing drupal/entity_browser (2.0.0-alpha2)
Deleting docroot/modules/contrib/entity_browser - deleted
Removing package drupal/entity_embed so that it can be re-installed and re-patched.
  - Removing drupal/entity_embed (1.0.0-beta2)
Deleting docroot/modules/contrib/entity_embed - deleted
Removing package drupal/media_entity_instagram so that it can be re-installed and re-patched.
  - Removing drupal/media_entity_instagram (2.0.0-alpha1)
Deleting docroot/modules/contrib/media_entity_instagram - deleted
Removing package drupal/panels so that it can be re-installed and re-patched.
  - Removing drupal/panels (4.3.0)
Deleting docroot/modules/contrib/panels - deleted
Removing package drupal/restrict_ip so that it can be re-installed and re-patched.
  - Removing drupal/restrict_ip (1.3.0)
Deleting docroot/modules/contrib/restrict_ip - deleted
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 13 installs, 58 updates, 2 removals
  - Removing sebastian/version (2.0.1)
No patches supplied.
Gathering patches for dependencies. This might take a minute.
  - Updating composer/installers (v1.5.0 => v1.6.0): Loading from cache
  - Updating symfony/yaml (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/process (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/finder (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/event-dispatcher (v3.4.14 => v3.4.17): Loading from cache
  - Updating symfony/debug (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/console (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/filesystem (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/config (v3.4.14 => v3.4.17): Loading from cache
  - Updating symfony/var-dumper (v4.1.3 => v4.1.6): Downloading (100%)         
  - Updating nikic/php-parser (v4.0.3 => v4.0.4): Loading from cache
  - Updating jakub-onderka/php-console-color (0.1 => v0.2): Loading from cache
  - Updating psy/psysh (v0.9.7 => v0.9.8): Loading from cache
  - Updating consolidation/site-alias (1.1.1 => 1.1.5): Loading from cache
  - Updating symfony/dependency-injection (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating consolidation/annotated-command (2.8.4 => 2.9.1): Loading from cache
  - Installing g1a/composer-test-scenarios (2.2.0): Loading from cache
  - Installing consolidation/self-update (1.1.3): Loading from cache
  - Updating consolidation/robo (1.3.0 => 1.3.1): Loading from cache
  - Updating chi-teck/drupal-code-generator (1.26.0 => 1.26.3): Loading from cache
  - Downgrading drush/drush (dev-master 1844496 => 9.4.0):  Checking out 9d46a2a675
  - Updating zendframework/zend-stdlib (3.2.0 => 3.2.1): Loading from cache
  - Updating zendframework/zend-diactoros (1.8.5 => 1.8.6): Loading from cache
  - Updating symfony/translation (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/validator (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/serializer (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/routing (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/http-foundation (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/psr-http-message-bridge (v1.0.2 => v1.1.0): Loading from cache
  - Updating symfony/http-kernel (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/class-loader (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating egulias/email-validator (1.2.14 => 1.2.15): Loading from cache
  - Updating doctrine/cache (v1.7.1 => v1.8.0): Loading from cache
  - Updating doctrine/persistence (v1.0.0 => v1.0.1): Loading from cache
  - Installing drupal/core (8.5.7): Loading from cache
  - Applying patches for drupal/core
    https://www.drupal.org/files/issues/1356278-408--8.5.x-real.patch (1356276 - Allow profiles to provide a base/parent profile and load them in the correct order)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/1356278-408--8.5.x-real.patch
    https://www.drupal.org/files/issues/2880374-remove-experimental-warnings-6.patch (2880374 - Experimental modules should not have warnings after being installed)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2880374-remove-experimental-warnings-6.patch
    https://www.drupal.org/files/issues/2869592-remove-update-warning-7.patch (2869592 - Disabled update module shouldn't produce a status report warning)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2869592-remove-update-warning-7.patch
    https://www.drupal.org/files/issues/2885441-2.patch (2885441 - EntityReferenceAutocompleteWidget should define its size setting as an integer)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2885441-2.patch
    https://www.drupal.org/files/issues/2018-06-07/2815221-102.patch (2815221 - Add quickedit to the latest-revision route)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2018-06-07/2815221-102.patch
    https://www.drupal.org/files/issues/2877383-56.patch (2877383 - Add action support to Media module)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2877383-56.patch
    https://www.drupal.org/files/issues/2670730-81-89-8.5.0-rc1.patch (2670730 - Provide a delete action for each content entity type)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2670730-81-89-8.5.0-rc1.patch
    https://www.drupal.org/files/issues/2018-08-01/2937543.patch (Issue 2937543 - Updating symfony)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2018-08-01/2937543.patch

  - Updating symfony/dom-crawler (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating symfony/css-selector (v3.4.14 => v3.4.17): Downloading (100%)         
  - Updating drupal/workbench (1.0.0 => 1.1.0): Loading from cache
  - Updating drupal/webform (5.0.0-rc18 => 5.0.0-rc22): Loading from cache
  - Updating drupal/token (1.3.0 => 1.5.0): Loading from cache
  - Updating drupal/styled_google_map (1.2.0 => 1.3.0): Downloading (100%)         
  - Updating drupal/search_api (1.9.0 => 1.10.0): Loading from cache
  - Installing drupal/scheduled_publish (1.2.0): Loading from cache
  - Installing drupal/restrict_ip (1.3.0): Loading from cache
  - Applying patches for drupal/restrict_ip
    https://www.drupal.org/files/issues/2905569_check_user_authentication.patch (2905569 - Avoid redirect loop for authenticated users)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2905569_check_user_authentication.patch

  - Updating drupal/metatag (1.5.0 => 1.7.0): Loading from cache
  - Updating drupal/pathauto (1.2.0 => 1.3.0): Loading from cache
  - Applying patches for drupal/pathauto
    https://www.drupal.org/files/issues/2018-05-11/pathauto-duplicate_ignore_words_config_schema-2961850-8.patch (Issue 2961850 - Duplicated keys in schema)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2018-05-11/pathauto-duplicate_ignore_words_config_schema-2961850-8.patch

  - Updating drupal/features (3.7.0 => 3.8.0): Loading from cache
  - Updating drupal/lightning_core (2.8.0 => 2.10.0): Loading from cache
  - Installing drupal/media_entity_instagram (2.0.0-alpha1): Loading from cache
  - Applying patches for drupal/media_entity_instagram
    https://www.drupal.org/files/issues/2917454-2.patch (2917454 - Field formatter test is broken)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2917454-2.patch

  - Installing drupal/entity_embed (1.0.0-beta2): Loading from cache
  - Applying patches for drupal/entity_embed
    https://www.drupal.org/files/issues/2832504-2.patch (2832504 - Send the CKEditor instance ID to the embed.preview route)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2832504-2.patch

  - Installing drupal/entity_browser (2.0.0-alpha2): Loading from cache
  - Applying patches for drupal/entity_browser
    https://www.drupal.org/files/issues/entity-browser-view-context-2865928-14.patch (2865928 - The View widget should filter based on field settings)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/entity-browser-view-context-2865928-14.patch
    https://www.drupal.org/files/issues/2877751-27-8.x-2.x.patch (2877751 - Inform users how many items they can add to a field that uses an entity browser)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/2877751-27-8.x-2.x.patch

  - Updating drupal/lightning_media (2.3.0 => 2.4.0): Loading from cache
  - Installing drupal/panels (4.3.0): Loading from cache
  - Applying patches for drupal/panels
    https://www.drupal.org/files/issues/panels-ipe-2878684-3.patch (2878684 - Use String.match to correlate regions when switching Layouts in Panels IPE)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/panels-ipe-2878684-3.patch
    https://www.drupal.org/files/issues/panels-ipe-propogate-errors-2825034-5.patch (2825034 - Form error messages do not appear in IPE)
   Could not apply patch! Skipping. The error was: Cannot apply patch https://www.drupal.org/files/issues/panels-ipe-propogate-errors-2825034-5.patch

  - Updating drupal/lightning_layout (1.3.0 => 1.4.0): Loading from cache
  - Updating drupal/consumers (1.0.0 => 1.2.0): Loading from cache
  - Updating drupal/simple_oauth (3.8.0 => 3.10.0): Loading from cache
  - Updating drupal/schemata (1.0.0-alpha3 => 1.0.0-alpha5): Loading from cache
  - Updating drupal/openapi (1.0.0-beta1 => 1.0.0-beta2): Loading from cache
  - Installing drupal/openapi_ui (1.0.0-rc1): Loading from cache
  - Installing swagger-api/swagger-ui (v3.19.2): Loading from cache
  - Installing drupal/openapi_ui_swagger (1.0.0-rc2): Loading from cache
  - Installing drupal/openapi_ui_redoc (1.0.0-rc2): Loading from cache
  - Updating drupal/lightning_api (2.5.0 => 2.7.0): Loading from cache
  - Updating dflydev/dot-access-configuration (v1.0.2 => v1.0.3): Loading from cache
  - Updating drupal/block_field (1.0.0-alpha7 => 1.0.0-alpha8): Loading from cache
  - Updating commerceguys/addressing (v1.0.0 => v1.0.1): Loading from cache
  - Updating drupal/bootstrap (3.12.0 => 3.13.0): Loading from cache
  - Updating degov/degov (2.0.13 => 2.0.14): Loading from cache
  - Updating drupal/spamspan dev-1.x (279061d => 21544d3):  Checking out 21544d369a
Package egeloen/http-adapter is abandoned, you should avoid using it. Use php-http/httplug instead.
Package phansys/getid3 is abandoned, you should avoid using it. Use james-heinrich/getid3 instead.
Writing lock file
Generating autoload files
  - .csslintrc (https://cgit.drupalcode.org/drupal/plain/.csslintrc?h=8.5.7): Downloading (100%)         
  - .editorconfig (https://cgit.drupalcode.org/drupal/plain/.editorconfig?h=8.5.7): Downloading (100%)         
  - .eslintignore (https://cgit.drupalcode.org/drupal/plain/.eslintignore?h=8.5.7): Downloading (100%)         
  - .eslintrc.json (https://cgit.drupalcode.org/drupal/plain/.eslintrc.json?h=8.5.7): Downloading (100%)         
  - .gitattributes (https://cgit.drupalcode.org/drupal/plain/.gitattributes?h=8.5.7): Downloading (100%)         
  - .ht.router.php (https://cgit.drupalcode.org/drupal/plain/.ht.router.php?h=8.5.7): Downloading (100%)         
  - .htaccess (https://cgit.drupalcode.org/drupal/plain/.htaccess?h=8.5.7): Downloading (100%)         
  - index.php (https://cgit.drupalcode.org/drupal/plain/index.php?h=8.5.7): Downloading (100%)         
  - robots.txt (https://cgit.drupalcode.org/drupal/plain/robots.txt?h=8.5.7): Downloading (100%)         
  - sites/default/default.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/default/default.services.yml?h=8.5.7): Downloading (connectDownloading (100%)         
  - sites/default/default.settings.php (https://cgit.drupalcode.org/drupal/plain/sites/default/default.settings.php?h=8.5.7): Downloading (connectDownloading (100%)         
  - sites/development.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/development.services.yml?h=8.5.7): Downloading (100%)         
  - sites/example.settings.local.php (https://cgit.drupalcode.org/drupal/plain/sites/example.settings.local.php?h=8.5.7): Downloading (connecting.Downloading (100%)         
  - sites/example.sites.php (https://cgit.drupalcode.org/drupal/plain/sites/example.sites.php?h=8.5.7): Downloading (100%)         
  - update.php (https://cgit.drupalcode.org/drupal/plain/update.php?h=8.5.7): Downloading (100%)         
  - web.config (https://cgit.drupalcode.org/drupal/plain/web.config?h=8.5.7): Downloading (100%)         
> DrupalComposer\DrupalScaffold\Plugin::scaffold
  - .csslintrc (https://cgit.drupalcode.org/drupal/plain/.csslintrc?h=8.5.7): Downloading (100%)         
  - .editorconfig (https://cgit.drupalcode.org/drupal/plain/.editorconfig?h=8.5.7): Downloading (100%)         
  - .eslintignore (https://cgit.drupalcode.org/drupal/plain/.eslintignore?h=8.5.7): Downloading (100%)         
  - .eslintrc.json (https://cgit.drupalcode.org/drupal/plain/.eslintrc.json?h=8.5.7): Downloading (100%)         
  - .gitattributes (https://cgit.drupalcode.org/drupal/plain/.gitattributes?h=8.5.7): Downloading (100%)         
  - .ht.router.php (https://cgit.drupalcode.org/drupal/plain/.ht.router.php?h=8.5.7): Downloading (100%)         
  - .htaccess (https://cgit.drupalcode.org/drupal/plain/.htaccess?h=8.5.7): Downloading (100%)         
  - index.php (https://cgit.drupalcode.org/drupal/plain/index.php?h=8.5.7): Downloading (100%)         
  - robots.txt (https://cgit.drupalcode.org/drupal/plain/robots.txt?h=8.5.7): Downloading (100%)         
  - sites/default/default.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/default/default.services.yml?h=8.5.7): Downloading (connectDownloading (100%)         
  - sites/default/default.settings.php (https://cgit.drupalcode.org/drupal/plain/sites/default/default.settings.php?h=8.5.7): Downloading (connectDownloading (100%)         
  - sites/development.services.yml (https://cgit.drupalcode.org/drupal/plain/sites/development.services.yml?h=8.5.7): Downloading (100%)         
  - sites/example.settings.local.php (https://cgit.drupalcode.org/drupal/plain/sites/example.settings.local.php?h=8.5.7): Downloading (connecting.Downloading (100%)         
  - sites/example.sites.php (https://cgit.drupalcode.org/drupal/plain/sites/example.sites.php?h=8.5.7): Downloading (100%)         
  - update.php (https://cgit.drupalcode.org/drupal/plain/update.php?h=8.5.7): Downloading (100%)         
  - web.config (https://cgit.drupalcode.org/drupal/plain/web.config?h=8.5.7): Downloading (100%)         
demo@prod ~/d/d/p/w/html> cd docroot/
demo@prod ~/d/d/p/w/h/docroot> drush updb
 [warning]  (Currently using Styled Google Map Google Maps API key or Client ID was not found. As of 2016/06/22, keyless access is no longer supported and it may impact rendering of maps. For more information visit: <a href="http://googlegeodevelopers.blogspot.ca/2016/06/building-for-scale-updates-to-google.html">http://googlegeodevelopers.blogspot.ca/2016/06/building-for-scale-updates-to-google.html</a>)
 -------------------------- ----------- --------------- ---------------------------------------------------------------------------------------- 
  Module                     Update ID   Type            Description                                                                             
 -------------------------- ----------- --------------- ---------------------------------------------------------------------------------------- 
  degov                      8445        hook_update_n   Remove password policy module                                                           
  degov                      8447        hook_update_n   Add missing image preview icons                                                         
  degov                      8448        hook_update_n   Safe scheduled_updates uninstall                                                        
  degov_common               8027        hook_update_n   Updated media browser view                                                              
  degov_media_audio          8012        hook_update_n   Add missing view mode preview wide                                                      
  degov_media_citation       8012        hook_update_n   Updated date format in media type view mode's display to DD.MM.YYYY                     
  degov_media_gallery        8012        hook_update_n   Add missing view mode preview wide                                                      
  degov_media_gallery        8013        hook_update_n   Added source field to generic media type 'gallery'                                      
  degov_media_image          8002        hook_update_n   Add type svg to image types                                                             
  degov_media_image          8003        hook_update_n   Add missing view mode preview wide                                                      
  degov_media_video_upload   8012        hook_update_n   Add missing view mode preview wide                                                      
  degov_node_press           8014        hook_update_n   Updated date format in node view mode's display to DD.MM.YYYY                           
  degov_paragraph_header     8001        hook_update_n   Remove the unused rich text field field_header_text                                     
  pathauto                   8107        hook_update_n   Initialize the new safe tokens setting.                                                 
  webform                    8137        hook_update_n   Issue #2983137: WYSIWYG editor not saving inline images for advanced htmltext editor.   
  webform                    8138        hook_update_n   Issue #2947303: Location element's geocomplete library is not supported.                
  webform                    8139        hook_update_n   Issue #2918669: Elements separated by conditional inside multistep wizard are both      
                                                         saved to submission.                                                                    
  webform                    8140        hook_update_n   Issue #2994411: Allow help to be hidden.                                                
  webform                    8141        hook_update_n   Issue #2993327: Provide Default View to Override Submission Results Tab.                
  webform                    8142        hook_update_n   Issue #2995413: Allow email handler theme to be customized.                             
  webform                    8143        hook_update_n   Issue #2943879: How to display alternate text when the user is not allowed to create a  
                                                         Webform?                                                                                
  webform                    8144        hook_update_n   Issue #2998239: Swift Mailer no longer working after custom email handler theme option  
                                                         was added                                                                               
  webform                    8145        hook_update_n   Issue #3002183: Upload Total Limit instead of Upload File limit.                        
  webform                    8146        hook_update_n   Issue #3000542: Remove webform_devel.module logger debugging.                           
 -------------------------- ----------- --------------- ---------------------------------------------------------------------------------------- 

 Do you wish to run the specified pending updates? (yes/no) [yes]:
 > yes

 [notice] Update started: webform_update_8137
 [ok] Update completed: webform_update_8137
 [notice] Update started: webform_update_8138
 [ok] Update completed: webform_update_8138
 [notice] Update started: webform_update_8139
 [ok] Update completed: webform_update_8139
 [notice] Update started: webform_update_8140
 [ok] Update completed: webform_update_8140
 [notice] Update started: webform_update_8141
 [notice] The new webform submissions view has been created.
 [ok] Update completed: webform_update_8141
 [notice] Update started: webform_update_8142
 [ok] Update completed: webform_update_8142
 [notice] Update started: webform_update_8143
 [ok] Update completed: webform_update_8143
 [notice] Update started: degov_update_8445
 [ok] Update completed: degov_update_8445
 [notice] Update started: webform_update_8144
 [ok] Update completed: webform_update_8144
 [notice] Update started: webform_update_8145
 [ok] Update completed: webform_update_8145
 [notice] Update started: degov_media_gallery_update_8012
 [ok] Update completed: degov_media_gallery_update_8012
 [notice] Update started: degov_media_image_update_8002
 [ok] Update completed: degov_media_image_update_8002
 [notice] Update started: degov_update_8447
 [ok] Update completed: degov_update_8447
 [notice] Update started: degov_paragraph_header_update_8001
 [ok] Update completed: degov_paragraph_header_update_8001
 [notice] Update started: pathauto_update_8107
 [ok] Update completed: pathauto_update_8107
 [notice] Update started: degov_node_press_update_8014
 [ok] Update completed: degov_node_press_update_8014
 [notice] Update started: degov_media_video_upload_update_8012
 [notice] core.entity_form_display.media.video_upload.default rewritten by degov_media_video_upload
 [notice] core.entity_form_display.media.video_upload.media_browser rewritten by degov_media_video_upload
 [notice] core.entity_view_display.media.video_upload.default rewritten by degov_media_video_upload
 [ok] Update completed: degov_media_video_upload_update_8012
 [notice] Update started: degov_media_image_update_8003
 [notice] core.entity_form_display.media.image.default rewritten by degov_media_image
 [notice] core.entity_form_display.media.image.media_browser rewritten by degov_media_image
 [notice] core.entity_view_display.media.image.default rewritten by degov_media_image
 [ok] Update completed: degov_media_image_update_8003
 [notice] Update started: degov_media_gallery_update_8013
 [ok] Update completed: degov_media_gallery_update_8013
 [notice] Update started: degov_media_citation_update_8012
 [ok] Update completed: degov_media_citation_update_8012
 [notice] Update started: degov_media_audio_update_8012
 [notice] core.entity_form_display.media.audio.default rewritten by degov_media_audio
 [notice] core.entity_form_display.media.audio.media_browser rewritten by degov_media_audio
 [notice] core.entity_view_display.media.audio.default rewritten by degov_media_audio
 [ok] Update completed: degov_media_audio_update_8012
 [notice] Update started: degov_common_update_8027
 [warning] file_get_contents(profiles/contrib/degov/modules/degov_common/views.view.media.yml): failed to open stream: No such file or directory degov_common.install:137
 [warning] Invalid argument supplied for foreach() NestedArray.php:327
 [ok] Update completed: degov_common_update_8027
 [notice] Update started: degov_update_8448
 [ok] Update completed: degov_update_8448
 [notice] Update started: webform_update_8146
 [ok] Update completed: webform_update_8146
 [success] Finished performing updates.
demo@prod ~/d/d/p/w/h/docroot> 
```