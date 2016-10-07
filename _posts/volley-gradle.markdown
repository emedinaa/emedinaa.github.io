---
layout: post
title:  "Tip Volley & Gradle"
date:   2015-09-21 20:23:27
categories: android volley
---
Les comparto un Tip con los pasos para generar el jar o aar de la librería Volley usando gradle con Android Studio.

{% highlight java %}
	Log.v(TAG, "Volley & Gradle");
{% endhighlight %}

* Paso 1

Descargar la librería Volley [https://android.googlesource.com/platform/frameworks/volley][volley] 
"git clone https://android.googlesource.com/platform/frameworks/volley"

* Paso 2

Descargar gradle http://gradle.org/gradle-download/

* Paso 3

Identificar el path del SDK de Android y configurar ANDROID_HOME export ANDROID_HOME=/Users/emedinaa/Dev/android/sdk PATH=${PATH}:$ANDROID_HOME/tools:$ANDROID_HOME/platform-tools

* Paso 4

Ejecutar el comando gradle build en la carpeta raiz de Volley
	/Users/emedinaa/Documents/gradle/gradle-2.5/bin/./gradle build --x test
	"BUILD SUCCESSFUL"

* Paso 5

El jar y el aar build/intermediates/bundles/release/classes.jar build/outputs/aar/volley-release.aar


El proyecto está disponible para su descarga en mi [GitHub][repo].

[gb]:      https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/android_volley_gradle
[gdglima]: http://www.gdglima.com/
[volley]: https://android.googlesource.com/platform/frameworks/volley

