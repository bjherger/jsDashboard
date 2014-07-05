***************************************
*               Dashboard             *
*               Version 2.0           *
***************************************
*                                     *
*          Brendan J. Herger          *
*  Digital Practices, Perkins + Will  *
*  brendan.herger@perkinswill.com     *
*  13herger@gmail.com                 *
*                                     *
***************************************

Updated: 6/16/2014

SECTIONS
*************************************

This is a relatively light-weight, interactive data visualization format, optimized for easy implementation.

This README has three primary sections:
  > How to use this dashboard (the black box method)
  > How this dashboard works (the pedant approach)
  > Data guidelines


HOW TO USE THIS DASHBOARD:
*************************************
This dashboard can be modified entirely in the .html file (without ever using any javascript).

1) On the line that looks like:
     <script type="text/javascript">var filename = “YourData.json";</script> <!-- Put your data here  -->
   replace YourData with the name of your json file. Many online tools can convert CSV, xlsx, and other formats to JSON.

2) Wherever you see chart-ring-KEYWORD or chart-row-KEYWORD, replace the current KEYWORD with a keyword from your database (e.g. a column header in the excel file you converted to JSON.

3) (Optional) If you would like to change a chart from row to ring or ring to row, simply change the chart name (i.e. chart-ring-KEYWORD becomes chart-row-KEYWORD)

4) (Optional) Confirm that you’re data adheres to guidelines (end of this document)

4) Celebrate. 


HOW THIS DASHBOARD WORKS:
*************************************

The primary components of this page are:
  > dc.js: (a javascript graphing package built on d3)- version 2.0.0 - http://dc-js.github.io/dc.js/
  > Crossfilter: (database tool optimized for quick rendering) - version 1.3.7 - http://square.github.io/crossfilter/
  > Bootstrap: framework (for element sizing and layout) - version 3.1.1 - http://getbootstrap.com
      > bootswatch yeti theme: (for coloration, layout of bootstrap template ) - version 3.1.1 - http://bootswatch.com
      > layoutit: (online tool for designing bootstrap layout) - online versioning - http://www.layoutit.com/build 
  > Specific hacks to each of these packages

A few brief notes on each:

DC.JS:
*******************
This is a light weight set of charts which are interactive and rather highly customizable. For the purpose of dashboard, the charts automatically size to their parent’s width ( a container created by bootstrap).

Whereas generally these charts are created individually, this dashboard will find all instances of either chart-ring-KEYWORD or chart-row-KEYWORD in the HTML document calling it, then populate the  ring and row charts in a for loop. This assumes that the KEYWORD is a key in the database being used, and the value returned by the database for each observation can be treated as a string. As this dashboard relies on crossfilter, it cannot handle NaN values or undefined values (where row[localQ] is not set to a value). Maps for both row and ring charts exist, such that individual charts can be modified by looking them up in the map and then modifying the returned reference.

CROSSFILTER
*******************
This is an extremely quick database developed for javascript, which dc.js relies on. It allows for the interactive filtering that makes dc.js worth while. The page-specific javascript file attached does little to utilize advance features of crossfilter, instead using it only as is necessary to create charts in dc.js. 

It is a bit of a paradigm shift from most database platforms which fill a similar need. In particular, it is built on a few concepts:
  > Dimension: Essentially a column in a traditional excel database. This is a measurement taken for every observation (row in the excel equivalent). It is absolutely necessary to have an value for each observaion, as crossfilter will silently break if presented with NaN or undefined value (displaying values which appear OK but are actually non-sensical).

  > Filter: Essentially pressing ‘sort by column’ in the excel equivalent, this is a count of how many ‘people’ have an attribute. For example, if there were a shirt color column and each person were one row, the filter might show that there are 8 people with blue shirts, 4 with red, etc. This is the data that dc.js plots.

This dashboard implements a custom filter function which can handle a few missing values, but this is only for legacy reasons. Any database which does not have all questions answered for all rows is very ill suited for crossfitler, and therefore ill-suited for this dashboard. 

