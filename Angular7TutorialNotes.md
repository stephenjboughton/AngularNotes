## 3 Create a new (Hello World) App

Create an angular application by using the angular CLI in the VS Code integrated terminal (or just command prompt):

    ng new hello-world

Once you have the new project created, cd into the folder and then type:

    ng serve

This will make a connection to the localhost and keep a live update version of the project in your browser.

Every Angular application is comprised of some higher level parts: 

    1. Every Angular appication has at least 1 module - the root module, which by convention will be called app.module.ts (AppModule) when you use the Angular CLI to start your project
    2. Each module is made up of components and services.  Each component makes up a portion of the screen view.  Each application has a root component which will be standardized as app.component.ts (AppComponent.) 
    3. Each component contains an html template and a class file that help define the view and control the logic.
    4. A service helps control the business logic that your app runs off of.
    5. Modules import and export different classes and views in order to render the overall project view on your screen.

Every Angular application starts out with some files to note:

    1. package.json - contains the dependencies - the libraries that angular needs in order for your application to work. These all get installed inside the node_modules folder within the project hierarchy.
    2. main.ts file - a file inside the src folder that kickstarts the whole project by bootsrapping the AppModule
    3. app.module.ts - a file inside the app folder that boostraps the different components in order to render the view, combining the template of the app.component.ts file and the logic of the app.component.ts class file. Styles and Templates can be set inline (using backticks for multiple lines) inside the metadata or linked to a url where that info resides in a separate file.

## 4 Components

A component is made up of a template file and a class file.

The template uses html to represent the view for the component

The class uses typescript code to represent the data (our model) that is returned by a service and methods. A decorator is used to provide metadata that help Angular recognize the class as a component and tie it together with the selector, styles, and html that are needed to create the view. the @Component decorator is used to define a class as a component and not just something that contains some data or an interface.

## 5 Interpolation

In order to make things in our templates dynamic we will make use of interpolation.  We create a property within our component class file, then reference the property name inside double curly braces in order to get the value of the property to show up dynamically when the template gets rendered in the dom.

Interpolation also allows us to perform some mathematical operations and string concatenations within our template.  For example, given a class property and function such as the ones below, we can do all these things using interpolation:

    public name = "Steve"

    greetUser(){
        return "Hello " + this.name;
    }

    <div>{{2+2}}</div>      <div>{{"Welcome " + name}}      <div>{{name.length}}</div>     <div>{{greetUser()}}</div>
    4                       Welcome Steve                   5                              Welcome Steve

Some things you can't do....assign the result of an expression to a variable, use a global javascript variable such as window.location.url.

## 6 Property Binding

Something to consider about html and the dom - There is a difference between attributes and properties:

    * Attributes are native to and only really exist in html. Once they initialize a property they no longer exist in the DOM and they will not change even if the property is updated.
    * Properties are initialized by attributes but then live in the DOM, and as a result, properties can be changed by the user

The follwing html will bind the html element to our class property dynamically, so that any time the class property changes, the html element property changes as well:

    |   <input [id]="myId" type="text" value="SteveB">

    |   public myId = "testId";

## 7 Class Binding

Class binding allows us to bind classes to properties within our component model so that we can assign dynamically through these properties instead of through the class attribute on an html element.  For instance, if our class has styles like this in the css file or style section of our decorator metada:

    .text-success {
        color: green;
    }

    .text-danger {
        color: red;
    }

    .text-special {
        font-style: italic;
    }

And we define some properties in our component class:

    public successClass = "text-success"
    public hasError = false;
    public isSpecial = true;
    public messageClasses = {
        "text-success": this.hasError,
        "text-danger": !this.hasError,
        "text-special": this.isSpecial;
    }

Then in our template, instead of this:

    <h2 class="text-success">Codevolution</h2>

We can write this:

    <h2 [class]="successClass">Codevolution</h2>

Note that if we tried to use both a class attribute and a property binding, the property binding invalidates the class attribute.  We can also conditionally bind a class by using a boolean property and use the class dot operator based on whether that property is true or false:

    <h2 [class.text-danger]="hasError">Codevolution</h2>

