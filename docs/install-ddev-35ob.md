# 安装 ddev

> 原文：<https://dev.to/patrickschanen/install-ddev-35ob>

#### [https://ddev.readthedocs.io/en/latest/](https://ddev.readthedocs.io/en/latest/)

```
 ~  curl -L https://raw.githubusercontent.com/drud/ddev/master/install_ddev.sh | bash

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2561  100  2561    0     0   8565      0 --:--:-- --:--:-- --:--:--  8565
ddev_linux.v1.2.0.tar.gz: OK
Download verified. Ready to place ddev in your /usr/local/bin.
Running "sudo mv /tmp/ddev /usr/local/bin/" Please enter your password if prompted.
[sudo] Passwort für psnebc: 
Bash completion for ddev was not installed. You may manually install /tmp/ddev_bash_completion.sh in your bash_completions.d directory.
ddev is now installed. Run "ddev" to verify your installation and see usage.
 ~  ddev 
This Command Line Interface (CLI) gives you the ability to interact with the ddev to create a development environment.

Usage:
  ddev [command]

Available Commands:
  auth-pantheon    Provide a machine token for the global pantheon auth.
  config           Create or modify a ddev project configuration in the current directory
  describe         Get a detailed description of a running ddev project.
  exec             Execute a shell command in the container for a service. Uses the web service by default.
  help             Help about any command
  hostname         Manage your hostfile entries.
  import-db        Import the database of an existing project to the dev environment.
  import-files     Import the uploaded files directory of an existing project to the default public upload directory of your project.
  list             List projects
  logs             Get the logs from your running services.
  pull             Import files and database using a configured provider plugin.
  remove           Remove the development environment for a project.
  restart          Restart the development environment for a project.
  restore-snapshot Restore a project's database to the provided snapshot version.
  snapshot         Create a database snapshot for one or more projects.
  ssh              Starts a shell session in the container for a service. Uses web service by default.
  start            Start a ddev project.
  stop             Stop the development environment for a project.
  version          print ddev version and component versions

Flags:
  -h, --help          help for ddev
  -j, --json-output   If true, user-oriented output will be in JSON format.

Use "ddev [command] --help" for more information about a command. 
```

### 首次 ssh 登录&测试 Drush

