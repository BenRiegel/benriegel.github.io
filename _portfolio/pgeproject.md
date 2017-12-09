---
layout: post
title: PGE Project
thumbnail-path: "img/PGE_Case_Study/IMG2.png"
short-description: The People's Guide to Energy Project (PGE Project) is a student-learning project created by Dr. Autumn Thoyre, a professor at Stan Francisco State University.
---

**Background**

The People's Guide to Energy Project (PGE Project) is a student-learning project created by Dr. Autumn Thoyre, a professor at Stan Francisco State University. The goal of the project is for students to research locations where disadvantaged groups of people have been harmed unjustly by activities related to energy production or consumption (e.g. the construction of a power plant that disproportionally harms residents of a low-income neighborhood). Once created, student projects are posted on a website for viewing by the general public.

The PGE Project was first developed in 2014 while Dr. Thoyre was working at Colgate University in New York State. The original website was a WordPress site that featured an interactive web map that displayed the locations of student research sites as point graphics. (See image below for screen shot of the original website. Note that the interactive map has been replaced by an advertisement.) Users could clink on a site, and a popup window would open that would display a brief summary of the student project as well as a link to the webpage that contained the full write-up. The original website also allowed students to log in and publish their projects using WordPress tools.

![Original PGE Website](/img/PGE_Case_Study/IMG1.png)

**Problem**

WordPress websites typically are not very interactive due to the fact that they do not allow developers to include custom JavaScript code. The original PGE website required a plug-in for the interactive web map. According to WordPress' terms of service, plug-ins are not permitted under the basic, free plan. In order to have plug-ins, one must upgrade to a plan that costs $25 per month. While Dr. Thoyre was at Colgate University, this fee was paid by the university; however, when she moved to San Francisco State University, this was no longer possible and she would have had to pay the fee out of pocket. While Dr. Thoyre wanted to maintain the interactive map and the ease with which students can publish material on the WordPress site, she did not want to pay the rather expensive fee. I was recruited to help find a solution to this problem. An adequate website would have three components. It would:

1)	Feature a similar interactive web map,  
2)	Allow students to input their projects, but
3)	Not require paying a monthly fee

**Solution**

I considered several possible solutions to this problem. 	First, I thought about creating a full-service website that would both feature the web map and also have a component that would allow students to log in and publish projects. I decided against this course of action because the scope of the project was too large, in particular the second component. The alternative was to maintain the current WordPress site but mainly for students to create and publish their projects. I would then develop a second website that would be viewed by the public. It would feature the interactive map and allow users to access the student project information. There are a couple different ways in which the student project information can be viewed from the public-facing website. I discovered that it's possible to export the HTML from the WordPress site to an XML file. I could then create some code, probably using Python, to parse the XML file and extract the HTML for each site. The HTML could then be displayed when the user clicks on a site in the web map. While this approach would potentially allow me to create custom styling for the projects, investigating how to parse the XML file would have required a decent amount of work. I eventually decided to create a website that works in the following way. When a user clicks on a site in the web map, a popup window would open. There would then be a "Read More" option which, which selected, would expand the popup window and load the project WordPress page in an iframe. This solution would satisfy all of the criteria listed above and would also be technically feasible.

**Implementation**

