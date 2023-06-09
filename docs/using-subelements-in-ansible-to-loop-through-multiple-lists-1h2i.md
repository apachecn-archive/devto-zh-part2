# 在 Ansible 中使用子元素遍历多个列表。

> 原文：<https://dev.to/engonzal/using-subelements-in-ansible-to-loop-through-multiple-lists-1h2i>

在开发 Ansible Galaxy [用户角色](https://galaxy.ansible.com/engonzal/users)时，我遇到了一种情况，我需要遍历列表中字典内的列表。对于这个特定的例子，我有一个用户列表，每个用户可以有多个授权的 sshkeys 存储在一个“pubkeys”值中。为了添加每个 sshkey，我在 Ansible 中使用了子元素来遍历多个列表。

这里有一个变量的例子。注意有两个用户，一个用户有两个公钥，另一个有一个:

```
host\_local\_users:
 - name: user1
 pubkeys:
 - 'ssh-rsa myr4nd0mk3y engonzal@home'
 - 'ssh-rsa my000r4nd0m000k3y000tw0 engonzal@workstation'
 - name: user2
 pubkeys:
 - 'ssh-rsa myr4nd0mk3y engonzal@home' 
```

## 创建任务

在这种情况下，“主机本地用户”是一个列表。“host_local_users”的每一项都是一个字典。键“pubkeys”是一个列表。现在假设我们希望有一个任务，为“name”中的用户添加 pubkeys 下的每个键。看起来应该是这样的:

```
- name: Add public keys to authorized\_hosts
 authorized\_key:
 user: "{{ item.0.name }}"
 state: "{{ item.0.state | default('present') }}"
 key: "{{ item.1 }}"
 loop: "{{ host\_local\_users | subelements('pubkeys', 'skip\_missing=True') }}" 
```

在这个任务中有很多事情要做！首先注意“loop”参数使用了我们的“host_local_users”变量。我们使用“子元素”过滤器为每个用户挑选 ssh 密钥列表。host_local_users 的每一项都用“item.0”表示。pubkeys 的每一项都由“item.1”表示(注意，我们向“subelements”过滤器提供了“pubkeys”)。

## 运行任务

实际上，即使只有两个用户，也会运行 authorized_key 任务 3 次，如下所示:

```
TASK [engonzal.users : Add public keys to authorized_hosts] ********************
changed: [instance] => (item=[{u'pubkeys': [u'ssh-rsa myr4nd0mk3y engonzal@home', u'ssh-rsa my000r4nd0m000k3y000tw0 engonzal@workstation'], u'name': u'user1'}, u'ssh-rsa myr4nd0mk3y engonzal@home'])

changed: [instance] => (item=[{u'pubkeys': [u'ssh-rsa myr4nd0mk3y engonzal@home', u'ssh-rsa my000r4nd0m000k3y000tw0 engonzal@workstation'], u'name': u'user1'}, u'ssh-rsa my000r4nd0m000k3y000tw0 engonzal@workstation'])

changed: [instance] => (item=[{u'pubkeys': [u'ssh-rsa myr4nd0mk3y engonzal@home'], u'name': u'user2'}, u'ssh-rsa myr4nd0mk3y engonzal@home']) 
```

这次运行根据我们放入变量“host_local_users”的内容，为 user1 添加了两个 ssh pubkey，为 user2 添加了一个 ssh pubkey。

我认为 Ansible 允许这些类型的复杂变量是非常酷的。许多人会认为，对于某些问题，使用 bash 或编程语言来完成复杂的任务更有意义。我发现让这些脚本幂等增加了 Ansible 已经包含的另一层复杂性。

要安装 Ansible，请查看我关于如何使用 virtualenvs 安装 Ansible 的帖子。要查看实践中的子元素，请看一下我在 Github 上的[“用户”角色中的任务。](https://github.com/engonzal/ansible_role_users/blob/master/tasks/main.yml)

post [使用 Ansible 中的子元素遍历多个列表。](https://buildahomelab.com/2018/11/03/subelements-ansible-loop-nested-lists/)最早出现在[建立家庭实验室](https://buildahomelab.com)。