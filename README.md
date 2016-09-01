# french-girls-challenge

## Features

* Introduction screen
	* Intro screen consists of multiple steps that the user must complete to start the app.
	* Each step will consist of text/image at the top of the screen and a button at the bottom of the screen.
	* When the user presses the button the text at the top will animate to the next "step" in the introduction process.
	* Pressing OK on the final screen will finish the introduction by animating out the introduction elements (button and text) and scaling our main character to be fully visible centered in the screen.
<br/><br/>

* Costume selection screen
	* The costume selection screen is the heart of this app and will consist of our main character centered on the screen surrounded by costume "bubbles" that can be selected.
	* Costume bubbles should be loaded from the data layer and will consist of local bundled data as well as a remote data retreived from an API.
	* When the user taps a costume bubble there will be an animation as the costume is applied to the character's body.
	* When the costume is applied the text at the top of screen will be animated in with the new costume title and description.
	* When the user touches the character's body the current costume will be removed and the costume bubbles will be reset.  The costume title and description will animate out of view.
<br/><br/>

* Costume bubbles
	* The costume bubbles are one of the more complicated parts of the UI.  They can be spun like a wheel with user interaction and have momentum to give them a realistic spinning motion.  They will also fall with realistic gravity forces applied to them.
	* Each costume bubble can be touched and when selected it will perform a scaling transformation to "dress" the character in the costume. All of the other costume bubbles will fall to the bottom of the screen.
	* When a user taps the character to reset the costumes then the bubble's should animate back to their positions in the "wheel" of costume bubbles
<br/><br/>

* Data layer
	* An API class to retrieve updated costumes
	* The local/remote data is in a Plist format so this should be translated into a set of model objects
	* Save Plist to disk for persistance between sessions
	* Remote image caching/preloading for better performance
	* Error handling to handle remote API errors


## Architecture Overview

I see two different routes we could take when building this app.  The app itself is very simple with only two screens.  We could easily create a "single view" app that started with the introduction screen and then just hid the introduction elements when the introduction was completed.  The other option would be to contain all of the introduction screen in it's own view controller and then create a separate view controller for the costume selection screen.  This adds complexity only because of the transition between the two screens.  This can be accomplished with a custom UIViewController transition so I have chosen the second approach to avoid ending up with a bloated single view controller app.

* DataController
	* The DataController should handle the data layer of the application.
	* This will include methods to load the Plist data into CostumeModel objects that will then be stored in the DataController.
	* The API will be integrated into this class to retreive remote data and load the Plist into CostumeModel objects.
	* API errors should be handled appropriately by either silently failing (and logging) or notifying the user.
	* This class will be responsible for writing the Plist data to disk when the user leaves the application so that we are able to persist data between sessions.
<br/><br/>

* CostumeModel
	* The model class that will be used to define all the properties of a costume such as the image, width, alignment, etc.
<br/>

* IntroViewController
	* The intro view controller uses data that is bundled with the app.  This should be localized into a strings file for whatever languages we want to support.
	* The view will generally consist of our character image in the center with a button on the bottom of the view and a text label at the top of the view.
	* The button logic will handle switching the button text and animating the next text label into view at the top of the screen.
<br/><br/>

* IntroCostumeAnimationController
	* This class should handle the custom UIViewController transition between the introduction screen and the costume selection screen.
	* The "from" view will be the IntroViewController's view and should animate the text and buttons out of the view.  The "to" view will be the CostumeViewController's view with the character centered on the screen.  During this transition the character will scale down to be centered in the middle of the screen to match the "to" view.
	* When the animation completes we should be left with the CostumeViewController's view with the character scaled down and centered.
	* This will give the impression to the user that we have not actually transitioned between two screens but have just moved from the Intro to the Costume portion of the app.
<br/><br/>

* CostumeViewController
	* This class will get it's costume data from the DataController and will create a CostumeBubbleView for each of the costume's available to the user.
	* Remote data should be loaded when the CostumeViewController first appears.
	* UIKit Dynamics should be used to create the interactions with the CostumeBubbleView's.
	* These views should have the ability to be interacted with and "spun" around a central point which is defined as the center mass of the character.
	* When a user taps a CostumeBubbleView to select a costume then the bubble's should "drop" realistically using UIKit Dynamics to simulate gravity and collisions.
	* When a user selects a costume the selected CostumeBubbleView should not fall to the floor. Instead the outer "ring" image should scale up until it is out of view and at the same time the costume image should scale up and align itself with the character.  Another image with the title and description of the costume should animate in at the top of the view.
	* When a costume is currently active and the user taps the character then the costume animation should be reversed and the bubbles should animate back into their positions on the "wheel".
<br/><br/>

* CostumeBubbleView
	* The view will consist of an image, a button, and another "ring" image that is the circle that surrounds all the bubbles.
	* Should have a setData: method that accepts a CostumeModel as a parameter and initializes the view data.
	* CostumeModel should be stored in a property of the view to handle the "tap" event when the user selects the costume.
