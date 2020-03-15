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

## Create Amazing Forms

### Design efficient forms

- Use existing data to pre-populate fields and be sure to enable autofill.

Make sure your forms have no repeated actions, only as many fields as necessary, and take advantage of autofill, so that users can easily complete forms with pre-populated data.

- Use clearly-labeled progress bars to help users get through multi-part forms.

Progress bars and menus should accurately convey overall progress through multi-step forms and processes.

- Provide visual calendar so users don’t have to leave your site and jump to the calendar app on their smartphones.

### Choose the best input type

- Choose the most appropriate input type for your data to simplify input.

| Input type     | note                                                                                                                                                                  |
| -------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| url            | URL. It must start with a valid URI scheme, for example http://, ftp:// or mailto:                                                                                    |
| tel            | phone numbers. It does not enforce a particular syntax for validation, so if you want to ensure a particular format, you can use pattern.                             |
| email          | email addresses, and hints that the @ should be shown on the keyboard by default. You can add the multiple attribute if more than one email address will be provided. |
| search         | A text input field styled in a way that is consistent with the platform's search field.                                                                               |
| number         | numeric input, can be any rational integer. Additionally, iOS requires using pattern="\d\*" to show the numeric keyboard.                                             |
| range          | number input, but unlike the number input type, the value is less important. It is displayed to the user as a slider control.                                         |
| datetime-local | a date and time value where the time zone provided is the local time zone.                                                                                            |
| date           | a date (only) with no time zone provided.                                                                                                                             |
| time           | a time (only) with no time zone provided.                                                                                                                             |
| week           | a week (only) with no time zone provided.                                                                                                                             |
| month          | a month (only) with no time zone provided.                                                                                                                            |
| color          | For picking a color.                                                                                                                                                  |

Caution: Remember to keep localization in mind when choosing an input type, some locales use a dot (.) as a separator instead of a comma (,)

- Offer suggestions as the user types with the datalist element.

datalist lets the browser suggest autocomplete options as the user types.
Unlike select elements where users must scan long lists to find the value
they're looking for, and limiting them only to those lists, datalist element
provides hints as the user types.

```
<label for="frmFavChocolate">Favorite Type of Chocolate</label>
<input type="text" name="fav-choc" id="frmFavChocolate" list="chocType">
<datalist id="chocType">
  <option value="white">
  <option value="milk">
  <option value="dark">
</datalist>
```

Note: The datalist values are provided as suggestions, and users are not
restricted to the suggestions provided.

### Label and name inputs properly

- Always use labels on form inputs, and ensure they're visible when the field is in focus.

The label element provides direction to the user, telling them what information
is needed in a form element.
Applying labels to form elements also helps to improve the touch target size:
the user can touch either the label or the input in order to place focus on the
input element.

```
<label for="frmAddressS">Address</label>
<input
  type="text"
  name="ship-address"
  required
  id="frmAddressS"
  placeholder="123 Any Street"
  autocomplete="shipping
  street-address"
>

// or
<label>
  Address
  <input
    type="text"
    name="ship-address"
    required
    placeholder="123 Any Street"
    autocomplete="shipping
    street-address"
  >
</label>
```

Labels and inputs should be large enough to be easy to press.
In portrait viewports, field labels should be above input elements,
and beside them in landscape. Ensure field labels and the corresponding
input boxes are visible at the same time. Be careful with custom scroll
handlers that may scroll input elements to the top of the page hiding
the label, or labels placed below input elements may be covered by the
virtual keyboard.

- Use placeholders to provide guidance about what you expect.

The placeholder attribute provides a hint to the user about what's expected
in the input, typically by displaying the value as light text until the user
starts typing in the element.

- To help the browser auto-complete the form, use established name's for elements and include the autocomplete attribute.

give hints to the browser by providing both the name attribute and the
autocomplete attribute on each input element.

Note: Chrome requires input elements to be wrapped in a <form> tag to
enable auto-complete. If they're not wrapped in a form tag, Chrome will
offer suggestions, but will not complete the form.

｜ Content type ｜ name attribute ｜ autocomplete attribute ｜
｜-｜-｜-｜
｜ Name ｜ name fname mname lname ｜

- name (full name)
- given-name (first name)
- additional-name (middle name)
- family-name (last name)｜
  ｜ Email ｜ email| email|
  ｜ Address ｜ address city region province state zip zip2 postal country ｜- For one address input:
  street-address
