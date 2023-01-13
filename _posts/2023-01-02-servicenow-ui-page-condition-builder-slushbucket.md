# ServiceNow UI Page with Condition Builder and Slushbucket
This post covers a couple of poorly documented features, which are extremely useful in ServiceNow UI Page development.
 - Condition Builder
 - Slushbucket

I recently worked on a requirement where these were part of the solution. I found that I needed to pick through multiple community posts and various other external sites to piece together what I needed. Because of that experience, I've thought to bring together everything I could find on these two features here.

At the bottom of the article you can find a list of pages I found in my own investigation and also an example update set you can install on a PDI to demonstrate the functionality.

## Condition Builder
### What is the condition builder?
The condition builder is the name given to the user interface that is present at the top of all list pages in ServiceNow, to filter the records in the list.
![ServiceNow Condition Builder](https://servicenow-be-prod.servicenow.com/bundle/tokyo-platform-user-interface/page/use/common-ui-elements/image/ExampleConditionActiveIsTrueAndCallerIsNotEmpty.png?_LANG=enus)

Some forms also contain condition builders. For example:
 - sys_report
   - to filter the records to include in the report 
 - contract_sla
   - to define the Start, Stop and Pause conditions

When the condition builder is present on a form it has been added like any other column on the table. The condition builder is dependent on there being another field available to define which table it will reference. This is defined on the Condition field's sys_dictionary record. In most cases, there will be a field of type Table name on the same table and the Condition field will be dependent on that. 

When the condition builder has a value it is stored as an encoded query string, just like you would get from the condition builder on a list page. The value can be accessed using the usual client or server-side methods.
{{"{% highlight javascript "}}%}
g_form.getValue("condition_field") //in the client

gr.getValue("condition_field") //on the server-side
{{ "{% endhighlight "}}%}
### Adding a condition builder to a UI Page
Adding a condition builder to a UI Page is actually very simple, it is just very poorly documented.
ServiceNow provide a Jelly tag `<g:ui_element></g:ui_element>` which is not mentioned in the Jelly Tags or Extensions to Jelly syntax docs.
This tag allows us to bring any field (except reference fields which are handled with `<g:ui_reference></g:ui_reference>`) in to a UI page.
The tag has three attributes:
 - table - the name of the table which has on it the field you want to bring in
 - field - the name of the field you want to bring in
 - id - the sys_id of the sys_dictionary entry for this field

The field will have some, but not all of the same behaviour as if it were on the form where it is defined. And you may find that some things behave differently. For example:
 - ACLs are observed, so if a user cannot read or write the field on the form normally, do not expect them to see it on the UI page. However ACLs that use a script and evaluate the `current` object will error, as current will not be defined and so users who can usually read or write a field still may not be able to.

Try adding a `<g:ui_element></g:ui_element>` tag to a UI Script for something other than a condition builder, just an example.
```html
<!-- if this doesn't work, double check the sys_id of the sys_dictionary record for your PDIs incident.category field -->
<g:ui_element table="incident" field="category" id="20b7bee345910110a86630cf9552f3cb"></g:ui_element>
```
The process to add a condition builder to a UI Page just has one additional step, which is to also include the Table name field on which it is dependent.
```hmtml
<g:ui_element table="table" field="table_name_field" id="table_name_field_sys_dictionary_sys_id"></g:ui_element>
<g:ui_element table="table" field="condition_field" id="condition_field_sys_dictionary_sys_id"></g:ui_element>
```
And that's it to include the condition builder. In the next sections I'll cover setting and getting the values, hiding the Table name field and common issues people seem to run in to.

