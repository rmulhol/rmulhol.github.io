---
layout: post
title:  "Show Your Work: Recording Data With Java's Logging Utility"
date:   2015-06-19
categories: Java
tags: server logging
--- 

As I've been putting together [an HTTP server][http_server] in Java to pass [the Cob Spec Fitnesse suite][cob_spec], one task I've had to carry out is building a log of incoming requests that could be served up to authorized users attempting to access the `/logs` uri.[^1]

My initial solution to this challenge was to build and manage my own log, writing all incoming requests to a text file and then serving up the contents of that file to authorized users.[^2]

This solution worked, but it overlooked a built-in feature of the JDK that could have aided me along the path: `java.util.logging`.[^3]

This package provides a simple and easy-to-implement solution to your logging needs. All it requires is that you set up a logger and tell it when and where to log data.

Here's some sample code:

{% highlight java linenos %}
public class Main {

    static final Logger serverLogger =
            Logger.getLogger(Main.class.getName());

    public static void main(String[] args) throws IOException {
        try {
            FileHandler requestLogHandler = new FileHandler(System.getProperty("user.dir") + "/logs.txt");
            serverLogger.addHandler(requestLogHandler);
        } catch (IOException e) {
            serverLogger.log(Level.SEVERE, "Couldn't add handler to logger", e);
        }

        ServerSocket serverSocket = new ServerSocket(5000);

        serverLogger.log(Level.INFO, "Server starting... ");

        while (true) {
            Socket clientSocket = serverSocket.accept();

            OutputStream out = null;
            BufferedReader in = null;
            try {
                out = clientSocket.getOutputStream();
                in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));

                String request = in.readLine();
                serverLogger.log(Level.INFO, request);

                byte[] response = ("HTTP/1.1 200 OK\r\n\r\nYour request: " + request).getBytes();
                out.write(response);
                out.close();
            } catch (IOException e) {
                serverLogger.log(Level.SEVERE, "Couldn't complete request/response cycle", e);
                e.printStackTrace();
            }
        }
    }
}
{% endhighlight %}

Here we have a fully-functional echo server with logging capabilities. How does it work?

First, we set up a logger for our `Main` class on lines 3-4. This is the tool we'll use to log requests.

Next, we add a `FileHandler` on lines 7-12. This step is optional, since our logged data will already be written to the console without this step. But it assures that we have a stored copy of our logs even if we clear the console.

Line 14 introduces our first actual logging. We note that the server is starting, attributing this message an importance of `Level.INFO`. You can see a full rundown of the different log levels [here][logging_levels], but for now all you need to know is that `Level.INFO` is sufficient to assure our logged data is written to the console.

Finally, we make another `Level.INFO` log whenever a request has been read. That way we have a record of each request our server receives.

That's pretty much it. We now have a persistent record (`logs.txt`) that keeps a record of each time the server is started and each request that's received.

There are two other places where data is logged in the application above, on lines 11 and 34. Those happen whenever an `IOException` is caught, and they're attributed `Level.SEVERE` since something has gone very wrong if they're ever triggered.

So that's pretty much it - `java.util.logging` provides all you need to get some basic logging work done.[^4]

[^1]: Cob Spec tests basic authorization functionality by checking whether attempts to access the `/logs` uri are allowed only when the request includes an authorization header.
[^2]: Unauthorized users receive the `401 Unauthorized` status code.
[^3]: There are other logging resources to check out if you're interested in the topic, perhaps most notably [Log4j][log4j] and [SLF4J][slf4j].
[^4]: There's a lot more you can accomplish with `java.util.logging` and other logging tools, so, if you're interested, don't stop here!

[http_server]: https://github.com/rmulhol/httpServer
[cob_spec]: https://github.com/8thlight/cob_spec
[logging_levels]: http://docs.oracle.com/javase/7/docs/api/java/util/logging/Level.html
[log4j]: http://logging.apache.org/log4j/2.x/
[slf4j]: http://www.slf4j.org/