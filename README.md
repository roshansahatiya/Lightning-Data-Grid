[![Deploy](https://deploy-to-sfdx.com/dist/assets/images/DeployToSFDX.svg)](https://deploy-to-sfdx.com)
<a href="https://githubsfdeploy.herokuapp.com">
  <img alt="Deploy to Salesforce"
       src="https://raw.githubusercontent.com/afawcett/githubsfdeploy/master/deploy.png">
</a>

# Lightning-Data-Grid [![Build Status](https://travis-ci.org/madmax983/Lightning-Data-Grid.svg?branch=master)](https://travis-ci.org/madmax983/Lightning-Data-Grid)
A data grid for the Lightning Component Framework  

![screenshot of lightning data grid](/assets/screenshot.png "Lighting Data Grid Screenshot")

You probably do not need this. The lightning:datatable and lightning:treegrid(Coming in Spring '18!) base components probably fulfill your use case, and you should look at those components first.
However, if your use case for a data grid is not fulfilled by either of those two base components, Lightning-Data-Grid might be the answer. This component is intended as an extensible and low-level data grid that can be built up into more usable components. 
Currently supports inline-editing of hierarchical data , virtual scrolling, and global search. This is still a huge WIP, use as your own risk.

## Quick Demo Setup

1. Click on `Deploy to SFDX` button.
2. Navigate to a page you want to add the data grid component.
3. Add in the `DataGridImpl` component to a page layout.

## About

The Data Grid contains a toolbar component, a header component (right now this just renders the column names, in the future it can contain filtering/column menus), a row component, and a cell component.  

The Data Grid currently has a pretty simple API, it expects a config javascript object, and a data javascript object.   

The config should look like:  
<code>
<pre>
{
  columns: [
      {
          name: "columnName that matches the name of the dataField",
          label: "Label string that is displayed for the column header"
      }
  ],
  rowsDisplayed: *Integer*,
  scrollable: *boolean*,
  editable: *boolean*
}
</pre>
</code>

columns: Column definition of the data grid. Requires name and label for rendering. Any properties you set here will get populated down to the cell decorators and cell editors.

rowsDisplayed: How many rows are initially rendered. Control how big the table is!  

scrollable: Enables virtual scrolling on desktop.

editable: You can control whether the entire grid is editable. Columns also take an editable flag, so you can control it ast the column level as well.

The data object currently takes the following shape:
<code>
<pre>
{
    data: {
        id: "dataId",
        name: "dataName"
        etc . . . These keys should match your column names, and is how the data in the row will get displayed
    },
    parent: null
}
</pre>
</code>

Right now I am trying to keep the actual record data separate from some of the row properties needed for displaying hierarchies and such. This way you only have to extract the data object out of the row data, instead of doing a bunch of property deleting, which is always fun.  

Explanation of other properties:  

parent: id of the parent row for a child row. In the future, I would like to be able to determine this via configuration, and specify a parent field within the data object.  

Decorators and Editors implement the cellFacet interface. Check out the defaultDecorator, defaultEditor, programmaticDecorator, and programmaticEditor for examples. The two default use aura:if for their implementation, while the two programmatic components perform their logic with a javascript controller and $A.createComponent. They get fed the cell value and the associated column data. The general idea is to conditionally render a component based on that information.

You can look at the DefaultSearch component to see how a global search for the grid is being implemented, and how you could change it to implement your own search. The GetAttribute event can be used to get any attribute off of the grid, and takes a callback that can be invoked with that value. The DefaultSearch uses this get the grid data, but it can be used for other use cases outside of this. It then filters out the matches it wants, and sends a GridViewMutation event, which sets the current grid view. The plan is to use this event for sorting and filtering as well. 

Look at DataGridImpl component for an example of all of this being setup.

## Methods

Init: Once you have the data for the Grid, you can call the init method on it, and the grid will take care of setting everything up and rendering the view.
Refresh: Call refresh on the grid when you change the data structure. 
