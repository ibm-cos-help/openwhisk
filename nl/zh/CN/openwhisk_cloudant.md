---

copyright:
  years: 2016, 2018
lastupdated: "2018-01-09"

---

{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# 使用 Cloudant 包
{: #openwhisk_catalog_cloudant}
`/whisk.system/cloudant` 包支持您使用 Cloudant 数据库。此包中包含以下操作和订阅源。

| 实体| 类型| 参数| 描述
|
| --- | --- | --- | --- |
| `/whisk.system/cloudant`| 包| dbname、host、username 和 password| 使用 Cloudant 数据库|
| `/whisk.system/cloudant/read`| 操作| dbname 和 id| 从数据库中读取文档|
| `/whisk.system/cloudant/write`| 操作| dbname、overwrite 和 doc| 将文档写入数据库|
| `/whisk.system/cloudant/changes`| 订阅源| dbname、filter、query_params、maxTriggers| 对数据库进行更改时触发触发器事件|
{: shortdesc}

以下主题将逐步指导您设置 Cloudant 数据库，配置关联的包以及如何使用 `/whisk.system/cloudant` 包中的操作和订阅源。

## 在 {{site.data.keyword.Bluemix_notm}} 中设置 Cloudant 数据库
{: #openwhisk_catalog_cloudant_in}

如果是在 {{site.data.keyword.Bluemix_notm}} 中使用 OpenWhisk，那么 OpenWhisk 将为 Cloudant 服务实例自动创建包绑定。如果不是在 {{site.data.keyword.Bluemix_notm}} 中使用 OpenWhisk 和 Cloudant，请跳至下一步。

1. 在 {{site.data.keyword.Bluemix_notm}} [仪表板](http://console.ng.Bluemix.net)中创建 Cloudant 服务实例。

  请确保为每个新服务实例创建一个凭证密钥。

2. 刷新名称空间中的包。刷新操作将自动为已定义凭证密钥的每个 Cloudant 服务实例创建包绑定。

  ```
wsk package refresh
  ```
  {: pre}
  ```
  created bindings:
  Bluemix_testCloudant_Credentials-1
  ```

  ```
  wsk package list
  ```
  {: pre}
  ```
  packages
  /myBluemixOrg_myBluemixSpace/Bluemix_testCloudant_Credentials-1 private binding
  ```

  现在，包绑定包含与 Cloudant 服务实例关联的凭证。

3. 检查以确认先前创建的包绑定是否已使用 Cloudant {{site.data.keyword.Bluemix_notm}} 服务实例主机和凭证进行配置。

  ```
  wsk package get /myBluemixOrg_myBluemixSpace/Bluemix_testCloudant_Credentials-1 parameters
  ```
  {: pre}
  ```
  ok: got package /myBluemixOrg_myBluemixSpace/Bluemix_testCloudant_Credentials-1, displaying field parameters
  ```
  ```json
  [
      {
          "key": "username",
          "value": "cdb18832-2bbb-4df2-b7b1-Bluemix"
      },
      {
          "key": "host",
          "value": "cdb18832-2bbb-4df2-b7b1-Bluemix.cloudant.com"
      },
      {
          "key": "password",
          "value": "c9088667484a9ac827daf8884972737"
      }
      ]
  ```

## 在 {{site.data.keyword.Bluemix_notm}} 外部设置 Cloudant 数据库
{: #openwhisk_catalog_cloudant_outside}

如果不是在 {{site.data.keyword.Bluemix_notm}} 中使用 OpenWhisk，或者如果要在 {{site.data.keyword.Bluemix_notm}} 外部设置 Cloudant 数据库，那么必须为 Cloudant 帐户手动创建包绑定。您需要 Cloudant 帐户主机名、用户名和密码。

1. 创建为您的 Cloudant 帐户配置的包绑定。

  ```
  wsk package bind /whisk.system/cloudant myCloudant -p username MYUSERNAME -p password MYPASSWORD -p host MYCLOUDANTACCOUNT.cloudant.com
  ```
  {: pre}
  

2. 验证包绑定是否存在。

  ```
  wsk package list
  ```
  {: pre}
  ```
packages
  /myNamespace/myCloudant private binding
  ```


## 侦听对 Cloudant 数据库的更改
{: #openwhisk_catalog_cloudant_listen}

### 过滤数据库更改事件

可以定义过滤函数，以避免不必要的更改事件触发触发器。

要创建新的过滤函数，可以使用操作。

使用以下过滤函数，创建 json 文档文件 `design_doc.json`
```json
{
  "doc": {
    "_id": "_design/mailbox",
    "filters": {
      "by_status": "function(doc, req){if (doc.status != req.query.status){return false;} return true;}"
    }
  }
}
```

使用过滤函数在数据库上创建设计文档
```
wsk action invoke /_/myCloudant/write -p dbname testdb -p overwrite true -P design_doc.json -r
```
新设计文档的信息会在屏幕上打印出来。
```json
{
    "id": "_design/mailbox",
    "ok": true,
    "rev": "1-5c361ed5141bc7856d4d7c24e4daddfd"
}
```

### 使用过滤函数创建触发器

可以使用 `changes` 订阅源来配置服务，以在每次对 Cloudant 数据库进行更改时都触发触发器。参数如下所示：

- `dbname`：Cloudant 数据库的名称。
- `maxTriggers`：达到此限制时，停止触发触发器。缺省值为无限。
- `filter`：在设计文档上定义的过滤函数。
- `query_params`：过滤函数的额外查询参数。


1. 使用先前创建的包绑定中的 `changes` 订阅源来创建触发器。包含 `filter` 和 `query_params` 函数以在添加或修改文档且状态为 `new` 时触发触发器。确保将 `/_/myCloudant` 替换为您的包名。

  ```
  wsk trigger create myCloudantTrigger --feed /_/myCloudant/changes \
  --param dbname testdb \
  --param filter "mailbox/by_status" \
  --param query_params '{"status":"new"}'
  ```
  {: pre}
  ```
ok: created trigger feed myCloudantTrigger
  ```

2. 轮询激活。

  ```
wsk activation poll
  ```
  {: pre}

3. 在 Cloudant 仪表板中，修改现有文档或创建新文档。

4. 针对每次文档更改，仅在文档状态根据过滤函数和查询参数为 `new` 时，才会观察到 `myCloudantTrigger` 触发器的新激活。
  
  **注**：如果无法观察到新激活，请查看有关对 Cloudant 数据库执行读写操作的后续各部分。测试以下读写步骤将帮助验证 Cloudant 凭证是否正确。
  
  现在，可以创建规则并将其关联到操作，以对文档更新做出反应。
  
  生成的事件的内容具有以下参数：
  
  - `id`：文档标识。
  - `seq`：Cloudant 生成的序列标识。
  - `changes`：一组对象，其中每个对象都有 `rev` 字段，用于包含文档的修订版标识。
  
  触发器事件的 JSON 表示如下所示：
  
  ```json
  {
      "id": "6ca436c44074c4c2aa6a40c9a188b348",
      "seq": "2-g1AAAAL9aJyV-GJCaEuqx4-BktQkYp_dmIfC",
      "changes": [
          {
              "rev": "2-da3f80848a480379486fb4a2ad98fa16"
          }
      ]
  }
  ```
  
## 写入 Cloudant 数据库
{: #openwhisk_catalog_cloudant_write}

可以使用操作将文档存储在名为 `testdb` 的 Cloudant 数据库中。确保此数据库在 Cloudant 帐户中存在。

1. 使用先前创建的包绑定中的 `write` 操作来存储文档。确保将 `/_/myCloudant` 替换为您的包名。

  ```
  wsk action invoke /_/myCloudant/write --blocking --result --param dbname testdb --param doc "{\"_id\":\"heisenberg\",\"name\":\"Walter White\"}"
  ```
  ```
  ok: invoked /_/myCloudant/write with id 62bf696b38464fd1bcaff216a68b8287
  ```
  ```json
  {
    "id": "heisenberg",
    "ok": true,
    "rev": "1-9a94fb93abc88d8863781a248f63c8c3"
  }
  ```

2. 通过在 Cloudant 仪表板中浏览以查找该文档，验证该文档是否存在。

  `testdb` 数据库的仪表板 URL 类似于以下内容：`https://MYCLOUDANTACCOUNT.cloudant.com/dashboard.html#database/testdb/_all_docs?limit=100`。


## 从 Cloudant 数据库进行读取
{: #openwhisk_catalog_cloudant_read}

可以使用操作从名为 `testdb` 的 Cloudant 数据库中访存文档。确保此数据库在 Cloudant 帐户中存在。

- 使用先前创建的包绑定中的 `read` 操作来访存文档。确保将 `/_/myCloudant` 替换为您的包名。

  ```
  wsk action invoke /_/myCloudant/read --blocking --result --param dbname testdb --param id heisenberg
  ```
  {: pre}
  ```json
  {
    "_id": "heisenberg",
    "_rev": "1-9a94fb93abc88d8863781a248f63c8c3",
    "name": "Walter White"
  }
  ```

## 使用操作序列和更改触发器处理 Cloudant 数据库中的文档
{: #openwhisk_catalog_cloudant_read_change notoc}

您可以使用规则中的操作序列来访存和处理与 Cloudant 更改事件关联的文档。

下面是处理文档的操作的样本代码：
```javascript
function main(doc){
  return { "isWalter:" : doc.name === "Walter White"};
}
```

创建用于处理 Cloudant 中文档的操作：
```
wsk action create myAction myAction.js
```
{: pre}

要从数据库读取文档，可以使用 Cloudant 包中的 `read` 操作。`read` 操作可随 `myAction` 一起编写以创建操作序列。
```
wsk action create sequenceAction --sequence /_/myCloudant/read,myAction
```
{: pre}

操作 `sequenceAction` 可以在对新 Cloudant 触发器事件激活该操作的规则中使用。
```
wsk rule create myRule myCloudantTrigger sequenceAction
```
{: pre}

**注**：Cloudant `changes` 触发器以前支持参数 `includeDoc`，但现在不再支持该参数。
  您可以重新创建之前使用 `includeDoc` 创建的触发器。请执行以下步骤来重新创建触发器： 
  ```
  wsk trigger delete myCloudantTrigger
  ```
  {: pre}
  ```
  wsk trigger create myCloudantTrigger --feed /_/myCloudant/changes --param dbname testdb
  ```
  {: pre}

  此示例可用于创建操作序列，以读取更改后的文档并调用现有操作。
  请务必禁用不再有效的任何规则，然后使用操作序列模式创建新规则。