- For two address inputs:
  address-line1
  address-line2
- address-level1 (state or province)
- address-level2 (city)
- postal-code (zip code)
- country ｜
  ｜ Phone ｜ phone mobile country-code area-code exchange suffix ext ｜ tel ｜
  ｜ Credit Card ｜ ccname cardnumber cvc ccmonth ccyear exp-date card-type ｜
  cc-name
  cc-number
  cc-csc
  cc-exp-month
  cc-exp-year
  cc-exp
  cc-type
  ｜
  ｜ Usernames ｜ username ｜ username ｜
  ｜ Passwords ｜ password ｜

  - current-password (for sign-in forms)
  - new-password (for sign-up and password-change forms)｜

Note: Use either only street-address or both address-line1 and
address-line2. address-level1 and address-level2 are only necessary
if they're required for your address format.

On some forms, for example the Google home page where the only thing
you want the user to do is fill out a particular field, you can add
the autofocus attribute.

```
  <input type="text" autofocus ...>
```

Note: Mobile browsers ignore the autofocus attribute, to prevent the
keyboard from randomly appearing.

Note: Be careful using the autofocus attribute because it will steal
keyboard focus and potentially preventing the backspace character
from being used for navigation.

# Accessibility

Web pages and applications should be accessible to all users,
including those with visual, motor, hearing, and cognitive impairments.
Using HTML, CSS, JavaScript, you'll be asked to show you can integrate
accessibility best practices into your web pages and applications by:

- Using a logical tab order for tabbed navigation
- Using skip navigation links to bypass navbars and asides
- Avoiding hidden content on the page that impedes tab navigation
- Using heading tags that provide a logical page structure
- Using text alternatives to visual content, such as alt, <label>, aria-label, and aria-labelledby
- Applying color contrast to all elements and following accessibility best practices
- Sending timely alerts for urgent messages using aria-live
- Using semantic markup to keep content and presentation separate when appropriate

## Focus

Focus determines where keyboard events go in the page at any given moment.
Ensuring that you design your page with a logical tab order is an important step.
There's generally no need to focus something if the user can't interact with it.

Keyboard Accessible: Make all functionality available from a keyboard

