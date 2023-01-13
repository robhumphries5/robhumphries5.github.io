# ServiceNow UI Page with Condition Builder and Slushbucket
This post covers a couple of poorly documented features, which are extremely useful in ServiceNow UI Page development.
 - Condition Builder
 - Slushbucket

I recently worked on a requirement where these were part of the solution. I found that I needed to pick through multiple community posts and various other external sites to piece together what I needed. Because of that experience, I've thought to bring together everything I could find on these two features here.

At the bottom of the article you can find a list of pages I found in my own investigation and also an example update set you can install on a PDI to demonstrate the functionality.

## Condition Builder
### What is the condition builder?
The condition builder is name given to the user interface that is present at the top of all list pages in ServiceNow to filter the records in the list.
![ServiceNow Condition Builder](https://servicenow-be-prod.servicenow.com/bundle/tokyo-platform-user-interface/page/use/common-ui-elements/image/ExampleConditionActiveIsTrueAndCallerIsNotEmpty.png?_LANG=enus)

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

Try adding a ui_element tag to a UI Script for something other than a condition builder, just an example.
```html
<!-- if this doesn't work, double check the sys_id of the sys_dictionary record for your PDIs incident.category field -->
<g:ui_element table="incident" field="category" id="20b7bee345910110a86630cf9552f3cb"></g:ui_element>
```


## Supporting links
### ServiceNow Docs
 - [Conditions Fields](https://docs.servicenow.com/bundle/tokyo-platform-administration/page/administer/field-administration/concept/condition-field-types.html)
 - [Jelly Tags](https://docs.servicenow.com/bundle/tokyo-application-development/page/script/general-scripting/reference/r_JellyTags.html)
 - [Extensions to Jelly Syntax](https://docs.servicenow.com/bundle/tokyo-application-development/page/script/general-scripting/reference/r_JellyTags.html](https://docs.servicenow.com/bundle/tokyo-application-development/page/script/general-scripting/concept/c_ExtensionsToJellySyntax.html))

### Community Posts

### Other Blogs

