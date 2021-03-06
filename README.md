<a href="https://www.patreon.com/stefansarya"><img src="http://anisics.stream/assets/img/support-badge.png" height="20"></a>

[![Written by](https://img.shields.io/badge/Written%20by-ScarletsFiction-%231e87ff.svg)](LICENSE)
[![Software License](https://img.shields.io/badge/License-MIT-brightgreen.svg)](LICENSE)
[![Tweet](https://img.shields.io/twitter/url/http/shields.io.svg?style=social)](https://twitter.com/intent/tweet?text=Serberries%20is%20a%20fast%20Nodejs%20live%20webserver%20that%20able%20to%20live%20refresh%20your%20script%20on%20the%20realtime.&url=https://github.com/ScarletsFiction/Serberries&via=github&hashtags=serberries,live,refresh,server,node)

# Serberries
Serberries is a fast Nodejs live webserver that able to live refresh your script on the realtime.

This library was useful if you always updating your server script but tired of restarting your server every changes.

## Install
To install this library, you can download from this repository or use
> $ npm install serberries

## Sample Usage

```js
var Serberries = require('serberries');

var myserver = new Serberries({
    path:__dirname+'/router' // Required
    // (Put all your router on root folder)
});

myserver.on('error', function(errcode, msg, trace){
    console.error("Error code: "+errcode+' ('+msg+')');
    if(trace){
        console.error(trace.message);
        for (var i = 0; i < trace.stack.length; i++) {
            console.error("   at "+trace.stack[i]);
        }
    }
    console.error("");
});

myserver.on('loading', function(filename){
    console.log('Loading '+filename);
});

myserver.on('loaded', function(urlpath, type){
    console.log('URL to '+urlpath+' was '+type);
});

myserver.on('stop', function(){
    console.log("Server shutdown");
});

myserver.on('started', function(){
    console.log("Server started on http://localhost:80");
});

myserver.on('removed', function(urlpath){
    console.log('URL to '+urlpath+' was removed');
});

myserver.on('httpstatus', function(code, callback){
    callback('HTTP status '+code);
});

myserver.on('navigation', function(data){
    if(data.headers['user-agent'].indexOf('Indy Library')!=-1)
        return; // Some people might have this browser bugs on port 80

    console.log("Navigation to '"+data.path+"'");
    console.log('  - '+data.headers['user-agent']);
});

myserver.start(80);
```

The main router of the server 

```js
// hello.js

// This will be initialized as an object on initialize
// you can keep your variable here and load it even
// the script reloaded
var scope = null;

// You can also live reload your other script
var dependency = require('./ext/hello.js');

function response(req, res, closeConnection){
    res.writeHead(200);
    var output = dependency.hello() + " " + (req.get.who ? req.get.who : 'world') + '!';
    //res.write(output);
    closeConnection(output);
}

module.exports = {
    // URL path
    path: '/hello', // URL path must be started with backslash
    // If you doesn't provide the backslash, it can't be accessed with URL
    // To use this router for all subpath '/hello#'

    // Response handler
    response:response,

    // Scope initialization after script loaded
    scope:function(ref, allRef){
        scope = ref;

        // You can also access other scope with 'allRef'
    }
}
```

When you want to reload your script dependency, you should put it inside your server folder and specify it's parent in `module.exports`

```js
// ext/hello.js
function hello(){
    return 'Hello';
}

module.exports = {
    // This parent is hello.js
    childOf:'hello',
    hello:hello
}
```

## Contribution

If you want to help in Serberries, please fork this project and edit on your repository, then make a pull request to here.

Keep the code simple and clear.

## License

Serberries is under the MIT license.

But don't forget to put the a link to this repository.
