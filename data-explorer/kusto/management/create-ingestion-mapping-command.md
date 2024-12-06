---
title: .create ingestion mapping command
description: Learn how to use the `.create ingestion mapping` command to create an ingestion mapping.
ms.reviewer: orspodek
ms.topic: reference
ms.date: 11/13/2024
---
# .create ingestion mapping command

> [!INCLUDE [applies](../includes/applies-to-version/applies.md)] [!INCLUDE [fabric](../includes/applies-to-version/fabric.md)] [!INCLUDE [azure-data-explorer](../includes/applies-to-version/azure-data-explorer.md)]

Creates an ingestion mapping that can be associated with a specific format and a specific table or database.

If a mapping with same name in the given scope already exists, `.create` fails. Use [`.create-or-alter`](create-or-alter-ingestion-mapping-command.md) instead.

## Permissions

At least [Database Ingestor](../access-control/role-based-access-control.md) permissions are required to create a database ingestion mapping, and at least [Table Ingestor](../access-control/role-based-access-control.md) permissions are required to create a table ingestion mapping.

## Syntax

`.create` `table` *TableName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

`.create` `database` *DatabaseName* `ingestion` *MappingKind* `mapping` *MappingName* *MappingFormattedAsJson*

[!INCLUDE [syntax-conventions-note](../includes/syntax-conventions-note.md)]

## Parameters

|Name|Type|Required|Description|
|--|--|--|--|
| *TableName* | `string` |  :heavy_check_mark: | The name of the table.|
| *DatabaseName* | `string` |  :heavy_check_mark: | The name of the database.|
| *MappingKind* | `string` |  :heavy_check_mark: | The type of mapping. Valid values are `csv`, `json`, `avro`, `parquet`, and `orc`.|
| *MappingName* | `string` |  :heavy_check_mark: | The name of the mapping.|
| *MappingFormattedAsJson* | `string` |  :heavy_check_mark: | The ingestion mapping definition formatted as a JSON value.|

> [!NOTE]
>
> * Once created, the mapping can be referenced by its name in ingestion commands, instead of specifying the complete mapping as part of the command.
> * If a mapping with the same name is created in both the table scope and the database scope, the mapping in the table scope will have higher priority.
> * When ingesting into a table and referencing a mapping whose schema does not match the ingested table schema, the ingest operation will fail.

## Examples

### Create a table and database with CSV ingestion mapping

The following example creates a table, `MyTable` with CSV ingestion mapping `Mapping1` and a database, `MyDatabase` with CSV ingestion mapping `Mapping2`.

```kusto
.create table MyTable ingestion csv mapping "Mapping1"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'

.create database MyDatabase ingestion csv mapping "Mapping2"
'['
'   { "column" : "rownumber", "DataType":"int", "Properties":{"Ordinal":"0"}},'
'   { "column" : "rowguid", "DataType":"string", "Properties":{"Ordinal":"1"}}'
']'
```

**Output**

| Name | Kind | Mapping | Database | Table |
|--|--|--|
| mapping1 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` | MyDatabase | MyTable |
| mapping2 | CSV  | `[{"Name":"rownumber","DataType":"int","CsvDataType":null,"Ordinal":0,"ConstValue":null},{"Name":"rowguid","DataType":"string","CsvDataType":null,"Ordinal":1,"ConstValue":null}]` | MyDatabase | |

### Create ingestion mapping with escape characters

The following example creates a table, `test_table` with JSON ingestion mapping `test_mapping_name`. To avoid errors, backslashes are used to escape single quotes in the JSON path strings.

```kusto
.create table test_table ingestion json mapping "test_mapping_name"
'['
'{"column":"timeStamp","path":"$[\'timeStamp\']","datatype":"","transform":null},{"column":"name","path":"$[\'name\']","datatype":"","transform":null},{"column":"x-opt-partition-key","path":"$[\'x-opt-partition-key\']","datatype":"","transform":null}'
']'
```

## Related content

* For detailed descriptions of various ingestion mapping formats, such as CSV, JSON, Avro, Parquet, and Orc, see [Data mappings](mappings.md).
* To learn more about JSON mapping as an ingestion source, see [JSON mapping](json-mapping.md)
