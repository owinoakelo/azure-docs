---
title: Template functions - date
description: Describes the functions to use in an Azure Resource Manager template (ARM template) to work with dates.
ms.topic: conceptual
ms.date: 09/09/2021
---

# Date functions for ARM templates

Resource Manager provides the following functions for working with dates in your Azure Resource Manager template (ARM template):

* [dateTimeAdd](#datetimeadd)
* [utcNow](#utcnow)

## dateTimeAdd

`dateTimeAdd(base, duration, [format])`

Adds a time duration to a base value. ISO 8601 format is expected.

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| base | Yes | string | The starting datetime value for the addition. Use [ISO 8601 timestamp format](https://en.wikipedia.org/wiki/ISO_8601). |
| duration | Yes | string | The time value to add to the base. It can be a negative value. Use [ISO 8601 duration format](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
| format | No | string | The output format for the date time result. If not provided, the format of the base value is used. Use either [standard format strings](/dotnet/standard/base-types/standard-date-and-time-format-strings) or [custom format strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### Return value

The datetime value that results from adding the duration value to the base value.

### Examples

The following example template shows different ways of adding time values.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd.json":::

When the preceding template is deployed with a base time of `2020-04-07 14:53:14Z`, the output is:

| Name | Type | Value |
| ---- | ---- | ----- |
| add3YearsOutput | String | 4/7/2023 2:53:14 PM |
| subtract9DaysOutput | String | 3/29/2020 2:53:14 PM |
| add1HourOutput | String | 4/7/2020 3:53:14 PM |

The next example template shows how to set the start time for an Automation schedule.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/datetimeadd-automation.json":::

## utcNow

`utcNow(format)`

Returns the current (UTC) datetime value in the specified format. If no format is provided, the ISO 8601 (`yyyyMMddTHHmmssZ`) format is used. **This function can only be used in the default value for a parameter.**

### Parameters

| Parameter | Required | Type | Description |
|:--- |:--- |:--- |:--- |
| format |No |string |The URI encoded value to convert to a string. Use either [standard format strings](/dotnet/standard/base-types/standard-date-and-time-format-strings) or [custom format strings](/dotnet/standard/base-types/custom-date-and-time-format-strings). |

### Remarks

You can only use this function within an expression for the default value of a parameter. Using this function anywhere else in a template returns an error. The function isn't allowed in other parts of the template because it returns a different value each time it's called. Deploying the same template with the same parameters wouldn't reliably produce the same results.

If you use the [option to rollback on error](rollback-on-error.md) to an earlier successful deployment, and the earlier deployment includes a parameter that uses utcNow, the parameter isn't reevaluated. Instead, the parameter value from the earlier deployment is automatically reused in the rollback deployment.

Be careful redeploying a template that relies on the utcNow function for a default value. When you redeploy and don't provide a value for the parameter, the function is reevaluated. If you want to update an existing resource rather than create a new one, pass in the parameter value from the earlier deployment.

### Return value

The current UTC datetime value.

### Examples

The following example template shows different formats for the datetime value.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow.json":::

The output from the preceding example varies for each deployment but will be similar to:

| Name | Type | Value |
| ---- | ---- | ----- |
| utcOutput | string | 20190305T175318Z |
| utcShortOutput | string | 03/05/2019 |
| utcCustomOutput | string | 3 5 |

The next example shows how to use a value from the function when setting a tag value.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/functions/date/utcnow-tag.json":::

## Next steps

* For a description of the sections in an ARM template, see [Understand the structure and syntax of ARM templates](./syntax.md).
