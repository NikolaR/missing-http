# missing-http
Proper HTTP APIs for MATLAB.

# The Problem

MATLAB's support for HTTP calls leaves a lot to be desired. `urlread` and `urlwrite` had numerous shortcomings. You couldn't set arbitrary headers. You couldn't see response headers. _You couldn't even see the response code._

More recent versions of MATLAB have addressed some of the issues. You can nows pecify a timeout, for instance. And 2014b's `webread` and `webwrite` are big improvements. But in typical Mathworks style, they managed to still F it up.

For instance, `weboptions` allows you to set headers. (Finally!) But while setting a Content Type is easy, if you want to set a header that's less common, you have to use a more cumbersome form, because Mathworks is concerned you might hurt youself with this scary internet business. Observe:
```
data = webread(SOME_URL, weboptions('UserAgent', 'twatlab 2014b')); % works fine
data = webread(SOME_URL, weboptions('Authentication', ['Bearer ' TOKEN]); % does not work
data = webread(SOME_URL, weboptions('KeyName', 'Authentication', 'KeyValue', ['Bearer ' TOKEN]); % works
```

And that's just scratching the surface. (Still can't see the status codes! Those are useful!) As with most tasks that don't resemble linear algebra, MATLAB does a fairly piss poor job.

# An Obvious Solution

MATLAB supports Java, and Java has the *fantastic* [Apache HttpComponents](https://hc.apache.org/), so why not just add those jars to the dynamic classpath, grumble a bit, and continue on?

Except MATLAB uses HttpComponents internally, so it's already on the classpath. But they use an old version (4.1, I believe), and their java classpath trumps yours. So your code will fail with strange errors about classes and methods not found.

# A Working Solution

Thankfully, the Java world also produced a tool called [Jar Jar Links](https://code.google.com/p/jarjar/) that lets you mess with jar files. One thing you can do is rename things. So if we move all of the HttpComponents classes to a new package, there's no longer any overlap, and we can use our newer versions with no worry of accidentally getting an older version packaged with MATLAB.

That's exactly what missing-http does. All occurances of `org.apache.*` are altered to `net.psexton.ext.org.apache.*` in the HttpComponents jar file bytecode. Additionally, we combine the half a dozen jars from Apache into a single `missing-http.jar` file.

# License

HttpComponents is licensed under the [Apache 2 license](http://opensource.org/licenses/Apache-2.0). missing-http is a derivative work and is licensed under the same terms. Copyright for all code inside the `missing-http.jar` with a classpath of `net.psexton.ext.*` remains with its original owners (e.g. The Apache Foundation).
