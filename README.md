# overwolf-sample-plugin-documentation
This is meant as a companion to the [Overwolf Sample Plugin](https://github.com/overwolf/overwolf-sample-plugin), providing a bit more information on the structure of the plugin and links to further information

What is NPAPI
-------------
-- expand here

What is the purpose of the XULRunner SDK / Gecko SDK
----------------------------------------------------
>The Gecko SDK, also known as the XULRunner SDK, is a set of XPIDL files, headers and tools to develop XPCOM components which can then in turn e.g. be accessed from XUL using JavaScript. https://developer.mozilla.org/en/docs/Gecko_SDK

Using the XULRunner SDK you are able to interact with your c/c++ code through javascript.

You can explore the full API at https://developer.mozilla.org/en-US/Add-ons/Plugins/Gecko_Plugin_API_Reference

How is the sample app structured
--------------------------------
The meat of the plugin is provided by a [Scriptable Object](https://developer.mozilla.org/en-US/docs/Interfaces/About_Scriptable_Interfaces) which defines and exposes the functions that are available to javascript.

In the sample plugin, an abstraction layer over the [NPObject API](https://developer.mozilla.org/en/docs/NPObject) has already been implemented in [nsScriptableObjectBase](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsScriptableObjectBase.cpp) with default method implementations that can be overriden as necessary.

nsScriptableObjectBase then serves as the base of the class [(nsScriptableObjectOverwolfSample)](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsPluginInstanceOverwolfSample.cpp). It's this class, nsScriptableObjectOverwolfSample, that provides the actual "echo" and "add" implementations.

Along with the registration of the javascript methods and the code implementation, nsScriptableObjectOverwolfSample also includes an example of running the code on a new background thread to [prevent blocking on the UI thread](https://msdn.microsoft.com/en-us/magazine/ee309514.aspx).

--expand here to add the plugin registration and the main method

Mapping Javascript methods to c/c++ methods
-------------------------------------------
The scriptable object nsScriptableObjectOverwolfSample includes a map, [methods_](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsScriptableObjectOverwolfSample.h#L81), which is used to hold the available public methods, and a [REGISTER_METHOD](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsScriptableObjectOverwolfSample.cpp#L8) function that handles adding functions to the map by name.

By calling REGISTER_METHOD in the [Init()](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsScriptableObjectOverwolfSample.cpp#L28) function, you map the desired javascript method name to the implementation and make it available to be called in javascript.

--show example

Registering the Scriptable Object
---------------------------------
Now that there is a scriptable object with functions that are able to be called in javascript, the scriptable object must be registered with the plugin instance.

In the sample app, this happens in the [nsPluginInstanceOverwolfSample](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/nsPluginInstanceOverwolfSample.cpp) class. In the GetValue function, the nsScriptableObjectOverwolfSample is created, initialized, and assigned if it doesn't already exist. 

---discuss what methods are implementations of the XULRunner SDK API, or do this in structure
---(as a non c/c++ person, is this particular implementation thread safe (I'm used to double checked locking), does it matter in this context?)

--add sidebar about the note in this class regarding Chromeium dropping NPAPI support.

Registering the Plugin
----------------------
Now that the functions have been registered with the scriptable object and the scriptable object has been registered with the plugin instance, the plugin instance must be created (and destroyed). For this we get to the main method [(main)](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/main.cpp).

The plugin is created in [NS_NewPluginInstance](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/main.cpp#L79) and destroyed via [NS_DestroyPluginInstance](https://github.com/overwolf/overwolf-sample-plugin/blob/master/npOverwolfSamplePlugin/main.cpp#L92).

Now that the plugin is ready to go, there's still another few cleanup steps. If you look at the top of the main file, you'll see a mimeType being set. You need to change this to your own type.

