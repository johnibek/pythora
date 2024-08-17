# Pythora: Python Web Framework built for learning purposes

![purpose](https://img.shields.io/badge/purpose-learning-green)
![PyPI - Version](https://img.shields.io/pypi/v/pythora)

Pythora is a web framework built for learning purposes.
It is a WSGI Framework and can be used with any WSGI application server such as Gunicorn.

## Installation
```shell
pip install pythora
```

## How to use it

### Basic Usage:

```python
from pythora.app import Pythora

app = Pythora()

@app.route("/home", allowed_methods=['get'])
def home(request, response):
    response.text = "Hello from Home Page"

@app.route("/hello/{name}")
def hello(request, response, name):
    response.text = f"Hello from {name}"

@app.route("/template")
def template(req, resp):
    resp.body = app.template("test.html", context={"new_title": "Best Title", "new_body": "Best Body"})
```

## Unit Tests

The recommended way of writing unit tests is with [pytest](https://docs.pytest.org/en/stable/)
There are two built-in fixtures that you may want to use when writing unit tests with `Pythora`.
The first one is `app` which is an instance of the main `Pythora` class.

```python
def test_basic_route_adding(app):
    @app.route("/home")
    def home(req, resp):
        resp.text = "Hello from home"
```

The other one is `test_client` that you can use to send HTTP requests to your handlers. 
It is based on the famous [requests](https://requests.readthedocs.io/en/latest/) and it should feel very familiar:

```python
def test_parameterized_routing(app, test_client):
    @app.route("/hello/{name}")
    def hello(req, resp, name):
        resp.text = f"Hello {name}"

    assert test_client.get("http://testserver/hello/John").text == "Hello John"
    assert test_client.get("http://testserver/hello/Matthew").text == "Hello Matthew"
```

## Templates

The default folder for templates is `templates`. You can change it when initializing main `Pythora()` class.

```python
from pythora.app import Pythora
app = Pythora(template_dir='templates_dir_name')
```

Then you can use HTML files in that folder like so in a handler:

```python
from pythora.app import Pythora

app = Pythora()

@app.route("/test-template")
def template(req, resp):
    resp.html = app.template('test.html', context={"new_title": "Best Title", "new_body": "Best body "})
```

## Static files

Just like templates, the default folder for static files is `static`, and you can override it.

```python
from pythora.app import Pythora
app = Pythora(static_dir="static_dir_name")
```

Then you can use static files inside this directory in HTML file.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>{{new_title}}</title>
    <link rel="stylesheet" href="static/home.css">
</head>
<body>
    <p>{{new_body}}</p>
</body>
</html>
```


## Middleware

You can create custom middleware classes by inheriting from `pythora.middleware.Middleware` class
and overriding its two methods that are called before and after each request.

```python
from pythora.middleware import Middleware
from pythora.app import Pythora
app = Pythora()

class LoggingMiddleware(Middleware):
    def process_request(self, req):
        print("Request is being processed", req.url)

    def process_response(self, req, resp):
        print("Response has been generated", req.url)

app.add_middleware(LoggingMiddleware)
```
