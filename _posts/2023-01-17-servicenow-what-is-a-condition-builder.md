# ServiceNow  - What is the Condition Builder?
This is article in the [ServiceNow Condition Builder & Slushbucket series](https://robhumphries5.github.io/2023/01/17/servicenow-condition-builder-and-slushbucket-series.html)

The Condition Builder is the name given to the user interface that is present at the top of all list pages in ServiceNow, to filter the records in the list.
![ServiceNow Condition Builder](https://servicenow-be-prod.servicenow.com/bundle/tokyo-platform-user-interface/page/use/common-ui-elements/image/ExampleConditionActiveIsTrueAndCallerIsNotEmpty.png?_LANG=enus)

Some forms also contain Condition Builders. For example:
 - sys_report
   - to filter the records to include in the report 
 - contract_sla
   - to define the Start, Stop and Pause conditions

When the Condition Builder is present on a form it has been added like any other column on the table. The Condition Builder is dependent on there being another field available to define which table it will reference. This is defined on the Condition field's sys_dictionary record. In most cases, there will be a field of type Table name on the same table and the Condition field will be dependent on that. 

When the Condition Builder has a value it is stored as an encoded query string, just like you would get from the Condition Builder on a list page. The value can be accessed using the usual client or server-side methods.
```js
g_form.getValue("condition_field") //in the client

gr.getValue("condition_field") //on the server-side
```
The next article in the series is [Adding a Condition Builder to a UI Page]()
