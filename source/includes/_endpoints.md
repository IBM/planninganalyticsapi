# Showing a widget

You can use an endpoint to return an HTML page that renders a Planning Analytics Workspace book, a Planning Analytics Workspace view, a TM1 legacy view, a drill through view, a dimension editor, a set editor, or websheet.

## Syntax
> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan&view=Budget%20Input%20Detailed
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan&view=Budget%20Input%20Detailed
```

Method: `GET`

URL Format: `/ui?p1=a1&p2=a2&...#f1=a3&f2=a4&...`

Query parameters: `?p1=a1&p2=a2&...`

Fragment parameters: `#f1=a3&f2=a4&...`

You have flexibility when it comes to how you want to structure your requests. All parameters can be passed as either a query or a fragment parameter. Queries and fragment parameters are processed in the same way. 

The following requests are all equivalent:

* `/?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan`
* `/#type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan`
* `/?type=cube-viewer#server=Planning%20Sample&cube=plan_BudgetPlan`

All of the above requests will return the following:

* `200 OK` on success. 
* A response body containing an HTML page that can be put into an iframe. The HTML page will automatically load the requested widget.


# Parameters

The `type` parameter (alias `Type`) determines which type of widget or asset to render. Each widget/asset type requires a different set of parameters. Below are the widgets/assets that you can return and the parameters associated with each.

<aside class="notice">
All parameters are case sensitive.
</aside>

## Planning Analytics Workspace books

> Examples for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=book&path=/shared/myBook1
http(s)://<Planning Analytics Workspace URL>/?perspective=book&path=/shared/book1&embed=true
```

> Example for New experience view:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=book&path=/shared/myBook1
```

> Example for opening a book in the New experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=book&path=/shared/myBook1
```

> Example for opening a book in the New experience using the `embed` parameter:

```shell
http(s)://<Planning Analytics Workspace URL>/?perspective=dashboard&path=/shared/book1&embed=true
``` 

The following parameters apply to `type=book`.

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=book`
path | Yes | The absolute location of the asset| - `path=/shared/myBook` <br> - `path=/shared/path/to/my/book/myBook` <br> - `path=/personal/path/to/my/book/myBook`

You can open a New experience book by using the Planning Analytics Workspace URL. If you want to control synchronization settings with the URL or JavaScript APIs, use the `/ui` parameter. You can also open a book by using the `embed` parameter which shows or hides the top navigation bar.

## Planning Analytics Workspace views

> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan&view=Budget%20Input%20Detailed
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan&view=Budget%20Input%20Detailed
```
> Example for New experience using `properties` parameter:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=cube-viewer&server=Planning%20Sample&cube=plan_BudgetPlan&view=Budget%20Input%20Detailed&properties={"dimensionOverview_name":false, "dimensionOverview_sectionheaders": false}
```

The following parameters apply to `type=cube-viewer` when `path` is defined.

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=cube-viewer`
path | Yes | The absolute location of the asset.| - `path=/shared/myView` <br> - `path=/shared/path/to/my/view/myView`
server | No | Overrides which TM1 server the view is located in. You can use this parameter if you have multiple TM1 servers with the same cube view; if you have a Planning Analytics Workspace view for one TM1 server, but want to use another TM1 server, you can use the `server` parameter to define which server to use.| - `server=Planning_Sample2`
toolbar | No | See the TM1 Legacy views section below for more detail. | - `toolbar=all` <br> <img width=400/>
properties | No | JSON object where key is the property name and value is a boolean denoting the property is on or off. | Supported properties are: <br> - `dimensionOverview_name` (default is true) <br> - `dimensionOverview_iconography`(default is true) <br> - `dimensionOverview_contextinformation`(default is true) <br> - `dimensionOverview_sectionheaders` <br> -  `dimensionOverview_sectionicons`

## TM1 Legacy views

> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=cube-viewer&path=/shared/ABC/KamView
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=cube-viewer&path=/shared/ABC/KamView
```

