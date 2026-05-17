# iPARK CONCEPT

Domain: ipark.hungthinhcloud.freeddns.org

Landing page: `LOGIN` page, where users can enter their username and password to login to the iPark web-application. After successful login, users will be redirected to the `HOME_DASHBOARDS` page.

## DEVELOPMENT & UI NOTE

- **Development Priority:** Functionality over aesthetics. The team will focus on getting the core logic and features to "work" first, and make it "look good" later.
- **Design Style:** The initial UI will follow a mock-up design featuring modern curve shapes. Any major aesthetic modifications or additions will be addressed in a later polishing phase.


## 0. LOGIN

A simple login page for users to login to the iPark web-application. Users need to enter their username and password to login. A valid username and password combination is required to login successfully.

*Alternative flow:*

- If the users `forget their password`, they can click the "Forgot Password" button to prompt a dialog to enter their registered `email`. *(no need to implement the actual email sending, just reset the password to a default value such as `Password@123` after the user enters their registered email and clicks the reset button)*
- If the users enter an `invalid username or password`, they will see a generic error message "Invalid username or password" and can try again. Max attempts is 5, after that the IP address of the user will be blocked for 30 minutes, and the user can not login from that IP address until it is unblocked automatically after 30 minutes or manually by an admin *(no need to be exactly 30 minutes, can be 1 minute for short demonstration)*. A security event will also be triggered and logged in the `EVENT_HISTORY_DB`.

## 1. HOME_DASHBOARDS

Home page of the iPark web-application, where users can view the dashboards that are assigned to them based on their permissions. Each dashboard can have multiple widgets that display different information about the parks, staffs, events, etc.

### 1.1. DASHBOARD

Dashboard is a collection of widgets that are displayed on the home page of the iPark web-application. Each dashboard can have multiple widgets that display different information about the parks, staffs, events, etc. The dashboards are assigned to users based on their permissions, and users can only view the dashboards that they have permission to view.

**Due to demonstration purpose**, creating completely custom widget is not supported, instead, users can only choose from a list of pre-configured widgets with specific data sources and parameters. Users can add/remove/rearrange/resize the widgets in the dashboard, but can not edit the content and functionality of the widgets. The pre-configured widgets are designed to cover the most common use cases and scenarios for park management, such as showing current slot usage, estimated income, staff working time, etc.

A dropdown menu to select the dashboard to view. The dashboard that is pinned will be loaded by default when the user logs in. Users can pin/unpin the dashboard to set it as the default dashboard to load when they log in.

Quick actions:

- **View**: view the dashboard with the widgets, pin/unpin. Requires `view_dashboard` permission
- **Edit**: edit toggle button to enter edit mode, in which the user can add/remove/rearrange/resize the widgets in the selected dashboard. Requires `edit_dashboard` permission
- **Pin/Unpin**: pin/unpin the dashboard to set it as the default dashboard to load when the user logs in. Requires `edit_dashboard` permission
- **Add**: add a new dashboard to the system. Requires `add_dashboard` permission
- **Delete**: delete the dashboard from the system. Requires `delete_dashboard` permission

### 1.2. WIDGET

Widget is a component that displays specific information or functionality on the dashboard. Each widget has a specific data source and parameters that determine what information it shows and how it shows it **however the `datasource` is not editable**.

Attributes:
*(DEV NOTE: These attributes map directly to the keys inside the JSON objects stored in the `widgets_list` array of `DASHBOARD_DB`. Ensure your code handles this JSON mapping when rendering widgets).*

- **id**: the unique identifier of the widget *(system edit only)*
- **label**: the display label of the widget
- **description**: the description of the widget
- **position_x**: the position X of the top-left corner of the widget *(must not overlap with other widgets)*
- **position_y**: the position Y of the top-left corner of the widget *(must not overlap with other widgets)*
- **width**: the width of the widget *(must not overlap with other widgets)*
- **height**: the height of the widget *(must not overlap with other widgets)*
- **is_fixed**: the widget is fixed or not *(to move/resize)*
- **is_enable**: the widget is enable or not *(to show/hide)*
- **data_source**: the data source of the widget (not editable)

#### 1.2.1 WIDGET DATASOURCE *PARK*

