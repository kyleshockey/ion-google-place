ion-google-place
================

Ionic directive for a location dropdown that utilizes google maps

![Animated demo](https://github.com/israelidanny/ion-google-place/raw/master/demo.gif)

This is a simple directive for an autocomplete overlay location field built for Ionic Framework.

#Installation

Installation should be dead simple, you can grab a copy from bower:
```bash
bower install ion-google-place
```

Then you will need to include to appropiate files in your header

```bash
<link href="lib/ion-google-place/ion-google-place.css" rel="stylesheet">
<script src="lib/ion-autocomplete/dist/ion-autocomplete.js"></script>
```

For the geolocation service to work, you'll need to have Google Maps javascript API somewhere in your HEAD tag:
```
<script src="http://maps.googleapis.com/maps/api/js?libraries=places&sensor=false"></script>
```

Finally, you'll need to add `ion-google-place` as a dependency on your Ionic app:
```javascript
angular.module('myApp', [
  'ionic',
  'ion-google-place'
]);
```

That's pretty much it. Now you can use the directive like so:
`<ion-google-place placeholder="Enter an address, Apt# and ZIP" ng-model="data.location" ng-change="googleChange()" />`

There is some really important logic going on from the directive to your ionic controller. Simply using ng-model="location" will (probablly) not work. Instead use data.location and then in your view's controller you want:

```javascript
$scope.googleChange = function(){
  console.debug($scope.data.location);
};
```

That will allow you to grab the returned result data if you are trying to do something like the Google Maps example seen here: [https://developers.google.com/maps/documentation/javascript/examples/places-autocomplete-addressform](https://developers.google.com/maps/documentation/javascript/examples/places-autocomplete-addressform)
  
  
If you want an angularjs version of their fillInAddress() function here is a good starting point. Put this in your views controller function, next to where you are putting $scope.googleChange();

```javascript
$scope.placeSearch = "";
$scope.autocomplete = "";

//important to structure like this. $scope.location = "" wont work.
$scope.data={"location":{}};

$scope.googleChange = function(){
  $scope.fillInAddress($scope.data.location);
};
  
$scope.newCustomerData = {
    street_addy : "", //addy 1
    street_more : "", //addy 2
    city: "", //city
    thestate: "", //state
    postal_code: "" //zipcode
  }; 
  
$scope.componentForm = {
  street_number: 'short_name',
  route: 'long_name',
  locality: 'long_name',
  administrative_area_level_1: 'short_name',
  country: 'long_name',
  postal_code: 'short_name'
};

$scope.fillInAddress = function(place) {
 
  for (var component in $scope.componentForm) {
    document.getElementById(component).value = '';
    document.getElementById(component).disabled = false;
  }

  // Get each component of the address from the place details
  // and fill the corresponding field on the form.
  for (var i = 0; i < place.address_components.length; i++) {
    var addressType = place.address_components[i].types[0];
    if ($scope.componentForm[addressType]) {
      var val = place.address_components[i][$scope.componentForm[addressType]];
      console.debug(addressType);
      if(addressType == "street_number"){
        $scope.newCustomerData.street_addy = val;
      }
      if(addressType == "route"){
        $scope.newCustomerData.street_more = val;
      }
      if(addressType == "locality"){
        $scope.newCustomerData.city = val;
      }
      if(addressType == "administrative_area_level_1"){
        $scope.newCustomerData.thestate = val;
      }
      if(addressType == "postal_code"){
        $scope.newCustomerData.postal_code = val;
      }
    }
  }
};
```
