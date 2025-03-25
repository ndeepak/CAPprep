**Methodology of finding CSRF vulnerabilities**
The methodology is to find state-changing requests, then try to bypass the security manually.
* Important rule: Always make an exploit code and run it to show the POC.
	* Is this request vulnerable to CSRF? yes if you can give me a page to exploit me
* Fuzz to find hidden path and parameters, they mostly are not protected against CSRF
* Pay attention to the SameSite flag of the cookie (Lax or Strict)
	* If SameSite is not None, you need an XSS in any subdomains
	* If SameSite is Lax, you can still exploit the CSRF with a top level navigation exploit (GET only)
* JSON request with cookies are more likely vulnerable to CSRF, why?
	* The endpoint might accept other content types
	* JSON requests with token are safe (why?)
* Does mobile endpoints work with Authentication Cookies?
* CSRF Bypass
	* Change the content type (why?)
	* `application/json -> application/x-www-form-urlencoded`
	* Sometime be creative (do the following requests trigger the pre-flight?)
	`application/json --> text/plain; application/json`
	`application/json --> appliaction/x-www-form=urlencoded; application/json`
	* Send empty value or remove the CSRF token
	```
	data=values&CSRF_TOKEN=token  -> data=values&CSRF_TOKEN=
	data=values&CSRF_TOKEN=token --> data=values
	```
	*  Change the token with a valid one (your token)
	`data=values&CSRF_TOKEN=token -> data=values&CSRF_TOKEN=your_token`
	* Verb tamper the request, works? try previous bypasses
	```
	POST / HTTP/1.1
	username=user&CSRF_TOKEN=value

	GET /?username=user&CSRF_TOKEN=value HTTP/1.1
	```
* Put the headers' CSRF token in body (not vice versa, why?), works? try previous bypasses

