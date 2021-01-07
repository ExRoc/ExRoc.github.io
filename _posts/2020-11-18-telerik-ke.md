---
title: Telerik Kendo Grid - command template - hide or show row button depending on row field value
author: PipisCrew
date: 2020-11-18
categories: [js]
toc: true
---

```js
$("#grid").kendoGrid({
.
.
.
columns: [
	{ field: "Customer", title: "Customer" },
	{ field: "Address", title: "Address" },
	{
		template: "[My Caption]()",
		className: "",
		name: "myButton",
		dataItem: this,
		visible: function (dataItem) { return dataItem.fromAPI.IsVisible; },
		click: function (e) {
			e.preventDefault();

			var tr = $(e.target).closest("tr");
			var data = this.dataItem(tr);

			console.log(data);
		}
	}
   ]
});
```

refs :
https://stackoverflow.com/a/39705765
https://stackoverflow.com/a/32624986
https://stackoverflow.com/a/40337316
https://developerslogblog.wordpress.com/2017/12/26/kendo-ui-call-a-function-from-a-template/

origin - https://www.pipiscrew.com/?p=19382 telerik-kendo-grid-command-template-hide-or-show-row-button-depending-on-row-field-value