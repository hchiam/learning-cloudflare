# Learning serverless API functions with CloudFlare workers

Use a GUI (and some code) to create serverless API functions.

Just one of the things I'm learning. <https://github.com/hchiam/learning>

## Tutorials I followed

1. <https://gomakethings.com/getting-started-with-serverless-using-cloudflare-workers-and-vanilla-js>

2. <https://gomakethings.com/securing-serverless-functions-with-cloudflare-workers/>

## Steps

1. Sign up: <https://workers.cloudflare.com>
2. Subdomain
3. Worker
4. Code:

   ```js
   // add fetch listener to CloudFlare API:
   addEventListener("fetch", (event) => {
     event.respondWith(handleRequest(event.request));
   });

   /**
    * Respond to the request
    * @param {Request} request
    */
   async function handleRequest(request) {
     // note: these headers are good for general use, but we'll need to do other things for securing endpoints:
     const headers = new Headers({
       "Access-Control-Allow-Origin": "*",
       "Access-Control-Allow-Methods": "GET, HEAD, POST, OPTIONS",
       "Access-Control-Allow-Headers": "*",
     });
     const responseString = JSON.stringify({
       greeting: "Hi there, friend!",
     });
     const infoObject = {
       status: 200,
       headers: headers,
     };
     return new Response(responseString, infoObject);
   }
   ```

5. Save and Deploy
6. Try it:

   - Example: <https://lucky-rice-5b80.hchiam.workers.dev> returns `{"greeting":"Hi there, friend!"}`.

   - Or you can use fetch:

   ```js
   callCloudFlareApi();
   // returns: Object { greeting: "Hi there, friend!" }

   function callCloudFlareApi() {
     fetch("https://lucky-rice-5b80.hchiam.workers.dev")
       .then((response) => {
         if (response.ok) {
           return response.json();
         }
         return Promise.reject(response);
       })
       .then((data) => {
         console.log(data);
       })
       .catch((error) => {
         console.warn(error);
       });
   }
   ```

## Example worker that restricts what sites can call it by using CORS headers

```js
// add fetch listener to CloudFlare API:
addEventListener("fetch", (event) => {
  event.respondWith(handleRequest(event.request));
});

const allowedDomainOrigins = [
  "https://www.google.com",
  "https://lucky-rice-5b80.hchiam.workers.dev",
];

/**
 * Respond to the request
 * @param {Request} request
 */
async function handleRequest(request) {
  // note: these headers are good for general use, but we'll need to do other things for securing endpoints:
  const headers = new Headers({
    "Access-Control-Allow-Origin": "*",
    "Access-Control-Allow-Methods": "GET, HEAD, POST, OPTIONS",
    "Access-Control-Allow-Headers": "*",
  });
  // If domain is not allowed, return error code
  if (!allowedDomainOrigins.includes(request.headers.get("origin"))) {
    return new Response("Not allowed", {
      status: 403,
      headers: headers,
    });
  }
  const responseString = JSON.stringify({
    greeting: "Hi there, friend!",
  });
  const infoObject = {
    status: 200,
    headers: headers,
  };
  return new Response(responseString, infoObject);
}
```

To try this one out, first go to <https://www.google.com> or <https://lucky-rice-5b80.hchiam.workers.dev>. And then run this code inside the web dev console:

```js
callCloudFlareApi();
// returns: Object { greeting: "Hi there, friend!" }

function callCloudFlareApi() {
  fetch("https://late-resonance-5cbf.hchiam.workers.dev")
    .then((response) => {
      if (response.ok) {
        return response.json();
      }
      return Promise.reject(response);
    })
    .then((data) => {
      console.log(data);
    })
    .catch((error) => {
      console.warn(error);
    });
}
```
