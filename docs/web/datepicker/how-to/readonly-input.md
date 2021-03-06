---
title: Make Input Elements Readonly
page_title: Make Input Elements Readonly | Kendo UI DatePicker Widget
description: "Learn how to make input elements readonly in Kendo UI DatePicker."
slug: howto_make_input_elements_readonly_datepicker
---

# Make Input Elements Readonly

The example below demonstrates how to make an input element readonly and in this way prevent users from typing.

###### Example

```html
    <div id="example">
        <div class="demo-section k-content">

            <h4>Show e-mails from:</h4>
            <input id="datepicker" value="10/10/2011" style="width: 100%" />

            <h4 style="margin-top: 2em;">Add to archive mail from:</h4>
            <input id="monthpicker" value="November 2011" style="width: 100%" />
            </p>
        </div>
    <script>
        $(document).ready(function() {
            // create DatePicker from input HTML element
            $("#datepicker").kendoDatePicker();

            $("#monthpicker").kendoDatePicker({
                // defines the start view
                start: "year",

                // defines when the calendar should return date
                depth: "year",

                // display month and year in the input
                format: "MMMM yyyy"
            });


            //DISABLE inputs
            $("#datepicker").attr("readonly", true);
            $("#monthpicker").attr("readonly", true);
        });
    </script>
    </div>

```

## See Also

Other articles on Kendo UI DatePicker:

* [JavaScript API Reference](/api/javascript/ui/datepicker)
* [How to Create Date Masking]({% slug howto_create_date_masking_datepicker %})
* [How to Select Ranges between DatePickers]({% slug howto_select_ranges_between_datepicker %})
* [How to Set the First Weekday]({% slug howto_set_first_weekday_datepicker %})
* [How to Use AngularJS Copy Functionality]({% slug howto_use_angularjs_copy_functionality_datepicker %})
* [How to Integrate DatePicker with DateJS Library]({% slug howto_integrate_withdatejs_library_datepicker %})
* [How to Disable Dates]({% slug howto_disable_dates_datepicker %})
* [How to Hide the Deafult Button]({% slug howto_hide_default_button_datepicker %})
* [How to Globally Modify Default Options]({% slug howto_globally_modify_default_options_datepicker %})
* [How to Persist Entered Dates]({% slug howto_persist_entered_dates_datepicker %})
* [How to Resize Calendar Based on Input Width]({% slug howto_use_resize_calendar_basedon_input_width_datepicker %})
* [How to Restrict User Input to Min/Max Values]({% slug howto_restrict_user_input_minandmax_values_datepicker %})
* [How to Show Out-of-Range Dates as Disabled]({% slug howto_show_outofrange_dates_disabled_datepicker %})
* [How to Submit Forms on ENTER]({% slug howto_submmit_forms_onenter_datepicker %})