*PARK* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks
- **Unit**: `slot`, `%` (percentage). Default is `slot`
- **Interval**: `hour`, `day`, `week`, `month`. Default is `day`

1. **curr_slot_max_slot**: show current used slot / total of max slot of `Park`
    - **curr_slot**: the current used slot
    - **max_slot**: the total of max slot
    - **unit**: show the unit
    - **park**: the park ID
2. **stats_curr_slot**: by `interval`, show the statistics of current used slot of `Park`
    - **lowest**: show the minimum used slot
    - **highest**: show the maximum used slot
    - **avg**: show the average used slot
    - **interval**: show the time interval of the stats
    - **unit**: show the unit
    - **park**: the park ID
3. **chart_curr_slot**: by `interval`, show the line chart of current used slot of `Park`
    - **y_axis**: show the average used slot
    - **x_axis**: show the timeline of the bars chart
    - **pointer_x**: the x-coordinate of the pointer
    - **pointer_y**: the y-coordinate of the pointer
    - **pointer_value**: the value of the pointer
    - **line_color**: the color of the line
    - **pointer_color**: the color of the pointer
    - **interval**: the time interval of the stats
    - **park**: the park ID
    - **unit**: show the unit

#### 1.2.2 WIDGET DATASOURCE *FEE*

*FEE* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks
- **Unit**: `VND`, `đ` (Unicode UTF-8). Default is `VND`
- **Interval**: `hour`, `day`, `week`, `month`. Default is `day`

1. **curr_fee**: show current fee of `Park`
    - **curr_fee**: the current fee
    - **unit**: show the unit
    - **park**: the park ID
2. **estimate_income**: by `interval`, show the estimated income of `Park`
    - **estimate_income**: the estimated income
    - **interval**: the time interval of the stats
    - **unit**: show the unit
    - **park**: the park ID
3. **chart_estimate_income**: by `interval`, show the line chart of estimated income of `Park`
    - **y_axis**: show the estimated income
    - **x_axis**: show the timeline of the bars chart
    - **pointer_x**: the x-coordinate of the pointer
    - **pointer_y**: the y-coordinate of the pointer
    - **pointer_value**: the value of the pointer
    - **line_color**: the color of the line
    - **pointer_color**: the color of the pointer
    - **interval**: the time interval of the stats
    - **park**: the park ID
    - **unit**: show the unit

#### 1.2.3 WIDGET DATASOURCE *STAFF*

*STAFF* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks
- **Unit**: `person`, `%` (percentage). Default is `person`
- **Interval**: `hour`, `day`, `week`, `month`. Default is `day`

1. **curr_staff_max_staff**: show current in working staff/ total assigned staff of `Park`
    - **curr_staff**: the current in working staff
    - **max_staff**: the total assigned staff
    - **unit**: show the unit
    - **park**: the park ID
2. **stats_curr_staff**: by `interval`, show the statistics of current in working staff of `Park`
    - **lowest**: show the minimum current in working staff
    - **highest**: show the maximum current in working staff
    - **avg**: show the average current in working staff
    - **interval**: show the time interval of the stats
    - **unit**: show the unit
    - **park**: the park ID
3. **chart_curr_staff**: by `interval`, show the line chart of current in working staff of `Park`
    - **y_axis**: show the average current in working staff
    - **x_axis**: show the timeline of the bars chart
    - **pointer_x**: the x-coordinate of the pointer
    - **pointer_y**: the y-coordinate of the pointer
    - **pointer_value**: the value of the pointer
    - **line_color**: the color of the line
    - **pointer_color**: the color of the pointer
    - **interval**: the time interval of the stats
    - **park**: the park ID
    - **unit**: show the unit
4. **estimate_payment**: by `interval`, show the estimated payment of `Park`
    - **estimate_payment**: the estimated payment
    - **interval**: the time interval of the stats
    - **unit**: show the unit
    - **park**: the park ID
