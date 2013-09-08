# [Streaming:](http://www.senchalabs.org/connect/multipart.html)

When defer is used files are not streamed to tmpfiles, you may
access them via the "part" events and stream them accordingly:

```javascript
req.form.on('part', function(part){
  // transfer to s3 etc
  console.log('upload %s %s', part.name, part.filename);
  var out = fs.createWriteStream('/tmp/' + part.filename);
  part.pipe(out);
});

req.form.on('close', function(){
  res.end('uploaded!');
});
```