Since hasError is false, it will not apply the red class style. If it were true, it would.

There is also an angular class directive that we can use to bind an object with several class properties:

    <h2 [ngClass]="messageClasses">Codevolution</h2>

When it binds the messageClasses object it will evaluate the condition of each of the properties in the object and then apply the classes they reference appropriately.

## 8 Style Binding

We can bind inline styles to html elements in several ways, the first of which is:

    <h2 [style.color]="'orange'">Style Binding</h2>

Now this doesn't really reference a component property the way the class binding does, but it still allows us to do things dynamically, and even to do it conditionally the way class binding does:

    <h2 [style.color]="hasError ? 'red' : 'green'">Style Binding</h2>

So now depending on what state hasError property is in, we will get a different color. We can also bind the html element style property to a component property as we did with class binding:

    <h2 [style.color]="hightlightColor">Style Binding 2</h2>

This will assign the color property of the element's styling to whatever value assigned to the component property highlightColor.

There is also an ngStyle directive that operates similarly to the ngClass directive, using an object and its properties to assign multiple html element style properties.

    <h2 [ngStyle]="titleStyles">Style Binding<h2>

    public titleStyles = {
        color: "blue"
        fontStyle: "italic"
    }

One thing to remember with this is that property names cannot include hyphens the way css tyles might ordinarily.

## 9 Event Binding

All of these types of binding generally just provide data flow from the component class to the component template, but in order to make a rich user experience we need a mechanism for brining events in the dom to the attention of the component class, which brings us to event binding. We add an event binding to whatever dom element we want to listen for the event on:

    <h2>Welcome {{name}}</h2>
    <button (click)="onClick()">Greet</button>

This will bind a click event to a method within the component class called onclick():

    onClick(){
        console.log('Welcome to Codevolution');
    }

We can also assign properties using an event handler that listens to a dom element:

    public greeting = "";

    onclick(){
        this.greeting = "Welcome to Codevolution";
    }

    <h2>Welcome {{name}}</h2>
    <button (click)="onClick()">Greet</button>
    {{greeting}}

The interpolation of the greeting property will at first show nothing in the browser because it's assigned to an empty string, but the click event will set the property to an actual string and the interpolation will then update the dom with this property assignment.

At times we may also want to save information about the event - we can track this by passing a paramater to the event handler in our event binding:

    <h2>Welcome {{name}}</h2>
    <button (click)="onClick($event)">Greet</button>
    {{greeting}}

    onclick(){
        console.log(event);
    }

Logging this event will give us access to all the associated event properties, including type of event, position within the window, the target element, etc. The event becomes a variable and we can access its properties to assign them dynamically.

If the action we want to take upon receiving the event is simple enough, we can even set it within the template element instead of setting an event handler:

    <button (click)="greeting='Welcome User'">Greet</button>
    {{greeting}}

This simply assigns the greeting property to Welcome User upon receieving a click event instead of calling a handler function.

## 10 Template reference variables

Allow us to take properties and values from dom elements in the template file if/when we need to log or use data entered by the user.

We create the variable by adding #variableName to the element in the template:

    <input #myInput type="text">
    <button (click="logMessage(myInput.value)")>Log</button>

In this example adding #myInput to our input element allows us to pass the value of the input field to our logging function as a parameter referred to by myInput.value. If we left off the .value property of the myInput variable then we could pass the entire dom element to the function and then we would have to specify within the function that we are going to log just the value property - but we could also log any other available properties or perform other actions besides logging.

## 11 Two Way Binding

We know that if we want a dom element to display the value of a class property we can use property binding, and if we want text entered by the user to flow to a class and be used in some way we use event binding, but what if we want both, so that our data in the template/dom is always consistent with our data in the class?  We use two way binding.  As an example, say we have a class with the property:

    public name = "";

and a dom template with an input form:

    <input text="type">

we can add the ngModel DIRECTIVE as so:

    <input [(ngModel)]="name" text="type">
    {{name}}