5. **chart_estimate_payment**: by `interval`, show the line chart of estimated payment of `Park`
    - **y_axis**: show the estimated payment
    - **x_axis**: show the timeline of the bars chart
    - **pointer_x**: the x-coordinate of the pointer
    - **pointer_y**: the y-coordinate of the pointer
    - **pointer_value**: the value of the pointer
    - **line_color**: the color of the line
    - **pointer_color**: the color of the pointer
    - **interval**: the time interval of the stats
    - **park**: the park ID
    - **unit**: show the unit

#### 1.2.4 WIDGET DATASOURCE *WORKING TIME*

*WORKING TIME* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks
- **Unit**: `sec` (`s`), `min` (`m`), `hour` (`h`), `day` (`d`), `week` (`w`), `month` (`mo`), `year` (`y`). Default is `h`

1. **start_end_time**: show start/end of working shift of `Park`
    - **start_time**: the start time
    - **end_time**: the end time
    - **unit**: show the unit
    - **park**: the park ID
2. **curr_total_working_time**: show current total working time of `Park`
    - **curr_total_working_time**: the current total working time
    - **unit**: show the unit
    - **park**: the park ID
3. **stats_curr_total_working_time**: by `interval`, show the statistics of current total working time of `Park`
    - **lowest**: show the minimum current total working time
    - **highest**: show the maximum current total working time
    - **avg**: show the average current total working time
    - **interval**: show the time interval of the stats
    - **unit**: show the unit
    - **park**: the park ID
4. **chart_curr_total_working_time**: by `interval`, show the line chart of current total working time of `Park`
    - **y_axis**: show the average current total working time
    - **x_axis**: show the timeline of the bars chart
    - **pointer_x**: the x-coordinate of the pointer
    - **pointer_y**: the y-coordinate of the pointer
    - **pointer_value**: the value of the pointer
    - **line_color**: the color of the line
    - **pointer_color**: the color of the pointer
    - **interval**: the time interval of the stats
    - **park**: the park ID
    - **unit**: show the unit

#### 1.2.5. WIDGET DATASOURCE *EVENT*

*EVENT* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks is not allowed, must be a specific park ID
- **type**: `warning`, `info`, `error`, `all`. Default is `warning`
- **Interval**: `hour`, `day`, `week`, `month`. Default is `day`

1. **curr_event**: show current latest event of `Park`
    - **curr_event**: the latest event, source from `EVENT_HISTORY_DB`
    - **park**: the park ID
2. **list_event**: show list of events of `Park`
    - **list_event**: the list of 20 last events, source from `EVENT_HISTORY_DB`
    - **park**: the park ID
3. **count_type_event**: by `interval`, show count of `type` events of `Park`
    - **count_warning_event**: the count of warning events
    - **park**: the park ID
    - **interval**: the time interval of the stats
    - **type**: the type of the events

#### 1.2.6. WIDGET DATASOURCE *ACTION*

1. **action**: the quick action button to perform a specific action *pre-defined already*:
    - `fire_alarms`: trigger the fire alarm of all parks immediately
    - `open_gates`: open the gates of all parks immediately
    - `restart_system`: restart the iPark system after 30 seconds, with a warning message to all users
2. **switch**: the switch button to turn on/off a specific function of the system *pre-defined already*:
    - `enable_maintenance_mode`: turn on/off the maintenance mode of the system, when maintenance mode is on, all users except admins will be logged out and can not login until maintenance mode is off, with a warning message to all users. Turning off maintenance mode requires admin password for confirmation
    - `enable_emergency_mode`: turn on/off the emergency mode of the system, when emergency mode is on, all gates of the parks will be opened, all alarms will go off, and all users will receive a warning message about the emergency situation, when emergency mode is off, the system will return to normal state. Turning off emergency mode requires admin password for confirmation
    - `turn_onoff_lights`: turn on/off the lights of `Park`.
    - `turn_onoff_cameras`: turn on/off the cameras of `Park`. Turning off requires admin password for confirmation
    - `turn_onoff_sensors`: turn on/off the sensors of `Park`. Turning off requires admin password for confirmation

#### 1.2.7. WIDGET DATASOURCE *MISC*

*MISC* pre-configured widgets:

- **Park**: the park ID. `ALL` for all parks is not allowed, must be a specific park ID

