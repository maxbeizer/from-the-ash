# From the Ash Notes

Why are we here?

Take some of the biggest buzzwords in our industry: IoT, MicroServices,
WebScale, Isomorphic, "The cloud." Our world has to be more connected, more
reliable, and faster than ever before. Our users demand it.

More than ever before, now is real-time's "time to shine." To accomplish that we
have to think beyond the typical web request/response cycle. We will require
bi-directional communication that pushes messages across a single connection.
It's a good thing that none of this is new. A standard for this was created in
2011 and it is RFC 6455.

The protocol consists of an opening handshake followed by basic message framing,
layered over TCP. Which essentially allows for regular GET request with an
upgrade offer. But at least some of you are Node programmer so none of this
should be news to you...

...because this type of interaction fits perfectly with a server that is
non-blocking, event-driven, and allows tons of connections. Ladies and
gentleman, this is Node at its best. This is where Node shines. This is where
Node sparkles and it is one of the reasons we have seen such a meteoric rise in
popularity for Node. In a good number of use cases, WebSockets and Node are
perfect match. Node is great.

Except when it's not. JavaScript, I hate to break it to you, is single-threaded.
It can’t automatically take advantage of additional CPU cores. Further, it’s
based on V8 - which has a hard memory limit of about 1.5 GB - so it also cannot
automatically take advantage of additional memory. Instead, Node.js apps must
fork multiple processes to maximize their available resources. This is where you
see Node clustering and abstractions like Throng, written by my colleague at
Heroku, Hunter Loftis.

So not so much with Node and concurrency. Node's own about page notes that Node
is different from the common concurrency model where OS threads are employed.
Thread-based networking is relatively inefficient and very difficult to use.
Furthermore, users of Node are free from worries of dead-locking the
process—there are no locks. Instead we get a what can grow to be an unruly mess
of callbacks and promises. You may not know it, Node experts that you are but
there is mental overheard to this type of programming that threatens the "it's
all just JavaScript" utopia.

And when the time comes when you actually have to do something blocking,
something synchronous, something computationally expensive. You may find
yourself wishing there were a better way.

You keep up with the industry trends. You read your weblogs, your tweeters, your
hackernewspaper. Since you are JavaScriptistas, you are required to know what's
"hot" right now. And the pendulum is in swing, friends. Proving that no
philosophy has nailed it, functional is back in a big way. Nodevember 2015
features at least two other talks about functional programming. If you keep up
with this sort of thing you may have realized that there are paradigms out there
make concurrency sane by adding the special sauce of immutability. With
immutability, you lose the pain of locking. And you can even lose inefficiency
of the common OS threading model that the Node marketing decries--with Elixir.

Elixir is a dynamic, functional programming language developed by José Valim
around 2012. It runs on top of the Erlang VM. If you are used to well-marketed
and fun sounding names and Erlang sounds like a disease you can catch, you are
not alone. But all you really need to know about Erlang for the purposes of this
talk is that its concurrency model is totally different from what Node claims to
be better than. Rather than expensive forking to make threads, Elixir uses
lightweight processes that carry immutable data around to allow for efficient
use of resources.

If any of this sounds neckbeard-y to you, if it sounds like something at a lower
level of abstraction than you are comfortable with, don't worry. Elixir and its
most popular web framework, Phoenix, were built for the modern web. So where
silly Rails developers like me can't just `npm install` out of the box, Phoenix
devs can. Stock Phoenix installs come with Brunch installed. Again, out of the
box, Brunch handles watching files and reloading the browser. It does uglifying
and even comes with Babel so you can work in ES6 today, no sweat.

So in Node land when you want to use WebSockets, you run into the classic
double-sided coin of Node: there are so many packages out there. NPM is a
wonderful tool that hides the vast forest of dependencies that your project
carries. Your dependencies and their dependencies represent a non-insignificant
portion of your application and threat to long term maintainability. So what do
you choose? How do you know what the soup du jour is?

Well for this particular problem, the answer is a menu item in Phoenix. The
creator of Phoenix, Chris McCord, came from a place of being frustrated with
pain involved in trying to solve this Rails land. So soft-realtime web with
WebSockets was a first class feature of the framework from early on. So much so
that Chris has even called a WebSocket enabled chat app the "Hello World" app of
Phoenix.

So how does it all work? Phoenix has a number of components to handle the
typical HTTP request/response side of things. For soft-realtime, we turn to
Channels. Channels are an abstraction that is a layered system that covers
everything from upgrading the connection and multiplexes sockets over the
connections. Channels handle the routing of events of a given topic by pattern
matching. Channels have a PubSub layer that you can change with different
adapters. Most importantly, Channels handle the messages that flow across the
WebSocket.

Channels consist of a few simple callbacks that we will see in a minute.
Notably, you will see `join` and `handle_in`. Those callbacks handle Messages
coming in from clients and going back out to clients. Messages consist of
topics--that help identify which channel the Message should be routed to--,
events--that help the handler functions match a response--, payload--which is
the JSON payload that the handler will use--, and ref--which will not deal with
today, but assign a unique ID to a message for reference.

I should also note, that we will be looking at PhoenixJS and the client that
ships with Phoenix, but there are client libraries out there for iOS, Android,
C#, and , of course, JavaScript. Let's write some code.


__Demo__


So now it's time to make some unfair comparisons! Let's look at some case
studies that I hope will convince you that Elixir and Phoenix are worth looking
at, even if you are dyed-in-the-wool JavaScript.

A person with the handle mroth did some fairly extensive, however local,
benchmarking. Many people in the Elixir community like to trumpet these numbers
and something to be really proud of, but I would add the caveat as mroth does
that these benchmarks were done a laptop. The findings are more exploratory than
anything else and should not be taken as gospel. Still you will notice a big
jump between the Node Cluster numbers and the Phoenix numbers. Gin is obviously
a Go web framework and we see some big numbers with Scala's Play and plug, which
is the building blocks of web services on the Erlang VM. Plug obviously plays a
big role in Phoenix.

Also, there was a really interesting blog post from a few years back where
someone tried to max out the number of concurrent connections to a Node server.
This guy, Aaron Robinson, tweaked his configuration and got up to one million
connections. About two weeks ago, some of the core contributors to Phoenix
actually got up to two million, with some tweaking. They wrote an interesting
blog post on it if you'd like to read more. But it's worth noting that the
Erlang VM was built for distributed systems--so the possibilities for scaling
are immense. This is where you see amazing things like WhatsApp happening.

I got to attend the most recent ElixirConf and it's fun to we where Phoenix is
going. The community is growing and there are aspirations to keep pushing the
boundaries of the modern web with things like GraphQL. Perhaps more than
anything else, this talk was about how the modern web does not have to be all
Node all the time. There is a great a growing community around Elixir and
Phoenix. I urge you to look into it if this is something that is interesting to
you. Elixir and Phoenix are young, relatively speaking. Well, not even
relatively speaking. What that means is that there is a lot of opportunity out
there for talented JavaScript and non-JavaScript developers out there to
contribute meaningfully. I encourage you to get involved on GitHub and on IRC.

Thank you.




---
* So many Node Websocket libs out there--which to choose from?
* High throughput, but what about expensive operations
* "The 'Hello World' app for Phoenix is a chat app" -- Chris McCord
* Elixir: functional, concurrent, fun
* Documentation as a first-class citizen
* Uses many pieces of the Node tool set you are used to using, like npm
* Live code-reloading