what the ngModel does is bind click events to the name property so that it updates within the class upon clicks AND binds the dom element to the value of the property so that it is always consistent with the property data.  It gets both the square brackets of property binding AND the parentheses of the event binding.  And of course, by using interpolation we can display the results of any update to the class property right next to the input element, which confirms visually that our two way binding is working.

One thing we have to remember: The ngModel directive is not automatically accessible within angular - we have to be sure we import the Forms module, whithin which the ngModel directive resides, to our app module, then include the forms module in our imports array metadata within the app module so that it can be used by any of the componenets of our app.

## 12 ngIf directive

Next we tackle the first of three DIRECTIVES that help us conditionally control the structure of the dom, the ngIf directive.

If we have a dom element that we want to display based on a truthy or falsey evaluation, we can add the ngIf directive and set it equal to a simple truthy value, wihch we might access through a class property:

    |   <h2 *ngIf="displayGreeting">Welcome Stephen!<h2>

    |   public displayGreeting = "true";

Now if displayGreeting was set to false we would not see the h2 with our greeting, and not only would it not display on the screen, but it would also be taken out of the dom entirely, which is a bit different than when we set a css selector applied to the element to display: none.

Additionally, we can attach an else to our *ngIf by using an ng-template and modifying the original ngIf as follows:

    <h2 *ngIf="displayGreeting; else elseBlock">Welcome Stephen!<h2>
    <ng-template #elseBlock><h2>User not recognized<h2></ng-template>

in this case, if our displayGreeting property is set to false the ngIf will skip over our greeting and look at the else portion, which tells it to look for a template reference variable of "elseBlock", and since that is within an ng-template block, the ngIf will add that element to the dom in place of the Greeting. We could translate that first line of code to "If displayGreeting is true, show this Greeting, else, if it's false, show the element referred to by "elseBlock". Of course while the ngIf is true the ng-template block is omitted entirely from the dom.

Another way we an construct this that is more verbose but perhaps easier to read:

    <div *ngIf="displayGreeting; then thenBlock; else elseBlock"></div>

    <ng-template #thenBlock><h2>Welcome Stephen!<h2></ng-template>
    <ng-template #elseBlock><h2>User not recognized<h2></ng-template>

This is easily read as "If displayGreeting is true then add thenBlock to the dom, else add elseBlock to the dom.  Clear, but not quite as concise.

## 13 ngSwitch directive

This conditional structural control directive is pretty much the same as a switch statement in any other language - it allow us to pick one of several cases based on the input and it will display the appropriate one (or perhaps a default if we don't match a specific case):

    |    <div [ngSwitch]="user">
    |       <div *ngSwitchCase="'Steve'">Hello Steve</div>
    |       <div *ngSwitchCase="'Pat'">Hello Pat</div>
    |       <div *ngSwitchCase="'Ryan'">Hello Ryan</div>
    |       <div *ngSwitchDefault>Hello User</div>
    |    </div>

    | public user="Brian"

In this example the switch cases compare the value of the property "user" to eah case and when they find the match, they display a greeting for that user's name, but if the value of user is none of the cases, it just displays "hello user".

## 14 ngFor directive

The ngFor DIRECTIVE is basically the equivalent of a foreach loop in C# where it allows us to iterate through an array of values that are assigned to a property:

    |    <div *ngFor="let user of users">
    |       <h2>{{user}}</h2>
    |    </div>

    | public users = ["steve", "pat", "ryan", "brian"];

"let" assigns a variable "user" and foreaches through each element of "users", and whatever we put within the element we attach the ngFor to gets repeated for each of the users.  Because we are iterating through an array, which has an index that the code has access to, angular gave us some modifiers to the *ngFor that work with that index value:

    <div *ngFor="let user of users; index as i">
        <h2>{i} {{user}}</h2>  
    </div>  

This will interpolate the value of the array index of each element added to the dom (0, 1, 2, 3, etc).

    <div *ngFor="let user of users; first as f">      <div *ngFor="let user of users; last as l">     

This will interpolate either true or false and add it to the dom based on whether the "user" is the first or last element within the array

    <div *ngFor="let user of users; even as e">        <div *ngFor="let user of users; odd as o">

Would interpolate either true or false based on whether the "user" is at an even or odd numbered index.  This could be handy if we want to assign different classes to alternating elements in the list of items.

## 15 Component Interaction - Sending data back and forth between Parent and Child componenets

In order to send data to a child component from the main component (AppComponent) we must:

1) Declare a property within the parent component
    C.TS>   public name = "Steve"
