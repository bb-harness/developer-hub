---
title: Import data into ServiceNow
description: Invoke ServiceNow's import set API to import data via a staging table.
sidebar_position: 5
---

You can use the **ServiceNow Import Set** step in your CD, approval, or custom stages to import data to your instance using an import set table as a staging area.

:::note

You can also create and update tickets using the following Harness ServiceNow steps:

- [Create ServiceNow Tickets in CD Stages](create-service-now-tickets-in-cd-stages.md)
- [Update ServiceNow Tickets in CD Stages](update-service-now-tickets-in-cd-stages.md)

:::

## Import set summary

In ServiceNow, an import set is a staging table that contains a temporary copy of data to import into the platform. Import sets can be used to import data from a variety of sources, such as CSV files or JDBC-compliant databases, and transform that data into a format that can be loaded into ServiceNow tables.

Import Sets are a useful tool for migrating data into ServiceNow, or for regularly importing data from external systems into the platform.

:::note

For more information on ServiceNow import sets, go to [Import Set - POST /now/import/{stagingTableName}](https://docs.servicenow.com/bundle/quebec-application-development/page/integrate/inbound-rest/concept/c_ImportSetAPI.html#import-POST) and [Import sets key concepts](https://docs.servicenow.com/en-US/bundle/tokyo-platform-administration/page/administer/import-sets/concept/c_ImportSetsKeyConcepts.html) from ServiceNow.

:::

## Add a ServiceNow Import Set step

You can add a ServiceNow Import Set step anywhere in CD, approval, or custom stages where you need to import data using a staging table.

1. In a Harness CD, approval, or custom stage, in **Execution**, click Add Step, and then select **ServiceNow Import Set**.
2. Enter a name for the step.
3. Enter a timeout period for the step. Once the timeout expires, Harness will initiate the step or stage [failure strategy](../../../platform/Pipelines/8_Pipelines/../../8_Pipelines/define-a-failure-strategy-on-stages-and-steps.md).
4. In **ServiceNow Connector**, select or create the [Harness ServiceNow Connector](../../../platform/7_Connectors/connect-to-service-now.md) to use.

## Select the stage table

The **Staging Table** setting specifies the staging table that will be used to import data into ServiceNow. These intermediate table names extend the **Import Set Row** table in ServiceNow. 

1. In **Staging Table**, select or enter the staging table to use. The list is populated using the ServiceNow connector you added in **ServiceNow Connector**.


## Enter the JSON body

The **JSON Body** setting contains the JSON that this step will pass when it makes a call to ServiceNow's Import Set API. 

For example, to initiate a transformation using the "sys_import_state_comment" field of the selected staging table based on its transformation map, you would use something like this:

```
{"sys_import_state_comment":"my comment"}
```

You can also use Harness runtime inputs, variable expressions, and Harness secrets in the JSON of **JSON Body**. 

For example, you could create a stage variable named `importset` and then reference it in **JSON Body** as {"u_example":"<+stage.variables.importset>"}.

For details on the table requirements and naming, go to [Import sets](https://docs.servicenow.com/en-US/bundle/tokyo-platform-administration/page/administer/import-sets/reference/import-sets-landing-page.html) from ServiceNow.

For details on creating a transform map, go to [Create a transform map](https://docs.servicenow.com/bundle/tokyo-platform-administration/page/script/server-scripting/task/t_CreateATransformMap.html). 

## Advanced settings

In **Advanced**, you can use the following options:

* [Delegate Selector](../../../platform/2_Delegates/delegate-guide/select-delegates-with-selectors.md)
* [Conditional Execution](../../../platform/8_Pipelines/w_pipeline-steps-reference/step-skip-condition-settings.md)
* [Failure Strategy](../../../platform/8_Pipelines/w_pipeline-steps-reference/step-failure-strategy-settings.md)
* [Looping Strategy](../../..//platform/8_Pipelines/looping-strategies-matrix-repeat-and-parallelism.md)



## Review transform map outcomes

Within ServiceNow, **Transform Table Maps** determine how data will be written from the staging table to existing target tables. Map rows determine whether the transformation will create or update a ticket, and determine the ticket type.

![Transform Table Maps](static/servicenow-transform-table.png)

You can view the outcomes of transform maps once the pipeline is run.

1. Click on the ServiceNow Import Set in the executed pipeline.
2. Click the **Output** tab.

You can see the results in the **Transform Map Outcomes** table for each record.

![Transform Map Outcomes](static/servicenow-outputs.png)

To reference these results as expressions in other pipeline steps, click the copy button next to the **Output Name**. You will get expressions like these:

```
<+pipeline.stages.stage1.spec.execution.steps.step1.output.transformMapOutcomes[0].transformMap>
<+pipeline.stages.stage1.spec.execution.steps.step1.output.transformMapOutcomes[0].status>
<+pipeline.stages.stage1.spec.execution.steps.step1.output.transformMapOutcomes[1].transformMap>
<+pipeline.stages.stage1.spec.execution.steps.step1.output.transformMapOutcomes[1].status>
```

You can echo them in a subsequent step, such as a [Shell Script step](../../cd-execution/cd-general-steps/using-shell-scripts.md).


