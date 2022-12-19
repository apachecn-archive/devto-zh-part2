# Ansible:目录布局

> 原文：<https://dev.to/tmidi/ansible-directory-layout-5edj>

根据 Ansible 的[最佳实践](http://docs.ansible.com/ansible/latest/playbooks_best_practices.html)，有许多可能的方式来组织剧本内容，这种布局的使用应该符合您的需求。我唯一强烈推荐的是使用*角色*而不是任务，这将给你带来灵活性和更好的代码组织。

Ansible 提供了两个目录布局的例子。第一个非常简单，是我在一个小环境中使用示例产品和暂存库存文件时使用的方法:

```
production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1                 # here we assign variables to particular groups
   group2                 # ""
host_vars/
   hostname1              # if systems need specific variables, put them here
   hostname2              # ""

library/                  # if any custom modules, put them here (optional)
module_utils/             # if any custom module_utils to support modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # "" 
```

Enter fullscreen mode Exit fullscreen mode

我总是使用下面的命令来创建上面的目录结构，并尽快开始使用:

```
  mkdir -p group_vars host_vars library module_utils filter_plugins
mkdir -p roles/common/{tasks,handlers,templates,files,vars,defaults,meta,library,module_utils,lookup_plugins}
touch production staging site.yml roles/common/{tasks,handlers,templates,files,vars,defaults,meta}/main.yml 

```

当我有一个更复杂的清单，包含多个组和子组时，我会选择以下替代目录布局:

```
inventories/
   production/
      hosts               # inventory file for production servers
      group_vars/
         group1           # here we assign variables to particular groups
         group2           # ""
      host_vars/
         hostname1        # if systems need specific variables, put them here
         hostname2        # ""

   staging/
      hosts               # inventory file for staging environment
      group_vars/
         group1           # here we assign variables to particular groups
         group2           # ""
      host_vars/
         stagehost1       # if systems need specific variables, put them here
         stagehost2       # ""

library/
module_utils/
filter_plugins/

site.yml
webservers.yml
dbservers.yml

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies
        library/          # roles can also include custom modules
        module_utils/     # roles can also include custom module_utils
        lookup_plugins/   # or other types of plugins, like lookup in this case 
```

Enter fullscreen mode Exit fullscreen mode

有了这个结构，我可以将每个库存文件及其`group_vars` \ `host_vars`放在单独的目录中。为了快速启动这个目录布局，我使用了以下命令:

```
  mkdir -p inventories/{production,staging}/{group_vars,host_vars}
touch inventories/{production,staging}/hosts
mkdir -p group_vars host_vars library module_utils filter_plugins
mkdir -p roles/common/{tasks,handlers,templates,files,vars,defaults,meta,library,module_utils,lookup_plugins}
touch site.yml roles/common/{tasks,handlers,templates,files,vars,defaults,meta}/main.yml 

```

我通常不对`roles/common`做任何工作，要创建一个新角色，我只需将`common`复制到一个新目录，这样我就有了一个角色模板，当我想创建一个新角色时就可以使用。