2) Bind the property to the child selector within the parent template
    P.HT>   <app-child [parentData]="name"></app-child>
3) Declare a property called parentdata in the child component and add the @input() decorator to it, or give it an alias name and insert 'parentdata' as a paremeter within the @input() decorator parentheses
    C.TS>   @Input('parentData') public name;
4) Bind the child property within the child template
    C.HT>   <h2>{{"Hello" + name}}</h2>
5) MAKE SURE you have imported the import decorator from the main module at the top of the child class file
    C.TS>   import {Component, OnInit, Input } from '@angular/core';

In order to send data to the parent component fro the child component, we must use a click event and emit it with the EventEmitter class:

1) Declare an instance of the EventEmitter class as a variable within the child component and add the @output() decorator to it
    C.TS>   @Output() public childEvent = new EventEmitter();
2) Insert a button in the child template that listens for a click event and upon hearing it, fires an EventEmitter method, which we can define within the child class
    C.HT>   <button (click)="fireEvent()">Send Event to App</button>

    C.TS>   fireEvent() {this.childEvent.emit('Hey Parent App!');}
3) Capture the event being emitted by adding a click event within the child selector in the parent template and binding it to a property that we will create within the parent component class
    P.HT>   <app-child (childEvent)="message=$event" [parentData]="name"></app-child>
    // the $event variable gives access to the argument that was supplied to the Event.emit method, which we assign to the property message within the parent class
    P.TS>   public message="";
4) Interpolate the property that captures the message withing the parent component in the parent template
    P.HT> <h1>{{message}}</h1>
5) MAKE SURE you have imported the output and EventEmitter decorators/classes from the main module at the top of the child class file
    C.TS>   C.TS>   import {Component, OnInit, Input, Output, EventEmitter} from '@angular/core';

WHEWWWWW!

## 16 Pipes

Pipes are Angular methods that provide some text/number/date formatting for us to help us achieve different display objectives.  We can use them to change caseing of strings, pad or round off numbers, give short or long formatted dates, display json etc.  They generally work by putting a pipe then the name of the pipe and and arguments it takes within an interpolation variable:

    {{name | lowercase}}    {{name | uppercase}}    {{message | titlecase}}     {{name | slice:3:8}}

In the case of slice, it is like doing a string.Substring() method in C# where you can supply just one argument and it will give you from that index to the end or you can supply two arguments, one for the starting index and one for the ending index.

    {{5.678 | number:'1.2-3'}}  {{5.678 | number:'3.4-5'}}  {{5.678 | number:'4.1-2'}}

In each case the first digit within the single quotes will be the number of digits before the decimal, the second number will be the minimum number of places after the decimal, the third will be the maximum number of places after. So above yields these results:

    5.678                       005.6780                    0005.68

There are also pipes for currency, json (helpful if you property contains an object), date:

    {{date | date}}                             {{date | date:'short'}}       {{date | date:'shortDate'}}

    Tue Oct 23 2018 14:13:35 EST etc....        10/23/2018 2:13 PM              10/23/2018

https://angular.io/api?type=pipe


## 17 Services

Let's say we are going to have multiple child components within an app that all need to access the same data.  We could copy that data into an array or object within each of the child components, but this violates DRY (Don't Repeat Yourself) and SRP (Single Responsibility Principle) - our components are supposed to be responsible for creating views and handling user interaction with those views, not holding or supplying data.  That's a good reason to create a service, which is a class that exists specifically for the purpse of supplying data to other components, sending and retrieving data from a database, and possibly conducting business logic using our data that should not be a part of the view components themselves.  We will create these files as Service.serice.ts and we will use Angular's Dependency Injection framework in order to provide our services to componenets.

