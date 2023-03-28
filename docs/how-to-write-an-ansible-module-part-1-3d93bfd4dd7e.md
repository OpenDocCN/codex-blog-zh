# 如何编写一个可解析的模块—第 1 部分

> 原文：<https://medium.com/codex/how-to-write-an-ansible-module-part-1-3d93bfd4dd7e?source=collection_archive---------2----------------------->

![](img/22aab307ea16f62ae2e38d26721e8cc6.png)

你肯定还记得你第一次运行一个可行的剧本:几行代码组合成一堆`tasks`,发挥了很大的作用。出于好奇，你肯定会问自己，那个叫做`module`的东西背后隐藏着什么。几个键/值对怎么可能抽象出通常几十行的 bash 代码？嗯，我们知道在软件工程中没有魔法这种东西，对于 Ansible 和 Ansible 模块也是如此。

来自 Ansible [文档](https://docs.ansible.com/ansible/latest/dev_guide/developing_modules_general.html)的一个`module`是:

"*ansi ble 代表您在本地或远程运行的可重用的独立脚本。模块与您的本地机器、API 或远程系统交互，以执行特定的任务…模块提供定义的接口，接受参数，并在退出前通过将 JSON 字符串打印到 stdout 将信息返回给 Ansible。*

这是我们感兴趣的部分(仍然来自 Ansible docs):

*如果您需要集合中数以千计的 Ansible 模块中没有的功能，您可以轻松地编写自己的定制模块。当你编写一个本地使用的模块时，你可以选择任何编程语言并遵循你自己的规则。*

简而言之，模块是一个抽象层，它隐藏了一段与机器交互的代码。如果您不喜欢 Ansible repo 上已经提供的内容，或者您需要一些不同的内容，您仍然可以轻松地编写自己的模块。

考虑到这一点，在本文和后续文章中，我们将看到如何应用 NTC 最佳实践编写一个 Ansible 模块。在本文的第一部分，我们将介绍编写名为`ntc_snmp`的 Cisco IOS 和 NXOS SNMP 模块示例所需的所有步骤，我们将在其中配置 SNMP 社区字符串、联系人和位置。作为一个要求，该模块应该是[幂等的](https://stackoverflow.com/questions/1077412/what-is-an-idempotent-operation)，并且如果传入的操作系统不是 IOS 或 NXOS，它会优雅地出错。

可完成的任务示例:

```
ntc_snmp:
  os: ""
  provider:
    username: "ntc_user"
    password: "ntc_password"
    hostname: ""
  community_strings:
    - type: "ro"
      string: "public"
  contact: "info@networktocode.com"
  location: "New_York"
  replace: true
```

让我们卷起袖子投入其中吧！

# 模块参数和代码入口点

首先，我们需要找到一种方法来传递我们的模块参数(例如，`contact`、`location`等)。)到我们的代码。我们可以用`AnsibleModule` import 很容易地做到这一点，它给了我们一个漂亮的字典，里面有我们起草模块所需的所有信息。

让我们看看`import`部分和模块参数:

```
#!/usr/bin/python3
try:
    from netmiko import ConnectHandler
    HAS_NETMIKO = True
except ImportError:
    HAS_NETMIKO = Falsefrom ansible.module_utils.basic import AnsibleModule
from ansible.module_utils.basic import missing_required_lib def main(): if not HAS_NETMIKO:
        module.fail_json(msg=missing_required_lib("netmiko")) provider_options = dict(
        username=dict(required=True),
        password=dict(required=True),
        hostname=dict(required=True),
    ) community_options = dict(
        type=dict(required=True, choices=['ro','rw']),
        string=dict(required=True, no_log=True),
    )

    argument_spec = dict(
        os=dict(type='list', required=True, choices=['ios','nxos']),
        provider=dict(type='dict', required=True, options=provider_options),
        community_strings=dict(type='list', elements='dict', subrequired=True, options=community_options),
        contact=dict(required=False),
        location=dict(required=False),
        replace=dict(type='bool', default=False)
      ) module = AnsibleModule(
        argument_spec=argument_spec,
        supports_check_mode=True,
        required_one_of=[['community_strings', 'contact', 'location']],
        )
```

让我们暂停一下，分析一下上面的代码。

在这个例子中，我们使用 Netmiko 来处理 SSH 会话，所以首先我们要检查是否安装了库。我们用一个`try`和`except`来创建一个布尔常量，稍后我们将使用它来验证是否安装了库。我们还进口了一些实用工具，如`AnsibleModule`和`missing_required_lib`，这将使我们以后的生活变得更加轻松。在这个例子中，我们只利用了几个`module_utils`进口，但我鼓励你在这里探索[看看有什么可用的，因为有一些可以为你做很多繁重的工作。](https://docs.ansible.com/ansible/latest/dev_guide/developing_module_utilities.html#standard-module-utilities)

然后我们定义我们的模块入口点`main()`，我们创建一个名为`argument_spec`的字典，包含我们来自模块参数的键/值对，其中 value 是一个字典，包含一些定义接受的模块参数类型的 kwargs。在我们的例子中，对于`os`、`provider`和`community_strings`，我们有`required=True`，这意味着这些是必须传递给我们的模块的参数。如果参数数据类型不同于字符串，您将需要指定，正如我们在其中一些类型中所做的那样:`type='list'`、`type='bool'`或`type='dict'`(注意，对于`list`，我们还指定了`elements`类型)。您也可以像我们在`os`(即`choices=["ios", "nxos"]`)中那样缩小到一组可能的选择，并像在`replace`(即`default=False`)中那样设置一个默认值。

简而言之,`argument_spec`的作用类似于模式验证，确保将正确的变量作为参数传递给模块。在这里，你可以探索所有可用的选项。

查看 Ansible [文档](https://docs.ansible.com/ansible/latest/reference_appendices/module_utils.html)我们可以看到`AnsibleModule`采用了不同类型的 kwargs。目前，我们传递包含模块参数的`argument_spec`;我们设置为`supports_check_mode=True`，所以我们的模块也可以在干模式下运行(所以现在你知道`ansible-playbook foo.yml --check`后面是什么了)；我们用`required_one_of`做了一种断言，在我们的模块中至少指定了`community_strings`、`contact`或`location`。简而言之，我们需要在我们的设备上推动这三个选项中的至少一个——否则，这个模块的目的是什么？。我鼓励你也研究一下`mutually_exclusive`、`required_together`，它们是对你的模块实施特定模式的一种强有力的方法。查看[和](https://mobygeek.net/blog/2016/02/16/ansible-module-development-parameters/)中的一些例子。

在我们继续编码之前，我们需要了解幂等工作流是如何在 Ansible 上构建的。每当我们运行一个模块时，Ansible 首先查看我们想要更改的设备或服务器配置。如果实际配置不同于我们的预期配置，则应用更改；否则，跳过该任务。

基于我们的示例，现在让我们假设我们想要更改 snmp 位置。模块做的第一件事是从模块参数构建预期的配置。然后，它将运行`show runninng-config`命令，在配置中找到 snmp 位置行，并将我们拥有的配置行(实际运行配置)与我们想要的(预期的)进行比较。不是 100%清楚？不要担心，当我们构建代码时，它会更有意义。

让我们为 cisco IOS 创建一个名为`ios_map_config_to_obj`的功能。我们将传递一个 SSH 会话(在单独的函数中创建)给它。然后，我们将 ssh 到我们的设备中，获取 SNMP 配置行，进行一些解析(如果需要)，并将配置映射到一个字典中。代码是不言自明的，所以，让我们来看看它:

```
def ios_map_config_to_obj(ssh_session): # initiate defualt "have" dict()
    have = {
        'location': None,
        'contact': None,
        'community_strings': None,
    }

    # send show command for "location" and map into dictionary
    location_output = ssh_session.send_command("show snmp location")
    if location_output:
        have['location'] = location_output

    # send show command for "contact" and map into dictionary
    contact_output = ssh_session.send_command("show snmp contact")
    if contact_output:
        have['contact'] = contact_output # initiate "community" dict() as we might have more than one community 
    community_dict = dict() # send show command for "community"
    community_output = ssh_session.send_command("show running-config | include snmp-server community") # parse "community" output and map into "community_output" dict()
    if community_output:
        for comm in community_output.splitlines():
            comm_string = comm.split()[-2]
            comm_type = comm.split()[-1] community_dict[comm_string] = comm_type # update "have" dict() with "community_output" dict()
        have['community_strings'] = community_dict # "have" dict() example
    #
    #  have = {
    #   "community_strings": {
    #       "ntc-private": "RW",
    #       "ntc-public": "RO",
    #       "public": "RW",
    #       "testro": "RW",
    #       "testwr": "RW"
    #     },
    #   "contact": "Mike",
    #   "location": "VG"
    # }
    return have
```

如您所见，我们运行了一系列 show 命令，并将它们映射到一个字典中。这将更容易比较实际配置和预期配置，并应用幂等原理。

接下来，我们现在必须将我们的模块参数映射到实际的 IOS 命令，这些命令稍后将被发送到设备。让我们定义一个名为`ios_map_obj_to_com`的函数，并传递`AnsibleModule`(它保存我们的模块参数)以及我们从`ios_map_config_to_obj`返回的字典。

为了简洁起见，我们将只考虑`contact`和`location`配置行。在注释行的帮助下，代码应该是不言自明的。

```
def ios_map_obj_to_commands(module, have):
    # module args unpacking
    want_contact = module.params.get('contact')
    want_location = module.params.get('location')
    want_replace = module.params.get('replace') # have dict() unpacking
    have_contact = have.get('contact')
    have_location = have.get('location') commands = list() # from module args, if we don't want to replace running-config
    if not replace:
        # if "contact" in module arg is different from "contact" in running-config,
        # append new config line to command list
        if want_contact != have_contact:
            commands.append('snmp-server contact {0}'.format(want_contact)) # if "location" in module arg is different from "location" in running-config,
        # append new config line to command list
        if want_location != have_location:
            commands.append('snmp-server location {0}'.format(want_location)) # return list of commands to push to device
        return commands # from module args: if we want to replace running-config
    if replace:
        # if we do not have "contact" in running-config and we have "contact" in module args
        if want_contact is None and have_contact:
            commands.append('no snmp-server contact {0}'.format(have_contact))
        # if we have "contact" in running-config and it's different from "contact" in module args
        if (want_contact is not None) and (want_contact != have_contact):
            commands.append('snmp-server contact {0}'.format(want_contact))

        # same above logic applies to "location"
        if want_location is None and have_location: 
            commands.append('no snmp-server location {0}'.format(have_location))
        if (want_location is not None) and (want_location != have_location):
            commands.append('snmp-server location {0}'.format(want_location))

        # return list of commands to push to device
        return commands
```

首先，我们解包`want`参数(模块参数)以及`have`参数。基于`replace`模块参数，我们触发不同的逻辑，并将我们的`have`与我们的`want`进行比较。在此基础上，我们将一个配置行添加到一个列表中，这个列表稍后将被推送到设备上——就这么简单。

现在让我们推送我们的配置并以 json 格式生成著名的 Ansible `result`。

在我们的`mian()`功能下，我们添加:

```
# This is required in order to call the right function based on OS
    want_call_dict = {
        'ios': ios_map_obj_to_commands,
        'nxos': nxos_map_obj_to_commands,
    } # Same here
    have_call_dict = {
        'ios': ios_map_config_to_obj,
        'nxos': nxos_map_config_to_obj,
    } # result dict() initialization with default changed=False
    result = dict(changed=False) # call the right have function based on OS. Pass ssh_session as arg
    have = have_call_dict[module.params.get('os')[0]](ssh_session(module)) # call the right want function based on OS. Pass have result and module args
    want = want_call_dict[module.params.get('os')[0]](module, have) # if we have want config...
    if want:
        # ...and not check_mode...
        if not module.check_mode:
            # ...then push cofig to device...
            ssh_session(module).send_config_set(want)
            # ...and update result dict()
            result.update(
                changed=True,
                have=have,
                cmds=want,
            )

    # return result dict() in json format.
    if result:
        module.exit_json(**result) if __name__ == "__main__":
    main()
```

这就是这篇文章的第一部分。在第二部分中，我们将看到如何编写`DOCUMENTATION`和`EXAMPLES`，以及如何为我们的模块构建和运行一些测试。

费德里科