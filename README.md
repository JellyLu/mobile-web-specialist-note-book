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

# Progressive Web Apps

## Scripting the service worker

```
// in .html file <script>
if ("serviceWorker" in navigator) {
  window.addEventListener("load", () => {
    navigator.serviceWorker
      .register("service-worker.js", {
          scope: "/below/"
        })
      .then(registration => {
        console.log("Service worker is registered", registration);
      })
      .catch(error => {
        console.log("Registration failed: ", error);
      });
  });
}
```

The above code registers the service-worker.js file as a service worker.
It first checks whether the browser supports service workers.

```
// install new service worker
self.addEventListener("install", event => {
  console.log("Service Worker is installing...");
  self.skipWaiting();
});

// activate the new service worker when previous service worker is not used anymore
self.addEventListener("activate", event => {
  console.log("Service Worker is activating...");
});

```

The browser detects a byte difference between the new and existing
service worker file (because of the added comment),
so the new service worker is installed.
Since only one service worker can be active at a time (for a given scope),
even though the new service worker is installed, it isn't activated until
the existing service worker is no longer in use.
By closing all pages under the old service worker's control,
we are able to activate the new service worker.

The browser detects a byte difference between the new and existing
service worker file (because of the added comment),
so the new service worker is installed.
Since only one service worker can be active at a time (for a given scope),
even though the new service worker is installed,
it isn't activated until the existing service worker is no longer in use.
By closing all pages under the old service worker's control,
we are able to activate the new service worker.

[service worker lifecycle](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle)
[navigator.serviceWorker.register()](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerContainer/register)

## Caching files with the service worker

```
const cacheName = "cache-sw-v2";
const urlsToCache = [
  "/",
  "/style/main.css",
  "/images/birds_medium.jpg",
  "/images/horses_medium.jpg",
  "/images/still_life_medium.jpg",
  "/images/volt_medium.jpg",
  "/pages/404.html",
  "/pages/offline.html",
  "/index.html"
  // other dynamic urls
];

self.addEventListener("install", event => {
  self.skipWaiting();
  console.log("installing...");
  event.waitUntil(
    caches.open(cacheName).then(cache => {
      cache.addAll(
        urlsToCache.map(url => {
          return new Request(url, { mode: "no-cors" });
        })
      );
    })
  );
});
```

```
self.addEventListener("activate", event => {
  console.log("activating...");
  const cacheWhitelist = [cacheName];
  event.waitUntil(
    caches.keys().then(cacheNames => {
      return Promise.all(
        cacheNames.map(cache => {
          if (cacheWhitelist.indexOf(cache) === -1) {
            caches.delete(cache);
          }
        })
      );
    })
  );
});
```