## 18 Dependency Injection in Angular

The thing to remember here is that a class should receive its dependencies from an outside source rather than instantiating instances of other classes within itself.  We inject the dependencies into the constructor as parameters so that the class has everything it needs as soon as it is instantiated.  When using Angular DI, our process will become:

    1) Define the service class
    2) Register the service with an injector
    3) Declare the service as a dependency in all components that need it

## 19 Using Services

Start with the angular CLI and put in the command "ng g s <nameOfService>" which generates a .ts file for your service with a basic service template.  We can add a method to this service class that returns some data in any number of ways - to start we can just hardcode an array of objects and have the method return that array.  Later on we can get into using an api call to bring back an array of objects from a database so that our service is truly dynamic. We also want to declare a property that will hold the data we bring back using our service within any class that we inject the service into.  An example could be "public data = [];". Once we inject the service into our class constructor this array will be filled with the array that our service method returns.

Next we need to register the service with the Injector.  It can be registered in several places, including within the class itself, but if we do that then only that class and that class's children will have access to the service.  However, if we go up to the app.module then all of our components and their children will have access.  So we do this by going to the app.module.ts file and adding the name of the service to the "providers" array within the module metadata ( providers: [nameOfService]).  We also need to add it to the imports list at the top of the class file (import { nameOfService } from './nameOf.service';). NOTE that the convention is that if you name the service file "data" then the .ts file will be data.service.ts and the class name within the .ts file will be DataService, so our providers list will include "DataService" and our import reference will be { DataService } from './data.service'.

Finally, we will declare the service as a dependency of the class we want to inject it into by putting it into the constructor:

    constructor(private _dataService: DataService) {}

This essentially reads as injecting the local variable _dataService of type DataService into our constructor. From here we need to call the method we defined within the service class inside our ngOnInit lifecycle hook within the class:

    ngOnInit() { 
        this.data = this._dataService.getData(); 
    }

Something to note about the service class typescript file is that your need the @injectable decorator at the top of this class in order to allow for a service to rely on other services, ie to have dependencies itself.  If you don't have that decorator you will not be able to inject other services into your service class.

## 20 HTTP and Observables - What are they?

When we get data from a DB through a web API we use HTTP, which sends a request to the DB, gets a response, and returns something called an Observable to our service.  It is up to us to cast the Observable into an array or some other collection we can use in our commponents.

An Observable is a sequence of items that arrive asynchrounously over time.  However, when we use an HTTP get request, the Observable is a single item - an HTTP response that arrives asynchrounously.  As mentioned, our service will generally convert that response into an array and then provide it to the components that need it, but we don't necessarily want to provide it to every component within the app, just the ones that need it.  Hence, as with a newspaper company, we only provide the array of data to components that have subscribed to that service/observable.  The component can assign the array of data to a local variable, and then, at that point, it's up to the component to decide what they want to do with the data that gets returned.

RxJS is a library of Reactive Extensions for Javascript that allows us to work with Observables.

## 21 Fetch data using HTTP

We need to import the HTTPClient module in the main app.module.ts file in order to be able to use HTTP Get requests:

    import { HttpClientModule } from '@angular/common/http';

We also want to add "HttpClientModule" in the imports array of our @NgModule metadata. Note that Angular registers the client module as a provider with our injector service so that we do not need to add it to the providers array.

Next we need to import HttpClient in our EmployeeService class and inject a "private http: HttpClient" dependency to our constructor within the service class, which gives us a local variable with which we can refer to an instance of the HttpClient.  

Now there will be four basic steps to returning some data from an API or DB:

1. HTTP Get request from the service.  We can modify our getEmployees() method to use a Get request in order to supply the data.

    private _url: string = "/assets/data/employees.json";

    getEmployees(){ return this.http.get(this._url); }

2. Receive the observable and cast it into an employee array.  Start by creating a new file called employee.ts and use it to create an interface that will cast our data.

    export interface IEmployee {
        id: number,
        name: string,
        age: number
    }