```
 ~/my-drupal8-site  ddev ssh
uid_1000@8e109bacec19:/var/www/html/web$ drush
Drush Commandline Tool 9.4.0

Run `drush help [command]` to view command-specific help.  Run `drush topic` to read even more documentation.

 Available commands:                                                                                                                         
 _global:                                                                                                                                    
   help                                       Display usage details for a command.                                                           
   list                                       List available commands.                                                                       
   generate (gen)                             Generate boilerplate code for modules/plugins/services etc.                                    
   runserver (rs, serve)                      Runs PHP's built-in http server for development.                                               
   updatedb (updb)                            Apply any database updates required (as with running update.php).                              
   browse                                     Display a link to a given path or open link in a browser.                                      
   version                                    Show Drush version.                                                                            
   drupal:directory (dd)                      Return the filesystem path for modules/themes and other key folders.                           
   updatedb:status (updbst)                   List any pending database updates.                                                             
 cache:                                                                                                                                      
   cache:get (cg)                             Fetch a cached object and display it.                                                          
   cache:clear (cc)                           Clear a specific cache, or all Drupal caches.                                                  
   cache:set (cs)                             Cache an object expressed in JSON or var_export() format.                                      
   cache:rebuild (cr, rebuild)                Rebuild a Drupal 8 site.                                                                       
 config:                                                                                                                                     
   config:pull (cpull)                        Export and transfer config from one environment to another.                                    
   config:get (cget)                          Display a config value, or a whole configuration object.                                       
   config:set (cset)                          Set config value directly. Does not perform a config import.                                   
   config:edit (cedit)                        Open a config file in a text editor. Edits are imported after closing editor.                  
   config:delete (cdel)                       Delete a configuration key, or a whole object.                                                 
   config:status (cst)                        Display status of configuration (differences between the filesystem configuration and database 
                                              configuration).                                                                                
   config:export (cex)                        Export Drupal configuration to a directory.                                                    
   config:import (cim)                        Import config from a config directory.                                                         
 core:                                                                                                                                       
   core:edit (conf, config)                   Edit drushrc, site alias, and Drupal settings.php files.                                       
   core:topic (topic)                         Read detailed documentation on a given topic.                                                  
   core:rsync (rsync)                         Rsync Drupal code or files to/from another server using ssh.                                   
   core:init (init)                           Enrich the bash startup file with bash aliases and a smart command prompt.                     
   core:status (status, st)                   An overview of the environment - Drush and Drupal.                                             
   core:execute (exec, execute)               Execute a shell command. Usually used with a site alias.                                       
   core:cron (cron)                           Run all cron hooks in all active modules for specified site.                                   
   core:requirements (status-report, rq)      Information about things that may be wrong in your Drupal installation.                        
 entity:                                                                                                                                     
   entity:updates (entup)                     Apply pending entity schema updates.                                                           
   entity:delete (edel)                       Delete content entities.                                                                       
 image:                                                                                                                                      
   image:flush (if)                           Flush all derived images for a given style.                                                    
   image:derive (id)                          Create an image derivative.                                                                    
 locale:                                                                                                                                     
   locale:check                               Checks for available translation updates.                                                      
   locale:update                              Imports the available translation updates.                                                     
 php:                                                                                                                                        
   php:eval (eval, ev)                        Evaluate arbitrary php code after bootstrapping Drupal (if available).                         
   php:script (scr)                           Run php a script after a full Drupal bootstrap.                                                
   php:cli (php, core:cli, core-cli)          Open an interactive shell on a Drupal site.                                                    
 pm:                                                                                                                                         
   pm:security (sec)                          Check Drupal Composer packages for pending security updates.                                   
   pm:enable (en)                             Enable one or more modules.                                                                    
   pm:uninstall (pmu)                         Uninstall one or more modules and their dependent modules.                                     
   pm:list (pml)                              Show a list of available extensions (modules and themes).                                      
 queue:                                                                                                                                      
   queue:run                                  Run a specific queue by name.                                                                  
   queue:list                                 Returns a list of all defined queues.                                                          
   queue:delete                               Delete all items in a specific queue.                                                          
 role:                                                                                                                                       
   role:create (rcrt)                         Create a new role.                                                                             
   role:delete (rdel)                         Delete a new role.                                                                             
   role:perm:add (rap, role-add-perm)         Grant specified permission(s) to a role.                                                       
   role:perm:remove (rmp, role-remove-perm)   Remove specified permission(s) from a role.                                                    
   role:list (rls)                            Display a list of all roles defined on the system.                                             
 site:                                                                                                                                       
   site:set (use)                             Set a site alias that will persist for the current session.                                    
   site:alias (sa)                            Show site alias details, or a list of available site aliases.                                  
   site:alias-convert (sa-convert, sac)       Convert legacy site alias files to the new yml format.                                         
   site:ssh (ssh)                             Connect to a Drupal site's server via SSH.                                                     
   site:install (si, sin)                     Install Drupal along with modules/themes/configuration/profile.                                
 sql:                                                                                                                                        
   sql:sync                                   Copy DB data from a source site to a target site. Transfers data via rsync.                    
   sql:connect                                A string for connecting to the DB.                                                             
   sql:create                                 Create a database.                                                                             
   sql:drop                                   Drop all tables in a given database.                                                           
   sql:cli (sqlc)                             Open a SQL command-line interface using Drupal's credentials.                                  
   sql:query (sqlq)                           Execute a query against a database.                                                            
   sql:dump                                   Exports the Drupal DB as SQL using mysqldump or equivalent.                                    
   sql:sanitize (sqlsan)                      Sanitize the database by removing or obfuscating user data.                                    
 state:                                                                                                                                      
   state:get (sget)                           Display a state value.                                                                         
   state:set (sset)                           Set a state value.                                                                             
   state:delete (sdel)                        Delete a state entry.                                                                          
 theme:                                                                                                                                      
   theme:enable (then)                        Enable one or more themes.                                                                     
   theme:uninstall (thun)                     Uninstall theme.                                                                               
 twig:                                                                                                                                       
   twig:unused                                Find potentially unused Twig templates.                                                        
   twig:compile (twigc)                       Compile all Twig template(s).                                                                  
 user:                                                                                                                                       
   user:login (uli)                           Display a one time login link for user ID 1, or another user.                                  
   user:information (uinf)                    Print information about the specified user(s).                                                 
   user:block (ublk)                          Block the specified user(s).                                                                   
   user:unblock (uublk)                       UnBlock the specified user(s).                                                                 
   user:role:add (urol, user-add-role)        Add a role to the specified user accounts.                                                     
   user:role:remove (urrol, user-remove-role) Remove a role from the specified user accounts.                                                
   user:create (ucrt)                         Create a user account.                                                                         
   user:cancel (ucan)                         Cancel user account(s) with the specified name(s).                                             
   user:password (upwd)                       Set the password for the user account with the specified name.                                 
 views:                                                                                                                                      
   views:dev (vd)                             Set several Views settings to more developer-oriented values.                                  
   views:list (vl)                            Get a list of all views in the system.                                                         
   views:execute (vex)                        Execute a view and show a count of the results, or the rendered HTML.                          
   views:analyze (va)                         Get a list of all Views and analyze warnings.                                                  
   views:enable (ven)                         Enable the specified views.                                                                    
   views:disable (vdis)                       Disable the specified views.                                                                   
 watchdog:                                                                                                                                   
   watchdog:show (wd-show, ws)                Show watchdog messages.                                                                        
   watchdog:list (wd-list)                    Interactively filter the watchdog message listing.                                             
   watchdog:delete (wd-del, wd-delete, wd)    Delete watchdog log records.                                                                   
   watchdog:show-one (wd-one)                 Show one log record by ID. 
```

