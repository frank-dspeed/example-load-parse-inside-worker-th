# example-load-parse-inside-worker-th
This example illustrates the Stream Fetch API with a webworker that takes the emitted chunks for parsing.


```js
// Fetch the original image
fetch('./tortoise.png')
// Retrieve its body as ReadableStream
.then(response => response.body)
.then(body => {
  const reader = body.getReader()
  return new ReadableStream({
    start(controller) {
      return pump();
      function pump() {
        return reader.read().then(({ done, value }) => {
          // When no more data needs to be consumed, close the stream
          if (done) {
              controller.close();
              return;
          }
          // Enqueue the next data chunk into our target stream
          controller.enqueue(value);
          return pump();
        });
      }
    }  
  })
})
.then(stream => new Response(stream))
.then(response => response.blob())
.then(blob => URL.createObjectURL(blob))
.then(url => console.log(image.src = url))
.catch(err => console.error(err));
```