Once we import the IEmployee from './employee' and the Observable from 'rxjs/Observable' within our EmployeeService class we can modify our getData() method to receive the Observable and convert it into an array of employees:

    getEmployees(): Observable<IEmployee[]>{
        return this.http.get<IEmployee[]>(this._url);
    }

3. Subscribe to the observable from EmpList and EmpDetail components which will use some or all of the data we returned by modifying the ngOnInit() method's assignment of the employees property to a method call of our current instance of the employeeService:

    ngOnInit() {
        this.employees = this._employeeService.getEmployees();
    }

    CHANGES to

    ngOnInit() {
        this._employeeService.getEmployees()
            .subscribe(data => this.employees = data);
    }

4. The subscribe function takes an argument called data which is returned asynchronously and we use an arrow function to assign that data returned by getEmployees() to the employees property within our EmployeeList.component.

## HTTP Error Handling

In order to handle potential api/internet access errors with our service, we need to modify the getEmployees method to catch and error and throw the appropriate error message to our components, then modify our components to display this error.  We do this with multiple steps:

1. add the catch function to our getEmployees method in the service:

    getEmployees(): Observable<IEmployee[]> {
    return this.http.get<IEmployee[]>(this._url)
                    .catch(this.errorHandler);
    }

2. We obviously then need to define an error handler function in the service as well:

    errorHandler(error: HttpErrorResponse){
    return Observable.throw(error.message || "Server Error")

    }

3. The errorHandler method which we pass as a parameter to catch throws the error for us.  Now any component that has subscribed to the getEmployees function can be modified to get access to the error if there is an error instead of data returned.  We then assign the error message to a property on our component and bind that property to the view so that it will display an error message if we have one.

## 23 Routing and Navigation

When you create a new Angular application you can add "--routing" to the end of the new app command in angular cli and it will prepare the project for routing, but if you want to add routing to an existing project, you need to do a few things:

    1. add base tag to the head element of index.html file in the app in order to instruct Angular on how to construct the route: <base href="/">
    2. create a file called app=routing.module.ts within the app folder
    3. import app routing module within the app.module.ts file and add to imports array:
        import { AppRoutingModule } from '.app-routing.module';

Once we have a project with routing configured we need to add paths to the routes array within the app-routing.module.ts file:

    const routes: Routes = [
        { path: 'departments', component: DepartmentListComponent },
        { path: 'employees', component: EmployeeListComponent }
    ];

This does about what you would think: it adds 'departments' to the url route for a view that reflects the Department List component and 'employees' for the url route to the Empoloyee List component's view.  Also, since we will have to have these components in the import list at the top of the app-routing module we can export them as a const array from there and remove them from our app.module imports, but add the const to our import from the app-routing.module and declare them in the app module metadata. Best practice to get rid of duplication.  Notice in the template for the main app.component we have a <router-outlet> selector at the bottom.  This is where the application will display the appropriate templet (view) for each component depending on the route we use. If you append /departments to localhost:4200 you will get the view for the department list component and if you append /employees, the employee list view.

We can also create some buttons to put into a nav on our main app page so the user doesn't have to type the url in their browser.  This is as simple as nesting some anchor elements in a nav element and using two handy directives that the routing module gives us - routerLink and routerLinkActive.  routerLink allows us to change the url via a click event and routerLinkActive allows us to apply a style that will highlight whichever route is the current view to make it more user friendly:

    <nav>
        <a routerLink="/departments" routerLinkActive="active">Departments</a>&nbsp;
        <a routerLink="/employees" routerLinkActive="active">Employees</a>
    </nav>

Whatever we set up our nav a.active style to be in our css file will help us highlight which of the buttons is active, making it more obvious to the user what they are seeing.

## 24 Wildcard Route and Redirecting Route (handling bad route exceptions)

If a user tries to append a route that is not configured and is not looking at the console they may be a bit curious why nothing happens.  We can handle this by creating a component and directing all unmatched routes to this component using the wild card route.  This route uses the following object in our routes array in the arouting module:

    { path: "**", component: PageNotFoundComponent }