### Setting the initial value of the fields
When the UI Page is rendered, if you inspect the HTML you will see that you cannot find the `<g:ui_element></g:ui_element>` tags that you added to the UI Page HTML field.
ServiceNow replaces these tags with the correct HTML to render the field that you have requested.
To be able to find the `<input />` tag for the Table name field you should look for an element with `id="table.table_name_field_name"` and set the value.
In the UI Page Client script you can do this with the following code.
```js
var table = "your_table_name" ; // define the name of the table where your condition and table name fields are
var tableNameFieldName = "your_table_name_field_name"; // define the name of the table name field
addLoadEvent(function(){	
	//get the g:ui_element element and set the value to table you want to reference, so that the condition builder will look at that table
	var tableUI = document.getElementById(table+"."+tableNameFieldName);
	tableUI.value = "the_name_of_the_table_to_reference";
 }
```
If you want to set the initial value of the filter you can do that with the same method.
```js
var table = "your_table_name" ; // define the name of the table where your condition and table name fields are
var tableNameFieldName = "your_table_name_field_name"; // define the name of the table name field
var conditionFieldName = "your_condition_field_name"; // define the name of the condition field
addLoadEvent(function(){	
	//get the input for the table name field and set the value to table you want to reference, so that the condition builder will look at that table
	var tableUI = document.getElementById(table+"."+tableNameFieldName);
	tableUI.value = "the_name_of_the_table_to_reference";
 
 //get the input for the conditon builder and set the value to an encoded query string
	var conditionUI = document.getElementById(table+"."+conditionFieldName);
	conditionUI.value = "active=true";
 }
```
The `addLoadEvent()` function means that ServiceNow will wait until it has finished loading the page and rendering the HTML until it runs this function. This prevents an error where the element you want to update doesn't exist yet.

### Getting the values of the fields
This is probably pretty obvious now, but you can get the values of the fields in the same way.
```js
var tableUIValue = document.getElementById(table+"."+tableNameFieldName).value;
 
var conditionUIValue = document.getElementById(table+"."+conditionFieldName).value;
```
There is an alternative method to get the value of the condition builder. ServiceNow provide a client side method `getFilter(filter_id)`. In our case we can access this as below.
```js
var query = getFilter(table+"."+conditionFieldName);
```

### Hiding the Table name field
If you want to hide the table name field you can add `tableUI.hide();` to your `addLoadEvent()` function giving you this.
```js
var table = "your_table_name" ; // define the name of the table where your condition and table name fields are
var tableNameFieldName = "your_table_name_field_name"; // define the name of the table name field
var conditionFieldName = "your_condition_field_name"; // define the name of the condition field
addLoadEvent(function(){	
	//get the input for the table name field and set the value to table you want to reference, so that the condition builder will look at that table
	var tableUI = document.getElementById(table+"."+tableNameFieldName);
	tableUI.value = "the_name_of_the_table_to_reference";
	tableUI.hide();
 
 //get the input for the conditon builder and set the value to an encoded query string
	var conditionUI = document.getElementById(table+"."+conditionFieldName);
	conditionUI.value = "active=true";
 }
```

## Slushbucket
### What is the Slushbucket?
The Slushbucket is the name of the user interface that we find when we want to select and process multiple elements from a longer list of elements. For example when adding users or roels to a group.
![ServiceNow Role Slushbucket](https://support.servicenow.com/sys_attachment.do?sys_id=436bcfbcdb04b0d016d2a345ca961962)
It is comprised of two `<select></select>` elements and the items on each side are `<option></option>` elements.

### Adding a Slushbucket to a UI Page
Again this can be done simply with another undocumented Jelly tag `<g:ui_slushbucket></g:ui_slushbucket>`.
The tag


## Common issues
### Cannot set value of null
If you are trying to set the value of any of the elements created as part of this process and receiving this error in the console, you can try:
 - wrapping your code in an `addLoadEvent()` function. The function takes a callback function as a parameter or can be written inline.eg.
```js
addLoadEvent(function(){	
  //run your code
});
```

### My users cannot see the condition builder
The advice on a lot of the content I found was to use the Business Rule [sys_script] table condition builder. This is fine if your UI Page is only going to be available to system administrators and developers, however the ACLs on the sys_script table prevent most users from being able to see these fields. You need to either find an existing table that your users can see where there is a Condition and Table name field that your users can see and use those, or create new ones somewhere they can see them.
In my case I was buildling functionality for itil users and so I used the cmdb_baseline.table and cmdb_baseline.condition fields as these are already available to itil users.

## Supporting links
### ServiceNow Docs
 - [Conditions Fields](https://docs.servicenow.com/bundle/tokyo-platform-administration/page/administer/field-administration/concept/condition-field-types.html)
 - [Jelly Tags](https://docs.servicenow.com/bundle/tokyo-application-development/page/script/general-scripting/reference/r_JellyTags.html)
 - [Extensions to Jelly Syntax](https://docs.servicenow.com/bundle/tokyo-application-development/page/script/general-scripting/concept/c_ExtensionsToJellySyntax.html)

### Community Posts

### Other Blogs

