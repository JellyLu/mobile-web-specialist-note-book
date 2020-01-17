Mobile Web Specialist Certification Notes

# Basic website layout and styling

## Responsive design

- Set the visual viewport

```
<meta name="viewport" content="width=device-width, initial-scale=1.0" >
```

[Responsive Web Design Basics](https://developers.google.com/web/fundamentals/design-and-ux/responsive/#set-the-viewport)

[Using the viewport meta tag to control layout on mobile browsers - MDN](https://developer.mozilla.org/en-US/docs/Mozilla/Mobile/Viewport_meta_tag)

- Use media queries

```
// 48rem(768 pixels at browser's default font size or 48 times the
// default font size in the user's browser)
@media screen and (max-width: 48rem)
```

- Using Flexbox

[A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)

[Material Design](https://material.io/design/)

## Responsive images

- Set the relative width

make sure images won't overflow the screen

```
img {
  max-width: 100%;
}
```

- Using the `srcset` attribute

The goal is to get the browser to fetch the version of the image with the smallest dimensions that is still bigger than the final display size of the image.

`srcset` lets us list a set of images at different resolutions for the browser to choose from when fetching the image. The browser's choice depends on the viewport dimensions, the image size relative to the viewport, the pixel density of the user's device, and the source file's dimensions.

```
// On a 1x display, the browser fetches sfo-500_small.jpg when the
// window is narrower than 500px, sfo-800_medium.jpg when it is
// narrower than 800px, and so forth.
<img
  id="sfo"
  src="images/sfo-500_small.jpg"
  alt="View from aircraft window near San Francisco airport"
  srcset="
    images/sfo-1600_large.jpg 1600w,
    images/sfo-1000_large.jpg 1000w,
    images/sfo-800_medium.jpg  800w,
    images/sfo-500_small.jpg   500w
  "
/>
```

[srcset and sizes](https://ericportis.com/posts/2014/srcset-sizes/)

- Using media queries

```
// The `sizes` value matches the image's max-width value in the CSS.
<img
  id="sfo"
  src="images/sfo-500_small.jpg"
  alt="View from aircraft window near San Francisco airport"
  srcset="
    images/sfo-1600_large.jpg 1600w,
    images/sfo-1000_large.jpg 1000w,
    images/sfo-800_medium.jpg  800w,
    images/sfo-500_small.jpg   500w
  "
  sizes="(max-width: 700px) 90vw, 50vw"
/>
```

[@media](https://developer.mozilla.org/en-US/docs/Web/CSS/@media)

- Use the picture and source elements

```
<picture>
  <source
    srcset="
      images/horses-1600_large_2x.jpg 2x,
      images/horses-800_large_1x.jpg
    "
    media="(min-width: 750px)"
  />
  <source
    srcset="images/horses_medium.jpg"
    media="(min-width: 500px)"
  />
  <img src="images/horses_small.jpg" alt="Horses in Hawaii" />
</picture>
```

[picture MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/picture)
[source MDN](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/source)

[Responsive Images: If you're just changing resolutions, use srcset](https://css-tricks.com/responsive-images-youre-just-changing-resolutions-use-srcset/)

# Fetch API

## Fetch a resource

```
function validateResponse(response) {
  if (!response.ok) {
    throw Error(response.statusText);
  }
  return response;
}

function readResponseAsJSON(response) {
  return response.json();
}

function logResult(result) {
  console.log(result);
}

function logError(error) {
  console.log("Looks like there was a problem:", error);
}

function fetchJSON() {
  fetch('examples/animals.json') // 1
  .then(validateResponse) // 2
  .then(readResponseAsJSON) // 3
  .then(logResult) // 4
  .catch(logError);
}
```

[Reponse methods](https://developer.mozilla.org/en-US/docs/Web/API/Response#Methods)
[Readable Stream](https://developer.mozilla.org/en-US/docs/Web/API/ReadableStream)

## Fetch Image

```
// Fetch Image ----------
function showImage(responseAsBlob) {
  const container = document.getElementById("img-container");
  const imgElement = document.createElement("img");
  container.appendChild(imgElement);
  const imgUrl = URL.createObjectURL(responseAsBlob);
  imgElement.src = imgUrl;
}

function readResponseAsBlob(response) {
  return response.blob();
}

function fetchImage() {
  fetch("examples/fetching.jpg")
    .then(readResponseAsBlob)
    .then(showImage)
    .catch(logError);
}

const imgButton = document.getElementById("img-btn");
imgButton.addEventListener("click", fetchImage);
```

Note: The URL object's createObjectURL() method is used to generate
a data URL representing the Blob. This is important to note.
You cannot set an image's source directly to a Blob.
The Blob must be converted into a data URL.

[Blobs](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
[Response Blob](https://developer.mozilla.org/en-US/docs/Web/API/Body/blob)
[URL object](https://developer.mozilla.org/en-US/docs/Web/API/URL)

## Fetch Text

```
function showText(responseAsText) {
  const message = document.getElementById("message");
  message.textContent = responseAsText;
}

function readResponseAsText(response) {
  return response.text();
}

function fetchText() {
  fetch("examples/words.txt")
    .then(readResponseAsText)
    .then(showText)
    .catch(logError);
}
const textButton = document.getElementById("text-btn");
textButton.addEventListener("click", fetchText);
```

Note: While it may be tempting to fetch HTML and append it using
the innerHTML attribute, be careful. This can expose your site to
cross-site scripting attacks!

[Response Text](https://developer.mozilla.org/en-US/docs/Web/API/Body/text)

## Using HEAD requests

```
// HEAD request ----------
function headerContentLength(response) {
  return response.headers.get("Content-Length");
}

function headRequest() {
  fetch("examples/words.txt", {
    method: "HEAD"
  })
    .then(headerContentLength)
    .then(showText)
    .catch(logError);
}
const headButton = document.getElementById("head-btn");
headButton.addEventListener("click", headRequest);
```

[Headers](https://developer.mozilla.org/en-US/docs/Web/API/Response/headers)
[headers.get()](https://developer.mozilla.org/en-US/docs/Web/API/Headers/get)
[Fetch Signature](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch)

## Using POST requests

```
function postRequest() {
  // use the FormData interface to easily grab data from forms.
  const formData = new FormData(document.getElementById("msg-form"));
  fetch("http://localhost:5000/", {
    method: "POST",
    body: formData
  })
    .then(validateResponse)
    .then(readResponseAsText)
    .then(showText)
    .catch(logError);
}
const postButton = document.getElementById("post-btn");
postButton.addEventListener("click", postRequest);

```

[FormData](https://developer.mozilla.org/en-US/docs/Web/API/FormData)

## CORS and custom headers

```
function postRequest() {
  // use the FormData interface to easily grab data from forms.
  const formData = new FormData(document.getElementById("msg-form"));
  fetch("http://localhost:5001/", {
    method: "POST",
    body: formData
  })
    .then(validateResponse)
    .then(readResponseAsText)
    .then(showText)
    .catch(logError);
}
const postButton = document.getElementById("post-btn");
postButton.addEventListener("click", postRequest);
```

error: \*\*\*\* has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource

```
function postRequest() {
  // use the FormData interface to easily grab data from forms.
  const formData = new FormData(document.getElementById("msg-form"));
  fetch("http://localhost:5001/", {
    method: "POST",
    body: formData,
    mode: "no-cors"
  })
    .then(logResult)
    .catch(logError);
}
const postButton = document.getElementById("post-btn");
postButton.addEventListener("click", postRequest);
```

Using mode: no-cors allows fetching an opaque response. This allows use to get a response, but prevents accessing the response with JavaScript (which is why we can't use validateResponse, readResponseAsText, or showResponse). The response can still be consumed by other APIs or cached by a service worker.

[cross-origin resource sharing](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)
