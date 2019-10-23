# Creating an API - some basics
**ASP.NET** (or **Active Server Pages**) is Microsoft's framework for developing web applications. These range from [RESTful APIs](https://searchapparchitecture.techtarget.com/definition/RESTful-API) to full [MVC applications](https://www.tutorialsteacher.com/mvc/mvc-architecture) for both server side rendering (using, for example, [Razor](https://docs.microsoft.com/en-us/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)) and single page apps, frequently using [Angular](https://angular.io/) but other frameworks too.
With the release of ASP.NET Core in 2016, many of these functionalities were combined into one pared down but easily extensible framework.
## Setting up a project
You can use the CLI to initialise a web api project. `dotnet new web` will set up an 'empty' project (though it will still handle much of the configuration of the hosting and serving of data for you), but there are several other starters for some of the scenarios listed above under the `new` command.
Inside a newly created empty project you will find `Program.cs` which is the entry point and configures the server and host, and `Startup.cs`, which exposes two methods two configure your application:
- `ConfigureServices` is called with a `services` argument, which can be used to add additional _services_ to an API. Services is a broad term that ranges to architecture, like MVC, to authentication.
- `Configure` is called with the built application `app`, including all the services previously added, and the development environment `env` which can be used to conditionally handle development, production and staging environments. Use this method to configure the pipeline from _request_ to _response_. This means inserting ordered functionality, known as _middleware_, to handle HTTP requests and determine what gets sent back in the response. By default the only middleware there is used to conditionally add useful exception pages for developers when manually testing their api on a browser; the last command is there as a default 'Hello World' capture but in general you will aim to intercept the pipeline earlier to send a response.
As ASP.NET Core is so reduced, many of the dependencies we need are found in individual packages that need installing to create required resources, helping keep the project as slim as possible.
### MVC
Microsoft has build in support for MVC pattern apps in ASP, and configuring them is very simple. The _dependency injection_ principle that ASP has built in is utilised here. ASP makes little presumption about how you intend to construct your project and encourages you to _decouple_ the elements of your code like architecture, persistence and logging, by injecting them in at appropriate places. All that is necessary is to call `services.AddMvc` in `ConfigureServices` and `app.UseMvc` in `Configure`. Both of these are provided by the `Microsoft.Extensions.DependencyInjection` package.
For the purposes of a RESTful JSON API, we can approach the _views_ part of MVC as the serialisation of our JSON data to be returned, and don't need to set up much in the way of directory structure. We will however need _controllers_, responsible for interpreting requests, and _models_, responsible for assembling the requested data. It's best practice to keep these in separate folders, under separate child namespaces in your project.
#### Controllers
A class can act as a _route_ for a requested resource. To do this, it must be public, it must inherit from `Microsoft.AspNetCore.Mvc` and it must have a `Route` attribute like so:
```cs
[Route("api/films")]
public class FilmsController : Controller
{
}
```
Now any request for data on a url prefixed `/api/films` will instantiate this class.
The method that is called on the class depends on the method of the HTTP request (most frequently `GET`, `POST`, `PATCH`, `PUT` or `DELETE`) and any additional parameters or child resources requested. The return type depends on what you want; for our purposes, an `IActionResult` will do - more on this later. A basic request on the above route would look like this:
```cs
[HttpGet()]
public IActionResult GetFilms()
{
  // employ model to get data, then return it in the form of an IActionResult
}
```
Other attributes for other HTTP methods exist: `HttpPost`, `HttpDelete`, etc.
If the endpoint employs a _parameter_ to request an individual resource, use curly braces to say it should be passed into the method as an argument. Any additional string inside the attribute invocation will be attached to the end of the api defined on the class's route. For example, this method placed in the class outline above will be called when the requested endpoint is `/api/films/6`:
```cs
[HttpGet("{id}")]
public IActionResult GetFilmById(int id)
{
  // etc.
}
```
#### Returning JSON and Error Handling
A RESTful api should return either JSON or XML - JSON is generally more in favour currently, but ideally the API would decouple the controller from the serialisation of the data it returns. For our purposes here, we will presume JSON.
However, there are cases where a basic serialisation of the data that we retrieve is inadequate, most notably when we want to handle errors. If our model logic turns out `null` when searching by id, it would be inappropriate to return it stringified into JSON (which would also be null) - this represents that the resource has not been found, and the response should reflect as much for the developer. In the case the _status code_ of the response should be `400`. Successful read requests for data should be accompanied by a `200` status code - you can read more about which status codes to return [here](https://www.restapitutorial.com/httpstatuscodes.html).
`Microsoft.AspNetCore.Mvc` provides a `JsonResult` which handles the serialisation of the data provided to it, but if there is a chance that what we want to return is not the data, the return type should be the interface that the assorted status handlers employ, `IActionResult`. In this example, we use the `Ok` handler for a `200`, and `NotFound()` for a `404`:
```cs
[HttpGet("{id}")]
public IActionResult GetFilmById(int id)
{
  var film = getFilmByIdFromModel(id)
  if (film == null)
  {
    return NotFound();
  }
  return Ok(film);
}
```
#### Handling queries
URLs can include queries that inform the API of any actions that should be performed on the data. Frequently, queries are used for filtering, ordering or paginating responses. An example url with a query might look like `api/films?sort=name`.
ASP will call your controller with an argument for each query in the url. The variable name will be the key of the query (`sort`), and the value will match the passed in query value (`name`). This could be handled like so:
```cs
[HttpGet()]
public IActionResult GetFilms(string sort)
{
  List<FilmDto> films;
  if (sort) 
  {
    films = getSortedFilmsFromModel(sort);
  }
  else
  {
    films = getFilmsFromModel()
  }
  return Ok(films);
}
```
### Model logic & dependency injection
In the above example, our pretend `getFilmByIdFromModel` method is a _concrete_ dependency of our controller. This is bad because if we wish to change the model, we have to change the source code of the controller - we have to change code that was working well, something generally to be avoided.
As noted before, ASP provides mechanisms for dependency injection to assist with decoupling processes in your code. One thing that should be isolated is your model logic. If the mechanisms for holding your data change (a different database, for example), then the changes should be injected directly into the constructor, which can otherwise remain agnostic about what it is doing to collect the data.
This injection happens in `ConfigureServices`. For a persistent data store we should call `services.AddSingleton<T, U>()`. A _singleton_ is a design pattern that ensures only one instance of the object exists at any one time. It's useful in this case to ensure that different requests all end up interacting with the same data storage.
The actual types that are passed in depend on your setup, but the first should be the _interface_ from which the service derives and the second should be the default implementation. It will look something like this:
```cs
services.AddSingleton<IMyDatastore, MyDatastore>();
```
The constructor of our controller will now be called with an instance of the data store whenever a request is made that reaches there. It can be saved to a private field, safe to be used by the routed methods when required:
```cs
private readonly IMyDatastore _myDatastore;
public FilmsControllers(IMyDatastore myDatastore)
{
  _myDatastore = myDatastore;
}
[HttpGet("{id}")]
public IActionResult GetFilmById(int id)
{
  var film = _myDatastore.getFilmById(id)
  if (film == null)
  {
    return NotFound();
  }
  return Ok(film);
}
```
## Testing
Testing an API can be separated into two distinct approaches:
- _unit testing_, which involves testing individual components making up the api, irrespective of how it connects on a wider scale
- _integration testing_, which involves testing larger segments or the entire pipeline of request to response.
### Integration Testing
By its nature, _integration testing_ is harder to pull off. This is for a number of reasons:
- there are many permutations in how a client might interact with your pipeline, so it's hard to get full coverage
- some services are harder to test - knowing that an email has sent is difficult to identify...
- they could incur costs, such as database requests
- they can be unreliable, because they might rely on network connections; ideally tests should be relied upon to return the same value each time
- perhaps most importantly, they can slow the development process because they are likely to integrate asynchronously operating services
For this reason, integration tests are generally employed sparingly, on key endpoints that are hard to guarantee with individual unit tests. They sometimes aren't written as part of the development process or with TDD, but instead as future insurance against changes when behaviour has already been established. But different places will employ their own approaches - the only real rule is that you seek to create a balanced, manageable and useful test suite that covers unit testing, integration testing, end-to-end testing and any other testing approaches like security or performance testing that are important to the application.
### Unit Testing
More important to the development process is _unit testing_. Unit testing with _TDD_ (test driven development) can help you describe, document and development the individual components of your API so that you can be confident in employing them more widely.
As we are only looking at in-memory models at the moment, unit testing your models should be very similar to unit tests you have already run with Nunit, as long as they are properly isolated.
Testing controller logic presents more of a problem. The aim of testing a controller is to see how the controller behaves in certain situations - not necessarily that it returns the response with the correct data. This would essentially mean testing the model too, which would make this an integration test.
Fortunately, we've designed our controller to accept the model logic through constructor injection. And ASP has helped us by only requiring an interface as a parameter. We can instead insert a pretend version of our model, set it up to return whatever we like, and test what we are interested in - how the controller responds to individual situations. This is the basis of _mocking_.
### Mocking with Moq
This example will use a testing package called **Moq** to create mock model objects that will give us access to our controllers. The process is broken down in the following example:
```cs
using Moq; // don't forget this namespace!
public class FilmControllerTests
{
  [Fact]
  public void Get_FilmById_ValidId_ReturnsOK()
  {
    /* ARRANGE */
    // use the Mock generic method to return a mock repository
    var mockRepo = new Mock<IMyDatastore>();
    // set up the mock repo to behave in a certain what when a method on it is called. In this test, we expect 'getContents' to be called, and we want to make sure that returns a valid value - in this case, an empty array. Remember, the point is not to test the model, just to create the scenario when the controller method is called.
    FilmDto mockFilm = new FilmDto("The Truman Show", 1997);
    mockRepo.Setup(x => x.getFilmById(4)).Returns(mockFilm);
    // pass the mock's Object property in when instantiating the controllers. The name 'sut', or 'system under test' is commonly used.
    var sut = new FilmsControllers(mockRepo.Object);
    /* ACT */
    // test the method you want with the same argument you setup earlier
    IActionResult result = sut.GetFilmById(4);
    /* ASSERT */
    // use Xunit to check that you got the correct type of result back (a 200 in this case)
    Assert.IsType<OkObjectResult>(result);
    // if you care about the body of the response, then the above assertion will return the OkObjectResult for you to save to a variable
    var okObjectResult = Assert.IsType<OkObjectResult>(result);
    // you can then cast the value within the result back to the model value, and test if it as you expected.
    var returnedValue = okObjectResult.Value as FilmDto;
    Assert.Equals(mockFilm, returnedValue);
  }
  [Fact]
  public void Get_FilmById_InValidId_ReturnsNotFound()
  {
    /* ARRANGE */
    var mockRepo = new Mock<IMyDatastore>();
    // this time set up the mock to simulate a request for a film not being found
    mockRepo.Setup(x => x.getFilmById(444)).Returns(null);
    var sut = new FilmsControllers(mockRepo.Object);
    /* ACT */
    // test this time with the new argument
    IActionResult result = sut.GetFilmById(444);
    /* ASSERT */
    // this time check that you got the correct type of result back (a 404 this time!)
    Assert.IsType<NotFoundResult>(result);
  }
}
```