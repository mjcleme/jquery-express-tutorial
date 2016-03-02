# node-api-server-tutorial
Simple introduction to creating a REST service and api with node

We will show you how to take an existing application and put it into the express structure
First create a new express server with
<pre>
express poki
cd poki
npm install
</pre>

Then checkout this repository
<pre>
git clone https://github.com/mjcleme/jquery-express-tutorial.git
</pre>

Now copy the files into the correct places in the template
<pre>
cp jquery-express-tutorial/src/jq.html poki/public
cp jquery-express-tutorial/src/style.css  poki/public/stylesheets/
</pre>

Now create a route to the index.html by editing routes/index.js to be
```javascript
router.get('/', function(req, res) {
  res.sendFile('jq.html', { root: 'public' });
});
```

## Running the server

Make sure the server is running correctly by running 
<pre>
npm start
</pre>
and accessing the URL 'http://yourserver:3000'

You should see that you havent set up the '/pokemon' route because the list is empty and you can see the error in your console

First add the route and make sure it works when you access 'http://yourserver:3000/pokemon'
```javascript
router.get('/pokemon', function(req, res) {
  console.log("In Pokemon");
});
```

Then add the pokemon array to your routes/index.html
```javascript
var pokemon = [
  {
    name: 'Pikachu',
    avatarUrl: 'http://rs795.pbsrc.com/albums/yy232/PixKaruumi/Pokemon%20Pixels/Pikachu_Icon__free__by_Aminako.gif~c200'
  },
  {
    name: 'Charmander',
    avatarUrl: 'http://24.media.tumblr.com/tumblr_ma0tijLFPg1rfjowdo1_500.gif'

  },
  {
    name: 'Mew',
    avatarUrl: 'http://media3.giphy.com/media/J5JrPT8r1xGda/giphy.gif'
  },
  {
    name: 'Cubone',
    avatarUrl: 'http://rs1169.pbsrc.com/albums/r511/nthndo/tumblr_ljsx6dPMNm1qii50go1_400.gif~c200'
  },
  {
    name: 'Cleffa',
    avatarUrl: 'http://media1.giphy.com/media/pTh2K2xTJ1nag/giphy.gif'
  },
  {
    name: 'Gengar',
    avatarUrl: 'https://s-media-cache-ak0.pinimg.com/originals/7e/3b/67/7e3b67c53469cc4302035be70a7f2d60.gif'
  }
];
```

Now, send back the array in your route
```javascript
res.send(pokemon);
```

Some of you may want to access a REST service that doesnt have the CORS headers for your project.  Lets go through an example of how to do this.

Lets say we want to get information about candidates from 'https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod'

If we put the URL into our jquery code at public/jq.js with something like
```javascript
    var politics = "https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod";
    $.ajax({
      url: politics,
      success: function( data ) {
          console.log(data);
      }
    });
```

You will get a CORS error on the console of your browser.

So, lets make a proxy for this route in routes/index.js

First add the request module to the top of your routes/index.js file
```javascript
var request = require('request')
```
Then install the module
<pre>
npm install request --save
</pre>

Then use 'request' to pipe the output from the real URL back through the node server to your browser.
```javascript
var politics = "https://zlzlap7j50.execute-api.us-east-1.amazonaws.com/prod";
router.get('/politics', function(req,res) {
  console.log("In politics");
  request(politics).pipe(res);
});
```

Test the route by accessing the URL 'http://yourserver:3000/politics'

And change jquery ajax url call to point to this route in public/jq.html
```javascript
var politics = "politics";
```

What about saving a new pokimon?

First add a form to the public/jq.html file.
```
<h1> Enter A New Poki</h1>
<form id="newPoki" action="javascript:handleIt()">
  Name: <input type="text" id="pokiName" value=""><br>
  Url: <input type="url" id="pokiUrl" value=""><br>
  <input type="submit" value="Submit">
</form>
```

And add the function to execute on the submit
```javascript
function handleIt() {
  var formData = {name:$("#pokiName").val(),avatarUrl:$("#pokiUrl").val()};
  console.log(formData);
  var pokiURL = 'pokemon';
  $.ajax({
      url: pokiURL,
      type:"POST",
      data:formData,
      success: function( data ) {
          console.log("Response");
          console.log(data);
      }
  });
}
```
And now we need to build the back end.  We have created an object that should be pushed directly into the array on the back end.  Once we update the array, it should be permanent even if you refresh the browser.  Edit routes/index.js
```javascript
router.post('/pokemon', function(req, res) {
    console.log("In Pokemon Post");
    console.log(req.body);
    pokemon.push(req.body);
    res.end('{"success" : "Updated Successfully", "status" : 200}');
}); 
```