1. **curr_time**: show current time based on the location of the `Park`
    - **curr_time**: the current time
    - **park**: the park ID
    - **format**: `DD/MM/YYYY HH:mm:ss`, `DD/MM/YYYY HH:mm`, `DD/MM/YYYY`, `HH:mm:ss`, `HH:mm`. Default is `HH:mm:ss`
2. **curr_weather**: show current weather based on the location of the `Park`
    - **curr_weather**: the current weather searched from a weather API based on the `location` of the `Park` *(no need to implement the actual API call, just return a random weather condition for demonstration)* Example: Rainy 23°C, Sunny 30°C, Cloudy 25°C, etc.
    - **park**: the park ID

## 2. PARKS

A simple table view to show the list of parks in the system.

Taken from `PARK_DB` database.

|id|display_name|location|fee|is_enable|is_operating|
|--|--|--|--|--|--|
|The unique identifier of the park generated by the system, can not be edited|The display name of the park|The location of the park|The fee of the park per entrance|The park is enabled or not, disabled park can not be assigned to widgets|The park is currently operating or not|

Quick actions:

- **View**: view detailed information of the park, sort by any column shown above. Requires `view_parks` permission
- **Edit**: edit toggle button to enter edit mode, in which the user can select/deselect the park to view detailed information and perform actions on the park, such as edit attributes entries. Requires `edit_parks` permission
- **Add**: add a new park to the system. Requires `add_parks` permission
- **Delete**: delete the park from the system. Requires `delete_parks` permission

## 3. STAFFS

A simple table view to show the list of staff members in the system.

Taken from `STAFF_DB` database.

- *(Assumption: For this demo, a staff member is strictly assigned to one park.)*

|id|display_name|at_park_id|role|payment|is_enable|is_on_shift|
|--|--|--|--|--|--|--|
|The unique identifier of the staff generated by the system, can not be edited|The display name of the staff|The park ID where the staff is assigned to work|The role of the staff in the park|The payment of the staff per working shift|The staff is enabled or not, disabled staff can not be assigned to working|The staff is currently on shift or not|

Quick actions:

- **View**: view detailed information of the staff, sort by any column shown above. Requires `view_staffs` permission
- **Edit**: edit toggle button to enter edit mode, in which the user can select/deselect the staff to view detailed information and perform actions on the staff, such as edit attributes entries. Requires `edit_staffs` permission
- **Add**: add a new staff to the system. Requires `add_staffs` permission
- **Delete**: delete the staff from the system. Requires `delete_staffs` permission

## 4. EVENTS

A simple table view to show the list of events in the system.

Taken from `EVENT_HISTORY_DB` database.

|id|event_code|event_name|event_type|description|at_park_id|received_time|is_acknowledged|
|--|--|--|--|--|--|--|--|

|The unique identifier of the event generated by the system, can not be edited|The code of the event|The name of the event|The type of the event, is either `info` or `warning` or `error`|The description of the event|The park ID where the event is held|The time when the event was received|The event is acknowledged or not|

Quick actions:

- **View**: view detailed information of the event, sort by any column shown above. Selecting a row to view its details counts as an acknowledge action (turning `is_acknowledged` to `true`). Requires `view_events` permission
- **Export**: export the list of events to a CSV file. Requires `export_events` permission
- **Delete**: delete the event from the system. Requires `delete_events` permission

## 5. SETTINGS

A simple settings page for users to view and edit the system settings. To view the settings page, `view_settings` permission is required. Some settings might need `edit_settings` permission to edit, some do not require any permission, and some settings might require other settings to be enabled/disabled in order to be edited.

Any modification made in the settings page will be saved to the config file of the system, and will take effect immediately without restarting the system. Personal preferences (like Language and Theme) will be saved to the user's profile in the database.

### 5.1. NOTIFICATIONS SETTINGS

