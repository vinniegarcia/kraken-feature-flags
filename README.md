kraken-feature-flagger
===
Feature flags for Kraken JS apps.

##Install

```npm install kraken-feature-flags --save```

##Usage

### Configuration
In your `config/config.json` file, add a `features` section and some features. Set each feature to true or false to enable/disable:
```json
{
    "features": {
        "happy": true,
        "awesome": true,
        "deprecated-feature": false
    },
    ...
}
```

### App initialization

Create a kraken app as usual, and add the middleware to your app or an Express router/route.
```javascript
var kraken = require('kraken-js'),
    app = module.exports = require('express')(),
    features = require('kraken-feature-flagger'),
    options = {};

app.on('start', function () {
    console.log('app started and ready to serve requests.');
});

app.use(kraken(options));
//set features flag middleware here,
//or in your routers
app.use(features());
```
In a route, your enabled features will be available on `req.features`:
```javascript
module.exports = function (router) {
	router.get('/', function (req, res) {
		console.log(req.features.enabled); // => ['happy', 'awesome']
		if (req.features.has('awesome')) {
			res.render('awesome');
		} else {
			res.render('sadtrombone');
		}
	});
}
```
###In views

You can set feature classes in your views as well, as `res.locals.featureClasses` is created for you:
```html
<body class="{featureClasses}">...</body>
```
returns
```html
<body class="feature-happy feature-awesome">...</body>
```

###Route Gating

You can also "gate off" routes, which means that route is only available if your config enables it. Otherwise users will see a 503 Service Unavailable error. Here's an example:
```javascript
var gate = require('kraken-feature-flagger/gate');

router.get('/gated', gate('awesome-people-only'), function (req, res) {
	res.send('You must be awesome since they let you in here!');
});
```