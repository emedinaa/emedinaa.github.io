---
layout: post
title:  "Android Parcelable Generator - Plugin AS"
date:   2015-09-14 20:23:27
categories: android androidstudio
---
Ejemplo de como utilizar el plugin  "Android Parcelable code generator" para Android Studio

{% highlight java %}
	Log.v(TAG, "Android Parcelable Generator Plugin Android Studio");
{% endhighlight %}

* Instalación
![Intalación del plugin]({{ site.url }}/assets/parcelable/PluginAS_AndroidParcelable.png)

* Creamos la entidad "PersonEntity"
![Entidad]({{ site.url }}/assets/parcelable/Generate.png)

* Seleccionamos los campos
![Fields]({{ site.url }}/assets/parcelable/Fields.png)

* El resultado sería el siguiente 
![Parcelable]({{ site.url }}/assets/parcelable/Parcelable.png)

{% highlight java %}
	package com.emedinaa.exampleparcelable.model;

	import android.os.Parcel;
	import android.os.Parcelable;

	/**
 	- Created by emedinaa on 07/11/15.
	 */
	public class PersonEntity implements Parcelable {

	    private int id;
	    private String name;

	    public int getId() {
	        return id;
	    }

	    public void setId(int id) {
	        this.id = id;
	    }

	    public String getName() {
	        return name;
	    }

	    public void setName(String name) {
	        this.name = name;
	    }


	    @Override
	    public int describeContents() {
	        return 0;
	    }

	    @Override
	    public void writeToParcel(Parcel dest, int flags) {
	        dest.writeInt(this.id);
	        dest.writeString(this.name);
	    }

	    public PersonEntity() {
	    }

	    protected PersonEntity(Parcel in) {
	        this.id = in.readInt();
	        this.name = in.readString();
	    }

	    public static final Parcelable.Creator<PersonEntity> CREATOR = new Parcelable.Creator<PersonEntity>() {
	        public PersonEntity createFromParcel(Parcel source) {
	            return new PersonEntity(source);
	        }

	        public PersonEntity[] newArray(int size) {
	            return new PersonEntity[size];
	        }
	    };

	    @Override
	    public String toString() {
	        return "PersonEntity{" +
	                "id=" + id +
	                ", name='" + name + '\'' +
	                '}';
	    }
	}
{% endhighlight %}

* Enviar la objeto a otra Activity

{% highlight java %}
  private void sendParcelable() {

        PersonEntity personEntity= new PersonEntity();
        personEntity.setId(100);
        personEntity.setName("Eduardo Medina");

        Intent intent= new Intent(this,DashboardActivity.class);
        intent.putExtra("ENTITY", personEntity);
        startActivity(intent);
    }
{% endhighlight %}

* Recibir el objeto

{% highlight java %}
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_dashboard);
        extras();
        tviMessage= (TextView)findViewById(R.id.tviMessage);

        //mostrar parcelable
        if(personEntity!=null)
        tviMessage.setText(personEntity.toString());
    }

    private void extras() {
        if(getIntent()!=null)
        {
            personEntity= getIntent().getParcelableExtra("ENTITY");
        }
    }
{% endhighlight %}


El proyecto está disponible para su descarga en mi [GitHub][repo].

[gb]:      https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/android_parcelable_plugin_androidstudio
[gdglima]: http://www.gdglima.com/

