---
description: "Automatically generated file. DO NOT MODIFY"
---

```javascript

const options = {
	authProvider,
};

const client = Client.init(options);

let res = await client.api('/sites/{site-id}/lists/{list-id}?$select=id,name,lastModifiedDateTime&$expand=columns($select=name,description),items($expand=fields($select=Name,Color,Quantity))')
	.get();

```
