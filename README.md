# Index

* [Disclaimer](#disclaimer)
* [Overview](#overview)
* [How to use this repo](#how_to_use_this_repo)
* [Using the Chromium WebAR prototype and the new APIs](#using_the_chromium_webar_prototype_and_the_new_apis)
  * [How to install and use the Chromium WebAR prototype](#how_to_install_and_use_the_chromium_webar_prototype_on_android)
  * [Overview of the WebAR APIs](#overview_of_the_webar_apis)
  * [Using the WebAR APIs in ThreeJS](#using_the_webar_apis_in_threejs)
  * [Examples](#examples)
* How to build your own version of Chromium that includes the WebAR APIs
* Supported devices
* How to build this documentation
* License
* Future work

# <a name="overview">Overview</a>

This project's goal is to provide an initial implementation of a possible Augmented Reality (AR) API for the Web on top of Chromium. The initial prorotype is built on top of the [Tango](https://get.google.com/tango/) platform for Android by Google. Maybe, more platforms will be supported in the future. There is a precompiled and working prototype you can use right away along with documentation of the APIs and some examples. There is also a tutorial on how to build your own version of modified Chromium with the WebAR APIs in it.

A major objective of this project is to get a conversation going on the subject of how to provide Augmented Reality capabilities to the web: WebAR.

# <a name="disclaimer">Disclaimer</a>

Defining how a web standard will look like is a complex conversation. All the code and proposals in this project are not meant to be the definitive implementation of AR capabilities for the web, but some prototypes you can play around with at your own risk and have some starting point where to build upon.

# <a name="how_to_use_this_repo">How use this repo</a>

This repository can be used in 2 ways if you want to start playing around with WebAR:

1. To install the Chromium prototype, learn about the new WebAR APIs and try the examples: <a href="#using_the_chromium_webar_prototype_and_the_new_apis"><b>Using the WebAR prototype and the new APIs</b></a>.

2. To compile you own version of Chromium with WebAR capabilities and contribute to the project: <a href="#how_to_build_your_own_version_of_chromium_that_includes_the_webar_apis"><b>How to build your own version of Chromium that includes the WebAR APIs</b></a>.

# <a name="using_the_chromium_webar_prototype_and_the_new_apis">Using the Chromium WebAR prototype and the new APIs</a>

## <a name="how_to_install_and_use_the_prototype">How to install and use the Chromium WebAR prototype</a>

The `bin` folder in this repo holds the precompiled versions of Chromium that support the WebAR API. Check the [Supported devices]() section to learn what devices/platforms are currently supported and have been tested. 

### <a name="how_to_install_and_use_the_chromium_webar_prototype_on_android">How to install and use the Chromium WebAR prototype on Android</a>

To install the APK you can use the Android `adb` command from the command line. Assuming that you are in the bin folder:

```
bin$ adb install -r WebARChromium.apk
```

The `-r` parameter will reinstall the APK in case you already had it (and do nothing if you did not have it installed). There are other ways to install the APK like downloading it directly to your device via email for example and allowing Android to install it for you.

The installed application will display the `WebAR Chromium` name with the Android icon.

<img src="markdown/images/WebARChromiumIcon.png"/>

When executed, it will show an overly simplified version of a browser with just a QRCode button (explained later), the URL bar, the back and forward buttons as this version of the prototype is using the WebView version of Chromium.

<img src="markdown/images/WebARChromiumHead.png"/>

The QRCode button

<img src="markdown/images/QRCodeButton.png"/>

allows to introduce URLs encoded in QRCodes. I personally do not like introducing long/complex URLs using the on screen touch keyboard on Android, so QRCodes can come handy (not mandatory to be used). In order to use this functionality it will be required the installation of the [Barcode Scanner App](https://play.google.com/store/apps/details?id=com.google.zxing.client.android) from GooglePlay if it is not already installed on the device. Do not worry, the app itself will prompt you to install it and redirect you to the store automagically the first time you press the QRCode button if the Barcode Scanner app is not present. There are multiple QRCode generators around the web but I highly recommend to use [The QRCode Generator](https://www.the-qrcode-generator.com/).

The last introduced and loaded URL will be stored for future executions of the app. 

### <a name="overview_of_the_webar_apis>Overview of the WebAR APIs</a>

This implementation of WebAR is an addition of some features on top of the [WebVR API specification](https://webvr.info/). AR and VR share many common concepts like motion tracking or even a see through camera or a depth sensor, as they can be found in both AR and VR devices (Google Tango, Microsoft Hololens, HTC Vive, ...). As mentioned in the disclaimer above, this API is still experimental and it is just a proposal of a possible solution.

If you are not familiar with the [WebVR API](https://webvr.info/), I highly recommend that you review it before continuing as some basic knowledge of it will be assumed in the following paragraphs.

All the documentation specific to the new APIs inside WebVR can be found online at: [http://judax.github.io/webar/doc/webarapi](http://judax.github.io/webar/doc/webarapi) that is generated from the file WebARAPI.js found in this repository.

The main point of entry for the WebAR API is still the `VRDisplay` instance as in WebVR. Actually, if an AR device such as Tango wants to be used for 6DOF (6 Degrees Of Freedom) VR experiences (non AR), the WebVR API as is could be used. The `getPose` and `getFrameData` calls will correctly return the position and orientation acquired from the underlying hardware implementation. 

But there are some new features that the WebVR spec does not include and that provide additional functionality based on the underlying AR platform. These new characteristics can be identified using the `VRDisplayCapabilities` instance obtained from the `VRDisplay` instance that now exposes 2 new flags to specify if the device:

* [hasPointCloud](http://judax.github.io/webar/doc/webarapi/VRDisplayCapabilities.html): The `VRDisplay` instance is able to provide a point cloud acquired by a depth sensing device in the underlying plataform.
* [hasSeeThroughCamera](http://judax.github.io/webar/doc/webarapi/VRDisplayCapabilities.html): The `VRDisplay` instance is able to use an underlying see through camera to show the real world.

If any of these flags are enabled (true), a new set of functionalities and APIs can be used always using the [VRDisplay](http://judax.github.io/webar/doc/webarapi/VRDisplay.html) as a starting point. The new methods in the `VRDisplay` instance are:

* [getMaxNumberOfPointsInPointCloud](http://judax.github.io/webar/doc/webarapi/VRDisplay.html): Provides the maximum number of points in the point cloud.
* [getPointCloud](http://judax.github.io/webar/doc/webarapi/VRDisplay.html): Updates and/or also retrieves the points in the [point cloud](http://judax.github.io/webar/doc/webarapi/VRPointCloud.html).
* [getPickingPointAndPlaneInPointCloud](http://judax.github.io/webar/doc/webarapi/VRDisplay.html): Allows to calculate a colission [point and plane](http://judax.github.io/webar/doc/webarapi/VRPickingPointAndPlane.html) between a normalized 2D position and a ray casted on to the point cloud.
* [getSeeThroughCamera](http://judax.github.io/webar/doc/webarapi/VRDisplay.html): Retrieves a structure that represents the [see through camera](http://judax.github.io/webar/doc/webarapi/VRSeeThroughCamera.html) so it can be used for both correct fustrum calculation and for rendering the video feed.

At a glance it is obvious that some new data structures/classes have been created to support some new functionalities as the underlying Tango platform allows new types of interactions/features. Most of the calls are pretty straightforward and the documentation might provide some idea of how they could be integrated in any web application. The one that might need a bit more explanation is the [VRSeeThroughCamera](http://judax.github.io/webar/doc/webarapi/VRSeeThroughCamera.html) class as it provides some useful information about the camera parameters (what are called the camera intrinsics), but still does not expose how it could be used to render the camera feed in an application. In the current implementation, the approach that has been selected is to create a new overloaded function in the [WebGL API](https://www.khronos.org/registry/webgl/specs/1.0). The [WebGLRenderingContext](https://www.khronos.org/registry/webgl/specs/1.0/#5.14) now exposes the following function:

```
void texImage2D(GLenum target, GLint level, GLenum internalformat, GLenum format, GLenum type, VRSeeThroughCamera? source);
```

This approach has some benefits:

1. There is no need to retrieve the pixels of the image (it is not very efficient to pass a whole frame from native to JavaScript).
2. There is full control over the camera image in WebGL (in a fragment shader for example).
3. It uses a common way to handle video content (`texImage2D` already has some overloads for using `HTMLVideoElement`, `HTMLCanvasElement` or `HTMLImageElement` among others).

But the current implementation has a problem too as the way the camera image is handled inside the `texImage2D` call requires to use an OpenGL extension that is not available in WebGL at the moment: [OES_EGL_image_external](https://www.khronos.org/registry/gles/extensions/OES/OES_EGL_image_external.txt). The Android Chromium WebAR modification in this repository includes the activation of this extension internally. It is important to note that the fragment shader to render the video feed will also need to use the corresponding extension:

```
#extension GL_OES_EGL_image_external : require
...

uniform samplerExternalOES map;
...
```

The best recommendation to better understand the new WebAR API is to review the examples provided in this repository that try to explain some of the new functionalities from the ground up both using plain WebGL and also [ThreeJS](http://threejs.org), the most widely used 3D engine on the web.

## <a name="using_the_webar_apis_in_threejs">Using the WebAR APIs in ThreeJS</a>

As mentioned in the [previous section](#overview_of_the_webar_apis), in order to use the video feed from the underlying Tango platform, there's the need to use a WebGL extension that is not available in the WebGL standard at the moment. The Chromium implemenetation in this repository activates it so fragment shaders may use it. In the case of ThreeJS, as it internally maps the uniforms in the shaders, a very simple modification to the engine is required. In the `getSingularSetter` function that is able to identify the set-functions for the different types of uniforms/attributes in a shader, a new type needs to be added as follows:

```
function getSingularSetter( type ) {

	switch ( type ) {

		case 0x1406: return setValue1f; // FLOAT
		case 0x8b50: return setValue2fv; // _VEC2
		case 0x8b51: return setValue3fv; // _VEC3
		case 0x8b52: return setValue4fv; // _VEC4

		case 0x8b5a: return setValue2fm; // _MAT2
		case 0x8b5b: return setValue3fm; // _MAT3
		case 0x8b5c: return setValue4fm; // _MAT4

		case 0x8b5e: case 36198: return setValueT1; // SAMPLER_2D  // case 36198: Added by WebAR
		case 0x8b60: return setValueT6; // SAMPLER_CUBE

		case 0x1404: case 0x8b56: return setValue1i; // INT, BOOL
		case 0x8b53: case 0x8b57: return setValue2iv; // _VEC2
		case 0x8b54: case 0x8b58: return setValue3iv; // _VEC3
		case 0x8b55: case 0x8b59: return setValue4iv; // _VEC4

	}

}
```

See that the case 36198 is the id that identifies the shader uniforms of type `samplerExternalOES` used in the extension.

There is no additional modifications needed to the ThreeJS engine.

There is also a support library available in this repository under the `THREE.WebAR` folder that provides some functionalities to ease the use of the underlying WebAR APIs in ThreeJS by creating the basic types of structures needed like the `THREE.Mesh` instance that represents the video camera quad (along with the corresponding `THREE.VideoTexture` instance and the right fragment shader), the `THREE.Camera` instance that represents the orthogonal camera to correctly render the video camera feed, a `VRPointCloud` structure that handles a point mesh with a `THREE.BufferGeometry` internally to render the point cloud, etc.

All the documentation for the THREE.WebAR.js file is available at: [http://judax.github.io/webar/doc/THREE.WebAR](http://judax.github.io/webar/doc/THREE.WebAR)

## <a name="how_to_build_your_own_version_of_chromium_with_webar"></a> 1. How to build your own version of Chromium with WebAR

This repository includes only the modifications on the Chromium repository that allows to add Tango/WebAR capabilities into JavaScript. Chromium is a complex project with gigabytes of source code, resources and third party libraries. This repository does not include all Chromium but just the files necessary to make the changes to it in order to enable WebAR, so you will also have to checout the Chromium repository (how to do so will be explained in this tutorial). 

### The folder structure in this repo

Let's review the content on this repository to better understand it:

`/android_webview`: The modifications to the android webview Chromium project to be able to load the Tango handling dynamic library, store the URLs for future executions, read QRCodes, etc. This folder, internally, contains the Tango handling library and all the build files and resources needed. 

`/device`: The modifications to the vr device files to add both the tango_vr_device class and the modifications to the vr service to be able to expose some specific data related to AR (point cloud, see through camera data, picking, etc.).

`/examples`: A set of some basic examples based on straight WebGL or (mostly) using the ThreeJS framework.

`/gpu`: All the modifications in the GL command buffer to be able to use the external texture extension and make the right call to Tango implementation when the texImage2D is called using the VRSeeThroughCamera instance.

`/third_party`: Both the inclusion of the tango third party libraries (and the tango handling library) and the WebKit vr and webgl IDL classes modifications to be able to expose all the functionalities. ZXing is also added to be able to add the QRCode reading functionality to the WebView APK.

Building the modified version of Chromium is a 2 step process: 

1. Clone the Chromium project (copying the changes in this repository) and prepare it to be built
1. Build, install and run.

### 1.1 Clone the Chromium project (copying the changes in this repository) and prepare it to be built

Chromium cloning/building instruction are available online. 

[https://www.chromium.org/developers/how-tos/android-build-instructions](https://www.chromium.org/developers/how-tos/android-build-instructions)

Anyway, in order to help with the process, we recommend you follow the following steps. Remember, Tango is only available on the Android platform for the moment so in order to be able to use the modifications present in this project, you need to compile Chromium for Android that can only be done on Linux. Unfortunately, this document does not include instructions on how to setup a linux machine. Let's assume that the machine is installed along with:

* Java JDK 1.8 and JRE 1.8
* Android SDK
* Android NDK 12b
* GIT
* Setup the PATH variable to point to the tools.

Open a terminal window to be able 
 1. Install depot_tools: [Tutorial](https://commondatastorage.googleapis.com/chrome-infra-docs/flat/depot_tools/docs/html/depot_tools_tutorial.html#_setting_up)
	1. `git clone https://chromium.googlesource.com/chromium/tools/depot_tools.git`
	2. `export PATH=$PATH:/path/to/depot_tools`
 2. `$ mkdir ~/chromium && cd ~/chromium`
 3. `~/chromium$ fetch --nohooks android`.          **NOTE**: This process may take quite some time (an hour?)
 4. Verify that the `.gclient` file has `target_os = ['android']` in it
 5. `cd src` and then `~/chromium/src$ gclient sync`.          **NOTE**: This process may take some time too.
 6. Checkout a specific tag to a new branch. The tag used for this build is `54.0.2796.3`. The name of the branch to checkout could be `webar_54.0.2796.3` for example: `~/chromium/src$ git checkout -b webar_54.0.2796.3 54.0.2796.3`. Choose the name of the brnach you like but remember it to create a corresponding out folder.
 7. Create a folder for the final product compilation with the same name as the branch: `~/chromium/src$ mkdir -p out/webar_54.0.2796.3`
 8. Create and edit a new file `out/webar_54.0.2796.3/args.gn` with the command `~/chromium/src$ gedit out/webar_54.0.2796.3/args.gn` and copy and paste the following content in it:
	```
	target_os = "android"
	target_cpu = "arm"  # (default)
	is_debug = false  # (default)

	# Other args you may want to set:
	is_component_build = true
	is_clang = true
	symbol_level = 1  # Faster build with fewer symbols. -g1 rather than -g2
	enable_incremental_javac = true  # Much faster; experimental
	```
 9. Copy and paste all the content from the current folder of the WebAR repo into the chromium/src folder. Override every possible conflict that may arise if you use the file explorer. Otherwise, you can use the following command line: `cp -r PATH_TO_THIS_FOLDER/* ~/chromium/src`
 10. `~/chromium/src$ gn args out/webar_54.0.2796.3`.          **NOTE**: just exit "q!" in vi when it opens and shows args.gn (modified in the previous step using a proper editor ;))
 11. `~/chromium/src$ build/install-build-deps-android.sh` 
 12. Execute the following commands and select the right choice on each. Do not worry if some commands do not have any effect.
 	```
	sudo update-alternatives --config javac
	sudo update-alternatives --config java
	sudo update-alternatives --config javaws
	sudo update-alternatives --config javap
	sudo update-alternatives --config jar
	sudo update-alternatives --config jarsigner
	```
 13. `~/chromium/src$ gclient sync`
 14. `~/chromium/src$ . build/android/envsetup.sh`

### 1.2: Build, install and run

**IMPORTANT:** some changes have been done to the Chromium command buffer. These changes may require to rebuild the command buffer. The Python script to do so does not execute along with the regular building process so the script needs to be executed with the following command at least once (and everytime a new command is created in the command buffer):
```
~/chromium/src/python gpu/command_buffer/build_gles2_cmd_buffer.py
```
This tutorial specified that the name of the out folder created during the setup process above is the same as the branch (webar_54.0.2796.3). This is no coincidence, as the `build_install_run.sh` shell script provided along with this documentation allows to build the Chromium project depending on the current checked out git branch. This script not only compiles Chromium but also the Tango native library called `tango_chromium` that handle the Tango SDK calls. Moreover, this script also installs the final APK on to a connected device and runs it, so it is convenient that you to connect the Tango device via USB before executing it. The project that will be built by default is the Chromium WebView project, the only one that has been modified to provide Tango/WebAR capabilities.
```
~/chromium/src/build_install_run.sh
```

### Build the documentation

In order to build the documentation you are currently reading, there are some steps that need to be followed:

1. Install JSDoc: `npm install -g jsdoc`
2. `$ jsdoc WebARAPI.js THREE.WebAR/THREE.WebAR.js README.md`

