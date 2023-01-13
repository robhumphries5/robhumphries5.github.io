# ServiceNow UI Page Condition Builder - Issues using Business Rule [sys_script] fields
A lot of people seem to run in to this problem.

When you look on the community for advice on adding condition builders to UI pages, all the examples use the "sys_script" table in the `<g:ui_element>` tags.

Like this...
```
<g:ui_element table="sys_script" field="collection" id="0e66a301dd2003003399d83d70be0205"></g:ui_element>  
<g:ui_element table="sys_script" field="filter_condition" id="8a66a301dd2003003399d83d70be0211"></g:ui_element>
```

But this will only work for admin or developer users. The sys_script table has ACLs preventing other users from reading or writing to these fields and ACLs are respected for elements created with `<g:ui_element>` tags.

To ensure that your users can see you condition builder on your UI Page you can:
1. Create custom fields for the Condition field and Table name field and ensure that ACLs mean your users can see them.
   - This is the best option if you have the scope to do it.
2. Find existing fields your users can see.
   - As an example, the cmdb_baseline table has column table and condition that are visible to itil and asset users.
   - There is a risk that these fields change in the future and your functionality breaks.

N.B. It would not be a good idea to change the ACLs on the Business Rule table to allow everyone to read/write those fields.

For more information on condition builders on UI Pages see my [full article](https://robhumphries5.github.io/2023/01/02/servicenow-ui-page-condition-builder-slushbucket.html)
