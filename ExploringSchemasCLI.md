# Exploring the schemas in an iModel using the iModel Console

This section uses the [iModel Console](https://imodelconsole.bentley.com) to execute metadata queries against the `Bay Town Process Plant` sample iModel.

See [Exploring An iModel](ExploringAniModel.md) for instructions on opening an iModel in the iModel Console.

## The ECDbMeta Schema

The `ECDbMeta` Schema exposes the `ec_` tables in the iModel via ECSQL.  The most important entity classes are `ECSchemaDef`, `ECClassDef` and `ECPropertyDef`.  There are relationships that connect schemas to items in a schema and properties to the classes they are defined in.  These are important but there is one additional relationship that is required if you want to handle class inheritance, `ClassHasAllBaseClasses` this link table relationship flattens out the class hierarchy for each class so it can be queried without a recursive query.

  > NOTE: The `ECInstanceId` of an `ECClassDef` is the `ECClassId` of an instance of that class.

## ECDbMeta Queries

1. Query for all Schemas

    ```SQL
    SELECT * FROM ECDbMeta.ECSchemaDef
    ```

    > NOTE: When writing ECSQL you can use the schema name or the schema alias.  e.g. `BisCore.Element` is the same as `bis.Element`

1. Query for all Entity Classes

    ```SQL
    SELECT * FROM ECDbMeta.ECClassDef where Type = 0
    ```

    > NOTE 1: Type is an Enumeration property, you can find the enumeration definition in the Schema Explorer by searching for 'ECClassType'.

    > NOTE 2: The other class types are as follows: Relationship is `1`, Struct is `2` and CustomAttribute is `3`.  Mixins are considered Entity classes.

1. Query for all classes in a schema

    ```SQL
    SELECT * FROM ECDbMeta.ECClassDef c WHERE c.Schema.Id IN (SELECT ECInstanceId FROM ECDbMeta.ECSchemaDef WHERE Name = 'BisCore')
    ```

    > NOTE: This could also be done by JOINing with the `ECSchemaDef` table.

1. Getting a formatted class name from the id of a class

    ```SQL
    SELECT ec_classname(ECInstanceId) as formatted_name FROM ECDbMeta.ECClassDef
    ```

    > NOTE 1: ec_classname defaults to a format of `<schemaName>:<className>` but formatting can be controled via a format string, See [the ECSQL tutorial on built in functions](https://www.itwinjs.org/learning/ecsqltutorial/builtinfunctions/) for details.

    > NOTE 2: Using this function the previous query could be rewritten as: `SELECT * FROM ECDbMeta.ECClassDef WHERE ec_classname(ECInstanceId, 's') = 'BisCore'`

    > NOTE 3: this function works `ECClassId` of an instance as they are the same.

1. Get the id of a class from the formatted name

    ```SQL
    SELECT * FROM ECDbMeta.ECClassDef WHERE ECInstanceId = ec_classid('bis.element')
    ```

    > NOTE 1: This could also be done by matching on the `ECClassDef.Name` property and joining with ECSchemaDef class.

    > NOTE 2: It is important to note that ECSQL is case insensitive but all names are stored with case.  If you match on a name column (like `ECSchemaDef.Name`) a case insensitive comparison will be automatically used.  Using the function`ec_classname(<id>)` will return a string in the correct case and comparing against that string is case sensitive.

1. Selecting the properties of the `ProcessFunctional.NAMED_ITEM` class

    ```SQL
    SELECT c.Name, p.* FROM ECDbMeta.ECClassDef c JOIN ECDbMeta.ECPropertyDef p ON p.Class.Id = c.ECInstanceId WHERE c.ECInstanceId = ec_classid('ProcessFunctional.NAMED_ITEM')
    ```

    > NOTE: this only returns 2 properties because this query only returns the properties directly defined on the class.

1. Selecting the properties defined on `ProcessFunctional.NAMED_ITEM` and those inherited by the class

    ```SQL
    SELECT DISTINCT(p.Name) Property, ec_classname(p.Class.Id) OriginClass FROM ECDbMeta.ECClassDef c 
      JOIN ECDbMeta.ClassHasAllBaseClasses abc ON abc.SourceECInstanceId = c.ECInstanceId 
      JOIN ECDbMeta.ECPropertyDef p ON p.Class.Id = abc.TargetECInstanceId 
      WHERE c.ECInstanceId = ec_classid('ProcessFunctional.NAMED_ITEM')
    ```