The following parameters apply to `type=cube-viewer` when `path` is defined.

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=cube-viewer`
server | Yes | Name of TM1 server where the view is located. | - `server=SData` <br> - `server=Planning%20Sample`
cube | Yes | Name of cube where view is located. This cube must exist inside the specified `server`. | - `cube=Input%20Cube` <br> - `cube=plan_BudgetPlan`
view | No | Name of the view. This view must exist inside the specified `cube`. The default view is used if `view` is not defined. | - `view=CPView` <br> - `view=Budget%20Input%20Detailed`
private | No | Defines whether or not the view is private. Can be either `true` or `false`. The default is `false`. | - `private=true` <br> - `private=false`
toolbar | No | A comma delimited string of action names to display in the toolbar. The `all` preset denotes all actions. The default is `all` if this parameter is not defined. Supported actions are as follows: <br>    - `export` <br>    - `save` <br>    - `swapAxes` <br>    - `suppressZero` <br>    - `refresh` <br>    - `sandbox` <br>    - `toggleOverview` <br> The order that the actions are shown in the toolbar is the same order defined in this parameter. | - `toolbar=all` <br> - `toolbar=suppressZero,sandbox,refresh,save` <br> <img width=400/>

<!--
## Drill through views
-->
<!--
> Example:
-->
<!--
```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=drill-through&server=Planning%20Sample&dtCube=plan_BudgetPlan&dtType=Cube&dtScript=Budget_line_entry&dtCellCoord=FY%202004%20Budget,Total%20Business%20Unit,Total%20Organization,Sales,local%20exchange%20rate,Account%20Level%20Input,Q1-2004
```
-->
<!--
The following parameters apply to `type=drill-through`.
-->

<!--
Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=drill-through`
server | Yes | Name of TM1 server where cube is located| - `server=SData` <br> - `server=Planning%20Sample`
dtCube | Yes | Name of the cube to drill through. This cube must exist inside the specified `server`. | - `cube=Input%20Cube` <br> - `cube=plan_BudgetPlan`
dtType | Yes | The drill through type. | - `dtType=Cube`
dtScript | Yes | Name of the drill through script. | - `dtScript=Budget_line_entry`
dtCellCoord | Yes | A comma separated list of target cells. | - `dtCellCoord=Total%20Business%20Unit%2CTotal%20Organization` <br> (Decodes to `Total Business Unit,Total Organization`.) <br> <img width=500/>
-->

## Dimension editor

> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=dimension-editor&server=Planning%20Sample&dimension=plan_business_unit&hierarchy=plan_business_unit
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=dimension-editor&server=Planning%20Sample&dimension=plan_business_unit&hierarchy=plan_business_unit
```

The following parameters apply to `type=dimension-editor`.

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=dimension-editor`
server | Yes | Name of TM1 server where dimension is located. | - `server=SData` <br> - `server=Planning%20Sample`
dimension | Yes | Name of the dimension. This dimension must exist inside the specified `server`. | - `dimension=region` <br> - `dimension=plan_version`
hierarchy | No | Name of the hierarchy of the specified `dimension`. This hierarchy must exist inside the specified `dimension`. If not defined, it will default to the same value as `dimension`. | - `hierarchy=ManufacturingSource` <br> - `hierarchy=plan_version` <br> <img width=400/>

## Set editor

> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>/ui?type=set-editor&server=Planning%20Sample&dimension=plan_business_unit&hierarchy=plan_business_unit&uniqueName=All%20Business%20Units
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=set-editor&server=Planning%20Sample&dimension=plan_business_unit&hierarchy=plan_business_unit&uniqueName=All%20Business%20Units
```

The following parameters apply to `type=set-editor`.

Parameter name | Required | Description | Examples
---------- | ------- | ------- | ------- |
type | Yes | The type of widget to render (alias `Type`). | - `type=set-editor`
server | Yes | Name of the TM1 server where the set or subset is located. | - `server=SData` <br> - `server=Planning%20Sample`
cube | Yes | Name of the cube where view is located. This cube must exist inside the specified `server`. | - `cube=Input%20Cube` <br> - `cube=plan_BudgetPlan`
dimension | Yes | Name of the dimension. This dimension must exist inside the specified `server`. | - `dimension=region` <br> - `dimension=plan_version`
uniqueName | Yes | Unique ID of the set or subset. | - `uniqueName=Europe_all` <br> - `uniqueName=FY%202004%20Budget`
dimensionCaption | No | Caption name of the specified `dimension` (if applicable). |
hierarchy | No | Name of the hierarchy of the specified `dimension`. This hierarchy must exist inside the specified `dimension`. If not defined, it will default to the same value as `dimension`. | - `hierarchy=ManufacturingSource` <br> - `hierarchy=plan_version`
hierarchyCaption | No | Caption name for the specified `dimension` (if applicable). | 
alias | No | The alias name for the set or subset (if applicable). | 
private | No | Defines whether or not the set or subset is private. Can be either `true` or `false`. The default is `false`. | - `private=true` <br> - `private=false` <img width=400/>

## Websheet

> Example for Classic experience:

```shell
http(s)://<Planning Analytics Workspace URL>//ui?type=websheet&Action=Open&Workbook=Applications/Planning%20Sample/Management%20Reporting/Actual%20v%20Budget&AdminHost=localhost&TM1Server=Planning%20Sample
```

> Example for New experience:

```shell
http(s)://<Planning Analytics Workspace host>[<Planning Analytics Workspace port>/?type=websheet&Action=Open&Workbook=Applications/Planning%20Sample/Management%20Reporting/Actual%20v%20Budget&AdminHost=localhost&TM1Server=Planning%20Sample
```

The TM1 Web URL API parameters apply to `type=websheet`. Currently, none of the parameters passed in are changed. The parameters are simply copied when it redirects to the TM1Web URL API to render a websheet.

For more information, see <a href ='https://www.ibm.com/support/knowledgecenter/SSD29G_2.0.0/com.ibm.swg.ba.cognos.tm1_ug.2.0.0.doc/c_tm1webjv_urlapi_section.html'>TM1 Web URL API</a>