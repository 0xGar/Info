
Below is a basic API template for Golang and JavaScript, with CORS configured. 

Without CORS, or Cross-Origin Resource Sharing, you may run into problems during development. 
By default, modern browsers block JavaScript from communicating with external domains (blocks Cross-Origin Resource Sharing), 
which for this context includes when:

1) The domain is different than the domain serving the JavaScript (e.g., cannot communicate between http://www.YourDomain.com and http://www.NotYourDomain.com).
1) The same domain but different ports (e.g., cannot communicate between 127.0.0.1:3000 and 127.0.0.1:8000).

But more accurately, the web browser will ask the server what it wants to do:

Scenario 1:

`Browser: What do you want to do?`

`Server: No response.`

`Browser: All right, I'll block everything to be safe.`


Senario 2:

`Browser: What do you want to do?`

`Server: `If the client is visiting from www.example.com, let them through; otherwise, throw an error or something.`

`Browser: Roger that. The user is from www.example.com, so letting them through.`


The above communication takes place within the page header. Therefore, all the API server has to do is serve the page normally but with a modified header.

Note: I should point out that the above example is more prepresentative of prefetching, which is when the browser sends an OPTIONS
request prior to sending a GET or POST request, to know where things stand. This is true in the case of
sending JSON data, but the browser will sometimes just send one request instead of two. We may have to account
for both situations on the server side.

In any event, sometimes Senario 1 is desirable (e.g., not worrying about CORS and going about your day).
If you're using a technology like Next.js to prerender a static website,  then your JavaScript API calls
will be done by the server instead of the browswer -- so no problem encountered.
But if you're hydrating your content, or having the client make API requests, then the browser will be involved
and you'll be faced with the CORS problem.

The below code satisfies the cross-origin problem. The most notable line is:

```
   originRequest := handlers.AllowedOrigins([]string{"www.YourDomain.com"})
```

This tells the web browser: `Let the client connect to my server ONLY IF they're visiting from www.yourDomain.com.` 
(Use * instead of www.YourDomain.com for allowing ALL hosts; useful when you're unsure what domain to use using development)
Keep in mind that this is telling the web browser what to do, so people can bypass it if they feel inclined (e.g.,
by installing a browser plugin that modifies the Access-Control-Allow-Origin header). 
And that's okay. It's not about if someone bypasses it; it's about ensuring your website is reasonably accessible.

```

import (
  "github.com/gorilla/handlers"
  "github.com/gorilla/mux" 
)

func main() {

  router := mux.NewRouter()

  router.HandleFunc("/hello-world", HelloWorld).Methods("GET", "OPTIONS")
  
  originRequest := handlers.AllowedOrigins([]string{"*"}) // Specify * to allow all hosts; may be useful in devleopment
  headerRequest := handlers.AllowedHeaders([]string{"Content-Type", "X-Requested-With"})
  methodRequest := handlers.AllowedMethods([]string{"GET", "PUT", "DELETE", "PATCH", "POST", "HEAD", "OPTIONS"})

  log.Fatal( http.ListenAndServe(":8000", handlers.CORS(originRequest, headerRequest, methodRequest)(router)) )
}

func HelloWorld(w http.ResponseWriter, r *http.Request) {
  w.Write( []byte(`{"HelloWorld":"Hello World!"}`) )      
}

```

And for HTML/JavaScript:

```

<!doctype HTML>
<html>
  <body>
    <div id="helloWorld">
    </div>
  </body>
</html>

<script>
  async function helloWorld() {
       let properties = {
            method:"GET",
            headers:{
                 "Accept":"application/json",
                 "Content-Type":"application/json"
            }
       };

       let response = await fetch(url,properties).then(res => res.json());

       document.getElementById("helloWorld").innerHTML = response.HelloWorld;
  }
</script>

```
