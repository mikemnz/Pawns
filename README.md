Beginner's Guide to Using SignalR (WebSockets) via .NET Core

raddevus
Rate me:





5.00/5 (7 votes)
12 May 2024
CPOL
27 min read
 6.6K    221    20    5
Learn to use SignalR to asynchronously update all web clients in real-time using SignalR broadcasts.
The entire point of using a technology like SignalR is updating remote clients in real-time. I wanted a simple example that would allow readers to see how it might work, by making a helpful example and introduction to SignalR. This article covers creating a .NET Core Web App using Visual Studio Code and the dotnet command line to use SignalR in your project. We'll add new HTML page, JavaScript, references (using nuget), and generating a SignalR Hub class that will send the data to the other browsers.

Is your email address OK? You are signed up for our newsletters or notifications but your email address is either unconfirmed, or has not been reconfirmed in a long time. Please click here to have a confirmation email sent so we can start sending you newsletters again.
Download pawns_v006.zip - 59.3 KB
Download pawns_v005.zip - 58.8 KB
Download pawns_v004.zip - 58.1 KB
Download pawns_v003.zip - 57.4 KB
Download pawns_v002.zip - 56.3 KB
Download pawns_v001.zip - 32.8 KB
Introduction
Note
This is a complete update to the article I wrote back in May, 2017 (7 years ago!!).  You can see that one at: Beginner's Guide to Using SignalR via ASP.NET[^]

Using SignalR is even easier that it was back then, but I really liked the original concept of moving a pawn locally and having others (across the Internet) seeing it move in their browser so I've kept the original sample and updated it.

Eurkea!: Extra Content
As I was about to publish this article with the basic samples, I had a eureka moment. 

This article was going to end with sample v005 which simply moves the pawn around on everyone else's screen.  However, just as I was about to publish I thought of a much more cool example.  Move the pawn only on a private partner's screen.

Move Pawn On Private Partner's Screen
I thought of a way to move the pawn only on a private partner's screen and the code is included in the v006 sample, so even if you don't read the rest of the article, read that last section and examine that last sample, because it is one of those ideas that has made me think of a lot of other ideas.

Visual Studio Code
The other big difference in this article is that the previous one used Visual Studio 2017 running on Win10, and this one will use Visual Studio Code and walk you through the dotnet command line to do all the work. 

99% of my work is now done from my desktop running Ubuntu 22.04.4 LTS or my Mac Pro M3 laptop (currently running MacOS Sonoma) but you can do all this "Micrososoft" development on any platform now and it's really great.

Main Article Content
I've been wondering about how easy it might be to use SignalR so I decided to work through one of Microsoft's quick intro tutorials.  While doing that I ran into some challenges so I wanted to take a shot at explaining how SignalR works myself.

The Main Point: Updating Remote Clients In Real-Time
The entire point of using a technology like SignalR is updating remote clients in real-time.  I wanted a simple example that would allow readers to see how it might work.  A while back I used Firebase (more on this in a moment) to create a simple example which :

allows the user to grab and move a pawn on an HTML5 Canvas
updates all clients so remote users can see the pawn moving in their browser
Here's an Animated GIF of it in action. (but I think you have to click the image to see the animation).
Image 1

You Can Try It Right Now
Just open up two different browser windows and point them at my InterServer.net hosted site: 

pawns example[^]

Now, move one of the pawns in either of the browser windows and the corresponding pawn will move in the other browser window.

Now that you've seen it work, let's set up our SignalR project in Visual Studio and get started.

Visual Studio Code Web Project
As I stated earlier, I learned SignalR by working through one of Microsoft's tutorials.  You can see that tutorial at: Get started with ASP.NET Core SignalR | Microsoft Learn[^] However, that article presents a couple of challenges.

It guides you to install a new package manager tool (LibMan) that isn't entirely necessary. (My article shows you how to use the dotnet command line and built-in nuget to do the same work.)
We'll manually download the SignalR JS code and place it in the proper location.  The article has LibMan do that for us.
Let's open a terminal (console, command line interface (CLI), text user interface (TUI), etc.) and create our project from there.  This will work on any platform (MacOS, Linux, Windows) as long as you have .NET Core properly installed on your computer.

 

Create Pawns Project
Open up a terminal on your machine and go to a folder where you'd like to create a new project folder which will contain all of the files for our project and run the following command:

$ dotnet new web -o pawns
Image 2

When you hit <ENTER> your project folder and the initial files needed for the project will be created.

Now you can CD (change directory) into that project folder:

