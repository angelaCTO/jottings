## Avro Schema Types
---

| Complex Types | Named ? | Required | Optional | Type | Rules | 
|--|--|--|--|--|--|
| **record** | named | type, name, fields, [aliases] | aliases, namespace, doc | dict| Record must be named and contain fixed set of named fields. |
| **map** | unnamed | type, value || dict| Map values must all have the same type; record field values can each be different. |
| **array** | unnamed | type, items |  | list| Items must be all of the same type; otherwise, to contain mixed types, it must be declared as an union. |
| **union** | unnamed | *(See Rules)* |  | list| Union must contain at least 2 types. If a default value has been provided, the type of the default must precede the type of the primary. Directly nested unions are not allowed. |
| **enum** | named |  type, name, symbols, [aliases], [default] | aliases, namespace, doc, default | dict| Symbols must be a list of unique strings; no duplicates. |
| **fixed** | named |  type, name, size, [aliases] | aliases, namespace | dict | Size must be an int that specifies the number of bytes per value |

---

| Attributes | Used By | Type | Notes |
|--|--|--|--|
| **fields** (req) | record | Dict| Required: name, type, [default], [aliases]. Optional: aliases, default, order, doc  In order to maintain full compatibility, always provide fields with a default and aliases attributes *(see notes)*.|
| **items** (req) | array | Depends; accepts all types | Describes the schema of the array's items|
| **values** (req) | map | All types / tagged union| Describes the schema of the map's values. |
| **symbols** (req) | enum |list | Symbols must be unique and Avro qualified strings Symbols cannot be modified once defined. |
| **size** (req) | fixed |int| Specifies the number of bytes per value. |
| **type** (req) | All types (except for primitives) | Depends | Types cannot be changed once defined. Work-around: To change the type for a named field, create a new field and set the default value to null in the original field.|
| **name** (req) | All named types; fields | string |Name must be an Avro qualified string. Names cannot be changed in named type once defined. Work-around: New names must be provided as an alias. |
| **aliases** (rec) | All named types; fields | list | Aliases must be Avro strings Providing an aliases list will allow for the renaming of fields in schema. |
| **default** (rec) | Most important for fields | All types except for unions | Providing a default value (null) will allow for removing fields from schema. If a default value is provided, its type must be declared first in the tagged union, i.e. the types list.|
| **namespace** (opt) | All named types; fields |string| Namespaces are Avro strings, case-sensitive, and dot-seperated. [fullname] = [namespace].[name] |
| **order** (opt) | fields| One of: "ascending" (default), "descending", or "ignore"| Specifies how field impacts sort ordering of the record.|
| **doc** (opt) | record, fields| string | Documentation. |

---

Notes:
* Denoted [attribute] is optional, but is recommended to maintain as a requirement when modifying schemas in order to enable full compatibility (backwards, forwards).
* Avro qualified names must conform to the following rules: 
	* Begin with [A-Za-z_]; following contains only [A-Za-z0-9_]
*  While evolving for future versions...
	* Providing a default value (null) will allow for removing fields from schema
	* Providing an aliases list will allow for the renaming of fields in schema