[Keyboard](https://webaim.org/standards/wcag/checklist#sc2.1.1)
[Meaningful Sequence](https://webaim.org/standards/wcag/checklist#sc1.3.2)
[airline site mockup page](http://udacity.github.io/ud891/lesson2-focus/01-basic-form/)

- using only keyboard input, when you successfully complete the form with
  no input errors and activate the Search button, the form will simply clear
  and reset.
- reading and navigation order, as determined by code order, should be
  logical and intuitive.
- should prevent the panel from gaining focus when it's off screen, and only
  allow it to be focused when the user can interact with it.
  - `document.activeElement` from the console to figure out which element
    is currently focused.
  - Once you know which off screen element is being focused, you can set it
    to `display: none` or `visibility: hidden`, and then set it back
    to `display: block` or `visibility: visible` before showing it to the user.
- use the tabindex HTML attribute to explicitly set an element's (restrict it
  to custom interactive controls) tab position
- The element can be focused by pressing the Tab key, and the element can
  be focused by calling its focus() method

  - tabindex="0": Inserts an element into the natural tab order.
  - tabindex="-1": Removes an element from the natural tab order, but the
    element can still be focused by calling its focus() method
  - Using a tabindex greater than 0 is considered an anti-pattern.
  - Managing focus at the page level
    - identify the selected content area, give it a tabindex of -1 so that it
      doesn't appear in the natural tab order, and call its focus method
  - Managing focus in components
  - Modals and keyboard traps
    - keyboard focus should never be locked or trapped at one particular page element.

[Accessible Rich Internet Applications (ARIA) Authoring Practices guide](https://www.w3.org/TR/wai-aria-practices/)
[No Keyboard Trap](https://webaim.org/standards/wcag/checklist#sc2.1.2)
[Sample codebase](https://github.com/udacity/ud891.git)

## Semantics Build-in

GUI affordances are thus specifically designed to be unambiguous:
buttons, check boxes, and scroll bars are meant to convey their usage with
as little training as possible.

This non-visual exposure of an affordance's use is called its semantics.

screen reader tells you some information about each interface element.

- The element's role or type, if it is specified (it should be).
- The element's name, if it has one (it should).
- The element's value, if it has one (it may or may not).
- The element's state, e.g., whether it is enabled or disabled (if applicable).

Just as the rendering engine uses the native code to construct a
visual interface, the screen reader uses the metadata in the DOM
nodes to construct an accessible version

[Blind people use VoiceOver](https://www.youtube.com/watch?v=QW_dUs9D1oQ)
[ChromeVox lite demo page](http://udacity.github.io/ud891/lesson3-semantics-built-in/02-chromevox-lite/)

The accessibility tree is what most assistive technologies interact with.

- making sure that the important elements in the page have the correct
  `accessible roles`, `states`, and `properties`, and that we specify
  `accessible names` and `descriptions`.
- Using a native element also has the benefit of taking care of keyboard interactions for us
- screen readers will announce an element's `role`, `name`, `state`, and `value`
- providing text alternatives for any non-text content is
  [the very first item on the WebAIM checklist](https://webaim.org/standards/wcag/checklist#g1.1).

To associate a label with an element, either

```
// Place the input element inside a label element
<label>
  <input type="checkbox">Receive promotional offers?
</label>
```

```
// Use the label's for attribute and refer to the element's id
<input id="promo" type="checkbox">
<label for="promo">Receive promotional offers?</label>
```

#### Text Alternatives for Images

- `alt` allows you to specify a simple string to be used
  any time the image isn't available
- `alt` differs from `title`, or any type of caption, in
  that it is only used if the image is not available.
- all images should have an alt attribute, but they need not all have text.  
  Important images should have descriptive alt text that succinctly describes
  what the image is, while decorative images should have empty alt attributes
  — that is, `alt=""`.

#### Navigating content

An appropriate heading structure is more important than ever.[DOM order mater]
the heading levels are nested to indicate parent-child relationships among content blocks.

- [Semantic markup is used to designate headings](https://webaim.org/standards/wcag/checklist#sc1.3.1)
- [heading structure as a technique for bypassing blocks of content](https://webaim.org/standards/wcag/checklist#sc2.4.1)
- []()

#### ARIA (Accessible Rich Internet Applications)

Using ARIA attributes, can give the element the missing information
so the screen reader can properly interpret it.

ARIA doesn't augment any of the element's inherent behavior; it won't make
the element focusable or give it keyboard event listeners.

ARIA can add extra label and description text that is only exposed to
assistive technology APIs
ARIA can express semantic relationships between elements that extend
the standard parent/child connection
ARIA can make parts of the page "live", so they immediately inform
assistive technology when they change.

- [ARIA in HTML](https://www.w3.org/TR/html-aria/#sec-strong-native-semantics)
- [ ARIA Authoring Practices document](https://www.w3.org/TR/wai-aria-practices-1.1/)
- [ARIA guideline](https://www.w3.org/TR/wai-aria/)

- `aria-label` allows us to specify a string to be used as the accessible label.
  This overrides any other native labeling mechanism.
- `aria-labelledby` allows us to specify the ID of another element in the DOM
  as an element's label.
  `aria-labelledby` overrides all other name sources for an element(like `aria-label` and `label`)

  Relationships Attributes:

  - `aria-owns`
  - `aria-activedescendant`
  - `aria-labelledby`
  - `aria-describedby`
  - `aria-controls`
  - `aria-posinset`
  - `aria-setsize`

Hide Content

Do not use this CSS if you want the content to be read by a screen reader:
`visibility: hidden`
`display: none`
`width:0px, height:0px or other 0 pixel sizing techniques`

Visually hiding content that will be read by a screen reader:
`text-indent: -10000px;`
`hidden`

```
.hidden {
  position:absolute;
  left:-10000px;
  top:auto;
  width:1px;
  height:1px;
  overflow:hidden;
}

<div class="hidden">This text is hidden.</div>

```

```
<label for="password">Password *</label>
<input id="password" type="password" aria-describedby="pw-help">
<div id="pw-help" hidden>password must be more than 8 digits</div>
```

[Techniques for hiding text](https://webaim.org/techniques/css/invisiblecontent/#techniques)

# SEO

[Document does not have a meta description](https://web.dev/meta-description/?utm_source=lighthouse&utm_medium=devtools)

`<meta name="Description" content="Put your description here.">`
