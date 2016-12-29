Every few months I find myself looking up up the syntax of a relatively obscure HTTP header. Regularly I find myself wondering why there isn’t a good definitive list of common HTTP Response headers anywhere. Usually the lists on the Internet are missing half a dozen HTTP headers. So I’ve taken care to gather a list all of the HTTP response headers I could find. Hopefully this is useful to you, and removes some of the mystique behind how HTTP works if you’ve never seen headers before.

Note: this does not include things like IncapIP or other proxy/service specific headers that aren’t standard, and nor does it include request headers.

Header	Example Value	Notes
Access-Control-Allow-Credentials	true	
Access-Control-Allow-Headers	X-PINGOTHER	
Access-Control-Allow-Methods	PUT, DELETE, XMODIFY	
Access-Control-Allow-Origin	http://example.org	
Access-Control-Expose-Headers	X-My-Custom-Header, X-Another-Custom-Header	
Access-Control-Max-Age	2520	
Accept-Ranges	bytes	
Age	12	
Allow	GET, HEAD, POST, OPTIONS	Commonly includes other things, like PROPFIND etc…
Alternate-Protocol	443:npn-spdy/2,443:npn-spdy/2	
Cache-Control	private, no-cache, must-revalidate	
Client-Date	Tue, 27 Jan 2009 18:17:30 GMT	
Client-Peer	123.123.123.123:80	
Client-Response-Num	1	
Connection	Keep-Alive	
Content-Disposition	attachment; filename=”example.exe”	
Content-Encoding	gzip	
Content-Language	en	
Content-Length	1329	
Content-Location	/index.htm	
Content-MD5	Q2hlY2sgSW50ZWdyaXR5IQ==	
Content-Range	bytes 21010-47021/47022	
Content-Security-Policy, X-Content-Security-Policy, X-WebKit-CSP	default-src ‘self’	Different header needed to control different browsers
Content-Security-Policy-Report-Only	default-src ‘self’; …; report-uri /csp_report_parser;	
Content-Type	text/html	Can also include charset information (E.g.: text/html;charset=ISO-8859-1)
Date	Fri, 22 Jan 2010 04:00:00 GMT	
ETag	“737060cd8c284d8af7ad3082f209582d”	
Expires	Mon, 26 Jul 1997 05:00:00 GMT	
HTTP	/1.1 401 Unauthorized	Special header, no colon space delimiter
Keep-Alive	timeout=3, max=87	
Last-Modified	Tue, 15 Nov 1994 12:45:26 +0000	
Link	; rel=”cononical”	rel=”alternate”
Location	http://www.example.com/	
P3P	policyref=”http://www.example.com/w3c/p3p.xml”, CP=”NOI DSP COR ADMa OUR NOR STA”	
Pragma	no-cache	
Proxy-Authenticate	Basic	
Proxy-Connection	Keep-Alive	
Refresh	5; url=http://www.example.com/	
Retry-After	120	
Server	Apache	
Set-Cookie	test=1; domain=example.com; path=/; expires=Tue, 01-Oct-2013 19:16:48 GMT	Can also include the secure and HTTPOnly flag
Status	200 OK	
Strict-Transport-Security	max-age=16070400; includeSubDomains	
Timing-Allow-Origin	www.example.com	
Trailer	Max-Forwards	
Transfer-Encoding	chunked	compress, deflate, gzip, identity
Upgrade	HTTP/2.0, SHTTP/1.3, IRC/6.9, RTA/x11	
Vary	*	
Via	1.0 fred, 1.1 example.com (Apache/1.1)	
Warning	Warning: 199 Miscellaneous warning	
WWW-Authenticate	Basic	
X-Aspnet-Version	2.0.50727	
X-Content-Type-Options	nosniff	
X-Frame-Options	deny	
X-Permitted-Cross-Domain-Policies	master-only	Used by Adobe Flash
X-Pingback	http://www.example.com/pingback/xmlrpc	
X-Powered-By	PHP/5.4.0	
X-Robots-Tag	noindex,nofollow	
X-UA-Compatible	Chome=1	
X-XSS-Protection	1; mode=block	
If I’ve missed any response headers, please let us know by leaving a comment and I’ll add it into the list. Perhaps at some point I’ll create a similar list for Request headers, if people find this helpful enough.

This entry was posted in Industry Observations, Technical Insight, Tools and Applications and taggedbrowser, HTTP headers on February 10, 2014 by Robert Hansen.
