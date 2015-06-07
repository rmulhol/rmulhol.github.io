---
layout: post
title:  "Give and Take: Writing a Simple Server in Java"
date:   2015-06-06
categories: Java
tags: server socket
--- 

As I've been working to put together an HTTP server in Java, there's been a lot to learn. Correctly parsing and handling HTTP requests is a challenge. If you'd like to try for yourself, check out [the Cob Spec Firnesse suite][cob_spec].

While a lot of work goes into dealing with the various types of requests (and the different options they can be accompanied by), an initial difficulty is simply wrapping your head around how to construct an application that receives requests and delivers responses.

If that's the task you've set out to accomplish, I've got news for you: putting together a (very, very basic) server in Java is simple. You can do so with the following 11 lines of code:

{% highlight java %}
public static void main(String[] args) throws IOException {
    ServerSocket serverSocket = new ServerSocket(5000);
    while (true) {
        Socket clientSocket = serverSocket.accept();
        OutputStream out = clientSocket.getOutputStream();
        BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));
        String request = in.readLine();
        out.write(("HTTP/1.1 200 OK\r\n\r\nYour request: " + request).getBytes());
        out.close();
    }
}
{% endhighlight %}

Let's break down what's going on here.

When the application starts, it calls `main`. This method creates a `ServerSocket` object which will serve as a host to receive requests. The argument passed in specifies which port the server should listen on; I've set to 5000. That means you can make a request to the server by visiting `localhost:5000`.

The next line initiates an infinite loop, relying on `while(true)` to ceaselessly repeat a sequence of instructions. While infinite loops are normally problematic in software, it's fine here since we want our server to stay available after every request.

The body of the `while` loop sets up a protocol for dealing with client requests. First, we acknowledge any client that makes a request by allocating it a `Socket` object. Second, we identify the input and output streams for that client. While we might expect that our client will normally be a browser, it could also be other things (like a command line issuing a `curl` request). 

Once we've identified a client and its attendant input and output streams, we're ready to read a request[^1] and write a response. This is the meat of our server's functionality. After we've received a request, we return a `200 OK` response header with a body that displays the request we received.

But the work isn't finished there, as we still have to close the output stream to signal that the HTTP request/response cycle is complete.

With one client's request received, a response sent out, and the output stream closed, our server can move back to beginning of the `while` loop to await another request. 

That's it. With this small amount of code, we've got an HTTP server that runs. There's a lot more to do to make it truly functional, but getting over the initial hurdle of handling the request/response cycle is no problem.

[^1]: Knowledgeable readers will notice that we only read the first line of our client's request, which likely does not encapsulate the full request. That's fine for our very limited functionality, but a more sophisticated server would want to be able to parse a complete (multi-line) request. 

[cob_spec]: https://github.com/8thlight/cob_spec