The interactive website was created using the [ArcGIS API for JavaScript](https://developers.arcgis.com/javascript/3/). The API consists of a set of tools for creating web maps, adding features such as point or line graphics, and inserting widgets such as zoom buttons.  Other web mapping API's exist (e.g. one for Google Maps), but I decided to use the ArcGIS API mainly it allows for more powerful web applications and also because I have used the API previously in a different context.

The creation of the actual web map, including the ability to pan and zoom, is handled by the API. My task was, first of all, to load summary data to be displayed in the popup window when a user clicks on a site. I did this by creating a text file that contained an array of JSON objects, one for each project (see below). Each object stored information on the project's title, author, date, site location (lat/lon), short summary, link to the WordPress webpage, and link to an image for displaying in the popup. When the web application loads, this data is read and stored in memory.

```javascript
  {
    "projectName": "Fracking in Dimock, PA",
    "author": "",
    "university": "Colgate",
    "year": "2014",
    "lat": "41.7406",
    "lon": "-75.9231",
    "tags": ["Mining", "Natural Gas", "Fossil Fuels"],
    "introText": "Recently, natural gas has been hailed as the new, “clean” fossil fuel and hydraulic fracturing (fracking) as the 21st century technology making it possible. This project considers Dimock, Pennsylvania, where 130 violations were filed against Cabot Oil and Gas, which leased land from locals to extract shale gas.",
    "introImageLink": "http://www.frackcheckwv.net/wp-content/uploads/2013/02/Marcellus-shale-EIA.jpg",
    "link": "https://pgeproject.wordpress.com/2017/06/21/fracking-in-dimock-pa/"
  }
```

My second task was to create a custom drop-down menu for filtering the sites based on what category they fall under (e.g. "fossil fuels" or "mining"). When the user clicks on an option, only those sites with that description tag are displayed and the rest are hidden. Each menu option also displays how many sites have that tag. This widget is not included in the API, and I had to create custom JavaScript that interfaces with the map's point graphics when the user clicks a new option. I also decided to create a drop down menu from scratch using CSS and a minimal amount of JavaScript. While I could have created a drop down menu using an HTML select tag, I also wanted flexibility in styling the menu. With HTML select elements, this is typically done by the browser and developers have fewer options when it comes to styling.

![Drop-down Menu](/img/PGE_Case_Study/IMG3.png)

In addition to the drop-down menu, I also created a custom popup window for the web map that replaced the one provided by the API. My client felt strongly that all the summary information for a site should be displayed in the popup window. There should not be any scrolling in the popup window! (Dr. Thoyre feels very strongly about this.) This meant that the popup window would have to have a variable height. Unfortunately, the popup window tool provided in the API did not allow this. Therefore, I created custom popup window that had more flexibility.

![Popup Window](/img/PGE_Case_Study/IMG4.png)

![Popup Window 2](/img/PGE_Case_Study/IMG5.png)

This was significantly more work that the creating the drop-down menu. I had to create a group of elements in a container that would be hidden when the window was not open. When a user clicks on a site, the window would be displayed and information specific to that site would be displayed in the window. Perhaps the most challenging part of developing the popup window was coming up with an algorithm for placing the window on the screen. The user could click on a point anywhere on the map and the popup window would have to open in a location that was both completely on the map and which did not cover up the original point that was clicked on. I also wanted, if possible, to place the window in a location that was close as possible to the center of screen. My strategy for developing such an algorithm was the following: First, based on the screen coordinates of the point feature, I calculated how much empty space there is to the left, right, top, and bottom of the point. Then based on the height and width of the window (variable for each site), I then calculated how much empty space would be left if the window was placed in that relative location. If there was not enough room in one direction to place the window, then this possibility was discarded. The direction with the most empty space remaining was selected as the primary direction. For example, a point in the top left corner would likely have the most empty space to the right of the point, and this would likely be the primary direction. Figuring out the primary direction only pins down the location of the window along one axis, either the x- or the y-. The specific location along the other axis was selected in such a way as to center the window as much as possible.

The popup window features an arrow that extends from the main window to the point feature. Creating an element that looks like a triangle was actually a pretty challenging part of this process. I considered several ways to do this. First, it's possible to create a square div element and then rotate it by 45 degrees and then hide half of it in some way. Second, it's possible to create a div with no width or height but which has a colored border in one direction and a transparent border in the other three directions. A third alternative is to create an SVG feature with a triangle graphic in it. Each approach has its own strengths and weaknesses. Eventually I decided to use the second approach. The main downside of this is that it limits the styling options. For instance, it's not possible to do a box shadow on a div that is empty other than having border in one direction.

The new site was finished and deployed on netlify.com. The url is [pgeproject.netlify.com](pgeproject.netlify.com). Source code for the project can be found on my [my GitHub site]https://github.com/BenRiegel/pge.

![Screen Shot of Completed Website](/img/PGE_Case_Study/IMG2.png)

**Lessons Learned**

Overall, the redesign was successful. It accomplished all the main goals that I set out to accomplish. From a coding perspective, the project was also valuable in many ways.  I become much more familiar with web maps in general and the ArcGIS API in particular.  Moreover, it was exciting to create custom tools to replace aspects of the API. In the future, I would like to create my own web mapping tools that do not require any external JavaScript.

Also, this project was valuable in that I forced to tack some architectural issues during the development process. This was something that I struggled with for a while but I gained a solid basic conceptual understanding of the model-view-controller framework and the importance of separating the business data code from the user interface code.

Finally, I learned a lot about the development process of designing a web application. For me, this is not a linear process but rather one in which I am constantly going back and making revisions to make the code better. This process can be frustrating at times because I often had to undo many hours worth of code. However, I also learned that the feeling of finding a better solution to a problem can be really rewarding and satisfying.
