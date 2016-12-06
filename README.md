##Intro to React by an example of live car filtering page

Here I have built a live car filtering page using React.js. You can check the result page [here](https://duoshen.github.io/cs1300development/) and the source code [here](https://duoshen.github.io/cs1300presentation/) (please use Chrome to open these link). In the following paragraph, I will give you some brief understanding of React and how to get started with a simple web page just like live filtering.


###Why React?
Working as a front-end developer, have you got tired of writing hundreds or thousands js code to make interactive UI elements? Or have you felt it troublesome to deal with data changes? Here I would like to introduce a popular Javascript framework called REACT, which make creating interactive UI elements and computing data much more easier. 

###What is React?
React is an open-source JavaScript library maintained by Facebook. It is declarative, flexible and component-based. Each component has its own states and props, and data is passed through props. 

##Let’s get started!
###Quick understanding of the component structure
As mentioned before, React is component-based. A component takes in parameters, called props, and returns a hierarchy of views to display. 

The live car filtering page is broken down into several components structured as follows:

````
index.jsx
   |__ FilterableTable.jsx
          |__ ManufacturerSelector.jsx
          |__ ColorSelector.jsx
          |__ PriceRangeSelector.jsx
          |__ SortingOptions.jsx
          |__ ItemTable.jsx
                 |__ ItemRow.jsx
````
The entry point to the application is `index.js`, which initializes the `FilterableTable` component with the dataset.

###Creating FilterableTable

`FilterableTable` is a subclass of React.Component.

~~~~
var FilterableTable = React.createClass({
	return (
            <div>
                <div>
                    <ManufacturerSelector manufacturers={manufacturers} selected={this.props.selectedManufacture} selectionCallback={this.updateManufacturerSelection} />
                    <ColorSelector colors={colors} selected={this.props.selectedColor} selectionCallback={this.updateColorSelection} />
                    <PriceRangeSelector selectionCallback={this.updatePriceRangeSelection}/>
                </div>
                <div>
                    Sort by : <SortingOptions sortingCallback={this.updateSorting}/><br/>
                    <ItemTable items={displayItems} filePathPrefix={this.props.filePathPrefix} />
                </div>
            </div>
        );
    }
});
~~~~

FilterableTable’s states and their initial values are:

~~~~
getInitialState: function () {
    return {
        filterText: '',
        selectedManufacture: ALL.toLowerCase(),
        selectedColor: ALL.toLowerCase(),
        sortingField: "Price",
        sortInOrder: true,
        minPrice: 0,
        maxPrice: Number.MAX_VALUE
    };
}
~~~~

Besides  FilterableTable, I also created several components such as ManufacturerSelector, ColorSelector, PriceRangeSelector to filter by different criteria, SortingOptions to sort by price and year, and the ItemTable is to contain all the filtered results. 

Now I got the FilterableTable, before it could actually do any filter function, I should first put it into a web page.  Let’s say there’s an `index.html` in which has a \<div>

~~~~
<div id="container"></div>
~~~~

This is called a DOM node. I should render the `FilterableTable` React element in to this DOM node and pass its props in the `index.jsx` file.

~~~~
ReactDOM.render(
    <FilterableTable
        items={items}
        itemsLength={items.length}
        filePathPrefix="./img/"
        filterText="ass"/>,
    document.getElementById('container')
)
~~~~


###Adding Dataset
To filter the cars by different criteria, we need at least tens of cars’ data to filter from. Since this is a purely front-end web page, I chose to store the cars’ data in JSON type and simply put in `index.jsx` file.

Each car has information of its manufacturer, model, year, price, color and photo link. A typical JSON data looks like:

~~~~
{
	manufacturer: 'Porsche',
	model: '911',
	year: 2011,
	price: 135000,
	color: 'red',
	img: 'porsche_911_red.jpg'
}
~~~~


###Filter Function

By now, we have prepared everything for the FilterableTable. Let’s take ManufacturerSelector as an example of how to implement the filter function.

> ####1. An interactive ManufacturerSelector component:
 Let’s make the FilterableTable do filter after any criteria got clicked in ManufacturerSelector. Add an method `handleClick` in the `ManufacturerSelector.jsx`. 
 
> ~~~~
handleClick: function (e) {
    this.setState({selectedManufacture: e.target.value.toLowerCase()}, function() {
        this.props.selectionCallback(this.state.selectedManufacture);
    }.bind(this));
}
 > ~~~~

> Then call `handleClick` method when click event happens in the `render()` function:

> ~~~~
render: function () {
return (
    <form action="">
        {
            return <div><input type="text"
              className={classes}
              name="manufacturer"
              value={m}
              onClick={this.handleClick} /></div>
            }.bind(this))
        }
    </form>
    </div>
);
}
>~~~~

----

> ####2. Passing data through props
 Now we have the selected manufacture in ManufacturerSelector, how can we let the filterable know it and do filter? Remember when I create the FilterableTable, I call a `updateManufacturerSelection`. This method would get the selected data in inner component and update FilterableTable’s state.
 
> ~~~~
updateManufacturerSelection: function (selection) {
    this.props.selectedManufacture = selection;
    this.setState({selectedManufacture: selection});
}
> ~~~~ 
----

> ####3. Implementing filter function:
`filter` function would take in each item (car data) and check whether it’s manufacture equal to the selected manufacture.

> ~~~~
filterItems: function (items) {
    return items.filter(function (item) {
        if ((this.state.selectedManufacture != ALL.toLowerCase()) && (this.state.selectedManufacture != item.manufacturer.toLowerCase())) {
            return false;
        }
    }.bind(this))
}
> ~~~~

Filter by color and price is similar to the manufacture one, you can learn it and implement yours!

###Sorting Function
The process of creating `SortingOptions` component and passing data is similar as the filter. The `sort` function takes in two item each time and compare them according to the `sortingField` and `sortInOrder` states.

~~~~
sortItems: function (items) {
    return items.sort(function(a, b) {
        var aField, bField;
        switch(this.state.sortingField) {
            case "Year":
                aField = a.year;
                bField = b.year;
                break;
            case "Price":
                aField = a.price;
                bField = b.price;
                break;
        }
        
        var result = 0;
        if (aField < bField) {
            result = -1;
        }

        if (aField > bField) {
            result = 1;
        }

        if (!this.state.sortInOrder) {
            result = 0 - result;
        }

        return result;
    }.bind(this))
}
~~~~


**Congratulations! We’ve now finished the basic function using React. Isn’t it very easy within less than 200 lines of code!**

###Design the Web Page
You might think the filtering page is in bad visual design. Don’t worry! Add some pictures, change the colors, etc. You can create your own CSS to make it awesome. Just to be creative!
