Sample extension to demonstrate integration with an OAuth service.
2	
3	Overview
4	--------
5	This sample demonstrates the use of OAuth to authorize against 
6	Google's Contacts API inside of an extension.  It implements a library which
7	may be reused generically to authorize requests to any 3-legged OAuth API.
8	
9	Library
10	-------
11	The library files are:
12	 * chrome_ex_oauth.html
13	 * chrome_ex_oauth.js
14	 * chrome_ex_oauthsimple.js
15	
16	To use these files, place them in the root of your extension and include both
17	.js files in your background page in the following order:
18	
19	  <script type="text/javascript" src="chrome_ex_oauthsimple.js"></script>
20	  <script type="text/javascript" src="chrome_ex_oauth.js"></script>   
21	  
22	To initialize the API, create a ChromeExOAuth object in the background page:
23	
24	      var oauth = ChromeExOAuth.initBackgroundPage({
25	        'request_url'     :  <OAuth request URL>, 
26	        'authorize_url'   :  <OAuth authorize URL>,  
27	        'access_url'      :  <OAuth access token URL>,  
28	        'consumer_key'    :  <OAuth consumer key>,  
29	        'consumer_secret' :  <OAuth consumer secret>,  
30	        'scope'           :  <scope parameter for this auth>,
31	        'app_name'        :  <application name, not used by all OAuth providers>
32	      }); 
33	
34	Call the authorize() function to redirect the user to the OAuth provider in
35	order to obtain an access token.  The client library abstracts most of this 
36	process, so all you need to do is pass a callback to the authorize() function
37	and a new tab will open and redirect the user.  If the library already has
38	stored an access token for the current scope, then no tab will be opened.  In
39	either case, the callback will be called with the resulting token and secret.
40	
41	      oauth.authorize(onAuthorized);
42	      
43	There is no need to store the token and secret, as this library already stores
44	these values in localStorage.  Once the callback you specified is called, you
45	can call the sendSignedRequest function to send OAuth-signed requests to the
46	API.  The sendSignedRequest call takes an url to fetch, a callback function,
47	and an optional parameter object as its arguments.  The callback is passed
48	the response text as well as the XMLHttpRequest object which was used to 
49	make the request as its arguments.
50	  
51	      function callback(text, xhr) {
52	        //...
53	      };
54	      
55	      function onAuthorized() { 
56	        var url = <API url inside of the requested scope>;
57	        var request = {
58	          'method' : 'GET',
59	          'parameters' : {
60	             <Any request parameters as key : value pairs>
61	          }
62	        }
63	        oauth.sendSignedRequest(url, callback, request);
64	      };
65	      oauth.authorize(onAuthorized);
66	      
