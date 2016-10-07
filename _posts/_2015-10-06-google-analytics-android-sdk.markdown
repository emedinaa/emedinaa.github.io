---
layout: post
title:  "SDK Google Analytics para Android"
date:   2015-10-06 20:23:27
categories: android googleplayservices
---
Ejemplo de como utilizar el SDK de Google Analytics para Android.

{% highlight java %}
	Log.v(TAG, "SDK de Google Analytics para Android");
{% endhighlight %}
* Crear un nuevo proyecto en [Google Analytics][ga_web] 

![Cuenta nueva]({{ site.url }}/assets/ga_web1.PNG)

![Código de Seguimiento]({{ site.url }}/assets/ga_web2.PNG)

* Guardamos el ID de seguimiento en strings.xml (res/values/strings.xml)
 
{% highlight xml %}
	<resources>
	<string name="app_name">GAExample</string>
	<string name="ga_id">UA-68530030-1</string>
</resources>
{% endhighlight %}

* Agregamos las dependencias de GA y Android Support en el build.gradle de la App
	* Google Play Services
	* Android Support Library v7
	
{% highlight java %}
	dependencies {
	    compile fileTree(dir: 'libs', include: ['*.jar'])
	    compile 'com.android.support:appcompat-v7:22.2.1'
	    compile 'com.google.android.gms:play-services:7.3.0'
	    compile 'com.jakewharton:butterknife:7.0.1'
	}
{% endhighlight %}
* Configurar Android Manifest
	* Permisos
	* Metadata dentro de la etiqueta 'application' 
	
{% highlight xml %}
   <!-- Required permission for Google Analytics -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
{% endhighlight %}

{% highlight xml %}
    <meta-data
        android:name="com.google.android.gms.version"
        android:value="@integer/google_play_services_version" />
{% endhighlight %}

* Creamos la clase MyApplication que hereda de Application para poder acceder a una instancia de GA desde cualquier activity/fragment o clase .
{% highlight java %}
package com.emedinaa.ga;

import android.app.Application;

import com.google.android.gms.analytics.GoogleAnalytics;
import com.google.android.gms.analytics.Tracker;

/**
 * Created by emedinaa on 08/10/15.
 */
public class MyApplication extends Application {
    /**
     * The Analytics singleton. The field is set in onCreate method override when the application
     * class is initially created.
     */
    private static GoogleAnalytics analytics;

    /**
     * The default app tracker. The field is from onCreate callback when the application is
     * initially created.
     */
    private static Tracker tracker;

    /**
     * Access to the global Analytics singleton. If this method returns null you forgot to either
     * set android:name="&lt;this.class.name&gt;" attribute on your application element in
     * AndroidManifest.xml or you are not setting this.analytics field in onCreate method override.
     */
    public static GoogleAnalytics analytics() {
        return analytics;
    }

    /**
     * The default app tracker. If this method returns null you forgot to either set
     * android:name="&lt;this.class.name&gt;" attribute on your application element in
     * AndroidManifest.xml or you are not setting this.tracker field in onCreate method override.
     */
    public static Tracker tracker() {
        return tracker;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        analytics = GoogleAnalytics.getInstance(this);

        // TODO: Replace the tracker-id with your app one from https://www.google.com/analytics/web/
        tracker = analytics.newTracker(getString(R.string.ga_id));

        // Provide unhandled exceptions reports. Do that first after creating the tracker
        tracker.enableExceptionReporting(true);

        // Enable Remarketing, Demographics & Interests reports
        // https://developers.google.com/analytics/devguides/collection/android/display-features
        //tracker.enableAdvertisingIdCollection(true);

        // Enable automatic activity tracking for your app
        tracker.enableAutoActivityTracking(true);
    }
}

{% endhighlight %}

* Si deseamos registrar una pantalla.
{% highlight java %}
        MyApplication.tracker().setScreenName("Main screen");
{% endhighlight %}

* Si deseamos registrar un evento.
{% highlight java %}
	MyApplication.tracker().send(new HitBuilders.EventBuilder("UI", "login")
            .setLabel("Main login")
            .build());
{% endhighlight %}

* Revisamos en el proyecto de GA.

![Código de Seguimiento]({{ site.url }}/assets/ga_web3.png)

* Referencias
	* Google Analytics SDK Android [link][ga_sdk]

El proyecto está disponible para su descarga en mi [GitHub][repo].


[gb]:    https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/ga-sdk-android
[gdglima]: http://www.gdglima.com/
[ga_sdk]: https://developers.google.com/analytics/devguides/collection/android/v4/?hl=es
[ga_web]: https://www.google.com/analytics/

