---
title: Operation Mapping
weight: 4
type: docs
aliases:
- /docs/connectors/table/kudu/operation-mapping.html
---
<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

<!--
{% comment %}
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
{% endcomment %}
-->

# KuduOperationMapper

This section describes the Operation mapping logic in more detail.

The connector supports insert, upsert, update, and delete operations. The operation to be performed can vary dynamically based on the record. To allow for more flexibility, it is also possible for one record to trigger 0, 1, or more operations.

For the highest level of control, implement the `KuduOperationMapper` interface.

If one record from the DataStream corresponds to one table operation, extend the `AbstractSingleOperationMapper` class. An array of column names must be provided. This must match the Kudu table's schema.

The `getField` method must be overridden, which extracts the value for the table column whose name is at the `i`th place in the `columnNames` array.

If the operation is one of (`CREATE, UPSERT, UPDATE, DELETE`) and doesn't depend on the input record (constant during the life of the sink), it can be set in the constructor of `AbstractSingleOperationMapper`.

It is also possible to implement your own logic by overriding the `createBaseOperation` method that returns a Kudu [Operation](https://kudu.apache.org/apidocs/org/apache/kudu/client/Operation.html).

There are pre-defined operation mappers for Pojo, Flink Row, and Flink Tuple types for constant operation, 1-to-1 sinks.

* `PojoOperationMapper`: Each table column must correspond to a POJO field
  with the same name. The  `columnNames` array should contain those fields of the POJO that
  are present as table columns (the POJO fields can be a superset of table columns).
* `RowOperationMapper` and `TupleOperationMapper`: the mapping is based on position. The
  `i`th field of the Row/Tuple corresponds to the column of the table at the `i`th
  position in the `columnNames` array.
