# 如何在 Node 中用 Javascript 实现 Http Digest (RFC 2617)，使用 Passport。射流研究…

> 原文：<https://dev.to/leolanchas/how-to-implement-http-digest-rfc-2617-in-javascript-in-node-using-passportjs-17l8>

当实现一个 API 时，需要在每个请求中保护它的端点。所以你需要验证它们。

PassportJS 是 [Node.js](https://nodejs.org/en/) 的认证中间件。它非常灵活和模块化，旨在服务于一个目的:验证请求。它可以不引人注目地放入任何基于 [Express](http://expressjs.com) 的网络应用程序中。

Passport 认识到每个应用程序都有独特的身份验证要求。被称为策略的身份验证机制被打包成单独的模块。应用程序可以选择采用哪种策略，而不会产生不必要的依赖。

基础与摘要

除了定义 HTTP 的认证框架，RFC 2617 还定义了基本认证和摘要认证方案。这两种方案都使用用户名和密码作为认证用户的凭证，并且通常用于保护 API 端点。

应该注意的是，依赖用户名和密码可能会对安全性产生负面影响，尤其是在服务器和客户端之间没有高度信任的情况下。在这些情况下，建议使用授权框架，如 [OAuth 2.0](https://github.com/jaredhanson/passport-oauth2#readme) 。

在这篇文章中，我将解释如何实现摘要方案。

我猜是 Node。JS 安装在您的系统上。还需要快递:

```
$ npm install express 
```

Enter fullscreen mode Exit fullscreen mode

和

通行证编号:

```
$ npm install passport 
```

Enter fullscreen mode Exit fullscreen mode

及其基本和消化策略:

```
$ npm install passport-http 
```

Enter fullscreen mode Exit fullscreen mode

源代码托管在 github:[https://github.com/leolanchas/http-digest-passport-js](https://github.com/leolanchas/http-digest-passport-js)，你可以在 C9 . io:[https://c9.io/leolanchas/http-digest-passport-js](https://c9.io/leolanchas/http-digest-passport-js)运行

第一个文件只能在 localhost 中进行测试(不需要在 c9 或真实的服务器中使用它)，因为请求的域不能为 null。所以如果从 github 克隆回购，首先需要做的是:

```
$ node Server.js 
```

Enter fullscreen mode Exit fullscreen mode

然后在你的浏览器上，你必须转到“localhost:2563 ”,它只会为你提供 Httpdigest.html(在那里你只需要点击按钮。).

Server.js'

```
var express = require('express');
var app = express();

app.all('/', function(req, res) { res.sendfile('digest.html'); } );

app.listen(2563); 
```

Enter fullscreen mode Exit fullscreen mode

这是一个简单的 html 文件，显示一个调用 HttpDigest 函数的按钮。

重要提示:为了简单起见，我省略了令牌生成。这个令牌应该通过 sdk 生成，并且在每次调用中也是不同的。否则很容易被拦截。

这个过程基本上由两个 http 请求组成，如下所示:[http://en.wikipedia.org/wiki/Digest_access_authentication](http://en.wikipedia.org/wiki/Digest_access_authentication)

您可以忽略脚本的第一部分。它是 md5 算法压缩的。

HttpDigest.html'

```
<!DOCTYPE html>
<html>
    <head>
        <style> body { font-family: consolas} table { border-collapse:collapse; } table, td, th { border:1px solid black;} td.H { text-align: center; } </style>
        <script src="http://code.jquery.com/jquery-latest.js"></script>
        <script>
            var hexcase=0;var b64pad="";function hex_md5(a){return rstr2hex(rstr_md5(str2rstr_utf8(a)))}function b64_md5(a){return rstr2b64(rstr_md5(str2rstr_utf8(a)))}function any_md5(a,b){return rstr2any(rstr_md5(str2rstr_utf8(a)),b)}function hex_hmac_md5(a,b){return rstr2hex(rstr_hmac_md5(str2rstr_utf8(a),str2rstr_utf8(b)))}function b64_hmac_md5(a,b){return rstr2b64(rstr_hmac_md5(str2rstr_utf8(a),str2rstr_utf8(b)))}function any_hmac_md5(a,c,b){return rstr2any(rstr_hmac_md5(str2rstr_utf8(a),str2rstr_utf8(c)),b)}function md5_vm_test(){return hex_md5("abc").toLowerCase()=="900150983cd24fb0d6963f7d28e17f72"}function rstr_md5(a){return binl2rstr(binl_md5(rstr2binl(a),a.length*8))}function rstr_hmac_md5(c,f){var e=rstr2binl(c);if(e.length>16){e=binl_md5(e,c.length*8)}var a=Array(16),d=Array(16);for(var b=0;b<16;b++){a[b]=e[b]^909522486;d[b]=e[b]^1549556828}var g=binl_md5(a.concat(rstr2binl(f)),512+f.length*8);return binl2rstr(binl_md5(d.concat(g),512+128))}function rstr2hex(c){try{hexcase}catch(g){hexcase=0}var f=hexcase?"0123456789ABCDEF":"0123456789abcdef";var b="";var a;for(var d=0;d<c.length;d++){a=c.charCodeAt(d);b+=f.charAt((a>>>4)&15)+f.charAt(a&15)}return b}function rstr2b64(c){try{b64pad}catch(h){b64pad=""}var g="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789+/";var b="";var a=c.length;for(var f=0;f<a;f+=3){var k=(c.charCodeAt(f)<<16)|(f+1<a?c.charCodeAt(f+1)<<8:0)|(f+2<a?c.charCodeAt(f+2):0);for(var d=0;d<4;d++){if(f*8+d*6>c.length*8){b+=b64pad}else{b+=g.charAt((k>>>6*(3-d))&63)}}}return b}function rstr2any(m,c){var b=c.length;var l,f,a,n,e;var k=Array(Math.ceil(m.length/2));for(l=0;l<k.length;l++){k[l]=(m.charCodeAt(l*2)<<8)|m.charCodeAt(l*2+1)}var h=Math.ceil(m.length*8/(Math.log(c.length)/Math.log(2)));var g=Array(h);for(f=0;f<h;f++){e=Array();n=0;for(l=0;l<k.length;l++){n=(n<<16)+k[l];a=Math.floor(n/b);n-=a*b;if(e.length>0||a>0){e[e.length]=a}}g[f]=n;k=e}var d="";for(l=g.length-1;l>=0;l--){d+=c.charAt(g[l])}return d}function str2rstr_utf8(c){var b="";var d=-1;var a,e;while(++d<c.length){a=c.charCodeAt(d);e=d+1<c.length?c.charCodeAt(d+1):0;if(55296<=a&&a<=56319&&56320<=e&&e<=57343){a=65536+((a&1023)<<10)+(e&1023);d++}if(a<=127){b+=String.fromCharCode(a)}else{if(a<=2047){b+=String.fromCharCode(192|((a>>>6)&31),128|(a&63))}else{if(a<=65535){b+=String.fromCharCode(224|((a>>>12)&15),128|((a>>>6)&63),128|(a&63))}else{if(a<=2097151){b+=String.fromCharCode(240|((a>>>18)&7),128|((a>>>12)&63),128|((a>>>6)&63),128|(a&63))}}}}}return b}function str2rstr_utf16le(b){var a="";for(var c=0;c<b.length;c++){a+=String.fromCharCode(b.charCodeAt(c)&255,(b.charCodeAt(c)>>>8)&255)}return a}function str2rstr_utf16be(b){var a="";for(var c=0;c<b.length;c++){a+=String.fromCharCode((b.charCodeAt(c)>>>8)&255,b.charCodeAt(c)&255)}return a}function rstr2binl(b){var a=Array(b.length>>2);for(var c=0;c<a.length;c++){a[c]=0}for(var c=0;c<b.length*8;c+=8){a[c>>5]|=(b.charCodeAt(c/8)&255)<<(c%32)}return a}function binl2rstr(b){var a="";for(var c=0;c<b.length*32;c+=8){a+=String.fromCharCode((b[c>>5]>>>(c%32))&255)}return a}function binl_md5(p,k){p[k>>5]|=128<<((k)%32);p[(((k+64)>>>9)<<4)+14]=k;var o=1732584193;var n=-271733879;var m=-1732584194;var l=271733878;for(var g=0;g<p.length;g+=16){var j=o;var h=n;var f=m;var e=l;o=md5_ff(o,n,m,l,p[g+0],7,-680876936);l=md5_ff(l,o,n,m,p[g+1],12,-389564586);m=md5_ff(m,l,o,n,p[g+2],17,606105819);n=md5_ff(n,m,l,o,p[g+3],22,-1044525330);o=md5_ff(o,n,m,l,p[g+4],7,-176418897);l=md5_ff(l,o,n,m,p[g+5],12,1200080426);m=md5_ff(m,l,o,n,p[g+6],17,-1473231341);n=md5_ff(n,m,l,o,p[g+7],22,-45705983);o=md5_ff(o,n,m,l,p[g+8],7,1770035416);l=md5_ff(l,o,n,m,p[g+9],12,-1958414417);m=md5_ff(m,l,o,n,p[g+10],17,-42063);n=md5_ff(n,m,l,o,p[g+11],22,-1990404162);o=md5_ff(o,n,m,l,p[g+12],7,1804603682);l=md5_ff(l,o,n,m,p[g+13],12,-40341101);m=md5_ff(m,l,o,n,p[g+14],17,-1502002290);n=md5_ff(n,m,l,o,p[g+15],22,1236535329);o=md5_gg(o,n,m,l,p[g+1],5,-165796510);l=md5_gg(l,o,n,m,p[g+6],9,-1069501632);m=md5_gg(m,l,o,n,p[g+11],14,643717713);n=md5_gg(n,m,l,o,p[g+0],20,-373897302);o=md5_gg(o,n,m,l,p[g+5],5,-701558691);l=md5_gg(l,o,n,m,p[g+10],9,38016083);m=md5_gg(m,l,o,n,p[g+15],14,-660478335);n=md5_gg(n,m,l,o,p[g+4],20,-405537848);o=md5_gg(o,n,m,l,p[g+9],5,568446438);l=md5_gg(l,o,n,m,p[g+14],9,-1019803690);m=md5_gg(m,l,o,n,p[g+3],14,-187363961);n=md5_gg(n,m,l,o,p[g+8],20,1163531501);o=md5_gg(o,n,m,l,p[g+13],5,-1444681467);l=md5_gg(l,o,n,m,p[g+2],9,-51403784);m=md5_gg(m,l,o,n,p[g+7],14,1735328473);n=md5_gg(n,m,l,o,p[g+12],20,-1926607734);o=md5_hh(o,n,m,l,p[g+5],4,-378558);l=md5_hh(l,o,n,m,p[g+8],11,-2022574463);m=md5_hh(m,l,o,n,p[g+11],16,1839030562);n=md5_hh(n,m,l,o,p[g+14],23,-35309556);o=md5_hh(o,n,m,l,p[g+1],4,-1530992060);l=md5_hh(l,o,n,m,p[g+4],11,1272893353);m=md5_hh(m,l,o,n,p[g+7],16,-155497632);n=md5_hh(n,m,l,o,p[g+10],23,-1094730640);o=md5_hh(o,n,m,l,p[g+13],4,681279174);l=md5_hh(l,o,n,m,p[g+0],11,-358537222);m=md5_hh(m,l,o,n,p[g+3],16,-722521979);n=md5_hh(n,m,l,o,p[g+6],23,76029189);o=md5_hh(o,n,m,l,p[g+9],4,-640364487);l=md5_hh(l,o,n,m,p[g+12],11,-421815835);m=md5_hh(m,l,o,n,p[g+15],16,530742520);n=md5_hh(n,m,l,o,p[g+2],23,-995338651);o=md5_ii(o,n,m,l,p[g+0],6,-198630844);l=md5_ii(l,o,n,m,p[g+7],10,1126891415);m=md5_ii(m,l,o,n,p[g+14],15,-1416354905);n=md5_ii(n,m,l,o,p[g+5],21,-57434055);o=md5_ii(o,n,m,l,p[g+12],6,1700485571);l=md5_ii(l,o,n,m,p[g+3],10,-1894986606);m=md5_ii(m,l,o,n,p[g+10],15,-1051523);n=md5_ii(n,m,l,o,p[g+1],21,-2054922799);o=md5_ii(o,n,m,l,p[g+8],6,1873313359);l=md5_ii(l,o,n,m,p[g+15],10,-30611744);m=md5_ii(m,l,o,n,p[g+6],15,-1560198380);n=md5_ii(n,m,l,o,p[g+13],21,1309151649);o=md5_ii(o,n,m,l,p[g+4],6,-145523070);l=md5_ii(l,o,n,m,p[g+11],10,-1120210379);m=md5_ii(m,l,o,n,p[g+2],15,718787259);n=md5_ii(n,m,l,o,p[g+9],21,-343485551);o=safe_add(o,j);n=safe_add(n,h);m=safe_add(m,f);l=safe_add(l,e)}return Array(o,n,m,l)}function md5_cmn(h,e,d,c,g,f){return safe_add(bit_rol(safe_add(safe_add(e,h),safe_add(c,f)),g),d)}function md5_ff(g,f,k,j,e,i,h){return md5_cmn((f&k)|((~f)&j),g,f,e,i,h)}function md5_gg(g,f,k,j,e,i,h){return md5_cmn((f&j)|(k&(~j)),g,f,e,i,h)}function md5_hh(g,f,k,j,e,i,h){return md5_cmn(f^k^j,g,f,e,i,h)}function md5_ii(g,f,k,j,e,i,h){return md5_cmn(k^(f|(~j)),g,f,e,i,h)}function safe_add(a,d){var c=(a&65535)+(d&65535);var b=(a>>16)+(d>>16)+(c>>16);return(b<<16)|(c&65535)}function bit_rol(a,b){return(a<<b)|(a>>>(32-b))};

            function genNonce(b){var c=[],e="ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789",a=e.length;for(var d=0;d<b;++d){c.push(e[Math.random()*a|0])}return c.join("")};

            $(function()
            {
                $('#btn').click(
                    function() { HttpDigest("John", 'pass', 'http://localhost:3000', '/', function(d){ console.log(d) } ); }
                );
            });

            //function addInfo( key, val ) { $('#data').append( '<tr><td class="H"><b>' + key + '</b>&nbsp&nbsp</td><td>' + val + '</td></tr>' ); }

            function HttpDigest( USER, TOKEN, URL, URI, callback )
            {
                var nonce,  // unique string value specified by the server
                    realm,  // authentication realm, defaults to "IsmuserAPI"
                    qop;    // list of quality of protection values supported by the server (auth | auth-int); auth set.

                var cnonce  = genNonce( 10 ); // opaque random string value provided by the client
                var nc      = 0;              // count of the number of requests (including the current request) that the client has sent with the nonce value

                /* 
                 * Simple tokenization to start with:
                 *
                 * This will turn a WWW-Authenticate header field like:

                 * WWW-Authenticate: Digest realm="your.realm",
                                            qop="auth",
                                            nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093"
                 * into:
                 *
                 *  ['Digest', 'realm="your.realm"', 'qop="auth"', 'nonce="dcd98b7102dd2f0e8b11d0f600bfb0c093"']
                 */
                var ws      = '(?:(?:\\r\\n)?[ \\t])+',
                    token   = '(?:[\\x21\\x23-\\x27\\x2A\\x2B\\x2D\\x2E\\x30-\\x39\\x3F\\x41-\\x5A\\x5E-\\x7A\\x7C\\x7E]+)',
                    quotedString = '"(?:[\\x00-\\x0B\\x0D-\\x21\\x23-\\x5B\\\\x5D-\\x7F]|'+ws+'|\\\\[\\x00-\\x7F])*"',
                    tokenizer = RegExp(token+'(?:=(?:'+quotedString+'|'+token+'))?', 'g');

                // Parse the parameters (check existence and validity) and extract the values. 
                // Note that quoted-string values can be folded, so you need to unfold them with this function                  
                function unq(quotedString) { return quotedString.substr(1, quotedString.length-2).replace(/(?:(?:\r\n)?[ \t])+/g, " "); }

                var method  = "GET";
                var dType   = "JSON";

                // 1º ajax call, to get the cnonce (server nonce)
                var ajaxObj = $.ajax
                ({
                    url         : URL,
                    type        : method,
                    dataType    : dType,
                    crossDomain : true,
                    statusCode:
                    {
                        401: function () 
                        {
                            // Read the HTTP authentication header to get the nonce, realm and qop
                            var tokens = ( ajaxObj.getResponseHeader("WWW-Authenticate")
                                        // this is nasty, ugly-looking and disgusting but necessary to cheat "the great Safari browser"
                                        || ajaxObj.getResponseHeader("Content-Type") ) 
                                            .match(tokenizer);

                            $.each(tokens, function(index, value)
                            {
                                if (value.match ("WWW-Authenticate")) nonce = unq(value.split('=')[1]);
                                if (value.match ("realm"))            realm = unq(value.split('=')[1]);
                                if (value.match ("qop"))              qop   = unq(value.split('=')[1]);
                            });

                            /*
                             * HA1 = MD5(USER:REALM:PASS) --> john:your.realm:pass
                             */
                            var HA1 = hex_md5(USER + ":" + realm + ":" + TOKEN);

                            /*
                             * HA2 = MD5(METHOD:URI)--> GET:your.realm
                             */
                            var HA2 = hex_md5(method + ":" + URI);

                            /*
                             * response = digest = MD5(HA1 + ":" + NONCE + ":" + NC + ":" + CNONCE + ":" + QOP + ":" + HA2);
                             */
                            var res = hex_md5(HA1 + ":" + nonce + ":" + nc + ":" + cnonce + ":" + qop + ":" + HA2);

                            /*
                             *  Authorization header:
                             *
                             *  Authorization:  Digest username="John", realm="your.realm", 
                                                nonce="k7KYbGJOCIw4RAK0IcaUQsYszXwsJGOU", uri="/", 
                                                cnonce="MDAwODM0", nc=00000001, qop="auth", 
                                                response="77f88f3f6b4623eedf17af206098ebf8"
                             */
                            var header = 'Digest username="' + USER + '", realm="' + realm
                                        + '", nonce="' + nonce + '", uri="' + URI + '", cnonce="' 
                                        + cnonce + '", nc="' + nc + '", qop="' + qop + '", response="' 
                                        + res + '"';

                            // now we've already built the digest, let's make another request to the server to get the data
                            var request = $.ajax
                            ({
                                url         : URL,
                                type        : method,
                                dataType    : dType,
                                crossDomain : true,
                                contentType : 'application/json',
                                headers     : { "Authorization": header }
                            })
                            .fail(function ( jqXHR, status, error ) { callback( { status : status, error : error, statusCode : jqXHR.status } ); })
                            .done(function ( data, status, jqXHR )  { callback( data ); });
                        } // end 401 status code
                    } // end statusCode
                }); // end ajaxCall
            } // end HttpDigest()

        </script>
    </head>
    <body>
        <center>
            <p> Http Digest </p>
            <button id="btn" type="button">Request</button>
            <table id="data"></table>
        </center>
    </body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

和 App.js

```
var express= require('express'),
    passport       = require('passport'), // passportjs
    DigestStrategy = require('passport-http').DigestStrategy; // http digest

// #############################################################################################################

// ----------------
// Environment conf
// ----------------
var app = express();

// configure Express
app.configure(function()
{
    app.use(express.logger());

    app.use(express.methodOverride());

    // Initialize Passport! Note: no need to use session middleware when each
    // request carries authentication credentials, as is the case with HTTP
    // Digest.
    app.use(passport.initialize());

    app.use(app.router);
    app.use(express.static( __dirname + '/public' ));
});

app.configure( 'development', function () { app.use( express.errorHandler( { dumpExceptions: true, showStack: true } ) ); });
app.configure( 'production',  function () { app.use( express.errorHandler() ); } );

// #############################################################################################################

// fake database
var users = [
    { id: 1, username: 'John', password: 'pass',     domain: "http://localhost:2563/", email: 'john@example.com' }
  , { id: 2, username: 'joe',  password: 'birthday', domain: "www.domain.com",         email: 'joe@example.com' }
];

// #############################################################################################################

function findByUsername( username, fn )
{
    for (var i = 0, len = users.length; i < len; i++)
    {
        var user = users[i];
        if (user.username === username) return fn( null, user );
    }
    return fn(null, null);
}

// -------------
// Passport conf
// -------------

// Use the DigestStrategy within Passport.
// This strategy requires a `secret`function, which is used to look up the
// use and the user's password known to both the client and server. The
// password is used to compute a hash, and authentication will fail if the
// computed value does not match that of the request. Also required is a
// `validate` function, which can be used to validate nonces and other
// authentication parameters contained in the request.
passport.use
(
  new DigestStrategy(
    { qop: 'auth', realm: 'your.realm' },
    function( username, done )
    {
        // Find the user by username. If there is no user with the given username
        // set the user to `false` to indicate failure. Otherwise, return the
        // user and user's password.
        findByUsername(
            username, 
            function( err, user )
            {
                if ( err )   return done( err );
                if ( !user ) return done( null, false );
                return done( null, user, user.password );
            }
        );
    },
    function( params, done ) // second callback
    {
        // asynchronous validation, for effect...
        process.nextTick(
            function ()
            {
                // check nonces in params here, if desired
                console.log( params );
                /*
                nonce: 'MYto1vSuu6eK9PMNNYAqIdsmUXOA2ppU',
                cnonce: 'MDA4NjY5',
                nc: '00000001',
                opaque: undefined }
                */
                return done( null, true );
            }
        );
    }
));

// #################################################################################

app.all(
    '/',
    // Authenticate using HTTP Digest credentials, with session support disabled.
    passport.authenticate( 'digest', { session: false } ),
    function( req, res )
    {
        /************************************************/
        //  CROSS-DOMAIN RESOURCE SHARING
        res.setHeader( 'Access-Control-Allow-Origin', '*' );
        /************************************************/

        // domain check
        if ( req.user.domain.match( req.headers.origin ) )
          res.json( { logged: true, username: req.user.username, email: req.user.email } );
    }
);

app.listen( 3000 ); 
```

Enter fullscreen mode Exit fullscreen mode

在 digest.js 中，您可以指定您的身份验证领域。

digest.js '(第 74 行；node _ modules/passport-http/lib/passport-http/策略)

```
this._realm = options.realm || 'your.realm'; 
```

Enter fullscreen mode Exit fullscreen mode

authenticate . js(node _ modules/passport/lib/passport/middleware；第 150 行)。

```
The W3 spec for CORS preflight requests clearly states that user credentials should be excluded. There is a bug in Chrome and WebKit where OPTIONS requests returning a status of 401 still send the subsequent request.

Firefox has a related bug filed that ends with a link to the W3 public webapps mailing list asking for the CORS spec to be changed to allow authentication headers to be sent on the OPTIONS request at the benefit of IIS users. Basically, they are waiting for those servers to be obsoleted.

How can I get the OPTIONS request to send and respond consistently?

Simply have the server (API in this example) respond to OPTIONS requests without requiring authentication (http://stackoverflow.com/a/15734032). 
```

Enter fullscreen mode Exit fullscreen mode

为了允许 CORS 请求 ant 能够读取服务器发送的信息，我们需要公开 WWW-Authenticate 头以及 GET 和 OPTIONS 方法。

一个特殊的情况发生了。虽然暴露了 WWW-Authenticate 头，但 Safari for windows 不读取该头。所以我们得揭露另一个。

Authenticate.js'

```
 res.statusCode = rstatus || 401;
      if ( req.method === "OPTIONS" ) res.statusCode = rstatus || 200;

      if (rchallenge.length)
      {
        res.setHeader('WWW-Authenticate', rchallenge);
        res.setHeader('Content-Type', rchallenge);

        /**********************************/
        /* MODIFIED TO FULFIL DIGEST AUTH */
        /**********************************/
        res.setHeader('Access-Control-Expose-Headers', 'WWW-Authenticate, Content-Type');

        res.setHeader("Access-Control-Allow-Headers", "Authorization, Content-Type");
        res.setHeader("Access-Control-Allow-Methods", "GET, OPTIONS");
        res.setHeader('Access-Control-Allow-Origin', '*');

        /**********************************/
      }
      res.end('Unauthorized'); 
```

Enter fullscreen mode Exit fullscreen mode