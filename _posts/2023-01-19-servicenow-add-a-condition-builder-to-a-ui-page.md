# Adding a Condition Builder to a UI Page

This is article in the [ServiceNow Condition Builder & Slushbucket series](https://robhumphries5.github.io/2023/01/17/servicenow-condition-builder-and-slushbucket-series.html)

Adding a Condition Builder to a UI Page is actually very simple, it is just very poorly documented.
ServiceNow provide a Jelly tag `<g:ui_element></g:ui_element>` which is not mentioned in the Jelly Tags or Extensions to Jelly syntax docs.
This tag allows us to bring any field (except reference fields which are handled with `<g:ui_reference></g:ui_reference>`) in to a UI page.
The tag has three attributes:
 - table - the name of the table which has on it the field you want to bring in
 - field - the name of the field you want to bring in
 - id - the sys_id of the sys_dictionary entry for this field

The field will have some, but not all of the same behaviour as if it were on the form where it is defined. And you may find that some things behave differently. For example:
 - ACLs are observed, so if a user cannot read or write the field on the form normally, do not expect them to see it on the UI page. However ACLs that use a script and evaluate the `current` object will error, as current will not be defined and so users who can usually read or write a field still may not be able to.

Try adding a `<g:ui_element></g:ui_element>` tag to a UI Script for something other than a Condition Builder, just an example.
```xml
<!-- if this doesn't work, double check the sys_id of the sys_dictionary record for your PDIs incident.category field -->
<g:ui_element table="incident" field="category" id="20b7bee345910110a86630cf9552f3cb"></g:ui_element>
```
The process to add a Condition Builder to a UI Page just has two additional steps. 
The first is to also include the Table name field on which it is dependent.
```xml
<g:ui_element table="table" field="table_name_field" id="table_name_field_sys_dictionary_sys_id"></g:ui_element>
<g:ui_element table="table" field="condition_field" id="condition_field_sys_dictionary_sys_id"></g:ui_element>
```
The second is to include the following line before the `<g:ui_element></g:ui_element> tags` to ensure that the Condition Builder has access to the javascript it needs.
```xml
<g:requires name="elements/element_conditions.jsx"></g:requires> 
```
And that's it to include the Condition Builder. In the next article I'll cover setting and getting the values, hiding the Table name field and common issues people seem to run in to.
