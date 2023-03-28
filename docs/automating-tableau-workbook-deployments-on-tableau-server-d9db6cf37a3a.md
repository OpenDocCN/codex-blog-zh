# 在 Tableau 服务器上自动部署 Tableau 工作簿

> 原文：<https://medium.com/codex/automating-tableau-workbook-deployments-on-tableau-server-d9db6cf37a3a?source=collection_archive---------8----------------------->

## 使用 Tableau 服务器 API 在整个环境中自动提升工作簿

![](img/70e2f51d60b934553f3c05031beabe2f.png)

由 [ThisisEngineering RAEng](https://unsplash.com/@thisisengineering?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 在 [Unsplash](https://unsplash.com/s/photos/tableau-software?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 拍摄的照片

Tableau workbook promoter 的所有代码都可以在这个 [Github repo](https://github.com/ramdesh/tableau-workbook-promoter) 中找到。

[Tableau Server](https://www.tableau.com/products/new-features/data) 为组织提供了一个自行部署的解决方案，以按照他们自己的方式维护他们的报告基础设施和应用程序。像我们这样的组织通常会为其报告基础架构维护多个环境，而我们的环境是开发、试运行和生产环境。报告开发人员只能在开发环境中开发他们的报告仪表板，工作簿需要自动移动到试运行和生产环境中(没有人拥有试运行和生产的创建者权限)。这意味着像数据库连接这样的东西也需要修改，以便部署可以在更高的环境中成功完成。

我使用 [Python Tableau 服务器客户端](https://github.com/tableau/server-client-python)登录到上层 Tableau 环境并上传工作簿。这个库的问题是，虽然它接受像 db 连接 URL、用户名和密码这样的东西，但它不替换工作簿本身中的这些信息，这导致 Tableau server 在使用该库将工作簿上传到需要连接到不同数据库的不同环境时拒绝该工作簿。由于这个事实，我不得不创建一个方便的函数，在工作簿上运行一些正则表达式，根据上层环境的需要查找和替换数据库服务器和用户名字符串:

```
DB_URL = os.environ.get("DB_URL")
DB_PORT = os.environ.get("DB_PORT")
DB_NAME = os.environ.get("DB_NAME")
SUBSTITUTIONS = {
    "server": DB_URL,
    "port": DB_PORT,
    "dbname": DB_NAME,
}*def* replace_connection_items(workbook_file: *str*):
    *"""
    Replaces connection URLs in the workbook with provider var because Tableau doesn't
    accept new URLs in the request* ***:param*** *workbook_file: Path to workbook file* ***:return****: None
    """* file_contents = *open*(workbook_file, "r").read()
    *for* substitution *in* SUBSTITUTIONS.keys():
        *if* SUBSTITUTIONS[substitution] *is not None*:
            file_contents = re.sub(
                f"{substitution}='(.+?)'",
                f"{substitution}='{SUBSTITUTIONS[substitution]}'",
                file_contents,
            )
    *with open*(workbook_file, "w") *as* f:
        f.write(file_contents)
```

这将查看工作簿(它是一种奇怪的类似 XML 的格式)，并替换标签中的属性，如下所示:

```
<connection *authentication*='username-password' *class*='postgres' *dbname*='testdb' *one-time-sql*='' *port*='5432' *server*='testdb-postgres.cluster.us-east-1.rds.amazonaws.com' *sslmode*='require' *username*='readonly' *workgroup-auth-mode*='' />
```

完成后，我们可以继续将工作簿从`workbooks`文件夹中取出，并在登录后将它们移动到上层环境中:

```
tableau_auth = TSC.TableauAuth(USERNAME, PASSWORD)
server = TSC.Server(SERVER_URL)
server.version = "3.9"PATH_TO_WORKBOOK_FILES = "workbooks"
SKIP_DB_CONNECTION_CHECK = (
    *True
    if* os.environ.get("SKIP_DB_CONNECTION_CHECK", "false").lower() == "true"
    *else False* )
RUN_AS_JOB = *True if* os.environ.get("RUN_AS_JOB", "false").lower() == "true" *else False**def* upload_workbooks(
    workbooks_to_be_deployed: List[str],
    project_id: *str*,
):
    *"""
    Uploads given list of workbooks to a specified project.* ***:param*** *workbooks_to_be_deployed: List of workbooks to be deployed.* ***:param*** *project_id: ID of the project the workbooks need to be deployed to.* ***:return****: None
    """
    if* workbooks_to_be_deployed:
        connection_item = TSC.ConnectionItem()
        connection_item.connection_credentials = TSC.ConnectionCredentials(
            name=DB_USER, password=DB_PWD, embed=*True* )
        connection_item.server_address = DB_URL
        connection_item.server_port = DB_PORT
        *for* workbook *in* workbooks_to_be_deployed:
            logger.info(f"Uploading workbook {workbook}...")
            wb_item = TSC.WorkbookItem(project_id=project_id)
            logger.info("Replacing URL strings in workbook XML")
            replace_connection_items(workbook)
            logger.info(f"Uploading workbook {workbook}")
            *if* SKIP_DB_CONNECTION_CHECK:
                logger.info("Skipping database connection checks...")
            *if* RUN_AS_JOB:
                logger.info("Running deployment as background job...")
            server.workbooks.publish(
                wb_item,
                workbook,
                mode=TSC.Server.PublishMode.Overwrite,
                connections=[connection_item],
                skip_connection_check=SKIP_DB_CONNECTION_CHECK,
                as_job=RUN_AS_JOB,
            )
            logger.info(f"Completed publishing workbook {workbook}.")
    *else*:
        logger.info("No workbook(s) found to deploy. Exiting the process..")

*def* publish_workbooks():
    *"""
    Publishes workbooks to Tableau server.
    """* logger.info("Signing in to Tableau server...")
    *with* server.auth.sign_in(tableau_auth):
        all_project_items, pagination_item = server.projects.get()
        all_projects = [{project.name: project.id} *for* project *in* all_project_items]
        *if not* PROJECT_NAME:
            *raise ValueError*("Project name was not provided.")
        *if any*(PROJECT_NAME *in* project *for* project *in* all_projects):
            project_id = get_project_id_by_name(PROJECT_NAME, all_projects)
            workbooks_to_be_deployed = glob.glob(PATH_TO_WORKBOOK_FILES + "/*.twb*")
            logger.info(f"Found workbooks: {*str*(workbooks_to_be_deployed)}")
            upload_workbooks(workbooks_to_be_deployed, project_id)
        *else*:
            logger.error(f"Project {PROJECT_NAME} not found on server")
            *raise ValueError*(f"Project {PROJECT_NAME} not found on server")
```

注意这里的`SKIP_DB_CONNECTION_CHECK`和`RUN_AS_JOB`参数。这些是可配置的选项，可防止在数据库上运行大型查询的工作簿上载超时。上载工作簿时，Tableau server 会验证工作簿上的查询是否有效。`SKIP_DB_CONNECTION_CHECK`将完全跳过这个选项，而`RUN_AS_JOB`将把上传作为异步作业运行，并返回一个作业 ID，基本上消除了在完成部署之前等待查询完成的需要。

Tableau 还要求将工作簿放入一个项目中，在这种情况下，我将假设该项目已经存在于 Tableau server 中，我们将根据其名称获取该项目的项目 ID，这是从一个环境变量中获取的:

```
PROJECT_NAME = os.environ.get("TABLEAU_PROJECT", "")*def* get_project_id_by_name(
    project_name: *str*, all_projects: List[Dict[str, str]]
) -> *str*:
    *"""
    Evaluates all project list to get project ID for a project identified by name.* ***:param*** *project_name: Name of the project.* ***:param*** *all_projects: List of all projects available on the server.* ***:return****: String ID of the project.
    """* logger.info(f"Getting project Id for project {project_name}")
    *return* [
        project[project_name] *for* project *in* all_projects *if* project_name *in* project
    ][0]
```

您可以将这段代码绑定到您拥有的任何 CI/CD 平台，并让它在 Github repo 或压缩的工件文件上运行这段代码。你可以在 [Github](https://github.com/ramdesh/tableau-workbook-promoter) 上查看完整的代码，看看这些是如何一起工作的。