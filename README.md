# Learning serverless API functions with CloudFlare workers

Use a GUI (and some code) to create serverless API functions.

Just one of the things I'm learning. <https://github.com/hchiam/learning>

## Tutorial I followed

<https://gomakethings.com/getting-started-with-serverless-using-cloudflare-workers-and-vanilla-js>

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