$ cd pawns 
Once you are in that directory you can take a look at the basic files which were created for you (either c:\dir on windows or $ ls -al on linux / MacOS).

After that you can try running this initial project with the following command:

$ dotnet run

You'll see that dotnet starts up a web server for you and you should be able to CTRL-click the URL shown and it'll load this (very) basic page in your browser.

Image 3

Here's the extremely basic web page you'll see:

Image 4

Actually, if you look at the source, it's not really even a web page.  It's just the text "Hello World!". It doesn't even contain any HTML.

That's fine for now.  We just wanted to make sure dotnet runs properly on your machine.  Let's go and add the SignalR library to our project so we have what we need for this project to move forward.

Adding SignalR Via Nuget (dotnet command line)
Initial Project, Easy Downloadable From This Article
Note: Don't worry if any of this gives you a problem, because I'm going to zip up this first version of our project so you can just download the project after these first steps (including adding the SignalR component & SignalR JS file) in the first attached zip file on this article.

To get the SignalR component using dotnet I simply searched the web for: nuget signalr .net core

I saw a few links and I saw two different ones from nuget.

Image 5You may think it is the first one listed.  However, that one is the older version that should be used with .NET Framework (that's the older version of .NET before .NET Core).
If you examine the 3rd link down (showing 8.0.4) you will see at the Nuget site that it is the one you should use for .NET Core projects.

When you visit the Nuget site it will provide you with the dotnet command that we will use to add the component to our project:

$ dotnet add package Microsoft.AspNetCore.SignalR.Client --version 8.0.4
Go ahead and run that from your terminal inside your project folder.  If you're still running the web server from the previous step, then type CTRL-C to kill the web server before you run the command.

You should see a bit of output in your terminal.  To make sure it all went properly, you can get a quick view of the pawns.csproj file by typing or catting it to your terminal.

$ cat pawns.csproj
c:\<your-path>\type pawns.csproj
When you do that, you should see a reference to the newly added package within the csproj XML.

Image 6

One More Step For SignalR
There's just one more thing we need to do so that we have everything we need to use SignalR.  

We need to download the SignalR JavaScript.

SignalR JavaScript Provided By Microsoft
That source is located at: 

https://unpkg.com/@microsoft/signalr@latest/dist/browser/signalr.js[^]

JS Saved At A Specific Location
We need to place that JS source file at a very specific location in our project.  

However, that location doesn't currently exist in our project.  
The location is: wwwroot/js 

That location will be inside our main pawns project directory.   Let's go to the terminal and create this directory structure with one command:

$ mkdir -p wwwroot/js
That will create the initial wwwroot directory and all of the sub-directories beneath it.

A very similar command works on Windows (just used different directory separator characters):

c:\<your-path>\>mkdir -p wwwroot\js
Get The JavaScript Source
To get the source into our project we need to follow these steps:

Click the link and open it in a new browser tab or window.
right-click the content in the new tab / window
choose Save As...
Image 7

Save the file in the (Microsoft required) project directory we created earlier: wwwroot/js

We'll be saving another file in this js directory later on, since all of our JavaScript will run from this directory.

One More Thing For the Project
Now, let's make a few changes to the Program.cs so that it no longer just returns "Hello World!" and instead returns the index.htm file.

Open up the existing Program.cs and replace all of the cod with the following:

C#
using Microsoft.AspNetCore.Http.HttpResults;
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddRazorPages();
builder.Services.AddSignalR();
var app = builder.Build();

// will automatically redirect to the wwwroot/index.htm 
// when you load http://localhost:<port>
app.MapGet("/", () => Results.Redirect("/index.htm"));

app.UseStaticFiles();
app.UseDefaultFiles();
app.Run();
Now, when you run, the app will automatically redirect to the wwwroot/index.htm file which will be our the content of our SPA (single page app).

Let's just make sure everything builds.

$ dotnet build
Now that we have the project which includes all of our dependencies let's take a snapshot by saving it to a zip file which you can download to insure you have everything set up properly.

Get The Source - v001
If you have any troubles with the project creation you can grab the v001 zip file at the top of this article and unzip it and you'll be all set.  Of course, I deleted the downloaded nuget packages so the zip is just the source, but when you build ($ dotnet build) or run ($ dotnet run) the project the packages will be restored automagically.

Once you've done all the previous steps, you have everything you need to use SignalR in your project.

Project Doesn't Run Properly Yet
Also, if you run the project now and load it in your browser you will discover that the browser tells you that the index.htm file is missing.   That's ok, because in the next step we are going to resolve that issue.

Now, let's add some code.  

Add New HTML Page
In the Microsoft tutorial the first thing the author tells you to do is add a new class that implements the SignalR behavior.  However, I like to build the thing as we go to see how it all works together.  So first, we'll set up the HTML page that we'll use as our app's user interface.

Let's start by adding a new (main) HTML file to our project named index.htm.

As I said, I'm using Visual Studio Code (VSC) so go ahead and open the project folder in VSC so we can add the file.

Select the wwwroot folder in the project tree on the left of VSC and then add the file.

When you add the file, VSC will add an empty HTML file which will only display a blank page to the user.  Of course, for our purposes we want to display three different colored pawns on a blue grid background.

Let's alter the blank index.htm by pasting in the following code now:

HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>pawns</title>

</head>
<style>
    body, html {
        margin: 0;
        padding: 0;
    }
</style>
<body>
    <img style="visibility:hidden;display:none;" src="assets/redBlueGreenPawn.png" id="allPawns" />
    <canvas id="gamescreen">You're browser does not support HTML5.</canvas>
    
</body>
</html>
Normally, I'd keep the CSS (Cascading Style Sheet) data in a separate file but I'm attempting to simplify this tutorial and there is really just the one style which removes any margin or padding so I've added into our index.htm.

Image Asset
Next, you see that I reference an image that you don't have.  You can see it here and download it, if you like:

Image 8

You can see that even though there are three distinct pawns you can move around, the image itself is actually one PNG file.  That's because of how you can easily reference portions of an image as separate HTML5 Canvas objects with the Canvas API.

Adding Assets Folder
I will also add a new folder to the project named wwwroot\assets and I will place the redGreenBluePawn.png in the folder so it is a part of the project.  You'll get that file in the next (v002) download. 

Finally, we set up an HTML Canvas element where our grid and pawns will be drawn.

Real Work, Done In JavaScript
The real work is done by JavaScript, however.  I know that many of you feel about that, but it is the way of the Web so get used to it. 🤓 

We will keep our custom JavaScript for the project separate so I'm going to create a new Javascript file named pawns.js and add it to the wwwroot\js folder.  I'll show it to you and let you work out how to do this same thing.   Take a close look at where I add the pawns.js reference in my HTM file.  It's a bit important because that code references the Canvas and we are insuring that the Canvas element is already loaded.

SignalR Reference
Also notice the line that I've bolded in the HTML example below.  This part was a lot more complicated in the past but now you just need to add a script element to reference the signalr.js file at the top of the page (in the head section) so it loads before the rest of the page.

Pawns.js Reference
Next, at the bottom of the page, we make sure we have a reference to our pawns.js file so that it can interact with the Canvas element of our page.

HTML
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8" />
    <title>pawns</title>
    <script src="js/signalr.js"></script>
</head>
<style>
    body, html {
        margin: 0;
        padding: 0;
    }
</style>
<body>
    <img style="visibility:hidden;display:none;" src="assets/redBlueGreenPawn.png" id="allPawns" />
    <canvas id="gamescreen">You're browser does not support HTML5.</canvas>
    <script src="js/pawns.js"></script>
</body>
</html>
Get the Code v002
You can also download v002 of the code at the top of this article and you'll be up to date at this point and ready to write the code to draw the pawns and grid.

The code will build and run, but of course the displayed page will be completely blank.  Let's take a look at how to draw the pawns and grid.

Drawing Pawns & Grid
I'm going to race through most of this, since it is only indirectly a part of what I want to talk about.  

First of all I need to set up some variables I will need to use and set up the load event which will fire once the browser has loaded the target page (index.htm) and all of its associated resources (images and JavaScript files).

JavaScript
Shrink ▲   
// pawns.js

var ctx = null;
var theCanvas = null;
var firebaseTokenRef = null;

window.addEventListener("load", initApp);
var mouseIsCaptured = false;
var LINES = 20;
var lineInterval = 0;

var allTokens = [];

// hoverToken -- token being hovered over with mouse
var hoverToken = null;
var pawnR = null;

//$.on("mousemove", mouseMove

function token(userToken){

    this.size = userToken.size;
    this.imgSourceX = userToken.imgSourceX;
    this.imgSourceY = userToken.imgSourceY;
    this.imgSourceSize = userToken.imgSourceSize;
    this.imgIdTag = userToken.imgIdTag;
    this.gridLocation = userToken.gridLocation;
}

function gridlocation(value){
    this.x = value.x;
    this.y = value.y
}
I also add a couple of types I've created (token and gridLocation) to make it easier to keep track of things.  You'll see how their used a bit later.

When the browser loads everything the initApp() function will run.  Let's take a look at it.

JavaScript
function initApp()
{
    theCanvas = document.getElementById("gamescreen");
    ctx = theCanvas.getContext("2d");
    
    ctx.canvas.height  = 650;
    ctx.canvas.width = ctx.canvas.height;

    initBoard();
}
We begin to set up the Canvas where we'll draw the grid.

Next, we call our custom code in initBoard().

JavaScript
function initBoard(){
    lineInterval = Math.floor(ctx.canvas.width / LINES);
    console.log(lineInterval);
    initTokens();
}
I calculate the line intervals using the canvas width and the distance between the lines.  After that I call initTokens() to get ready to draw the pawns (tokens).

JavaScript
Shrink ▲   
function initTokens(){
        
    if (allTokens.length == 0)
    {
        allTokens = [];

        var currentToken =null;
        // add 3 pawns
            for (var i = 0; i < 3;i++)
            {
                currentToken = new token({
                        size:lineInterval,
                        imgSourceX:i*128,
                        imgSourceY:0*128,
                        imgSourceSize:128,
                        imgIdTag:'allPawns',
                        gridLocation: new gridlocation({x:i*lineInterval,y:3*lineInterval})
                    });
                    allTokens.push(currentToken);    
            }
        console.log(allTokens);
    }
    draw();
}
Here, we just make sure the allTokens array is initialized to empty.  Next we add all the tokens to the array while sourcing the correct portion of our PNG image.  It's easy to do with some math since each one is 128 pixels wide.

Finally, we call our draw() function which will draw all of our graphics to our Canvas element.

JavaScript
Shrink ▲   
function draw() {

    ctx.globalAlpha = 1;

    // fill the canvas background with white
    ctx.fillStyle = "white";
    ctx.fillRect(0, 0, ctx.canvas.height, ctx.canvas.width);

    // draw the blue grid background
    for (var lineCount = 0; lineCount < LINES; lineCount++) {
        ctx.fillStyle = "blue";
        ctx.fillRect(0, lineInterval * (lineCount + 1), ctx.canvas.width, 2);
        ctx.fillRect(lineInterval * (lineCount + 1), 0, 2, ctx.canvas.width);
    }
    // draw each token it its current location
    for (var tokenCount = 0; tokenCount < allTokens.length; tokenCount++) {

        drawClippedAsset(
            allTokens[tokenCount].imgSourceX,
            allTokens[tokenCount].imgSourceY,
            allTokens[tokenCount].imgSourceSize,
            allTokens[tokenCount].imgSourceSize,
            allTokens[tokenCount].gridLocation.x,
            allTokens[tokenCount].gridLocation.y,
            allTokens[tokenCount].size,
            allTokens[tokenCount].size,
            allTokens[tokenCount].imgIdTag
        );
    }
    // if the mouse is hovering over the location of a token, show yellow highlight
    if (hoverToken !== null) {
        ctx.fillStyle = "yellow";
        ctx.globalAlpha = .5
        ctx.fillRect(hoverToken.gridLocation.x, hoverToken.gridLocation.y, 
                  hoverToken.size, hoverToken.size);
        ctx.globalAlpha = 1;

        drawClippedAsset(
            hoverToken.imgSourceX,
            hoverToken.imgSourceY,
            hoverToken.imgSourceSize,
            hoverToken.imgSourceSize,
            hoverToken.gridLocation.x,
            hoverToken.gridLocation.y,
            hoverToken.size,
            hoverToken.size,
            hoverToken.imgIdTag
        );
    }
}
Basically, all we do is :

iterate through the allTokens array
draw token in its present location, according to its gridLocation value
If the token is being hovered over, then draw a light yellow shadow around it so the user can tell she can grab the token.
The draw() function does use another helper method named drawClippedAsset() which allows me to easily reference the tokens in our image.  It looks like the following:

JavaScript
function drawClippedAsset(sx,sy,swidth,sheight,x,y,w,h,imageId)
{
    var img = document.getElementById(imageId);
    if (img != null)
    {
        ctx.drawImage(img,sx,sy,swidth,sheight,x,y,w,h);
    }
    else
    {
        console.log("couldn't get element");
    }
}
Once you add all of this code you will finally have the background grid drawn and the pawns drawn at their initial location.  

initial view

Get the Code : v003
If you get the code v003 at the top of this article you will be up to date so you can continue along with the article.

At this point, we have drawn our grid and the pawns on top of the grid.  However, we cannot interact with the app yet because we haven't added the code which allows the user to grab and move the pawns around on the grid.

Image 10

Where Are We?
We're now very close to getting things going with the SignalR, but first we have to add the local code which will allow us to grab one of the pawns and move it around. Once we do that, we will allow the updated values to be broadcast to other clients.

Let's add the code to do that work now.  Yes, it's still more JavaScript.

We need some event handlers which will do some work when the mouse is clicked (mousedown) and when the mouse is moved.  

Back in our initApp() function we want to add the event listeners for those two events.  Now initApp() will look like the folllowing: (I've added the bolded lines)

JavaScript
function initApp()
{
    theCanvas = document.getElementById("gamescreen");
    ctx = theCanvas.getContext("2d");
    
    ctx.canvas.height  = 650;
    ctx.canvas.width = ctx.canvas.height;

    window.addEventListener("mousemove", handleMouseMove);
    window.addEventListener("mousedown", mouseDownHandler);

    initBoard();
}
This is the straight-up pure JavaScript way to add those listeners.  You can do it with jQuery too, but it's easy enough to do it like this.

Now, we've registered the event listeners, we need to implement the methods handleMouseMove() and mousedownHandler().

JavaScript
Shrink ▲   
function handleMouseMove(e)
{
    if (mouseIsCaptured)
    {    
        if (hoverItem.isMoving)
        {
        var tempx = e.clientX - hoverItem.offSetX;
        var tempy = e.clientY - hoverItem.offSetY;
        hoverItem.gridLocation.x = tempx;
        hoverItem.gridLocation.y = tempy;
         if (tempx < 0)
          {
            hoverItem.gridLocation.x = 0;
          }
          if (tempx + lineInterval > 650)
          {
            hoverItem.gridLocation.x = 650 - lineInterval;
          }
          if (tempy < 0)
          { 
            hoverItem.gridLocation.y = 0;
          }
          if (lineInterval + tempy > 650)
          {
            hoverItem.gridLocation.y = 650 - lineInterval;
          }
      
        allTokens[hoverItem.idx]=hoverItem;
        pawnR.server.send(hoverItem.gridLocation.x, hoverItem.gridLocation.y,hoverItem.idx);
        }
        draw();
    }
    // otherwise user is just moving mouse / highlight tokens
    else
    {
        hoverToken = hitTestHoverItem({x:e.clientX,y:e.clientY}, allTokens);
        draw();
    }
}
Any time the mouse is moved this function will run.  I could've been more specific and said, only run when the mouse is moved and the mouse is over the Canvas element, but this will work.

The first thing I do is check if mouseIsCaptured is true.  That value gets set when the mouseDownHandler fires (user clicks) and the method determines that the user is above one of the three pawns.  That work is done in the mouseDownHandler so let's take a look.

JavaScript
function mouseDownHandler(event) {

    var currentPoint = getMousePos(event);
    
    for (var tokenCount = allTokens.length - 1; tokenCount >= 0; tokenCount--) {
        if (hitTest(currentPoint, allTokens[tokenCount])) {
            currentToken = allTokens[tokenCount];
            // the offset value is the diff. between the place inside the token
            // where the user clicked and the token's xy origin.
            currentToken.offSetX = currentPoint.x - currentToken.gridLocation.x;
            currentToken.offSetY = currentPoint.y - currentToken.gridLocation.y;
            currentToken.isMoving = true;
            currentToken.idx = tokenCount;
            hoverItem = currentToken;
            console.log("b.x : " + currentToken.gridLocation.x + "  b.y : "
                  + currentToken.gridLocation.y);
            mouseIsCaptured = true;
            window.addEventListener("mouseup", mouseUpHandler);
            break;
        }
    }
}
In the mouseDownHandler we simply iterate through the allTokens array and check their gridLocation.  if the we determine that the mouse pointer is within that area we set the mouseIsCaptured boolean to true.

I've broken out the code that checks to see if the mouse location is within any one of the tokens location and placed that code in a a method called hitTest().

JavaScript
function hitTest(mouseLocation, hitTestObject)
{
  var testObjXmax = hitTestObject.gridLocation.x + hitTestObject.size;
  var testObjYmax = hitTestObject.gridLocation.y + hitTestObject.size;
  if ( ((mouseLocation.x >= hitTestObject.gridLocation.x) && (mouseLocation.x <= testObjXmax)) && 
    ((mouseLocation.y >= hitTestObject.gridLocation.y) && (mouseLocation.y <= testObjYmax)))
  {
    return true;
  }
  return false;
}
You can see we simply send in the mouseLocation and the object you want to test and the function iterates through and determines if it is a hit or not and returns true or false.  It makes it all very easy to use.

There are a couple other helper methods in there which will decide which pawn is being grabbed and which will continually call the draw() method so that the pawn is drawn as the mouse is moved.  At this point the user can grab any one of the pawns and move it around on the screen.

Get Code: User Can Grab & Drag Any Pawn v004
Download the v004 zip file at the top of this article and you can try dragging the pawns around.

 

Finally, We Are At the Main Challenge
We are finally ready to attempt to resolve the main challenge.  

What we want to do is :

Update every client that happens to be viewing our web page so that when one of the pawns is moved all other clients will see the pawn move.
To get this going the first thing we have to do is create a C# class that represents a SignalR Hub.

This is the code that runs on the server side and will be the host that forwards data that is sent to it (when the user moves a Pawn on the screen).

Add the PawnHub
Create a New Directory (named Hubs)
Create a New File (named PawnHub.cs) in the Hubs directory
Add the following code:
 

C#
using Microsoft.AspNetCore.SignalR;

namespace Pawns.Hubs
{    public class PawnHub : Hub
    {
        public async Task SendData( int x, int y, int idx){
            var drawData = new {x=x,y=y,idx=idx};
            await Clients.Others.SendAsync("ReceiveData", drawData );
        }
    }
}
As you can see our PawnHub is a SignalR Hub which wants us to implement the SendData method.

Client Code (JavaScript) Will Invoke This Method
Further along we will see that the JavaScript code will invoke this method.  When the JavaScript invokes the method it will also pass three values to SendData:

x - the x (screen) location of the selected pawn
y - the y (screen) location of the selected pawn
idx - the zero-based index (0,1,2) of the pawn image which is currently being moved by a user.
Hub Receives Data, Sends Out to Others
When the Hub receives the data, I take two steps:

Wrap the data in a simple object (drawData)
Pass the object (drawData) to all other clients so they can update the location of the moving pawn in real time.
When the line of code is called to send the data (next line of code shown below) then we also pass a string representing the name of the client side method which will be called (as the first parameter to the SendAsync function.  The following line of code calls the client-side function named ReceiveData and sends it the current drawData.

C#
await Clients.Others.SendAsync("ReceiveData", drawData );
Make Changes To Program.cs
Now, we just need to add one line of code to our Program.cs file and add a new using statement to it (to reference a class that will be used in the new line of code).

Add a using statement to the top of the file - 
Add the new line of code which registers the path to the pawnHub class which will be used to send the data to clients.
Here's what the entire Program.cs will look like now:

C#
using Microsoft.AspNetCore.Http.HttpResults;
using Pawns.Hubs;
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddRazorPages();
builder.Services.AddSignalR();
var app = builder.Build();

app.MapGet("/", () => Results.Redirect("/index.htm"));

app.UseStaticFiles();
app.UseDefaultFiles();
app.MapHub<Pawns.Hubs.PawnHub>("/pawnHub");
app.Run();
That wraps up all the work we need to do on the C# side.  Now let's go add our code to the JavaScript side in the pawns.js file.

Set Up The Connection to pawnHub
The first line of code that we add to pawns.js will be added as the first line of code in the file.

JavaScript
var connection = new signalR.HubConnectionBuilder().withUrl("/pawnHub").build();
This makes the connection to the pawnHub that we mapped in our Program.cs.

This ties the JavaScript client to the C# server so that when we call methods on the connection then the data is sent to the server and broadcast to all clients.

Initialize the JavaScript Methods
Now, we just need to make sure our JS methods are initialized properly and we will do that right after the connection above is set up.  We will do that in our initApp() JavaScript method.

Much of the following code was already there, since it is the code that sets up the HTML Canvas and gets all the drawing components set up to draw the grid and pawns.  
Now we add the lines of code to register a JavaScript function to do work when data is received.

In this case we tell the Hub Connection that when the "ReceiveData" function is called that we will do work with the handleData() function.  After that, we define what the handleData() method does.

Finally, we have to make sure that the Hub is started and we do that with the connection.start() function.  This is all code that just runs when the web app starts.

C#
Shrink ▲   
function initApp() {
    theCanvas = document.getElementById("gamescreen");
    ctx = theCanvas.getContext("2d");

    ctx.canvas.height = 650;
    ctx.canvas.width = ctx.canvas.height;
    window.addEventListener("mousemove", handleMouseMove);
    window.addEventListener("mousedown", mouseDownHandler);

    connection.on("ReceiveData", handleData);

    function handleData(drawData) {
        allTokens[drawData.idx].gridLocation.x = drawData.x;
        allTokens[drawData.idx].gridLocation.y = drawData.y;
        draw();
    };

    connection.start().then(function () {
        console.log("Hub is started.");
    }).catch(function (err) {
        return console.error(err.toString());
    });

    initBoard();
}
Everything is set up now, and the client is ready to receive data and the server Hub is ready to re-broadcast that data to all clients.  However, we don't have any action that is attempting to send any data.

Just One More Thing To Do
We want to call the Hub SendData() method so that any time a pawn is moved on the screen it will broadcast the data out to all other clients and move the pawn on all other client screens too.

To do that we just need to add one (long) line of code to the handleMouseMove() method in pawns.js.  

It again, uses the connection object, but this time it calls the SendData() method while passing the x, y location of the pawn and the idx value representing the pawn that is being moved.

The added line of code looks like the following:

JavaScript
connection.invoke("SendData", hoverItem.gridLocation.x, 
                       hoverItem.gridLocation.y, 
                       hoverItem.idx)
       .catch(function (error){
           return console.error(error.toString());
       });
The hoverItem is the currently selected pawn which contains its x, y and idx values and you can see that we pass all those values to the SendData() method.

Complete Solution : v005 
Get the complete solution in v005 of the download at the top of this article.

Build and run and then open two separate browser windows pointing to your same URL and move a pawn.  It will move in the other browser window too.

Refreshing Page Resets Pawn Locations
Also, if you refresh the page the pawns always move back to their original locations. That's because I did not do any persistence of those values to a data store.   

However, it will not reset the positiions of remote pawns because the SendData() method is only invoked on client-side mouse move.  There are ways to solve this by saving the data to a database on the server, but that's beyond the scope of this article.

Move Pawn On Private Partner's Screen: v006  Must See
As we saw, the v005 example sends the data to anyone (and everyone) who has simply loaded the page into their browser.

Share A Private Key?
I started thinking about how I might share data only with a "private partner": i.e. One or more people I've shared a secret key with so only they can see how I move the pawns (and only I can see how they move them).

What the v006 Sample Will Do
The sample will still work normally if you don't provide the key -- so everyone will be in a "global group" who can view anyone else moving the pawns.  However, it will add the abilty to generate or set a UUID (Universally Unique ID) so that only the users who share that ID will be able to see each others pawn movements.

We one new field (text input) to hold the UUID and a button to generate it, so the new app (v006) will look like the following:

Image 11

When the user clicks the button a random UUID will be generated.  That's a very basic method that allows us to do that and I'll let you examine it in the source code.  It's in pawns.js and the function is called uuidv4().

How the UUID Is Used
Once a partner has generated a UUID then she must share it with her partner so the partner can paste it into her web browser and click the [Gen/Set UUID] button. 

How The Gen/Set UUID Button Works
Here's how that button works.   When the UUID text box is empty the code will generate a random UUID and set the currentUuid variable to the value.  However, when the field is set to a value then the currentUuid variable is simply set to the value.

How is the UUID Value Used?
If the value is generated or set, the app will send the UUID with the rest of the pawn data (x,y,idx).

C# PawnHub Changes
In this new example, I've changed the PawnHub code to expect and additional parameter.  Here's what that code looks like now:

C#
public class PawnHub : Hub
    {
        public async Task SendData( int x, int y, int idx, string uuid){
            var drawData = new {x=x,y=y,idx=idx,uuid=uuid};
            await Clients.Others.SendAsync("ReceiveData", drawData );
        }
    }
As you can see, the SendData() function now takes an additional String value named uuid.

I also now wrap that new item up into the drawData object that we pass across when we call the SendAsync method.

This means that now we can use that value to filter out the data that is received by the browser so only the user(s) who have the secret key can get the data and see the pawns move. 

JavaScript Changes
Here's what the changes to the JavaScript side (in pawns.js) look like.

In the handleMouseMove() function we now need to send the UUID when the currentUuid has been set or generated.

C#
if (currentUuid == ""){
    connection.invoke("SendData", hoverItem.gridLocation.x, 
                        hoverItem.gridLocation.y, hoverItem.idx, "")
        .catch(function (error){
            return console.error(error.toString());
         });
}
else{
  connection.invoke("SendData", hoverItem.gridLocation.x, 
                      hoverItem.gridLocation.y, hoverItem.idx, currentUuid)
     .catch(function (error){
         return console.error(error.toString());
      });
}
Code Explained
If the currentUuid is not set then we just send the data with a blank UUID which will be ignored and any user viewing the site will be able  to see the pawns move and will be able to move pawns that everyone sees.

However, if the currentUuid is set then only the users who have the UUID set on their side will be able to see the pawns moving and move the pawns on that "secret channel".

Here's the other part, where the browser receives the data and filters it out.

How the (Not Entirely) Secret Channel Works
It all happens up in the handleData() function.

When the app receives data it will check for the existence of the uuid and see if it matches the currentUuid.  If it does then the data will be used.  However, if it doesn't then the pawns will not move.

C#
function handleData(drawData) {
        if (currentUuid == ""){
            allTokens[drawData.idx].gridLocation.x = drawData.x;
            allTokens[drawData.idx].gridLocation.y = drawData.y;
            draw();
        }
        else{
            if (currentUuid == drawData.uuid){
                allTokens[drawData.idx].gridLocation.x = drawData.x;
                allTokens[drawData.idx].gridLocation.y = drawData.y;
                draw();
            }
        }
    };
Not Entirely Secret UUID
As I thought of the code above I started thinking about how the data is sent.  That led me to discover that:

the data is sent to all clients
However, the clients who don't have the UUID do not use the data.
To be able to see this I added the following line of code as the first line in the handleData() function above.

C#
console.log(`${drawData.x} : uuid: ${drawData.uuid}`);
That line of code is fired no matter which client moves a pawn, whether they have set a UUID or not.  That means that data is sent to all clients who are pointed at the main URL of the app.  Of course if the sender is using a UUID and the receiver is not then their pawns won't move.

Additionally, I don't believe someone would be able to get that data without altering the JavaScript code directly.  

An Interesting Thing To Ponder
This is very interesting, because it gives us insight into how SignalR (WebSockets) work.  The data is sent to all clients, so you have to consider that when you're sending data using 
SignalR.  

Consider HTTPS Security
However, it is additionally interesting because in the case where the app is deployed to a server which includes an HTTPS connection (as my production one does at https://newlibre.com/pawns.index.htm) then hackers cannot grab the data since it is protected (encrypted) by the HTTPS certificate.  

Could A Hacker Listen In?
But, since all the data is sent to every client, is there are way for the hacker to "listen in" on data that is not intended for him?  Without hacking the actual web site and the JS behind it, I believe it would be difficult for at least one reason. 

The messages are only sent once and are gone
If You Think of a Way, Let Me Know
If the data isn't captured upon receipt there is no way to "replay" the data.  That means the hacker would have to somehow insert JavaScript which would read the drawData object is received in the browser.  There may be a way to do this.  If you think of it let me know.

Get The v006 Code & Try It Out
Download the code and try it out.  It's pretty cool and begins to stimulate ideas about how we might create a "game session" where players have different states, etc.  I hope this extra sample has excited you as much as it did me.  It was a lot of fun.

Deployment to InterServer.net Web Site
It was actually quite easy to deploy the app to my InterServer.net web site.  

All I had to do was use the following command to build a release version:

$ dotnet publish -r win-x64 -c Release
I had to do that because my web site is hosted on Windows x64.
I then copied the binaries up to a directory and turned that directory into a virtual directory where ASP.NET engine could run.

I had to make sure my config file was set to run the app dll as out-of-process like the following:

XML
<system.webServer>
      <handlers>
        <add name="aspNetCore" path="*" verb="*" modules="AspNetCoreModuleV2" 
               resourceType="Unspecified" />
      </handlers>
      <aspNetCore processPath=".\Pawns.exe" stdoutLogEnabled="false" 
         stdoutLogFile=".\logs\stdout" hostingModel="outofprocess" />
    </system.webServer>
I also had to make a change because my web host is all redirected to HTTPS using a secure cert.

That meant that I had to make sure the pawnHub could get registered even though everything redirects through HTTPS.  I had to change the connection initialization to the following in pawns.js.

JavaScript
var connection = new signalR.HubConnectionBuilder()
     .withUrl("https://newlibre.com/pawns/pawnHub").build();
I hope you found this article a helpful example and introduction to using SignalR.