- **Enable/Disable notifications**: a switch on/off to enable or disable the notifications of the system. When notifications are disabled, users will not receive any notifications (in-app push notifications and email notifications) about the events in the system, but the events will still be generated and stored in the database. Default is `true`. Requires `edit_settings` permission
- **Enable/Disable in-app push notifications**: a switch on/off to enable or disable the in-app push notifications of the system. When in-app push notifications are disabled, users will not receive any in-app push notifications about the events in the system, but they will still receive email notifications if email notifications are enabled. Default is `true`. Requires `edit_settings` permission and **Enable notifications** must be enabled
- **Enable/Disable email notifications**: a switch on/off to enable or disable the email notifications of the system. When email notifications are disabled, users will not receive any email notifications about the events in the system, but they will still receive in-app push notifications if in-app push notifications are enabled. Default is `false`.Requires `edit_settings` permission and **Enable notifications** must be enabled *(no need to implement the actual email sending, this button just toggles the setting and shows a message that email notifications are enabled/disabled)*

### 5.2. LANGUAGE_THEME SETTINGS

- **Language**: radio buttons to select the language of the system, options are `English`, `Vietnamese`. Default is `English`.
- **Theme**: radio buttons to select the theme of the system, options are `Light`, `Dark`, `System`. Default is `System`.

### 5.3. ACCOUNT_USER SETTINGS

A simple table to show the list of users in the system.

Taken from `USER_DB` database.

|id|user_name|display_name|email|group|is_enable|is_online|
|--|--|--|--|--|--|--|
|The unique identifier of the user generated by the system, can not be edited|The username of the user, used to login, must be unique|The display name of the user, can be identical to username or the other users' display name|The email of the user, must be unique|The group of the user, used to determine the permissions of the user|The user is enabled or not, disabled user can not login|The user is currently logged in or not|

Quick actions:

- **View**: view detailed information of the user, sort by any column shown above. Requires `view_settings` permission
- **Edit**: edit toggle button to enter edit mode, in which the user can select/deselect the user to view detailed information and perform actions on the user, such as edit attributes entries. Only one entry can be selected at a time. `admins` group is the only group that can edit all users' `password`, else users can only edit their own `password`. Only the user itself can edit their own `email`. Requires `edit_settings` permission
- **Revoke**: revoke the selected user's access to the system immediately by setting `is_online` to `false`, and the user can not login until the access is restored by setting `is_online` to `true`. Requires `edit_settings` permission
- **Add**: add a new user to the system. Requires `edit_settings` permission
- **Delete**: delete the user from the system. Requires `edit_settings` permission

## 7. DATABASE

*DEMONSTRATION HYBRID APPROACH*: Due to development time constraints, the system uses a hybrid database approach. Most databases are **read-only** (hardcoded with sample data, where adding/deleting/editing actions are simply mocked and do not persist). However, to demonstrate critical interactive features, `USER_DB`, `EVENT_HISTORY_DB`, and `SYSTEM_STATE_DB` **are editable** and should persist data (such as login failures, event acknowledgements, settings changes, and device toggles) during the demo session.

*NOTE*: If `is_enable` is `false`, the respective status flag (`is_operating`, `is_on_shift`, or `is_online`) is `false` and can not be `true` until `is_enable` is `true`. Moreover, false `is_enable` means the entry is disabled and can not be assigned to other related database entries, but the entry can still be viewed and edited in the system. For example, if a park is disabled, the park can not be assigned to staff members and widgets, but the park can still be viewed and edited in the system.

*Datatype*:

- **pos_int**: positive integer >= 1, smaller than `integer` type in most programming languages, can not be empty
- **id**: string, the unique identifier for event types, formatted as `ddd` where `d` is a digit (0-9), can not be empty
- **money**: positive integer >= 0, smaller than `integer` type in most programming languages, must be divided by 1000
- **err_code**: string, the error code, formatted as `0xhhhh` where `h` is a hexadecimal digit (0-9, a-f), for no error, the error code is `0x0000`, can not be empty
- **string**: string, no longer than 256 characters, can be empty string `""`
- **list**: list of `string`, each string is separated by comma `,`, can be empty string `""` for empty list
- **email**: string as email format, no longer than 256 characters, *no need to be a real email*
- **password**: string, no longer than 256 characters, at least 8 characters, must contain at least one uppercase letter, one lowercase letter, one digit, and one special character, saved as plaintext *no hashing required for demonstration*
- **object_name**: string, the name of an object, such as `group_name`, `park_name`, `staff_name`, etc., must be unique in the corresponding table, no longer than 256 characters, can not contain spaces and special characters except `_`
- **datetime**: datetime in the format of `YYYY-MM-DD HH:mm:ss`
- **time**: time in the format of `HH:mm:ss`
- **date**: date in the format of `YYYY-MM-DD`
- **boolean**: boolean value, either `true` or `false`
- **json**: a string formatted as JSON to store complex data structures

