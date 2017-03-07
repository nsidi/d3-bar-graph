# D3.js Bar Graph
Using the D3.js library, i have created a simple bar graph to experiment with data and showcase it visually. 

<b>Bar Graph</b>
<p>Let’s dive into creating a bar graph! For this example, I will be using the data provided by The Daily Bread Food Bank from the <a href="https://www1.toronto.ca/wps/portal/contentonly?vgnextoid=4de87768be338310VgnVCM1000003dd60f89RCRD&vgnextchannel=bc86e03bb8d1e310VgnVCM10000071d60f89RCRD"> City of Toronto website </a>. The data provided shows the number of visitors per month from 2009 to October 2015. In order to keep this example simple, I summed up the number of visitors for each year and left out 2015 (as this would have skewed the results for the lack of two months of data). As a result, this graph will show the total number of visitors for each year from 2009 to 2014. Keep in mind that I have simplified it so that we only work with two columns. </p>

Here is the data: 
Year	Visits
2009	860,200
2010	815,200
2011	784,200
2012	786,500
2013	715,000
2014	716,970

<b>So how can we translate this data into a bar graph? Follow the steps below: </b>
<b>Save your data into .tsv file</b>
<p>Copy and paste the above data into an Excel spreadsheet in the same format: two columns. Save this document as a Tab Delimited Text (.txt) file. In your documents folder, change the extension of this file to ".tsv" (tab separated values). </p>

<p>You may also want to check if the .tsv file does not contain data in string format – as this did happen to me. Remove the quotations around the visit numbers. Also, remove the commas that are placed in the number values. This was also another area that gave me errors in the console because it was not able to read the comma as part of the number. </p>

