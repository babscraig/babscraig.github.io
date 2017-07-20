Ionic is a fantastic framework built on top of Angular which allows Javascript developers build native mobile apps which can run on Android, iOS andWindows devices and be published to their respective stores as well as progressive web apps.

This allows us to write one codebase but deploy on multiple platforms.
Mobile devices are by their very nature mobile and sooner or later when building mobile apps, adding geolocation features becomes a requirement. In this article we’ll integrate the Google maps API and mobile device geolocation in an ionic app.

To follow along, you need a basic grasp of how Angular 2 works as well as some knowledge of Ionic 2 or 3. (I’ll just call it Ionic from now on). In future articles we’ll look in depth at both frameworks but for now let’s jump right in. The full project can be viewed and cloned from github [here](https://github.com/babzcraig/ionic2-geolocation-app).

We’ll be using the Ionic and Cordova CLIs so grab those here:

```$ npm install -g ionic cordova```

After that creating a new Ionic app is simple. Just run the following command in your terminal:

```$ ionic start my-ionic-app blank```

This will create a blank project called my-ionic-app and install all the dependencies. When they’re done being installed, cd into the app folder and run the app using the ionic serve command:

```
$ cd my-ionic-app
$ ionic serve
```

Great. All set up. Now let’s begin.

###Bringing in Google Maps SDK
The great thing about the Google maps SDK is the generous allowance they have on the free tier. You’d need to hit about 25,000 requests a day for 90 days in a row to get onto a paid tier which is just awesome for our current purposes.

Get an API Key [here](https://developers.google.com/maps/documentation/javascript/get-api-key)

Once you have it include the SDK into your project by adding the following to your `src/index.html` file. Place it right above the `cordova.js` file.

```html
<script src=”http://maps.google.com/maps/api/js?key=YOUR_API_KEY_HERE"></script>
```
Next head over to the src/pages/home.html file and replace the code there with the following:
```html
<ion-header>
	<ion-navbar>
		<ion-title>
			Ionic Map
		</ion-title>
		<ion-buttons end>
			<button ion-button (click)="setPin()"><ion-icon name="add"></ion-icon>Add Pin</button>
		</ion-buttons>
	</ion-navbar>
</ion-header>

<ion-content>
	<div #map id="map"></div>
</ion-content>
```
The important things to note here are that we provide a div with an id of *map* this is where our map instance will end up being rendered. In the header, we have a title of 'Ionic Map' and a button with a click event set to `setPin()` which we will add next. This button will make a pin appear at the location we specify in the function.

Let's change the home.ts file:
```javascript
import { Component, ViewChild, ElementRef } from '@angular/core';
import { NavController } from 'ionic-angular';
 
declare var google;
 
@Component({
  selector: 'home-page',
  templateUrl: 'home.html'
})
export class HomePage {
 
  @ViewChild('map') mapElement: ElementRef;
  map: any;
 
  constructor(public navCtrl: NavController) {
 
  }
 
  ionViewDidLoad(){
    this.loadMap();
  }
 
  loadMap(){
 
    let latLng = new google.maps.LatLng(-34.9290, 138.6010);
 
    let mapOptions = {
      center: latLng,
      zoom: 15,
      mapTypeId: google.maps.MapTypeId.ROADMAP
    }
 
    this.map = new google.maps.Map(this.mapElement.nativeElement, mapOptions);
 
  }
}
```

At the top we declare the global `google` variable it so as to ward off any typescript warnings. This variable is made available to us courtesy of the google maps script tag we included in our `index.html` file earlier. We have also created a `map` variable on the **HomePage** class.

Next thing of note is the `@ViewChild()` decorator. This is used in Angular 2 to allow parent components access and call methods belonging to child components. It provides one way for a parent to communicate with a child component. In this case the 'map' element is being given a reference so it can be called later in the `loadMap()` function. ElementRef allows us to reference the native element, 'map', that we passed into the ViewChild argument.

Now let's add some styling so the map can actually be displayed. In the `home.scss` file modify as follows:

```css
.ios, .md {

  home-page {

    .scroll {
      height: 100%
    }

    #map {
      width: 100%;
      height: 100%;
    }

  }

}
```
So far so good. The map should be displayed in your browser now. If you look at the `home.ts` file you'll notice that we hard coded the coordinates (set to Aso Rock Villa - the seat of power in Nigeria). Now is a good time to introduce the Geolocation API. This will allow the user dynamically set the map coordinates to his current location.

First, install the Ionic Native package for the Geolocation plugin
```
$ npm install --save @ionic-native/geolocation
```
If you're running the app on a device you will also need the cordova plugin
```
$ ionic plugin add cordova-plugin-geolocation
```
Then add it as a dependency in your app.module.ts file as such:
```javascript
import { Geolocation } from '@ionic-native/geolocation';
```

Include this line underneath the current import statements and also in your array of providers
```javascript
providers: [
    StatusBar,
    SplashScreen,
    Geolocation,
    {provide: ErrorHandler, useClass: IonicErrorHandler}
  ]
```
Now modify the `home.ts` file to look like this:
```javascript
import { Component, ViewChild, ElementRef } from '@angular/core';
import { NavController } from 'ionic-angular';
import { Geolocation } from '@ionic-native/geolocation';
 
declare var google;
 
@Component({
  selector: 'home-page',
  templateUrl: 'home.html'
})
export class HomePage {
 
  @ViewChild('map') mapElement: ElementRef;
  map: any;
 
  constructor(public navCtrl: NavController, public geolocation: Geolocation) {
 
  }
 
  ionViewDidLoad(){
    this.loadMap();
  }
 
  loadMap(){
 
    this.geolocation.getCurrentPosition().then((position) => {
 
      let latLng = new google.maps.LatLng(position.coords.latitude, position.coords.longitude);
 
      let mapOptions = {
        center: latLng,
        zoom: 15,
        mapTypeId: google.maps.MapTypeId.ROADMAP
      }
 
      this.map = new google.maps.Map(this.mapElement.nativeElement, mapOptions);
 
    }, (err) => {
      console.log(err);
    });
 
  }
 
}
```
The main change here is that we import Geolocation in line 3. The we set a public variable called geolocation which is an instance of the Geolocation service. This instance provides a `getCurrentPosition()` method that resolves with the user's current location. This is what we use for the longitude and latitude values in the `loadMap()` function.

Reload your browser and you should see that the map more accurately reflects your current location. (Be sure to allow location detection by the browser you are using).

Now let's add those pins you usually see on Google Maps.

Remember the `setPin()` function we bound to in our `home.html`? Let's declare it now.

In `home.ts` add the following methods:
```javascript
setPin() {
    let marker = new google.maps.Marker({
      map: this.map,
      animation: google.maps.Animation.DROP,
      position: this.map.getCenter()
    });

    let content = "<h4>We've got content!</h4>";

    this.addInfoWindow(marker, content);
  }

  addInfoWindow(marker, content){

    let infoWindow = new google.maps.InfoWindow({
      content: content
    });

    google.maps.event.addListener(marker, 'click', () => {
      infoWindow.open(this.map, marker);
    });

  }
```
Here, we use the Marker method provided to us by the `google` object to set a pin on the page. We pass in the options and then save a reference to the call in the 'marker' variable.

The `addInfoWindow()` method create a content window by attaching it self to a 'click' event on the pin. Now when we click the pin, the `addInfoWindow()` method will run and display the content we specified in the `content` variable.

I hope this rather basic implementation of the Geolocation and Google Maps API has been helpful. It's a great starting point to explore further and tap into the awesome potential of using Geolocation and Google Maps in your Ionic apps.

