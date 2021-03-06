---
title: Virtualization
page_title: Virtualization | Kendo UI ComboBox Widget
description: "Learn how to configure virtualization in Kendo UI ComboBox, DropDownList, AutoComplete and MultiSelect widgets."
slug: virtualization_kendoui_combobox_widget
position: 2
---

# Virtualization

[Kendo UI AutoComplete](http://demos.telerik.com/kendo-ui/autocomplete/index), [ComboBox](http://demos.telerik.com/kendo-ui/combobox/index), [DropDownList](http://demos.telerik.com/kendo-ui/dropdownlist/index) and [MultiSelect](http://demos.telerik.com/kendo-ui/multiselect/index) widgets support UI and data virtualization which is useful when  you want to display large data sets. The UI virtualization technique uses a fixed amount of list items in the widget's pop-up list regardless of the data set size. When the list is scrolled, the widget will reuse the existing items to display the relevant data, instead of creating new ones. The virtualization feature combines data and UI virtualization in order to retrieve and display only a subset of the whole data set.

## Data Virtualization

In the context of the widget, data virtualization is accomplished by using the `DataSource` paging functionality and remote data retrieval. Thus, the widget retrieves only a concrete data page instead of requesting the whole data set at once. The `DataSource` paging should be configured correctly in order to ensure the proper work of the widgets. Please refer to the [server paging](/api/javascript/data/datasource#configuration-serverPaging) configuration for more details.

## UI Virtualization

The widget uses a specific strategy of reusing a list of DOM elements in order to display the corresponding data chuck. The number of these elements is determined based on the [`height`](/api/javascript/ui/combobox#configuration-height) and [`itemHeight`](#itemheight) options. Once the number is calculated, the widget creates those elements and starts reusing them to display the current data source page.

## Data and UI Virtualization Combined

To ensure the correct work of the widget, the DataSource `pageSize` value is calculated automatically based on the (([`height`](/api/javascript/ui/combobox#configuration-height) / [`itemHeight`](#itemheight)) * 4) formula. This is done by the widget itself, and the defined `pageSize` value will be overriden if it does not match the calculated one.

> **Important**  
> To avoid multiple initial requests, define a correct `pageSize` value.

## Configuration

The following list of options describes how to configure the virtualization specific options:

### `itemHeight`

All items in the virtualized list must have the same height. If you do not specify a height value, the framework automatically sets `itemHeight` in the way they are set in the current theme and font size.

> **Important**  
> If you don't specify an `itemHeight` configuration option, the widget will perform an **extra DataSource request**. In most cases, this is not a critical issue.

### Container height

The virtualized list container must have a `height` option set in pixels. If you do not specify one, the list will use the default `height`, which is `200px`.

### `pageSize`

The virtualized widget will calculate the `pageSize` value automatically based on the (([`height`](/api/javascript/ui/combobox#configuration-height) / [`itemHeight`](#itemheight)) * 4) formula and will override the custom `pageSize` value. This is done to ensure the proper work of the virtualized list.

Consider the following scenario about a widget:
- `height` is `520px`
- `itemHeight` is `26`
In this case, the `pageSize` will be set to `80`, because ((520 / 26) * 4) is equal to `80`

> **Important**  
> To prevent the DataSource from making multiple requests for the same data, set the correct `pageSize` value using the aforementioned formula.

### `valueMapper`

The `valueMapper` function is mandatory for the functionality of the virtualized widget. The widget calls the `valueMapper` function when the widget receives a value, which is not fetched from the remote server yet. The widget will pass the selected values in the `valueMapper` function. In turn, the `valueMapper` implementation should return the respective data item indices.

```javascript
    valueMapper: function(options) {
        $.ajax({
            url: "http://demos.telerik.com/kendo-ui/service/Orders/ValueMapper",
            type: "GET",
            data: options.value, //send value to the server
            success: function (data) {
                options.success(data); //return the index number of the correspoding data item
            }
        })
    }
```

## `valueMapper` Function

### Overview

The `valueMapper` was introduced because (unlike simple Data and UI virtualization) ComboBox needs to maintain the selected item and also to display the selected data item based on value alone. In order to display the selected text widget needs to retrieve the selected data item, which is part of a particular data page that is unknown to you. The required information is gathered exactly with the [`valueMapper`](#valuemapper) callback, that passes the selected value and requests the corresponding row/dataitem index of that value. From this index, we can calculate the page number and thus pre-fetch only that page by sending an additional Ajax request.

The `valueMapper` will be called when we need to select a data item that is not present in the data source. To make that process clearer, imagine the following case:

The configuration of a widget is as follows:
- `pageSize` is set to `50`
- selected value is `foo`

On initial load, the widget will check whether the selected value is present in the loaded data. If it is not, it will perform the following actions:

1. The widget calls the `valueMapper`, requesting a row index that corresponds to the selected value `foo`.
2. The `valueMapper` calls the `service 1`, passing the selected value `foo` to it.
3. The `service 1` finds the row index that corresponds to the `foo` value. In this case it is `1250`.
4. The `valueMapper` function returns this row index to the widget.
5. The widget calculates the page number. In this case it is `25`.
6. The widget requests it from the `service 2` using the `dataSource`.
7. The `service 2` returns the corresponding 25th page.
8. The `dataSource` changes the page to `25` and displays the items showing the selected item too.

![Virtualization process](/web/combobox/virtualization.png)

### `valueMapper` Return

The `valueMapper` callback expects to receive a row index or list of indices (when multiple selection is available). That being said, the service should return either an index (number) or a list of indices. For instance, examine the result of [the test service](/service/Orders/ValueMapper) used in the online demos:

```javascript
$.ajax({
    url: "http://demos.telerik.com/kendo-ui/service/Orders/ValueMapper",
    type: "GET",
    dataType: "jsonp",
    data: { "values[0]": "10661" }
    success: function (data) {
        //returned data is [413]
        options.success(data);
    }
})
```

The Ajax method calls URLs like so:

    http://demos.telerik.com/kendo-ui/service/Orders/ValueMapper?values[0]=10661

The result is:

    callback([413]) //the result is JSONP

### `valueMapper` Implementation

As mentioned in the previous section, the service should map the selected value to a concrete row index. The implementation of this functionality is completely under your control. However, the most simplified implementation includes the iteration of all items counting the index of the rows. A more optimized solution still is to use a dedicated SQL method that handles this action internally. You can do this by using the [`ROW_NUMBER()`](https://msdn.microsoft.com/en-us/library/ms186734.aspx) function.

## Enable Virtualization

Enable virtualization in a ComboBox by using the example below. It demonstrates the minimum widget and `DataSource` configuration requirements for the virtualization to work as expected:

```html
    <input id="orders" style="width: 400px" />
    <script>
        $(document).ready(function() {
            $("#orders").kendoComboBox({
                template: '#= OrderID # | #= ShipName #',
                dataTextField: "ShipName",
                dataValueField: "OrderID",
                virtual: {
                    itemHeight: 26,
                    valueMapper: function(options) {
                        $.ajax({
                            url: "http://demos.telerik.com/kendo-ui/service/Orders/ValueMapper",
                            type: "GET",
                            data: convertValues(options.value),
                            success: function (data) {
                                options.success(data);
                            }
                        })
                    }
                },
                height: 520,
                dataSource: {
                    type: "odata",
                    transport: {
                        read: "http://demos.telerik.com/kendo-ui/service/Northwind.svc/Orders"
                    },
                    pageSize: 80,
                    serverPaging: true,
                    serverFiltering: true
                }
            });
        });

        //This is a helper method that serializes values into a understandable format for the server.
        //This method is not obligatory to use. Instead, you need to send the value in a format that is understandable for the server.
        function convertValues(value) {
            var data = {};

            value = $.isArray(value) ? value : [value];

            for (var idx = 0; idx < value.length; idx++) {
                data["values[" + idx + "]"] = value[idx];
            }

            return data;
        }
    </script>
```

## Known Limitations

- The virtualization feature can work with objects, while virtualization of primitive values is not supported.
- The rendered items should have equal height. In other words, every single item in the virtualized list will display a height that is set through the [`itemHeight`](#itemheight) option.

## See Also

Other articles on Kendo UI ComboBox:

* [Virtual Setup](/api/javascript/ui/combobox#configuration-virtual)
* [How to Configure Deferred Value Binding]({% slug howto_configure_deffered_value_binding_combobox %})
* [How to Expand Background of Long List Items]({% slug howto_expand_background_longlist_items_combobox %})
* [How to Expand ComboBox Located in Bootstrap Layout]({% slug howto_expand_widget_bootstrap_widget_combobox %})
* [How to Implement Cascading with Local Data]({% slug howto_implement_cascading_local_data_combobox %})
* [How to Select Items on TAB]({% slug howto_select_items_ontab_combobox %})
* [How to Disable Child Cascading ComboBoxes]({% slug howto_disable_child_cascading_combobox %})
* [JavaScript API Reference](/api/javascript/ui/combobox)

Articles on Kendo UI DataSource:

* [DataSource Overview](/framework/datasource/overview)
* [JavaScript DataSource API Reference](/api/javascript/data/datasource)