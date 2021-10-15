# Authentication - Cookie

Cookie authentication uses HTTP cookies to make authenticated requests.

### How it works from a PowerShell perspective

1. The client sends a request including the login credentials to the API.

```powershell
$response  = Invoke-WebRequest -uri 'https://localhost:44303/api/Login?username=Admin&password=Password' -Method Post
```

2. If the credentials are valid, the API sends back the response headers. The response headers include a _Set-Cookie_ header that contains a uniqueId.

![responseCookie](.\assets\Authentication-01-CookieAuth-1.png)

3. In order to make authenticated requests to the API, a _session_ object must be created. The session object contains a [System.Net.CookieContainer] object that holds the cookie.

We do this by making the same request -including the credentials- but this time we add the _-SessionVariable_ parameter. In this example it is set to _sessionWithCookie_.

```powershell
Invoke-WebRequest -uri 'https://localhost:44303/api/Login?username=Admin&password=Password' -Method Post -SessionVariable sessionWithCookie
```

The cookie can be viewed using the _GetCookie()_ method. This method takes in a Uri. (The base Uri to the API). This will also display when the cookie will expire.

```powershell
$sessionWithCookie.Cookies.GetCookies("https://localhost:44303")
```

![responseCookie](.\assets\Authentication-01-CookieAuth-2.png)

To make an authenticated request to the API, we will need to specify the _-WebSession_ parameter.

4. Make an authenticated GET request to the API.

```powershell
Invoke-WebRequest -uri 'https://localhost:44303/api/FindPet/Teddy' -Method Get -WebSession $sessionWithCookie
```

The API will then use the uniqueId from the cookie to verify if the request is indeed authenticated. If so, it will return the response.

![responseCookie](.\assets\Authentication-01-CookieAuth-3.png)

> In this example we use _Invoke-WebRequest_. The same principals can by applied using _Invoke-RestMethod_. Although, be aware that _Invoke-RestMethod_ on Windows PowerShell does not return the response headers. On PowerShell Core, the response headers will be returned when specifying the _-ResponseHeadersVariable_ parameter.