# Django 3.1 Async Views Testing with Django ORM

By: Andrew Chen Wang

Created on: 10 September 2020 midnight for some reason...

#### Abstract

We all know that the Django ORM is crazy easy to use. It's also integral in almost
every web application, specifically those using a database. With Django 3.1 coming
out, I wanted to see what I could do to increase the performance of my current
applications. However, I will not be testing websockets; they are by far the most
efficient and some would say secure (as the only attack method is improper authentication
into a socket and cross-site session cookiejacking) as the number of requests to
authenticate goes from several (HTTP requests) to just one.

Before we begin, please check out [psycopg3](https://github.com/psycopg/psycopg3)
to allow the Django ORM to be more async capable without needing to use the
primary thing I'll be using for this test.

---

## Tools

I'm running Python 3.8 on a core i5 on a Macbook Air with 16GB of RAM.

I'll be using psycopg2, Django 3.1 (which allows for async views), and locust,
a load testing tool that is going to crash my computer :) To be as efficient
as possible, I'll be using simple HTTP connections and using a proper `DEBUG=False`
production environment using Gunicorn for my WSGI server and Uvicorn workers for 
my ASGI server (Uvicorn replaces Gunicorn's worker but sits on top of Uvicorn's
framework).

I'll also be using miscellaneous views which will test redis-py vs. aioredis vs.
using `asgiref.sync_to_async` according to their environments (a/sync).
The tests won't be as sophisticated as the benchmarks that someone made for the
two (and some other Pythonic cachers).

### Methodology

For each, I'll be using my localhost and then my Raspberry Pi 3 to simulate "latency."

The Django views being implemented:

1. A generic class based TemplateView (and also a function based because why not) with nothing but a bit of text and some other scripts from CDNs.
2. A function view (using ORM) for getting an object.
3. A function view (using ORM) for posting a form.

Again, I'll be running two different tests: one running on ASGI and then the next running on WSGI.
I'll be doing it matched-pairs style in which I will first run the ASGI test then the WSGI and then
vice versa the next day.

That makes a total of 4 views. Lastly, I'll be implementing a personal favorite view of mine: cached responses.

It's nice to have a database and all, but once a website starts rolling, caching functionality
is key to keeping the server alive and making sure requests don't time out or is blocked due to
natural Denial of Service due to server runtime. I'll be using aioredis, asgiref's `sync_to_async`,
and naturally redis-py accordingly.

I've left out authentication in all these views and decided not to setup something like
Traefik for HTTPS as to maintain as pure of a development server as possible.

---

### License

Licensed under the MIT License
