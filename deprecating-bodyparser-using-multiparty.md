# [TJ's response to the temp files and bodyParser()](https://groups.google.com/d/msg/express-js/iP2VyhkypHo/5AXQiYN3RPcJ)

Express 3.4.0 and Connect 2.9.0 have made some small changes to `bodyParser()`, and more specifically the `multipart()` middleware used within it. There have been concerns regarding temporary-file usage, however to maintain backwards compatibility for now [I've added some documentation](http://www.senchalabs.org/connect/multipart.html).

We've also switched to the "multiparty" library, instead of using formidable, which allows you to stream the parts directly to arbitrary destinations without hitting disk. Keep in mind that the destination streams must properly implement node's backpressure mechanisms otherwise you're likely to cause large memory bloat causing the process to fail. The "defer" option let's subsequent middleware listen on "part" events to stream accordingly instead of writing to disk, providing the convenient `req.files` object that you might be used to.

Another alternative if you're concerned is to simply use `express.json()`, and `express.urlencoded()`, and leave out `multipart()` all together. Use `if (req.is('multipart/form-data')` and formidable, multipartyor parted directly.

The tmpfile used is `os.tmpDir()`'s value, so if you plan on continuing to use disk it's highly recommended to set up a strategy for dealing with unnecessary temporary files, this is good practice for any production environment, much like log rotation it is critical to any large deployment. An example tool is [`reap(1)](https://github.com/visionmedia/reap). Tools like this should be used regardless of the cleanup technique, as application processes may fail at any point in time, and may never have the chance to `unlink()` the file.

The default limits for `bodyParser()`, `urlencoded()`, `multipart()` and `json()` have also been adjusted. The default limit for multipart is now 100mb, and 1mb for the other two. If you anticipate requests larger than this you may pass `{  limit: '200mb' }` to either `bodyParser()` or the others. It's recommended to use each one individually, bodyParser() is a legacy convenience aggregate of the others, but applying a global .limit option between the three of them is not a great choice, as sending 200mb of JSON could halt the application.

If node sits behind a reverse proxy such as nginx you may easily tweak this behaviour there as well.

If you have questions, concerns, or suggestions let me know.