<b>Set up your folder</b>
<p>Create a folder for your project. In that folder, create a sub-folder and label is “d3”. This is where you will place the latest version of d3.JS (here is the present link – which is version 4: https://d3js.org/)  </p>

<b>Set up your Index.html</b>
<p>In your project folder, create an index.html file using your favourite coding software. Your file should look like this (I am using the Brackets software in this case): </p>

<p>Most of our work will take place between the script tags. </p>

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8">
		    <link rel="stylesheet" type="text/css" href="main.css"> 
		</head>

		<body>
		<!--HEADERS-->
		    <h1>Daily Bread Food Bank Visits Per Year</h1>
		    <h2>2009 to 2014</h2>

		<!--SCRIPT -->
		<script src="d3/d3.js"></script>
		<script>

		</script>
		</body>
		</html>


<p>You will notice that I have linked the CSS to an external file. More often, than not, I have noticed that those who use D3.JS like to place it in one file. I like my code to clean and have thus placed it separately. </p>

<b>Title your graph</b>
<p>You can place the title of the graph where it states <!-- Headers -->. <p>
Daily Bread Food Bank Visits Per Year
2009 to 2014

<b>Declare dimensions and margins of the graph</b>
<p>Let’s go ahead and declaring the margins and dimensions for the graph. We will call upon these variables soon!</p>

		var margin = {top: 80, right: 20, bottom: 60, left: 300},
		width = 960 - margin.left - margin.right,
		height = 500 - margin.top - margin.bottom;

<b>Next thing we want to set up is the range for x and y. </b>
<p>In order to set up the range for the x-axis, we will use a function called d3.scaleBand(). This will divide up the space evenly into bands across the range. This function is usually used for bar graphs. Using the chaining method, and the variables we declared earlier, we will make this scale band go from 0 to the end of the width.</p>

		var x = d3.scaleBand() //divides up the space into bands across the range
		.range([0, width])
		.padding(0.1);

<p>As for the y-axis, the we will use scaleLinear, instead of scale Band. The scaleLinear function is the most common one used in d3. It transforms data value into positions and lengths. Similar to the x-scale, we have declared the range to start from the height and ends at 0px.  </p>
  
		var y = d3.scaleLinear() 
		.range([height, 0])

<b>We now want to add a Scalable Vector Graphic (SVG) object to the body. </b>
<p>The SVG will help us create the actual bars for the graph. We would then append this to the body and give it attributes (width, height and margins – which, as you will recall, were already declared above.) So all we would have to do is call those variables, as we do not want to hard-wire them. </p>

		var svg = d3.select("body").append("svg")
		.attr("width", width + margin.left + margin.right)
		.attr("height", height + margin.top + margin.bottom)


<p>In that same chain, we will append a group element and label it “g”. The group element is basically a container where we will place all SVG elements inside. We will also give it attributes of the margin. </p>

		.append("g")
		.attr("transform", "translate(" + margin.left + "," + margin.top + ")");

<b>Time to grab the data! </b>
<p>Data can be inputted multiple ways (array of numbers, strings, objects, JSON, TSV, CSV etc.). Remember that .TSV file you saved your data in earlier? Time to get that running! </p>

<p>This piece of code is requesting for the data from our .tsv file: </p>

		d3.tsv("dailybreadvisits.tsv", function(data) {

<p>Within this function, we have another function that is pulling values out of each data, one row at a time: </p>

		data.forEach(function(d) {

<p>We now need a piece of code that ensures that the data is recognized as a number. The following will ensure it is: </p>

		d.visits = +d.visits;
		});

<b>Let’s now set up the scale ranges for x and y. </b>
<p>Using the data we just inputted, we can implement it into our scale. For the x-domain, we will extract the data from our file and map it out. In this case, our x-domain will include “years”. It will therefore display all the data in the “years” column. </p>

		x.domain(data.map(function(d) { 
		return d.year; 
		}));

<p>As for the y-domain, we will make use of data from the “visits” column. In this case, I have set up the minimum to be 680000, as 0 would not display the results accurately. </p>

		y.domain([680000, d3.max(data, function(d) { 
		return d.visits; 
		})]);

<b> Are we missing anything? Yes! The bars for our graph! We can use SVG to create them. </b>

<p>This may look odd because we will first start out by selecting all classes named “bar”, which do not exist yet! </p>

		svg.selectAll(".bar")

<p>This counts the number of values in our data – just the number of values, not to be confused with the values.  </p>

	       .data(data)

<p>This method creates placeholder elements and prepares for the next elements to step in. </p>

	       .enter()

<p>We then append the element to the SVG. In this case, we are appending the rectangle element. </p>

		.append("rect")

<p>Now we can give it attributes (not style – as this can be edited in our CSS file). </p>

            .attr("class", "bar") // This attribute sets the class name to “bar”
            .attr("x", function(d) { return x(d.year);}) //Sets the bars’ x position
            .attr("width", x.bandwidth()) //sets the band size 
            .attr("y", function(d) { return y(d.visits); })//Sets the bars’ y position
            .attr("height", function(d) { return height - y(d.visits); }); sets the height for each bar       according to the data. 

<p>The following code will then add the x and y axis to the group. </p>

		//Add the x-axis:
		    svg.append("g")
		       .attr("transform", "translate(0," + height + ")")
		       .call(d3.axisBottom(x));


		 //ADD THE Y-AXIS
		    svg.append("g")
		       .call(d3.axisLeft(y));});

<b>Here is the code to for your CSS. </b>

               .bar { 
                   fill: steelblue; 
               }

               .bar:hover {
                 fill: brown;
               }

               h1, h2 {
                   text-align: center;
               }

<b>Here is the full code: </b>

		<!DOCTYPE html>
		<html>
		<head>
		    <meta charset="utf-8">
		    <link rel="stylesheet" type="text/css" href="main.css"> 
		</head>

		<body>
		<!--HEADERS-->
			<h1>Daily Bread Food Bank Visits Per Year</h1>
		    <h2>2009 to 2014</h2>




		<!--SCRIPT -->
		<script src="d3/d3.js"></script>
		<script>
		    //DIMENSIONS AND MARGINS OF GRAPH
		    var margin = {top: 80, right: 20, bottom: 60, left: 300},
		    width = 960 - margin.left - margin.right,
		    height = 500 - margin.top - margin.bottom;


		    //RANGES SETUP 
		    var x = d3.scaleBand() //divides up the space into bands across the range
			      .range([0, width])
			      .padding(0.1);

		    //scaleLinear transforms one value in the domain interval into a value in the range interval
		    var y = d3.scaleLinear() 
			      .range([height, 0])



		    //APPEND SVG OBJECT TO BODY 
		    //APPEND "GROUP" ELEMENT TO SVG
		    //MOVE GROUP ELEMENT TO THE TOP LEFT MARGIN
		    var svg = d3.select("body").append("svg")
				.attr("width", width + margin.left + margin.right)
				.attr("height", height + margin.top + margin.bottom)
				.append("g")
				.attr("transform", "translate(" + margin.left + "," + margin.top + ")");



		    //GET THE DATA -- TSV IS EASIEST FORMAT TO IMPLEMENT
		    d3.tsv("dailybreadvisits.tsv", function(data) {
			    data.forEach(function(d) {
			    d.visits = +d.visits;
			});


		    //SCALE RANGE OF DATA IN DOMAIN
		    x.domain(data.map(function(d) { 
			return d.year; 
		    }));
		    y.domain([680000, d3.max(data, function(d) { 
			return d.visits; 
		    })]);




		    //APPEND RECTANGLES FOR BAR CHART
		    svg.selectAll(".bar")
		       .data(data)
		       .enter()
		       .append("rect")
			    .attr("class", "bar")
			    .attr("x", function(d) { return x(d.year);})
			    .attr("width", x.bandwidth())
			    .attr("y", function(d) { return y(d.visits); })
			    .attr("height", function(d) { return height - y(d.visits); });



		    //ADD THE X-AXIS
		    svg.append("g")
		       .attr("transform", "translate(0," + height + ")")
		       .call(d3.axisBottom(x));



		    //ADD THE Y-AXIS
		    svg.append("g")
		       .call(d3.axisLeft(y));});

		</script>
		</body>
		</html>