### 7.1. USER_DB

Database for users, each user can only be assigned to one group, but each group can have multiple users. The permissions of the user are determined by the permissions list of the group that the user is assigned to.

*Table*: user_db

|id|user_name|display_name|description|email|password|group|language|theme|pinned_dashboard_id|is_enable|created_at|last_modified_at|last_active|is_online|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|The unique identifier of the user generated by the system, can not be edited|The username of the user, used to login, must be unique|The display name of the user, can be identical to username or the other users' display name|The description of the user|The email of the user, must be unique|The password of the user saved as plaintext *no hashing required for demonstration*, used to login|The group of the user, used to determine the permissions of the user|The language preference of the user|The theme preference of the user|The ID of the dashboard pinned by the user|The user is enabled or not, disabled user can not login|The time when the user is created|The time when the user is last modified|The time when the user was last active|The user is currently logged in or not|
|pos_int|object_name|string|string|email|password|object_name|string|string|pos_int|boolean|datetime|datetime|datetime|boolean|
|**Example**|||||||||||||||
|1|admin|Administrator|Administrator of the iPark web-application|admin@ipark.com|Admin@123|admins|English|System|1|true|2026-01-01 00:00:00|2026-02-01 12:59:59|2026-03-01 17:45:00|true|
|2|user1|User 1|Regular user of the iPark|user1@ipark.com|User1@123|users|Vietnamese|Dark|2|true|2026-01-01 00:00:00|2026-02-01 12:59:59|2026-03-01 17:45:00|true|

### 7.2. GROUP_DB

Database for groups, each group can have multiple users, but each user can only be assigned to one group. The permissions of the users in the group are determined by the permissions list of the group, which is a list of strings that represent the permissions, such as `view_dashboard`, `edit_dashboard`, `add_dashboard`, `delete_dashboard`, `view_parks`, `edit_parks`, `add_parks`, `delete_parks`, `view_staffs`, `edit_staffs`, `add_staffs`, `delete_staffs`, `view_events`, `export_events`, `delete_events`, `view_settings`, `edit_settings`. The permissions list can be empty for no permissions, or can contain all permissions for full access.

*Table*: group_db

|id|group_name|display_name|description|permissions_list|is_enable|created_at|last_modified_at|last_active|is_active|
|--|--|--|--|--|--|--|--|--|--|
|The unique identifier of the group generated by the system, can not be edited|The name of the group, must be unique|The display name of the group, can be identical to group name or the other groups' display name|The description of the group|The list of permissions of the group, used to determine the permissions of the users in the group|The group is enabled or not, disabled group can not be assigned to users|The time when the group is created|The time when the group is last modified|The time when the group was last active|The group is currently active or not|
|pos_int|object_name|string|string|list|boolean|datetime|datetime|datetime|boolean|
|**Example**||||||||||||
|1|admins|Administrators Group|The group of administrators who have all permissions|view_dashboard,edit_dashboard,add_dashboard,delete_dashboard,view_parks,edit_parks,add_parks,delete_parks,view_staffs,edit_staffs,add_staffs,delete_staffs,view_events,export_events,delete_events,view_settings,edit_settings|true|2026-01-01 00:00:00|2026-02-01 12:59:59|2026-03-01 17:45:00|true|
|2|users|Users Group|The group of regular users *staff members* who have limited permissions|view_dashboard,view_parks,view_staffs,view_events,export_events|true|2026-01-01 00:00:00|2026-02-01 12:59:59|2026-03-01 17:45:00|true|

### 7.3. PARK_DB

Database for parks, each park can have multiple staff members, but each staff member can only be assigned to one park.

*Table*: park_db

