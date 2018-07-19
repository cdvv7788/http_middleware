# http_middleware

A middleware library for Dart [`http`](https://pub.dartlang.org/packages/http) library.

## Getting Started

`http_middleware` is a module that lets you build middleware for Dart's [`http`](https://pub.dartlang.org/packages/http) package.

### Installing
Include this library in your package.
```yaml
http_middleware: any
```

### Importing
```dart
import 'package:http_middleware/http_middleware.dart';
```

###Using `http_middleware`
Create an object of `HttpWithMiddleware` by using the `build` factory constructor.

The `build` constructor takes in a list of middlewares that are built for `http_middleware`. 
One very nice middleware you can use is the [`http_logger`]() package. We will use that here for demonstration.

##### (You can also build your own middleware for `http_middleware`. Check out [Build your own middleware](#building-your-own-middleware))

```dart
HttpWithMiddleware http = HttpWithMiddleware.build(middlewares: [
    Logger(),
]);
```

That is it! Now go ahead and use this `http` object as you would normally do.
```dart
//Simple POST request
var response = await http.post('https://jsonplaceholder.typicode.com/posts/',
    body: {"testing", "1234"});

//Simple GET request
var response = await http.get('https://jsonplaceholder.typicode.com/posts/');
```

`HttpWithMiddleware` supports all the functions that [`http`](https://pub.dartlang.org/packages/http) provides.

```dart
http.get(...);
http.post(...);
http.put(...);
http.delete(...);
http.head(...);
http.patch(...);
http.read(...);
http.readBytes(...);
```

#### Using a Client
If you want to use a `http.Client` in order to keep the connection alive with the server, use `HttpClientWithMiddleware`.
```dart
HttpClientWithMiddleware httpClient = HttpClientWithMiddleware.build(middlewares: [
     Logger(),
]);

var response = await httpClient.post('https://jsonplaceholder.typicode.com/posts/',
    body: {"testing", "1234"});

var response = await httpClient.get('https://jsonplaceholder.typicode.com/posts/');

//Don't forget to close the client once done.
httpClient.close();
```

### Building your own middleware
Building your own middleware with `http_middleware` is very easy, whether you want to create a package for `http_middleware` or you want to build a middleware solely for your own project.

Once you have the necessary imports, all you have to do is extend the `MiddlewareContract` class which will give you access to all the functions in `http` module.
You can then `@override` all the required functions you need to add middleware to.

Example (A simple logger that logs data in a POST request):
```dart
class CustomMiddleWare extends MiddlewareContract {
   @override
   Function(http.Response) interceptPost({RequestData data}) {
       print("POST Url: ${data.url}");
       return (response) {
           print("POST Status: ${response.body}");
       };
   }
}
```