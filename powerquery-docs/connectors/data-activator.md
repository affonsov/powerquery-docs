---
title: Data Activator
description: Includes basic information, prerequisites, and information on how to connect to Data Activator, along with a list of limitations.
author: DougKlopfenstein
ms.topic: conceptual
ms.date: 10/08/2024
ms.author: dougklo
---

# Data Activator

## Summary

| Item | Description |
| ---- | ----------- |
| Release State | General Availability |
| Products | Power BI Semantic models <br/> |
| Authentication Types Supported | Organizational Account<br/> |
| Function Reference Documentation | &mdash; |

## Prerequisites

* A Power BI Premium subscription. Go to [Power BI Premium](https://learn.microsoft.com/en-us/power-bi/enterprise/service-premium-what-is).

* A Data Activator. Go to [Data Activator Get Started](https://learn.microsoft.com/en-us/fabric/data-activator/data-activator-get-started).


## Capabilities supported

* Import
* DirectQuery (Power BI semantic models)

## Connect to Data Activator from Power Query Desktop

1. Select **Data Activator** in the get data experience. The get data experience in Power Query Desktop varies between apps. For more information about the Power Query Desktop get data experience for your app, go to [Where to get data](../where-to-get-data.md).

2. If you're connecting to the Data Activator for the first time, select **Sign in** and input your credentials. Then select **Connect**.

3. In the navigation page, select the data you require, and select either **Transform Data** to transform the data in Power Query or **Load** to load the data.

## Dynamic M query parameter TimeDrivenWindowSpec

Data Activator allows the user to change the attribute TimeDrivenWindowSpec from the EntityQuasiAnnotation template.

### Configuration DirectQuery and Import Mode

1. Open/create a Power BI report

2. In the Power BI Desktop Ribbon, select Home > Transform data > Transform data to open the Power Query Editor.

   :::image type="content" source="media/data-activator/transform-data.png" alt-text="Open transform data.":::

3. In the Power Query Editor, select New Parameters under Manage Parameters in the ribbon.
   
   :::image type="content" source="media/data-activator/new-parameter.png" alt-text="new parameter":::

4. In the Manage Parameters window, fill out the information about the parameter. For more information, see [Create a parameter](https://learn.microsoft.com/en-us/power-query/power-query-query-parameters#creating-a-parameter).
   
   :::image type="content" source="media/data-activator/parameter-details.png" alt-text="parameter details":::

6. When you're done adding parameters, select OK.

7. Once you create the parameters, you can reference them in the M query. To modify the M query while you have the query selected, open the Advanced editor.
   
   :::image type="content" source="media/data-activator/advanced-editor.png" alt-text="advanced editor":::

  - Reference the parameters in the M query. In the example below the parameter is referenced on *options = [TimeDrivenWindowSpec = ParamTimeDrivenWindowSpec],* and then used on *Source = DataActivator.Contents(options)*
``` 
let
    options = [TimeDrivenWindowSpec = ParamTimeDrivenWindowSpec],
    Source = DataActivator.Contents(options),
    #"325459fb-7756-4b56-a58b-9fad3d98e13f" = Source{[Key="325459fb-7756-4b56-a58b-9fad3d98e13f"]}[Data],
    #"f3f7fdc1-a527-4871-bfc9-2a2f789528e2" = #"325459fb-7756-4b56-a58b-9fad3d98e13f"{[Key="f3f7fdc1-a527-4871-bfc9-2a2f789528e2"]}[Data],
    #"gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII" = #"f3f7fdc1-a527-4871-bfc9-2a2f789528e2"{[Key="gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII"]}[Data],
    Events = #"gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII"{[Key="Events"]}[Data],
    #"gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc" = Events{[Key="gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc"]}[Data]
in
    #"gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc"
```

8. When you're done editing the query, select Done.

9. Now close the Power Query Editor using the button Home -> Close and Apply
   
   :::image type="content" source="media/data-activator/close-and-apply.png" alt-text="close and apply":::

### Additional Configuration for DirectQuery only

1. In the Power BI Desktop ribbon under Modeling, select New Table.
   
   :::image type="content" source="media/data-activator/new-table.png" alt-text="new table":::

2. Create a table for the values of the TimeWindow parameter, for example: TimeDrivenWindowSpec = {"PT1H", "PT12H", "P1D", "P3D", "P7D", "P14D", "P30D"}
   
   :::image type="content" source="media/data-activator/new-table-values.png" alt-text="new table values":::

3. To bind a field, first go to the Model View in the Power BI Desktop.
   
   :::image type="content" source="media/data-activator/open-model-view.png" alt-text="open view":::

4. Select the newly created field. In our case, Value. Then in the Properties pane select Advanced.
   
   :::image type="content" source="media/data-activator/select-parameter-to-bind.png" alt-text="select parameter to bind":::

5. Select the dropdown under Bind to parameter and select the parameter that you want to bind to the field
   
   :::image type="content" source="media/data-activator/bind-the-parameter.png" alt-text="bind the parameter":::

6. Go back to the Report View

7. You can now reference this field in a slicer or as a filter

8. Create a Slicer view

9. Drag and drop the parameter into the slicer, in our case Values from the Table TimeDrivenWindowSpec
   
   :::image type="content" source="media/data-activator/select-values.png" alt-text="select values":::

10. Click in any Value in the slicer to affect the aggregation visuals 

## Incremental Refresh

Incremental refresh works with both Power BI modes (Import Mode and DirectQuery) at the same time. Data from the past will be queried and stored as import mode and the last day of data (real-time) will be retrieved using DirectQuery Mode.
   
   :::image type="content" source="media/data-activator/incremental-refresh-overview.png" alt-text="incremental refresh overview":::

The benefits of using Incremental refresh is the lower volume of real-time data for DirectQuery.

### Requirements

- Two custom parameters `RangeStart` and `RageEnd`

- It is only possible to configure Incremental refresh for queries in Import mode queries

- Make sure to set the incremental refresh policy to get real time data with DirectQuery

- To leverage the aggregation APIs of Data Activator, it is required to set up more queries using Power Query editor

### Parameter Set up (Power BI Desktop)

1. Get Data from Data Activator in `Import Mode`

2. In the Power BI Desktop Ribbon, select Home > Transform data > Transform data to open the Power Query Editor.
   
   :::image type="content" source="media/data-activator/transform-data.png" alt-text="open transform data":::

3. In the Power Query Editor make sure to create all necessary queries. For example, if there is a necessity to have aggregation queries makes sure to create an aggregation query like the examples below:

```
let
    Source = DataActivator.Contents([]),
    #"1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa" = Source{[Key="1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa"]}[Data],
    #"eab03ad5-11b9-4668-82e9-42a77458f63d" = #"1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa"{[Key="eab03ad5-11b9-4668-82e9-42a77458f63d"]}[Data],
    #"gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs" = #"eab03ad5-11b9-4668-82e9-42a77458f63d"{[Key="gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs"]}[Data],
    Events = #"gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs"{[Key="Events"]}[Data],
    #"gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU" = Events{[Key="gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU"]}[Data],
    #"Grouped Rows" = Table.Group(#"gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU", {"___time"}, {{"Count", each Table.RowCount(_), Int64.Type}})
in
    #"Grouped Rows"
```
and/or if you have a `System.ObjectId`, it is possible to group by the `___time` and `System.ObjectId`

```
let
    Source = DataActivator.Contents([]),
    #"1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa" = Source{[Key="1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa"]}[Data],
    #"eab03ad5-11b9-4668-82e9-42a77458f63d" = #"1cbd3e6a-8ff8-4b2f-a5dd-c85c3b6646aa"{[Key="eab03ad5-11b9-4668-82e9-42a77458f63d"]}[Data],
    #"gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs" = #"eab03ad5-11b9-4668-82e9-42a77458f63d"{[Key="gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs"]}[Data],
    Events = #"gGEw-RPNmyhNzELUQ_iYEL7WxoEeIZHpuTWkJU3gqFpnU_sJaXs"{[Key="Events"]}[Data],
    #"gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU" = Events{[Key="gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU"]}[Data],
    #"Grouped Rows" = Table.Group(#"gIp6EpR6Ng--myhNzELUQ_iYEL7WxoEeIbSPemoxok4YncEmL1IxpjU", {"___time", "System.ObjectId"}, {{"Count", each Table.RowCount(_), Int64.Type}})
in
    #"Grouped Rows"
```

3. In the Power Query Editor, select New Parameters under Manage Parameters in the ribbon.
   
   :::image type="content" source="media/data-activator/new-parameter.png" alt-text="new parameter":::

4. In the Manage Parameters window, create two Date/Time parameters with the exact match case names: `RangeStart` and `RangeEnd`. For more information, see [Create a parameter](https://learn.microsoft.com/en-us/power-query/power-query-query-parameters#creating-a-parameter).
   
   :::image type="content" source="media/data-activator/parameters-range.png" alt-text="parameter details":::

6. When you're done adding parameters, select OK.

7. Once you create the parameters, you can reference them in the M query. To modify the M query while you have the query selected, open the Advanced editor.
   
   :::image type="content" source="media/data-activator/advanced-editor.png" alt-text="advanced editor":::

  - Reference the parameters in all the M queries. In the example below the parameter is referenced on *options = [RangeStart = RangeStart, RangeEnd = RangeEnd],* and then used on *Source = DataActivator.Contents(options)*
``` 
let
    options = [
        RangeStart = RangeStart, 
        RangeEnd = RangeEnd
        ],
    Source = DataActivator.Contents( options),
    #"325459fb-7756-4b56-a58b-9fad3d98e13f" = Source{[Key="325459fb-7756-4b56-a58b-9fad3d98e13f"]}[Data],
    #"f3f7fdc1-a527-4871-bfc9-2a2f789528e2" = #"325459fb-7756-4b56-a58b-9fad3d98e13f"{[Key="f3f7fdc1-a527-4871-bfc9-2a2f789528e2"]}[Data],
    #"gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII" = #"f3f7fdc1-a527-4871-bfc9-2a2f789528e2"{[Key="gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII"]}[Data],
    Events = #"gGEw-RPNbAwgx3yjRgqrzm1ufUrMFosK1OCzJUp-tZZZMr9VaII"{[Key="Events"]}[Data],
    #"gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc" = Events{[Key="gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc"]}[Data]
in
    #"gIp6EpR6Ng--bAwgx3yjRgqrzm1ufUrMFm75uCplGUmavmzXAccWlbc"
```
8. When you're done editing the query, select Done.

9. Now close the Power Query Editor using the button Home -> Close and Apply
   
   :::image type="content" source="media/data-activator/close-and-apply.png" alt-text="close and apply":::


### Incremental refresh configuration (Power BI Desktop)
1. Select `Table View`
   
   :::image type="content" source="media/data-activator/select-table-view.png" alt-text="Table View":::

2. Right Click on the table and click on Incremental Refresh
   
   :::image type="content" source="media/data-activator/select-incremental-refresh.png" alt-text="select incremental refresh":::

3. Define an incremental refresh policy for the table. Make sure to check the option `Get the latest data in real time DirectQuery (Premium Only)`
   
   :::image type="content" source="media/data-activator/define-policy.png" alt-text="defining a policy":::

4. Apply the policy (This will set the incremental refresh policy for this table, make sure to configure all the tables in your PBI report)


### Publishing and Finalizing the Incremental Refresh Setup (Power BI Service)
1. Publish the report. For more details see [Publish semantic models and reports from Power BI Desktop](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-upload-desktop-files)

2. Make sure to have a connection setup. For more details see [Add or remove a gateway data source](https://learn.microsoft.com/en-us/power-bi/connect-data/service-gateway-data-sources)

3. Manually refresh the semantic model.
    - Use the `Refresh Now` button 
      
      :::image type="content" source="media/data-activator/refresh-now-button.png" alt-text="fabric refresh now":::

    - Alternatively, click in the semantic model and then click in `Refresh Now`( This step is necessary to create the incremental refresh partitions) 
      
      :::image type="content" source="media/data-activator/semantic-model-refresh-now.png" alt-text="semantic refresh now":::

4. [Configure scheduled refresh](https://learn.microsoft.com/en-us/power-bi/connect-data/refresh-scheduled-refresh)

5. [Configure the auto page refresh](https://learn.microsoft.com/en-us/power-bi/create-reports/desktop-automatic-page-refresh)


## Query Folding & Limitations

The Data Activator APIs have limited query capabilities and folding a query will result in a mixture of client side calculations and operations that will run on the DataActivator server.

### When client side calculations happen?

- Column selections
- Aggregation cases that do not involve ___time or System.ObjectId
- Filters for columns that are not the System.ObjectId
- Renaming columns
- Limiting query
- Sorting
- Adding calculated columns

If the visual triggers any of the actions above, then in-memory calculation will be triggered.

### Operations are pushed to Data Activator Server

- Count aggregations that use as group by ___time and/or System.ObjectId
- Select all columns from a Event, Trigger and Attributes
- When filters uses System.ObjectId

### Checking Query Folding 

In the Power Query Editor, right click on a query step and click View Native Query. This will show what is folded and what is an in-memory calculation.

Let's take a look at this m query

```
let
    Source = DataActivator.Contents([RangeStart=RangeStart, RangeEnd=RangeEnd, TimeDrivenWindowSpec = "P3D"]),
    #"325459fb-7756-4b56-a58b-9fad3d98e13f" = Source{[Key="325459fb-7756-4b56-a58b-9fad3d98e13f"]}[Data],
    #"b167d331-ed6a-4988-9d73-05f42c0e380f" = #"325459fb-7756-4b56-a58b-9fad3d98e13f"{[Key="b167d331-ed6a-4988-9d73-05f42c0e380f"]}[Data],
    #"52216902-4a04-40c2-bfa0-4ffb09defed2" = #"b167d331-ed6a-4988-9d73-05f42c0e380f"{[Key="52216902-4a04-40c2-bfa0-4ffb09defed2"]}[Data],
    #"8857c818-dc25-4dc4-be57-2fd9397b07fb" = #"52216902-4a04-40c2-bfa0-4ffb09defed2"{[Key="8857c818-dc25-4dc4-be57-2fd9397b07fb"]}[Data],
    #"c07c1c2f-f8e1-4590-b507-e2ba7672424a" = #"8857c818-dc25-4dc4-be57-2fd9397b07fb"{[Key="c07c1c2f-f8e1-4590-b507-e2ba7672424a"]}[Data],
    #"c07c1c2f-f8e1-4590-b507-e2ba7672424a1" = #"c07c1c2f-f8e1-4590-b507-e2ba7672424a"{[Key="c07c1c2f-f8e1-4590-b507-e2ba7672424a"]}[Data],
    #"Grouped Rows" = Table.Group(#"c07c1c2f-f8e1-4590-b507-e2ba7672424a1", {"___time"}, {{"Count", each Table.RowCount(_), Int64.Type}})
in
    #"Grouped Rows"
```

Right clicking on the last step and on View Native Query, this will be the result of it:

```
{"entity":{"templateId":"EntityQuasiAnnotation","templateVersion":"1.0","steps":[{"name":"EntityAnnotationSelectStep","id":"1","rows":[{"name":"EntityQuasiAnnotationSelector","kind":"EntityQuasiAnnotationSelector","arguments":[{"name":"referenceType","type":"string","value":"DocumentId"},{"name":"entityId","type":"string","value":"3adeea22-3b9b-43cd-8545-a97ad9d14055"},{"name":"annotationName","type":"string","value":"EventCount"},{"name":"window","kind":"TimeDrivenWindowSpec","arguments":[{"name":"width","type":"string","value":"P3D"},{"name":"hop","type":"string","value":"P3D"}]}]}]}]},"outputSteps":[{"format":"Tabular","stepId":"1"}],"populationSelector":{"type":"InstanceSetFilter","values":[]},"timeWindow":{"type":"Absolute","startTime":"2024-09-24T00:00:00Z","endTime":"2024-09-25T00:00:00Z"}}
{[ OnRenameColumns = {{"value", "Count"} }  ] , [ OnSelectColumns = {"___time", "Count"}  ] } 
```

Now, we can break this in two parts. 

The first part of it is a json which was used on the body request for Data Activator API

```
{"entity":{"templateId":"EntityQuasiAnnotation","templateVersion":"1.0","steps":[{"name":"EntityAnnotationSelectStep","id":"1","rows":[{"name":"EntityQuasiAnnotationSelector","kind":"EntityQuasiAnnotationSelector","arguments":[{"name":"referenceType","type":"string","value":"DocumentId"},{"name":"entityId","type":"string","value":"3adeea22-3b9b-43cd-8545-a97ad9d14055"},{"name":"annotationName","type":"string","value":"EventCount"},{"name":"window","kind":"TimeDrivenWindowSpec","arguments":[{"name":"width","type":"string","value":"P3D"},{"name":"hop","type":"string","value":"P3D"}]}]}]}]},"outputSteps":[{"format":"Tabular","stepId":"1"}],"populationSelector":{"type":"InstanceSetFilter","values":[]},"timeWindow":{"type":"Absolute","startTime":"2024-09-24T00:00:00Z","endTime":"2024-09-25T00:00:00Z"}}
```

The second part of it is the in-memory calculation executed to this query

```
{[ OnRenameColumns = {{"value", "Count"} }  ] , [ OnSelectColumns = {"___time", "Count"}  ] }
```


## See also

* [Data Activator Introduction](https://learn.microsoft.com/en-us/fabric/data-activator/data-activator-introduction)
* [Incremental refresh and real-time data for semantic models](https://learn.microsoft.com/en-us/power-bi/connect-data/incremental-refresh-overview)
* [Dynamic M query parameters in Power BI Desktop](https://learn.microsoft.com/en-us/power-bi/connect-data/desktop-dynamic-m-query-parameters)
