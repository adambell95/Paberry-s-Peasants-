# First Flight – The AR Experience

Each Fall there is an orientation at Discovery Park for 1st-year students. It is called First Flight. Due to the building’s large size, students get horribly lost. It would be nice to have an activity that orients new students to Discovery Park and the department through Augmented Reality. 

The AR application will run on Android phones (see system requirements). As students play the game, they will learn about Discovery Park’s prominent locations. Ideally, at completion of the game students will no longer feel lost. In actuality, students will feel at home.

In development by the Paberry-s-Peasants-

## Table of Contents

* [System Requirements](#system-requirements)
* [Users Profile](#users-profile)
* [Features](#features)
* [Functional Requirements](#functional-requirements)
* [Nonfunctional Requirements](#nonfunctional-requirements)

### System Requirements:
**********************************************************************************************************************************
- Android 7.0 or higher
- Preferred resolution of 720×1280 pixels (Not optimized for tablet)
- Strong internet connection (Wi-Fi, 3G, or 4G)
- GPS and Location Services
- Intel CPUs are not supported

### Users Profile
**********************************************************************************************************************************
- The application is intended for 1st year students at University of North Texas. 
- Users need basic reading abilities (5th Grade or above reading level). 
- Users need a way to move a smart phone around to interact with AR features.
- Users need a way to navigate around / through Discovery Park. 
- Users need a way to see their phone screen and surroundings.

### Features
**********************************************************************************************************************************
- A main menu will be present in the application.
- A play button will be present within the main menu.
- AR interactions on campus, will familiarize students with Discovery Park.
- A head-up display will be present during gameplay.
- A mission log will be present within the HUD.
- User’s coin balance will be present within the HUD.

### Functional Requirements
**********************************************************************************************************************************
#### Main Menu

After initial application launch, the system will display a main menu consisting of the following buttons: play, shop, and quit.

#### Play Button

The play button resides within the main menu. After touching play, the main menu will disappear, and the system will begin utilizing the camera. The game has officially started.

#### In-Game Head-up Display

The HUD will allow users to view their coin balance and mission log.

#### Coin Balance

Coins are collected throughout the game’s campaign. The coins are used as a form of in-game currency to purchase clothing / accessories in the store page.

#### Mission Log

The mission log is opened by touching mission log in the HUD during gameplay. The mission log will inform the user of their current in-game objective.

#### Shop Page

Users can spend their coins on in-game 

#### Augmented Images
- https://developers.google.com/ar/discover/concepts
- https://developers.google.com/ar/develop/java/augmented-images/
- https://developers.google.com/ar/develop/java/augmented-images/guide
- The system will scan room numbers which will trigger AR events.
- Augmented Images allows you to build AR apps that can respond to specific 2D images such as product packaging or movie posters. Users can trigger AR experiences when they point their phone's camera at specific images - for instance, they could point their phone's camera at a movie poster and have a character pop out and enact a scene.
- Images can be compiled offline to create an image database, or individual images can be added in real time from the device. Once registered, ARCore will detect these images, the images boundaries, and return a corresponding pose.
- Augmented Images in ARCore lets you build AR apps that can respond to 2D images, such as posters or product packaging, in the user's environment. You provide a set of reference images, and ARCore tracking tells you where those images are physically located in an AR session, once they are detected in the camera view.

##### Is Augmented Images is Suitable for our App:

- Each image database can store feature point information for up to 1000 reference images.
- ARCore can track up to 20 images simultaneously in the environment, but it cannot track multiple instances of the same image.
- The physical image in the environment must be at least 15cm x 15cm and must be flat (for example, not wrinkled or wrapped around a bottle)
- Once tracked, ARCore provides estimates for position, orientation, and physical size. These estimates are continuously refined as ARCore gathers more data.
- ARCore cannot track a moving image, but it can resume tracking that image after it stops moving.
- All tracking happens on the device, so no internet connection is required. Reference images can be updated on-device or over the network without requiring an app update.

##### Tips for selecting reference images
- Augmented Images supports PNG and JPEG file formats. For JPEG files, avoid heavy compression for best performance.
- Detection is solely based on points of high contrast, so both color and black/white images are detected, regardless of whether a color or black/white reference image is used.
- The image's resolution should be at least 300 x 300 pixels.
- Using images with high resolution does not improve performance.
- Avoid images with sparse features.
- Avoid images with repetitive features.
- A good reference image is hard to spot with the human eye. Use the arcoreimg tool to get a score between 0 and 100 for each image. We recommend a score of at least 75. Here are two examples: see website for image

##### Tips for creating the image database
- The database stores a compressed representation of the reference images. Each image occupies ~6KB.
- It takes ~30ms to add an image to the database at runtime. Add images on a worker thread to avoid blocking the UI thread, or if possible, add images at compile time with the arcoreimg tool.
- If possible, specify the expected physical size of the image. This metadata improves tracking performance, especially for large physical images (over 75cm).
- Don't keep unused images in the database as there's a slight impact on system performance.

##### Tips for optimizing tracking
- The physical image must occupy 40% of the camera image. You can prompt users to fit the physical image in their camera frame with the FitToScan asset. See the Augmented Images sample app for an example of this prompt.
- When an image is initially detected by ARCore, and no expected physical size was specified, its tracking state will be paused. This means that ARCore has recognized the image, but has not gathered enough data to estimate its location in 3D space. Developers should not use the image's pose and size estimates until the image's tracking state is tracking.

##### Create an image database

- Each image database can store information for up to 1000 images.
- There two ways to create an AugmentedImageDatabase:
- Load a saved image database. Then optionally add more reference images.
- Create a new empty database. Then add reference images one at a time.

##### Load a saved image database

- Use ```AugmentedImageDatabase.deserialize()``` to load an existing image database:
```java
InputStream inputStream = context.getAssets().open("example.imgdb");
AugmentedImageDatabase imageDatabase = AugmentedImageDatabase.deserialize(inputStream);
```
- Image databases can be created using the ```arcoreimg``` command line tool during development, or by calling ``` AugmentedImageDatabase.serialize()``` on a database that contains that is loaded in memory.

##### Create a new empty database

- To create an empty image database at runtime, use the no-arg ```AugmentedImageDatabase()``` constructor:
```java
AugmentedImageDatabase imageDatabase = new AugmentedImageDatabase();
```

##### Add images to an existing database

- Add images to your image database by calling ```AugmentedImageDatabase.addImage()``` for each image:
```java
Bitmap bitmap;
try (InputStream inputStream = getAssets().open("dog.jpg")) {
  bitmap = BitmapFactory.decodeStream(inputStream);
} catch (IOException e) {
  Log.e(TAG, "I/O exception loading augmented image bitmap.", e);
}
int index = imageDatabase.addImage("dog", bitmap, imageWidthInMeters);
```
- The returned indexes can later be used to identify which reference image was detected.

##### Enable image tracking

- Configure your ARCore session to begin tracking images by setting the session config to one that is configured with the desired image database:
```java
config.setAugmentedImageDatabase(imageDatabase);
session.configure(config);
```
- During the session, ARCore looks for images by matching feature points from the camera image against those in the image database.

- To get the matched images, poll for updated ```AugmentedImage```s in your frame update loop.
```java
// Update loop, in onDrawFrame().
Frame frame = mSession.update();
Collection<AugmentedImage> updatedAugmentedImages =
  frame.getUpdatedTrackables(AugmentedImage.class);

for (AugmentedImage img : updatedAugmentedImages) {
  // Developers can:
  // 1. Check tracking state.
  // 2. Render something based on the pose, or attach an anchor.
  if (img.getTrackingState() == TrackingState.TRACKING) {
     // You can also check which image this is based on getName().
     if (img.getIndex() == dogIndex) {
       // TODO: Render a 3D version of a dog in front of img.getCenterPose().
     } else if (img.getIndex() == catIndex) {
       // TODO: Render a 3D version of a cat in front of img.getCenterPose().
     }
  }
}
```

#### Information Display

After AR Event has been triggered, information regarding Discovery Park(our school campus) will display to the screen. 

### Non-Functional Requirements
**********************************************************************************************************************************
- The system will run on Android 7.0 or above.
- The system will utilize a graphical user interface.
- The system can analyze real life images and produce information based off of those 