We delete old caches in the activate event to ensure that
we aren't deleting caches before the new service worker
has taken over the page (in case the new service worker
activation fails, in which case we don't want to remove
the existing service worker's caches). To remove outdated
caches, we create an array of caches that are currently in
use and delete all other caches.

```
self.addEventListener("fetch", event => {
  event.respondWith(
    caches
      .match(event.request)
      .then(response => {
        if (response) {
          console.log("load cache", event.request.url);
          return response;
        }
        return fetch(event.request).then(res => {
          console.log("fetching ", event.request.url);
          if (res.status === 404) {
            console.log("reponse 404");
            return caches.match("/pages/404.html");
          }
          return caches.open(cacheName).then(cache => {
            cache.put(event.request, res.clone());
            return res;
          });
        });
      })
      .catch(error => {
        console.log("fetch error ", error);
        return caches.match("pages/offline.html");
      })
  );
});
```

We need to pass a clone of the response to cache.put,
because the response is a stream and can only be read once

[install event](https://developer.mozilla.org/en-US/docs/Web/API/InstallEvent)
[ExtendableEvent.waitUntil](https://developer.mozilla.org/en-US/docs/Web/API/ExtendableEvent/waitUntil)
[FetchEvent.respondWith](https://developer.mozilla.org/en-US/docs/Web/API/FetchEvent/respondWith)
[Cache.match](https://developer.mozilla.org/en-US/docs/Web/API/Cache/match)
[Cache.put](https://developer.mozilla.org/en-US/docs/Web/API/Cache/put)
[What happens when you read a response](https://jakearchibald.com/2014/reading-responses/)

## Add to Home Screen

```
// manifest.json
{
  "name": "Space Missions",
  "short_name": "Space Missions",
  "lang": "en-US",
  "start_url": "/index.html",
  "display": "standalone",
  "theme_color": "#FF9800",
  "background_color": "#FF9800",
  "icons": [
    {
      "src": "images/touch/icon-128x128.png",
      "sizes": "128x128"
    },
    {
      "src": "images/touch/icon-192x192.png",
      "sizes": "192x192"
    },
    {
      "src": "images/touch/icon-256x256.png",
      "sizes": "256x256"
    },
    {
      "src": "images/touch/icon-384x384.png",
      "sizes": "384x384"
    },
    {
      "src": "images/touch/icon-512x512.png",
      "sizes": "512x512"
    }
  ]
}
```

```
<link rel="manifest" href="manifest.json">

<meta name="mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="application-name" content="Space Missions">
<meta name="apple-mobile-web-app-title" content="Space Missions">
<meta name="theme-color" content="#FF9800">
<meta name="msapplication-navbutton-color" content="#FF9800">
<meta name="apple-mobile-web-app-status-bar-style" content="black-translucent">
<meta name="msapplication-starturl" content="/index.html">
<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">

<link rel="icon" sizes="128x128" href="/images/touch/icon-128x128.png">
<link rel="apple-touch-icon" sizes="128x128" href="/images/touch/icon-128x128.png">
<link rel="icon" sizes="192x192" href="icon-192x192.png">
<link rel="apple-touch-icon" sizes="192x192" href="/images/touch/icon-192x192.png">
<link rel="icon" sizes="256x256" href="/images/touch/icon-256x256.png">
<link rel="apple-touch-icon" sizes="256x256" href="/images/touch/icon-256x256.png">
<link rel="icon" sizes="384x384" href="/images/touch/icon-384x384.png">
<link rel="apple-touch-icon" sizes="384x384" href="/images/touch/icon-384x384.png">
<link rel="icon" sizes="512x512" href="/images/touch/icon-512x512.png">
<link rel="apple-touch-icon" sizes="512x512" href="/images/touch/icon-512x512.png">
```

#### Activating the install prompt

```
// Add an "Install app" button and banner to the top of index.html
// (just after the <main> tag)
<section id="installBanner" class="banner">
    <button id="installBtn">Install app</button>
</section>
```

```
// main.css
.banner {
  align-content: center;
  display: none;
  justify-content: center;
  width: 100%;
}
```

```
// index.html
<script>
    let deferredPrompt;
    window.addEventListener('beforeinstallprompt', event => {

      // Prevent Chrome 67 and earlier from automatically showing the prompt
      event.preventDefault();

      // Stash the event so it can be triggered later.
      deferredPrompt = event;

      // Attach the install prompt to a user gesture
      document.querySelector('#installBtn').addEventListener('click', event => {

        // Show the prompt
        deferredPrompt.prompt();

        // Wait for the user to respond to the prompt
        deferredPrompt.userChoice
          .then((choiceResult) => {
            if (choiceResult.outcome === 'accepted') {
              console.log('User accepted the A2HS prompt');
            } else {
              console.log('User dismissed the A2HS prompt');
            }
            deferredPrompt = null;
          });
      });

      // Update UI notify the user they can add to home screen
      document.querySelector('#installBanner').style.display = 'flex';
    });
  </script>
```

# IndexedDB

## check whether browser supports IndexedDB

```
if (!("indexedDB" in window)) {
  console.log("This browser doesn't support IndexedDB");
  return;
}
```

```
var dbPromise = idb.open("couches-n-things", 2, upgradeDB => {
  switch (upgradeDB.oldVersion) {
    case 0:

    case 1: {
      console.log("creating products object store");
      upgradeDB.createObjectStore("products", { keyPath: "id" });
    }
    default:
  }
});
```

idb.open takes a database name, version number, and optional callback
function for performing database updates (not included in the above code).
The version number determines whether the upgrade callback function is called.
If the version number is greater than the version number of the database
existing in the browser, then the upgrade callback is executed.

[idb](https://github.com/jakearchibald/idb)
[createObjectStore](https://developer.mozilla.org/en-US/docs/Web/API/IDBDatabase/createObjectStore)

#### add data to objectStore

```
dbPromise.then(db => {
  const tx = db.transaction("products", "readwrite");
  const store = tx.objectStore("products");

  return Promise.all(
    products.map(product => {
      return store.add(product);
    })
  )
    .then(() => {
      console.log("added all products");
    })
    .catch(error => {
      console.log("add product error: ", error);
      tx.abort();
    });
});
```

All database operations must be carried out within a transaction.

We add each object to the store inside a Promise.all.
This way if any of the add operations fail, we can catch
the error and abort the transaction. Aborting the transaction
rolls back all the changes that happened in the transaction
so that if any of the events fail to add, none of them will
be added to the object store. This ensures the database is
not left in a partially updated state.

Note: Specify the transaction mode as readwrite when
making changes to the database (that is, for changes
that use the `add`, `put`, or `delete` methods).

[transaction](https://developer.mozilla.org/en-US/docs/Web/API/IDBTransaction)
[add](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/add)

#### add index by name for prodcuts

```
var dbPromise = idb.open("couches-n-things", 3, upgradeDB => {
  switch (upgradeDB.oldVersion) {
    case 0:

    case 1: {
      console.log("creating products object store");
      upgradeDB.createObjectStore("products", { keyPath: "id" });
    }
    case 2: {
      console.log("creating a name index");
      const store = upgradeDB.transaction.objectStore("products");
      store.createIndex("name", "name", { unique: true });
    }
    default:
  }
});
```

[index](https://developer.mozilla.org/en-US/docs/Web/API/IDBIndex)
[createIndex](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/createIndex)

#### Searching the database

```
return dbPromise.then(db => {
  const tx = db.transaction("products", "readonly");
  const store = tx.objectStore("products");
  const index = store.index("name");
  return index.get(key);
});
```

```
// create range
let range;
if (lower !== "" && upper !== "") {
  range = IDBKeyRange.bound(lowerNum, upperNum);
} else if (lower === "") {
  range = IDBKeyRange.upperBound(upperNum);
} else {
  range = IDBKeyRange.lowerBound(lowerNum);
}
```

```
// create range
let range = IDBKeyRange.only(key);
```

```
// show cursor
var s = "";
function showRange(cursor) {
  if (!cursor) return;
  console.log("cursor at: ", cursor.value.name);
  s += "<h2>Price - " + cursor.value.price + "</h2> <p>";
  for (let field in cursor.value) {
    s += field + "=" + cursor.value[field] + "</br>";
  }
  s += "</p>";
  return cursor.continue().then(showRange);
}

dbPromise
  .then(db => {
    const tx = db.transaction("products", "readonly");
    const store = tx.objectStore("products");
    const index = store.index("price");
    return index.openCursor(range);
  })
  .then(showRange)
  .then(() => {
    if (s === "") {
      s = "No result";
    }
    document.getElementById("results").innerHTML = s;
  });
```

[get](https://developer.mozilla.org/en-US/docs/Web/API/IDBObjectStore/get)

[IDBCursor](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor)
[IDBKeyRange](https://developer.mozilla.org/en-US/docs/Web/API/IDBKeyRange)
[continue](https://developer.mozilla.org/en-US/docs/Web/API/IDBCursor/continue)

```
// get all

dbPromise.then(function(db) {
  // TODO 5.3 - use a cursor to display the orders on the page
  const tx = db.transaction("orders", "readonly");
  const store = tx.objectStore("orders");
  return store.getAll();
});

```

```
// update all products items
const tx = db.transaction("products", "readwrite");
const store = tx.objectStore("products");
return Promise.all(
  products.map(product => {
    store.put(product);
  })
).then(tx.complete);
```

## Promise

A Promise is an object representing the eventual completion or failure of an
asynchronous operation.

[Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
[Promise Introduction](https://developers.google.com/web/fundamentals/primers/promises)
[Promise All](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)
[Promise Race](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/race)

## Push Notification

### Notifications API

```
// Check for support
if (!("Notification" in window)) {
  console.log("This browser does not support notifications");
  return;
}
```

```
// Request permission
Notification.requestPermission(status => {
  console.log("Notification permission status", status);
});
```

Note: In production, requesting permissions on page load is a poor user experience. Rather, permissions are better requested when a user opts into a specific feature that requires some permission.

```
// Display the notification
if (Notification.permission === "granted") {
  const options = {
    body: "Buzz! Buzz!",
    icon: "../images/notification-flat.png",
    vibrate: [200, 100, 200, 100, 200, 100, 200],
    tag: "notify-me-sample",
    data: {
      dateOfArrival: Date.now(),
      primaryKey: 1
    },
    actions: [
      {
        action: "explore",
        title: "Go to the site",
        icon: "../images/checkmark.png"
      },
      {
        action: "close",
        title: "Close the notification",
        icon: "../images/xmark.png"
      }
    ]
  };
  navigator.serviceWorker.getRegistration().then(function(registration) {
    registration.showNotification("Notify me", options);
  });
}


function showNotification() {
  Notification.requestPermission(function(result) {
    if (result === 'granted') {
      navigator.serviceWorker.ready.then(function(registration) {
        registration.showNotification('Vibration Sample', {
          body: 'Buzz! Buzz!',
          icon: '../images/touch/chrome-touch-icon-192x192.png',
          vibrate: [200, 100, 200, 100, 200, 100, 200],
          tag: 'vibration-sample',
          data: {
            dateOfArrival: Date.now(),
            primaryKey: 1
          },
        });
      });
    }
  });
}
```

```
// Service Worker handle notification close
self.addEventListener("notificationclose", event => {
  const notification = event.notification;
  const dateOfArrival = notification.data.dateOfArrival;
  console.log("notification close, dateOfArrival - ", dateOfArrival);
});
```

```
// Service Worker handle notification click
self.addEventListener("notificationclick", event => {
  const notifcation = event.notification;
  const primaryKey = notifcation.data.primaryKey;
  const action = event.action;
  if (action === "explore") {
    clients.openWindow(`samples/page${primaryKey}.html`);
  } else {
    clients.openWindow("https://google.com");
  }
});
```

```
self.addEventListener("push", event => {
  let body;
  if (event.data) {
    body = event.data.text();
  } else {
    body = "Default body";
  }
  const options = {
    body: body,
    icon: "../images/notification-flat.png",
    vibrate: [200, 100, 200, 100, 200, 100, 200],
    tag: "push-me-sample",
    data: {
      dateOfArrival: Date.now(),
      primaryKey: 2
    },
    actions: [
      {
        action: "explore",
        title: "Go to the site",
        icon: "../images/checkmark.png"
      },
      {
        action: "close",
        title: "Close the notification",
        icon: "../images/xmark.png"
      }
    ]
  };

  event.waitUntil(
    clients.matchAll().then(client => {
      console.log("client----", client);
      if (client.length === 0) {
        self.registration.showNotification("Push me", options);
      } else {
        console.log("Application is already open!");
      }
    })
  );
});
```

[Notifications API](https://developer.mozilla.org/en-US/docs/Web/API/Notifications_API)
[showNotification](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerRegistration/showNotification)
[openWindow](https://developer.mozilla.org/en-US/docs/Web/API/Clients/openWindow)

### Push API

Generate the VAPID Keys

- `npm install web-push -g`

- `web-push generate-vapid-keys [--json]`

```
function subscribeUser() {
  // subscribe to the push service
  // applicationServerPublicKey is YOUR_VAPID_PUBLIC_KEY

  const applicationServerKey = urlB64ToUint8Array(applicationServerPublicKey);
  swRegistration.pushManager
    .subscribe({
      userVisibleOnly: true,
      applicationServerKey: applicationServerKey
    })
    .then(subscription => {
      console.log("User is subscribing - ", subscription);
      updateSubscriptionOnServer(subscription);
      isSubscribed = true;
      updateBtn();
    })
    .catch(error => {
      if (Notification.permission === "denied") {
        console.warn("Permission for notifications was denied");
      } else {
        console.error("Failed to subscribe the user: ", error);
      }
      updateBtn();
    });
}
```

```
function unsubscribeUser() {
  // unsubscribe from the push service
  swRegistration.pushManager
    .getSubscription()
    .then(subscription => {
      if (subscription) {
        return subscription.unsubscribe();
      }
    })
    .catch(error => {
      console.error("Error unsubscribing", error);
    })
    .then(() => {
      updateSubscriptionOnServer(null);
      isSubscribed = false;
      updateBtn();
    });
}
```

[Push API](https://developer.mozilla.org/en-US/docs/Web/API/Push_API)
[Push Event](https://developer.mozilla.org/en-US/docs/Web/API/PushEvent)
[Using VAPID](https://blog.mozilla.org/services/2016/04/04/using-vapid-with-webpush/)

### Best practices

- group messages
  Whenever you create a notification with a tag and there is already a
  notification with the same tag visible to the user, the system
  automatically replaces it without creating a new notification.

  Your can use this to group messages that are contextually relevant
  into one notification. This is a good practice if your site creates
  many notifications that would otherwise become overwhelming to the user.

- Hide notifications on page focus
  If there are several open notifications originating from our
  app, we can close them all when the user clicks on one.

```
// notificationclick
self.registration.getNotifications().then(notifications => {
  notifications.forEach(notification => {
    notification.close();
  });
});
```

Note: If you don't want to clear out all of the notifications,
you can filter based on the tag attribute by passing the tag
into the getNotifications function. See the getNotifications
reference on MDN for more information.

[getNotifications](https://developer.mozilla.org/en-US/docs/Web/API/ServiceWorkerRegistration/getNotifications)

- Notifications and tabs

Note: The clients.openWindow method can only open a window
when called as the result of a notificationclick event.
Therefore, we need to wrap the clients.matchAll() method
in a waitUntil, so that the event does not complete before
openWindow is called. Otherwise, the browser throws an error.

Get all the clients of the service worker and assign the
first "visible" client to the client variable. Then we open
the page in this client. If there are no visible clients,
open the page in a new tab.

```
// notificationclick
event.waitUntil(
  clients.matchAll().then(clis => {
    const client = clis.find(c => {
      return c.visibilityState === 'visible';
    });
    if (client !== undefined) {
      client.navigate('samples/page' + primaryKey + '.html');
      client.focus();
    } else {
      // there are no visible windows. Open one.
      clients.openWindow('samples/page' + primaryKey + '.html');
      notification.close();
    }
  })
);
```

[openWindow](https://developer.mozilla.org/en-US/docs/Web/API/Clients/openWindow)
