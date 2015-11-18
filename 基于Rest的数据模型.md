## Rest Model
oms-server 的数据模型主要依赖`loop-connector-rest`和`loopback-datasource-juggler`,是loopback框架下的两个重要组件。

### 怎样定义model?
1. 在javascript代码中定义model

```js
```

2. 通过JSON模板定义model

```js
```
### 内建数据类型
- String
- Number
- Boolean
- Array
- Object

### 

Datasource 和 Connecter

1. DataSource extends from ModelBuilder
2. DataSource inherits that function from ModelBuilder
3. DataSource adds behaviors to model classes by mixing in methods from the DataAccessObject into the model class
  
  ```
  create, find,findById, update, delete,save, all, deleteAll
  ```
4. The connector module can export an initialize function to be called by the owning DataSource instance.
   ```js
   exports.initialize = function (dataSource, postInit) {

    var settings = dataSource.settings || {}; // The settings is passed in from the dataSource

    var connector = new MyConnector(settings); // Construct the connector instance
    dataSource.connector = connector; // Attach connector to dataSource
    connector.dataSource = dataSource; // Hold a reference to dataSource
    ...
};
   ```
5. Please note connector and dataSource set up a reference to each other.
   ```js
    dataSource.connector = connector; // Attach connector to dataSource
    connector.dataSource = dataSource; // Hold a reference to dataSource
   ```
   
6. all queries cached and executed when dataSource emit 'connected' event.
7. To disconnect from database server call dataSource.disconnect method. This call is forwarded to the connector if the connector have ability to connect/disconnect
8. DataSource has a built-in DataAccessObject to support CRUD operations. The connector can choose to use the CRUD DataAccessObject or define its own
   ```js
   User.create() --> dataSource.connector.create() --> Oracle.prototype.create()
   ```
9. Connectors are not used directly by application code. We create a DataSource to interact with the connector



