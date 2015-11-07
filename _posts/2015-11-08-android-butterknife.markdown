---
layout: post
title:  "View Injection con Butternife"
date:   2015-11-08 20:23:27
categories: android view
---

Ejemplo de como utilizar la librería Butteknife  para injección de vistas. [http://jakewharton.github.io/butterknife/][butterknife] 

Decirle adios al "findViewById"

{% highlight java %}
	Log.v(TAG, "Realm en Android");
{% endhighlight %}

* Agregamos Butterknife a nuestro proyecto

{% highlight java %}
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:21.0.3'
        compile 'com.android.support:support-v4:21.0.0'
        compile 'com.android.support:cardview-v7:21.0.+'
        compile 'com.android.support:recyclerview-v7:21.0.+'
        compile 'com.android.support:design:22.2.0'

        compile 'com.jakewharton:butterknife:7.0.1'
    }
{% endhighlight %}


El proyecto está disponible para su descarga en mi [GitHub][repo].


[gb]:    https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/realm_android
[gdglima]: http://www.gdglima.com/
[butterknife]: http://jakewharton.github.io/butterknife/

