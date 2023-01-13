# ServiceNow UI Page with Condition Builder and Slushbucket
This post covers a couple of poorly documented features, which are extremely useful in ServiceNow UI Page development.
 - Condition Builder
 - Slushbucket

I recently worked on a requirement where these were part of the solution. I found that I needed to pick through multiple community posts and various other external sites to piece together what I needed. Because of that experience, I've thought to bring together everything I could find on these two features here.

At the bottom of the article you can find a list of pages I found in my own investigation and also an example update set you can install on a PDI to demonstrate the functionality.

## Condition Builder
### What is the condition builder?
The condition builder is name given to the user interface that is present at the top of all list pages in ServiceNow to filter the records in the list.
Some forms also contain condition builders. For example:
 - sys_report
   - to filter the records to include in the report 
 - contract_sla
   - to define the Start, Stop and Pause conditions

When the condition builder is present on a form it has been added like any other column to the table. The condition builder is dependent on there being another field available to define which table it will reference. This is defined on the Condition field's sys_dictionary record. In most cases the there will be a field of type Table name on the same table and the Condition field will be dependent on that. 

When the condition builder has a value on the form it is stored as an encoded query string, just like you would get from the condition builder on a list page. The value can be accessed using the usual client or server-side methods.
```js
g_form.getValue("condition_field") //in the client

gr.getValue("condition_field") //on the server-side
```

## Supporting links
 - [ServiceNow Docs - Conditions Fields](https://docs.servicenow.com/bundle/tokyo-platform-administration/page/administer/field-administration/concept/condition-field-types.html)
