# HTTP protocol, create-react-app configuration, and Cookies setting

* [Create-React-App env configuration](http-protocol-create-react-app-configuration-and-cookies-setting.md#create-react-app-env-configuration)
* [Setting cookies in the Client and Server](http-protocol-create-react-app-configuration-and-cookies-setting.md#setting-cookies-in-the-client-and-server)

Data exchanges between different devices require a compatible data format, and the set of rules that defines how the data is transmitted, received, and understood is called a <mark style="background-color:blue;">protocol</mark>.

**HTTP** (Hypertext Transfer Protocol) is a **stateless** and **extensible** protocol that exchanges resources on a **client-server** architecture, designed in 1990.

The client and server exchange information through independent **request-response** messages, as opposed to streams of data. Each message is **self-contained** and unrelated to previous messages, even if they share the same connection.                                                                                                              During this communication, **proxies** relay and forward information between the client and server, while performing operations such as _caching_ and _authentication_.

<figure><img src="../../.gitbook/assets/client-server-chain.png" alt="" width="563"><figcaption><p>A client-server communication</p></figcaption></figure>

It is **extensible**, allowing its messages to include **custom headers**, such as _Content-Type_ for JSON post bodies, as well as new methods and status codes, which enables the addition of new information and functionalities.

It is **stateless**, meaning that each request doesn't automatically establish a session or create storage data. However, we can create and share session state by using **cookies** in either the client request or server response headers, which will be included subsequent requests, allowing us to maintain context and current state across multiple HTTP requests.

The structure of **HTTPS** request and response messages consists of the following components:

```css
Request:
HTTP method (a verb or noun describing the operation requested by the client)
URL path of the fetched resource
Additional headers sent for information
Response:
Status code and message
Response headers set by the server
```

<figure><img src="../../.gitbook/assets/RequestResponse.png" alt="" width="563"><figcaption><p>Request and Response messages</p></figcaption></figure>

For more information on specific request and response details, refer to [**CORS**](cors-implementation.md) (Cross-Origin Resource Sharing) documentation.

The **Fetch API** is built on top of the HTTP protocol, enabling JavaScript to send and receive HTTP requests, check the [API section](../../react/react-3/) for more.

**HTTPS** is an extension of HTTP, with the added 'S' standing for security, which encrypts data in transit to provide a secure connection between the client and server.

### CREATE-REACT-APP env configuration

Create-React-App is a **command-line interface** tool that creates a pre-configured **ReactJS** project.

We can edit the built-in environment variables of the app configuration, by creating an **.env** file at the root of the project.

```css
HTTPS: it enables HTTPS in the development (true/false) 
PORT: specifies the custom port (default is 3000)
HOST: customises the host (default is localhost)
CI: continous integration envirodment (true/false)

//CI configures the deployment process
//if true any warning will be treated as a failore to deploy.
```

### Setting cookies in the Client and Server

Introduced in **1994**, a cookie is a small piece of text data used to store various types of data during a **user session**. Any stored data gets **parsed** into its original type, which can be maintained during multiple sessions depending on its **expiration** date.

Cookie data is stored and retrieved from a protected area in the **file system**, rather than from RAM.  On _desktop_ devices, the text data is stored in a **SQLite** file.  In contrast, on _mobile_ devices, cookie data is **sandboxed**, meaning it is isolated from other apps and stored in a separate area.                                This sandboxing is a security feature that prevents a single app from accessing data from other apps.

Cookies can originate from both the server and the browser. They can be accessed and edited in the browser using the cookie property of the **Document** object.                                                                                The cookie syntax is represented as a string, but is stored as an **object** in the browser.

```jsx
//In the browser only the name/value pair will be visible
document.cookie = 
  "sessionId=biscotto; Path=/; Domain=localhost; Max-Age=15; Secure; SameSite=None`"
console.log( document.cookie )    //sessionId=biscotto

//In the server
res.cookie(name, value, [options])
```

Cookies are used to record requests from the **same user**, and can store small amounts of data, limited to a recommended maximum size of 4kb; for larger amounts, alternatives like the Storage API can be used.

Once set, **cookies** are included in subsequent **request headers**, enabling the storage of session data, such as user state and authentication tokens. This allows the browser to retrieve the data for personalization, tracking, and session management purposes, enabling **stateless HTTP** to maintain user context.

```jsx
//Only the name/value pairs

app.get("/testo", (req, res)=> {
  console.log( req.cookies )  //{username: 'john donny', username1: 'john doe'}
}
```

When making a **fetch request** from a browser, if the request either **receives** a cookie or **sends** it to the server, the <mark style="background-color:blue;">credentials</mark> property must be included in the fetch **options**.                                                              This is necessary as the default behavior of the Fetch API is to omit cookies to prevent CSRF attacks. Additionally, the server must also include the **credentials** property in its **CORS** options to allow the credentials to be sent and received.

<pre class="language-jsx"><code class="lang-jsx"><strong>//browser allowing cookies
</strong>let options= {
  method: "GET",
  credentials: "include"
}

//Server allowing cookies
let corsOrigin = {
  origin: "https://localhost:3000",
  credentials: true
}
</code></pre>

On the server side, cookies can be set using either the **res.cookie()** method or the **Set-Cookie** header, but only the Set-Cookie header makes the cookie visible in the document.cookie property.

{% hint style="info" %}
The **httpOnly** property makes the cookie inaccesible from the javascript.                                                                   The **expire** and **max-age** serve the same purpose.                                                                                                                      The **domain** and path properties define the scope of the cookie.                                                                 The **same-site** property will send the cookie response depending on the origin of the request. if set to "none" it needs the "**secure**" flag so that it's only sent to a secure channel (HTTPS)
{% endhint %}

```jsx
//Expires is an absolute Date() while max-age is relative to the current
//path= "example.com" includes subdomains like "foo.example.com"
res.header("Set-Cookie", `username="john doe"; Path=/; 
  Domain=localhost; Max-Age=20; Secure; httpOnly; SameSite=None`);

res.cookie( "username", "john doe", 
 {path: '/', domain: 'localhost', expires: new Date(new Date().getTime() +20 *1000),
  secure: true, sameSite: "none", httpOnly: true})
```

On google you can find the cookies in the **application**> cookies, while in firefox in archiviation> cookies.

<figure><img src="../../.gitbook/assets/sentcookies.jpg" alt=""><figcaption><p>Cookies sent to the browser</p></figcaption></figure>

1