|id|park_name|display_name|description|location|start_time|end_time|fee|max_slot|is_enable|created_at|last_modified_at|last_active|is_operating|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|The unique identifier of the park generated by the system, can not be edited|The name of the park, must be unique|The display name of the park, can be identical to park name or the other parks' display name|The description of the park|The location of the park|The start time of the park|The end time of the park|The fee of the park per entrance|The maximum slot of the park|The park is enabled or not, disabled park can not be assigned to staffs|The time when the park is created|The time when the park is last modified|The time when the park was last active|The park is currently operating or not|
|pos_int|object_name|string|string|string|time|time|money|pos_int|boolean|datetime|datetime|datetime|boolean|
|**Example**||||||||||||||
|1|central_park|Central Park|The central park in the HCMUT - Di An Campus|Di An, HCMC|06:00:00|18:00:00|2000|200|true|2026-01-01 00:00:00|2026-01-01 12:00:00|2026-03-01 17:45:00|true|
|2|north_park|North Park|The north park in the HCMUT - LTK Campus|District 10, HCMC|07:00:00|19:00:00|2500|150|true|2026-01-01 00:00:00|2026-01-01 12:00:00|2026-03-01 17:45:00|true|

### 7.4. STAFF_DB

Database for staff members who work in the parks, each staff can only be assigned to one park, but each park can have multiple staff members.

*Table*: staff_db

|id|staff_name|display_name|description|at_park_id|start_time|end_time|role|payment|is_enable|created_at|last_modified_at|last_active|is_on_shift|
|--|--|--|--|--|--|--|--|--|--|--|--|--|--|
|The unique identifier of the staff generated by the system, can not be edited|The name of the staff, must be unique|The display name of the staff, can be identical to staff name or the other staffs' display name|The description of the staff|The park ID where the staff is assigned to work|The start time of the staff's working shift|The end time of the staff's working shift|The role of the staff in the park|The payment of the staff per working shift|The staff is enabled or not, disabled staff can not be assigned to working|The time when the staff is created|The time when the staff is last modified|The time when the staff was last active|The staff is currently on shift or not|
|pos_int|object_name|string|string|pos_int|time|time|string|money|boolean|datetime|datetime|datetime|boolean|
|**Example**||||||||||||||
|1|john_doe|John Doe|A staff member who works in the central park|1|08:00:00|16:00:00|Attendant|200000|true|2026-01-01 00:00:00|2026-01-01 12:00:00|2026-03-01 17:45:00|true|
|2|jane_smith|Jane Smith|A manager who works in the north park|2|09:00:00|17:00:00|Manager|350000|true|2026-01-01 00:00:00|2026-01-01 12:00:00|2026-03-01 17:45:00|true|

### 7.5. EVENT_DB

Database for system events that can be added to `EVENT_HISTORY_DB` when they are generated in the system, such as when a staff member is late for work, or when the park is nearly full, etc. These are not editable by users but by developers only *who has access to modify this database directly*.

*Table*: event_db

|id|event_code|event_name|event_type|error_code|description|is_enable|
|--|--|--|--|--|--|--|
|The unique identifier of the event generated by the system, can not be edited|The code of the event, formatted as `ddd` where `d` is a digit (0-9)|The name of the event|The type of the event, is either `info` or `warning` or `error`|The error code, formatted as `0xhhhh`|The description of the event|The event is enabled or not, disabled event can not be assigned to events history|
|pos_int|id|string|string|err_code|string|boolean|datetime|datetime|
|**Example**||||||||||
|1|001|System Startup|info|0x0000|The system has started successfully|true|
|2|002|System Startup Failed|error|0x0001|The system failed to start due to an unknown error|true|
|3|003|System Shutdown|info|0x0000|The system has shut down successfully|true|
|4|004|System Shutdown Failed|error|0x0002|The system failed to shut down due to an unknown error|true|

### 7.6. EVENT_HISTORY_DB

Database for the history of events that are generated in the system, each event is based on a pre-defined event in `EVENT_DB`, but with additional information such as the park ID where the event is held, the time when the event is sent and received, and whether the event is read or not.

*Table*: event_history_db