Don't forget to import the PageNotFoundComponent to the routing module and add it to the export const.  Also note:  THE WILDCARD MUST BE THE LAST ROUTE IN THE routes ARRAY! If it is up higher, the router reads from the top down while trying to find a matching route and any time it gets to the wild card, it will match even if there is a better match below it.  So routes that would be valid will still display the page not found view.

We have one other potential problem - the only route in our routes array that will match the home page (empty route) is the wild card route, but we don't want to display a page not found on the main page!  So we add an empty route at the top of our array and designate a view that we want for our main page:

     { path:'', component: DepartmentListComponent }

Alternately, we can use a directive called redirectTo and the pathMatch property:

    { path:'', redirectTo: '/departments', pathMatch: 'full'}

This will take the empty path and redirect to departments if there is a full match of the path.  The other option is 'prefix' for pathmatch, but since the empty path is a prefix for all other prefixes, we would redirect every path to departments even if we don't want to.

## 25 Routing Parameters

So how do we add a parameter on to our routing?  We create another entry in our routing array and add a :<parameterName> after the first part of the path that the parameter represents.  For example, if we want department details for each one of 6 departments, we can create a departmentdetail component and set it up so that our route will go to each detail individually. We start by adding this route after the basic "/departments" route:

    { path: 'departments/:id', component: DepartmentDetailComponent }

We will need to import the departmentdetailcomponent and add it to the export array as well.

Then we bind a click event to each of the departments in the array that our *ngFor iterates over, using an event handler function and passing the department in as a parameter:

    <li (click)="onSelect(department)" *ngFor="let department of departments">

Our event handler function use the router module's navigate() function, so we will need to import that at the top of the component class file and we will inject it into the department-list constructor so that we can then use an instance of it within our event handler:

    onSelect(department) {
    this.router.navigate(['/departments', department.id]);
  }

The navigate() function takes a link parameters array as an argument from which Angular constructs the appropriate path. The first part of the array is the path we want Angular to use and the second part is the source of the route parameter. So now that we have this event handler, a click will take us to the correct url route and display a departmentdetailcomponent, but there is a second step here: Reading the id that we used to construct the route and dynamically generating the contents of the detail view based on that id - for which we use the activatedRoute service.

We import the ActivateRoute service in our department-detail component and inject it into department detail constructor.  Once we've done that we can assign a local variable id by looking at the snapshot of the current route (which is an instance of the ActivatedRoute service) and using the paramMap api to get the id from that snapshot.  Of course we need to parse it in order to assign it.

    ngOnInit() {
        let id = parseInt(this.route.snapshot.paramMap.get('id'));
        this.departmentId = id;
    }

Then we can assign that id to a departmentdetail property and bind that property to the view.

## 26 paramMap Observable

There is a drawback to using the snapshot from our ActivatedRoute instance to read the id from our route parameter:  If we were to set up buttons to cycle backwards and forwards through our departments (previous and next for example) the snapshot method will not indicate to Angular that it needs to create a new instance of the component and thus will not run the lifecycle hook that would update the parameter we our bound to.  So we can refactor the code we use to read and bind the id from our ActivatedRoute by importing the ParamMap api from the Router and using ParamMap's get function:

    this.route.paramMap.subscribe((params: ParamMap) => {
      let id = parseInt(params.get('id'));
      this.departmentId = id;
    });

Then we can create two new anchor tags, Next and Previous, which are bound to click events that have goNext and goPrevious handlers to navigate to the appropriate route using our link parameter array to navigate from code and reinitialize the departmentdetail componenet view:

    <h3>You selected department {{departmentId}}</h3>
    <a (click)="goPrevious()">Previous</a>
    <a (click)="goNext()">Next</a>
  
  
  goPrevious(){
    let previousId = this.departmentId - 1;
    this.router.navigate(['/departments', previousId]);
  }

  goNext(){
    let nextId = this.departmentId + 1;
    this.router.navigate(['/departments', nextId]);
  }