BOOTSTRAP
*******************
This is a layout platform developed for web implementation across screen sizes (i.e. it will work well on both desktop and mobile). Each element is subdivided into 12 equal units per row, and the user selects how many of those units each content element should fit. If the screen is wide enough, all 12 unit’s worth of content will be shown, otherwise bootstrap will display elements on separate rows. This is a fairly minimal framework tool, and as such bootswatch and layoutit are both required for a presentable product.

BOOTSWATCH YETI THEME
*******************
A simple theme which has been customized. Full bootstrap themes are available from bootswatch.com, as are the variable.less files. This theme is actually only based on the yeti theme, and has been customized (particularly for footer background color, hero box color, well background color, and a few others).

LAYOUTIT
*******************
A simple interface for creating quick bootstrap layouts. This could be done by hand, but this presents a preview layout and well formatted code. Mentioning this tool could have been omitted, but its a good reference to have. While there is nothing wrong with the layout in this file, this tool allows for different layouts which may better suite the dashboard being created. 

HACKS
*******************
Each of these packages is rather well developed and works well in its own context, but in many cases they have been hijacked to work in this context. In particular:

  > dc.js for loops: dc.js generally has a fixed reference ( such as var chart1 = dc.rowChart("#chart-row-chart1”)  ), but instead this dashboard creates all row and ring charts in a for loop, and places them in a map. This requires that all charts of each type is exactly the same (except for size, which is taken from the parent html element). Individual charts can be customized by looking them up from the appropriate map, and then customizing.

  > More than 32 charts: Crossfilter does not currently support more than 32 active filters (essentially limiting us to 32 charts). This feature is in semi-active development by the crossfilter community, but for now this dashboard cannot display more than 32 charts. 

  > Database formats: dc.js does support non-json data types. I have elected to drop this support for this dashboard. All data should be converted to a completely flattened JSON object. 

  > Data manipulation: By design, this dashboard is not capable of any data manipulation. Data should be completely flat, well formatted, and free of empty values, NaNs and undefined values. This dashboard is for data visualization only, and there are many resources available for data manipulation and formatting. 

  > Chart coloration: This file automatically assigns colors to responses (i.e. yes, no, 4+) as it encounters them. A color can be consistently assigned to a value (i.e. yes is always the color) by setting the domain of the color function. Additionally, the value can be assigned a particular color by setting the range. Finally, the colors used for all charts ca be changed by using a different color ordinal. This is all in the code : 

	   var color = d3.scale.category20c()
        	.domain("Yes", "No", "No Response"); 
        	// .range("#HexColorForYes”, "#HexColorForNo”, "...more")

  > Changing Bootstrap layout: This is more of a design question, suited for a google search. All subdivisions in a row should add up to 12 units, and colors can be changed with Bootswatch themes or a number of other resources. It is incredibly easy to change the layout and produce an array of visually distance dashboards with minimal work. 

  > Bootstrap layout hacks & quirks: It is important to note that the wells (boxes which surround content) will not stretch to the height of charts. For this reason it is necessary to include some character after the charts (I’ve used <small> &nbsp </small>), so that the box includes the charts. Additionally, this dashboard does not currently support resizing windows; once a page has loaded, the size and aspect ratio are set in stone, and re-sizing the window can have varying effects. 


DATA GUIDELINES
*******************

These limitations are primarily driven by crossfilter restrictions and text size issues. 

  > File Type: JSON. No others are supported

  > File Size: JSON file should be less than 5 MB (Internet explorer will not allow for larger files to be loaded)

  > JSON depth: Json should be completely flat, with the 0th level representing the whole JSON, 1st level representing individual observations, and the 2nd level representing all responses for observation. Other formats will cause failure. 

  > Non-Response: All questions must be answered for all observations. Non-response is not supported. 

  > Response Order: Ordering in legend is done solely by alphabetical order. Other ordering is not supported. 

  > Open Ended Questions: Not supported. Plan accordingly.

  > Branching Questions: Not inherently supported. Any branching questions must be flattened in the original database before import. 

  > Response Length: Responses should be shorter than 14 characters (longer answers will be truncated)

  > Response Data Type: Responses will be converted to strings. Plan accordingly.

  > Number of responses: No question should have more than 5 unique responses (otherwise ring chart legend will not be shown, and row chart will be omitted entirely)

  > Multiple select: Each observation can only have one response to each question. Multiple select is not supported. 