|id|event_code|event_name|event_type|error_code|description|at_park_id|extra_info|sent_time|received_time|is_acknowledged|
|--|--|--|--|--|--|--|--|--|--|--|
|The unique identifier of the event generated by the system, can not be edited|The code of the event|The name of the event|The type of the event, is either `info` or `warning` or `error`|The error code, formatted as `0xhhhh`|The description of the event|The park ID where the event is held|The extra information related to the event|The time when the event was sent|The time when the event was received|The event is acknowledged or not|
|pos_int|id|string|string|err_code|string|pos_int|string|datetime|datetime|boolean|
|**Example**|||||||||||||
|1|001|System Startup|info|0x0000|The system has started successfully|1||2026-03-01 08:00:00|2026-03-01 08:00:05|false|
|2|010|Staff Enter Shift|info|0x0000|A staff member has entered the working shift|1|ID 1234567|2026-03-01 08:15:00|2026-03-01 08:15:05|false|
|3|020|Client Enter Park|info|0x0000|A client has entered the park|1|ID 2314567||2026-03-01 08:30:00|2026-03-01 08:30:05|false|
|4|008|Park Nearly Full|warning|0x0000|The park is nearly full|1||2026-03-01 17:00:00|2026-03-01 17:00:05|false|
|5|00c|System Execution Failed|error|0x0003|The system failed to execute a specific function due to an unknown error|1||2026-03-01 17:30:00|2026-03-01 17:30:05|false|

### 7.7. DASHBOARD_DB

Database for the dashboard configuration, which contains the information about the widgets that are assigned to the dashboard, such as the widget type, the widget data source, and the widget position on the dashboard.

*Table*: dashboard_db

*Note: Access to view and edit dashboards is centrally managed by the permissions in `GROUP_DB`.*

|id|dashboard_name|display_name|description|widgets_list|is_enable|created_at|last_modified_at|
|--|--|--|--|--|--|--|--|
|The unique identifier of the dashboard generated by the system, can not be edited|The name of the dashboard, must be unique|The display name of the dashboard, can be identical to dashboard name or the other dashboards' display name|The description of the dashboard|The configuration of widgets assigned to the dashboard, stored as an array of JSON objects. Each object contains the widget's full configuration (e.g., `[{"id": "w1", "type": "curr_slot_max_slot", "park_id": "1", "x": 0, "y": 0, "w": 2, "h": 2}]`)|The dashboard is enabled or not, disabled dashboard can not be assigned to home page|The time when the dashboard is created|The time when the dashboard is last modified|
|pos_int|object_name|string|string|json|boolean|datetime|datetime|
|**Example**|||||||||
|1|admin_view|Admin View Dashboard|A dashboard for admins|[{"id": "w1", "type": "curr_slot_max_slot", "park_id": "1", "x": 0, "y": 0, "w": 2, "h": 2}, {"id": "w2", "type": "chart_estimate_income", "park_id": "1", "x": 2, "y": 0, "w": 4, "h": 2}]|true|2026-01-01 00:00:00|2026-01-01 12:00:00|
|2|staff_view|Staff View Dashboard|A dashboard for staff members to view the current status of the park|[{"id": "w1", "type": "curr_slot_max_slot", "park_id": "1", "x": 0, "y": 0, "w": 2, "h": 2}, {"id": "w2", "type": "curr_fee", "park_id": "1", "x": 2, "y": 0, "w": 2, "h": 2}]|true|2026-01-01 00:00:00|2026-01-01 12:00:00|

### 7.8. SYSTEM_STATE_DB

Database for tracking the current state of system toggles and devices across parks (used primarily by `ACTION` widgets). This database is updated when users toggle switches.

*Table*: system_state_db

|id|park_id|maintenance_mode|emergency_mode|lights_on|cameras_on|sensors_on|last_modified_at|
|--|--|--|--|--|--|--|--|
|The unique identifier of the state entry|The ID of the park (`0` for global system states like maintenance/emergency mode)|System is in maintenance mode|System is in emergency mode|Lights are currently on|Cameras are currently on|Sensors are currently on|The time when the state was last modified|
|pos_int|pos_int|boolean|boolean|boolean|boolean|boolean|datetime|
|**Example**||||||||
|1|0|false|false|false|false|false|2026-03-01 12:00:00|
|2|1|false|false|true|true|true|2026-03-01 12:00:00|
|3|2|false|false|false|true|true|2026-03-01 12:00:00|