### 通过 composer 安装一个 Drupal 项目(模块)

```
uid_1000@8e109bacec19:/var/www/html/web$ composer require drupal/webform
No composer.json in current directory, do you want to use the one at /var/www/html? [Y,n]? y
Using version ^5.0@RC for drupal/webform
./composer.json has been updated
> DrupalProject\composer\ScriptHandler::checkComposerVersion
Loading composer repositories with package information
Updating dependencies (including require-dev)
Package operations: 1 install, 0 updates, 0 removals
  - Installing drupal/webform (5.0.0-rc22): Downloading (100%)         
Writing lock file
Generating autoload files
> DrupalProject\composer\ScriptHandler::createRequiredFiles
uid_1000@8e109bacec19:/var/www/html/web$ drush en webform
 [success] Successfully enabled: webform
uid_1000@8e109bacec19:/var/www/html/web$ 
```

#### 测试 Drupal 控制台(Drupal)

```
 ~/my-drupal8-site  ddev ssh
uid_1000@8e109bacec19:/var/www/html/web$ drupal
Drupal Console version 1.8.0

Usage:
  command [options] [arguments]

Options:
  -h, --help             Display this help message
  -q, --quiet            Do not output any message
  -V, --version          Display this application version
      --ansi             Force ANSI output
      --no-ansi          Disable ANSI output
  -n, --no-interaction   Do not ask any interactive question
  -e, --env[=ENV]        The Environment name [default: "prod"]
      --root[=ROOT]      Define the Drupal root to be used in command execution
      --debug            Switches on debug mode
      --learning         Generate a verbose code output
  -c, --generate-chain   Shows command options and arguments as yaml output to be used in chain command
  -i, --generate-inline  Shows command options and arguments as inline command
  -d, --generate-doc     Shows command options and arguments as markdown
  -t, --target[=TARGET]  Site name you want to interact with (for local or remote sites)
  -l, --uri=URI          URI of the Drupal site to use (for multi-site environments or when running on an alternate port)
  -y, --yes              Skip confirmation and proceed
  -v|vv|vvv, --verbose   Increase the verbosity of messages: 1 for normal output, 2 for more verbose output and 3 for debug

Available commands:
  about                                   Displays basic information about Drupal Console project
  chain                                   Chain command execution
  check                                   System requirement checker
  composerize                             Converts Drupal codebase to composer.
  exec                                    Execute an external command.
  help                                    Displays help for a command
  init                                    Copy configuration files.
  list                                    Lists all available commands
  server (serve,rs)                       Runs PHP built-in web server
  shell                                   Open a shell providing an interactive REPL (Read–Eval–Print-Loop).
 cache
  cache:rebuild (cr,cc)                   Rebuild and clear all site caches.
  cache:tag:invalidate (cti)              Invalidate cache tags.
 config
  config:delete (cd)                      Delete configuration
  config:diff (cdi)                       Output configuration items that are different in active configuration compared with a directory.
  config:edit (ced,cdit)                  Change a configuration object with a text editor.
  config:export (ce)                      Export current application configuration.
  config:export:content:type (cect)       Export a specific content type and their fields.
  config:export:single (ces)              Export a single configuration or a list of configurations as yml file(s).
  config:export:view (cev)                Export a view in YAML format inside a provided module to reuse in other website.
  config:import (ci)                      Import configuration to current application.
  config:import:single (cis)              Import a single configuration or a list of configurations.
  config:override (co)                    Override config value in active configuration.
  config:validate (cv)                    Validate a drupal config against its schema
 create
  create:nodes (crn)                      Create dummy nodes for your Drupal 8 application.
  create:roles (crr)                      Create dummy roles for your Drupal 8 application.
  create:terms (crt)                      Create dummy terms for your Drupal 8 application.
  create:users (cru)                      Create dummy users for your Drupal 8 application.
  create:vocabularies (crv)               Create dummy vocabularies for your Drupal 8 application.
 cron
  cron:execute (croe,cre)                 Execute cron implementations by module or execute all crons
  cron:release (cror)                     Release cron system lock to run cron again
 database
  database:add (dba)                      Add a database to settings.php
  database:client (dbc)                   Launch a DB client if it's available
  database:connect (dbco,sqlc)            Shows DB connection
  database:drop (dbd)                     Drop all tables in a given database.
  database:dump (dbdu)                    Dump structure and contents of a database
  database:log:clear (dblc)               Remove events from DBLog table, filters are available
  database:log:poll (dblp)                Poll the watchdog and print new log entries every x seconds
  database:query (dbq,sqlq)               Executes a SQL statement directly as argument
  database:restore (dbr)                  Restore structure and contents of a database.
 debug
  debug:breakpoints (dbre)                Displays breakpoints available in application
  debug:cache:context (dcc)               Displays current cache context for the application.
  debug:chain (dch)                       List available chain files.
  debug:config (dc)                       List configuration objects names and single configuration object.
  debug:config:settings (dcs)             Displays current key:value on settings file.
  debug:config:validate (dcv)             Validate a schema implementation before a module is installed.
  debug:container (dco,cod)               Displays current services for an application.
  debug:cron (dcr)                        List of modules implementing a cron
  debug:database:log (dbb,ws)             Displays current log events for the application
  debug:database:table (ddt)              Show all tables in a given database.
  debug:dotenv                            Debug Dotenv debug values.
  debug:entity (de)                       Debug entities available in the system
  debug:event (dev)                       Displays current events 
  debug:image:styles (dis)                List image styles on the site
  debug:libraries (dl)                    Displays libraries available in application
  debug:module (dm)                       Displays current modules available for application
  debug:multisite (dmu,msd)               List all multi-sites available in system
  debug:permission (dp)                   Displays all user permissions.
  debug:plugin (dpl)                      Displays all plugin types.
  debug:queue (dq)                        Displays the queues of your application
  debug:roles (dusr)                      Displays current roles for the application
  debug:router (dr,rod)                   Displays current routes for the application or information for a particular route
  debug:settings (dse)                    List user Drupal Console settings.
  debug:site (dsi)                        List all known local and remote sites.
  debug:state (dst)                       Show the current State keys.
  debug:theme (dt,tde)                    Displays current themes for the application
  debug:theme:keys (dtk)                  Displays all theme keys provided by hook_theme functions
  debug:update (du,upd)                   Displays current updates available for the application
  debug:user (dus)                        Displays current users for the application
  debug:views (vde)                       Displays current views resources for the application
  debug:views:plugins (dvp)               Displays current views plugins for the application
 devel
  devel:dumper (dd)                       commands.devel.dumper.messages.change-devel-dumper-plugin
 develop
  develop:contribute                      Download Drupal + Drupal Console to contribute.
 docker
  docker:init                             Create a docker-compose.yml file
 dotenv
  dotenv:init                             Add support and required config to work with an .env file
 entity
  entity:delete (ed)                      Delete an specific entity
 field
  field:info (fii)                        View information about fields.
 generate
  generate:ajax:command (gac)             Generate & Register a custom ajax command
  generate:authentication:provider (gap)  Generate an Authentication Provider
  generate:breakpoint (gb)                Generate breakpoint
  generate:cache:context (gcc)            Generate a cache context
  generate:command (gco)                  Generate commands for the console.
  generate:controller (gcon)              Generate & Register a controller
  generate:entity:bundle (geb)            Generate a new content type (node / entity bundle)
  generate:entity:config (gec)            Generate a new config entity
  generate:entity:content (geco)          Generate a new content entity
  generate:event:subscriber (ges)         Generate an event subscriber
  generate:form (gf)                      Generate a new "FormBase"
  generate:form:alter (gfa)               Generate an implementation of hook_form_alter() or hook_form_FORM_ID_alter
  generate:form:config (gfc)              Generate a new "ConfigFormBase"
  generate:help (gh)                      Generate an implementation of hook_help()
  generate:jstest (gjt)                   Generate a JavaScript test.
  generate:module (gm)                    Generate a module.
  generate:module:file (gmf)              Generate a .module file
  generate:permissions (gp)               Generate module permissions
  generate:plugin:block (gpb)             Generate a plugin block
  generate:plugin:ckeditorbutton (gpc)    Generate CKEditor button plugin.
  generate:plugin:condition (gpco)        Generate a plugin condition.
  generate:plugin:field (gpf)             Generate field type, widget and formatter plugins.
  generate:plugin:fieldformatter (gpff)   Generate field formatter plugin.
  generate:plugin:fieldtype (gpft)        Generate field type plugin.
  generate:plugin:fieldwidget (gpfw)      Generate field widget plugin.
  generate:plugin:imageeffect (gpie)      Generate image effect plugin.
  generate:plugin:imageformatter (gpif)   Generate image formatter plugin.
  generate:plugin:mail (gpm)              Generate a plugin mail
  generate:plugin:migrate:process (gpmp)  Generate a migrate process plugin
  generate:plugin:migrate:source (gpms)   Generate a migrate source plugin
  generate:plugin:rest:resource (gprr)    Generate plugin rest resource
  generate:plugin:rulesaction (gpra)      commands.generate.plugin.rulesaction.description
  generate:plugin:skeleton (gps)          Generate an implementation of a skeleton plugin
  generate:plugin:type:annotation (gpta)  Generate a plugin type with annotation discovery
  generate:plugin:type:yaml (gpty)        Generate a plugin type with Yaml discovery
  generate:plugin:views:field (gpvf)      Generate a custom plugin view field.
  generate:post:update (gpu)              Generate an implementation of hook_post_update_NAME()
  generate:profile (gpr)                  Generate a profile.
  generate:routesubscriber (gr)           Generate a RouteSubscriber
  generate:service (gs)                   Generate service
  generate:site:alias (gsa)               Generates a site alias.
  generate:theme (gt)                     Generate a theme.
  generate:twig:extension (gte)           Generate a Twig extension.
  generate:update (gu)                    Generate an implementation of hook_update_N()
 image
  image:styles:flush (isf)                Execute flush function by image style or execute all flush images styles
 module
  module:dependency:install (modi)        Install dependencies module in the application
  module:download (mod)                   Download module or modules in application
  module:install (moi)                    Install module or modules in the application
  module:path (mop)                       Returns the relative path to the module (or absolute path)
  module:uninstall (mou)                  Uninstall module or modules in the application
  module:update (moup)                    Update core, module or modules in the application
 multisite
  multisite:new (mun,sn)                  Sets up the files for a new multisite install.
  multisite:update (muu)                  Update the files for a multisite installed.
 node
  node:access:rebuild (nar)               Rebuild node access permissions.
 queue
  queue:run (qr)                          Process the selected queue.
 quick
  quick:start                             Download, install and serve a new Drupal project
 role
  role:delete (rd)                        Delete roles for the application
  role:new (rn)                           Create roles for the application
 router
  router:rebuild (rr,ror)                 Rebuild routes for the application
 settings
  settings:set                            Change a specific setting value in DrupalConsole config file
 site
  site:import:local (sil)                 Import/Configure an existing local Drupal project
  site:install (si)                       Install a Drupal project
  site:maintenance (sma)                  Switch site into maintenance mode
  site:mode (smo)                         Switch system performance configuration
  site:new                                Download a new Drupal project
  site:statistics (sst)                   Show the current statistics of website.
  site:status (ss,st)                     View current Drupal Installation status
 state
  state:delete (std)                      Delete State
  state:override (sto)                    Override a State key.
 taxonomy
  taxonomy:term:delete (ttd)              Delete taxonomy terms from a vocabulary
 theme
  theme:download (thd)                    Download theme in application
  theme:install (thi)                     Install theme or themes in the application
  theme:path (thp)                        Returns the relative path to the theme (or absolute path)
  theme:uninstall (thu)                   Uninstall theme or themes in the application
 update
  update:entities (upe)                   Applying Entity Updates
  update:execute (upex,updb)              Execute a specific Update N function in a module, or execute all
 user
  user:create (uc)                        Create users for the application
  user:delete (ud)                        Delete users from the application
  user:login:clear:attempts (ulca,uslca)  Clear failed login attempts for an account.
  user:login:url (ulu,usli,uli)           Returns a one-time user login url.
  user:password:hash (uph,usph)           Generate a hash from a plaintext password.
  user:password:reset (upr,upsr)          Reset password for a specific user.
  user:role (ur)                          Adds/removes a role for a given user
 views
  views:disable (vd,vdi)                  Disable a View
  views:enable (ve)                       Enable a View

uid_1000@8e109bacec19:/var/www/html/web$ 
```