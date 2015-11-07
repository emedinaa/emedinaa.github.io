---
layout: post
title:  "Realm , una nueva base de datos para Android y IOS."
date:   2015-11-07 20:23:27
categories: android patterns
---
Ejemplo de como utilizar Realm en Android. [https://realm.io/][realm] 

Si han trabajado con SQlite o con algún ORM como por ejemplo ORMLite, GreenDAO y desean probar otra alternativa les recomiendo REALM. No es un sqlite, no es otro ORM para SQlite, es otra base de datos, mucho más rápida , esta hecha en C++ y es compatible para Android y IOS.

{% highlight java %}
	Log.v(TAG, "Realm en Android");
{% endhighlight %}

* Agregar Realm a nuestro proyecto

{% highlight java %}
    dependencies {
        compile fileTree(dir: 'libs', include: ['*.jar'])
        compile 'com.android.support:appcompat-v7:21.0.3'
        compile 'com.android.support:support-v4:21.0.0'
        compile 'com.android.support:cardview-v7:21.0.+'
        compile 'com.android.support:recyclerview-v7:21.0.+'
        compile 'com.android.support:design:22.2.0'

        compile 'io.realm:realm-android:0.83.1'
    }
{% endhighlight %}

* Creamos una entidad que extiende de "RealmObject"

{% highlight java %}
    package com.emedinaa.monkeyexample.storage;

    import io.realm.RealmObject;
    import io.realm.annotations.PrimaryKey;

    /**
     - Created by emedinaa on 18/10/15.
     */
    public class TypeRealm extends RealmObject {

        @PrimaryKey
        private String objectId;
        private String name;
        private int type_id;

        public String getObjectId() {
            return objectId;
        }

        public void setObjectId(String objectId) {
            this.objectId = objectId;
        }

        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getType_id() {
            return type_id;
        }

        public void setType_id(int type_id) {
            this.type_id = type_id;
        }
    }
{% endhighlight %}

* Luego para el CRUD, construimos otra clase "TypePokemonCrud"

{% highlight java %}
    package com.emedinaa.monkeyexample.storage;

    import android.content.Context;

    import com.emedinaa.monkeyexample.model.entity.TypeEntity;

    import java.util.ArrayList;
    import java.util.List;

    import io.realm.Realm;
    import io.realm.RealmResults;

    /**
     - Created by emedinaa on 18/10/15.
     */
    public class TypePokemonCrud {

        private Realm realm;
        private Context context;

        public TypePokemonCrud(Context context,Realm realm) {
            this.context= context;
            this.realm = realm;
        }

        public TypePokemonCrud(Context context) {
            this.context= context;
            this.realm = Realm.getInstance(this.context);
        }

        public void add(TypeEntity typeEntity)
        {
            realm.beginTransaction();

            // Add a type
            TypeRealm typeRealm = realm.createObject(TypeRealm.class);
            typeRealm.setObjectId(typeEntity.getObjectId());
            typeRealm.setName(typeEntity.getName());
            typeRealm.setType_id(typeEntity.getType_id());

            realm.commitTransaction();
        }

        public void saveList(List<TypeEntity> typeEntityList)
        {
            //realm.beginTransaction();
            //List<TypeEntity> realmRepos = realm.copyToRealmOrUpdate(typeEntityList);
            //realm.copyToRealmOrUpdate(typeEntityList);
            //realm.commitTransaction();
            realm.beginTransaction();
            TypeRealm typeRealm;
            for (TypeEntity typeEntity:typeEntityList
                 ) {
                typeRealm= new TypeRealm();
                typeRealm.setObjectId(typeEntity.getObjectId());
                typeRealm.setName(typeEntity.getName());
                typeRealm.setType_id(typeEntity.getType_id());
                realm.copyToRealmOrUpdate(typeRealm);
            }
            realm.commitTransaction();
        }

        public List<TypeEntity> all()
        {
            List<TypeRealm> data=realm.where(TypeRealm.class).findAll();
            List<TypeEntity> typeEntities = new ArrayList<>();
            TypeEntity typeEntity;
            for (TypeRealm typeRealm:data
                 ) {
                typeEntity= new TypeEntity();
                typeEntity.setObjectId(typeRealm.getObjectId());
                typeEntity.setName(typeRealm.getName());
                typeEntity.setType_id(typeRealm.getType_id());
                typeEntities.add(typeEntity);
            }
            return typeEntities;
        }

        public TypeEntity getTypeById(int type_id)
        {
            RealmResults<TypeRealm> typeEntities = realm.where(TypeRealm.class).equalTo("type_id", type_id).findAll();
            if(typeEntities.size()>0)
            {
                TypeRealm typeRealm=typeEntities.get(0);
                TypeEntity typeEntity= new TypeEntity();
                typeEntity.setObjectId(typeRealm.getObjectId());
                typeEntity.setName(typeRealm.getName());
                typeEntity.setType_id(typeRealm.getType_id());
                return typeEntity;
            }
            //User firstJohn = teenagers.where().equalTo("name", "John").findFirst();
            //TypeEntity typeEntity = teenagers.where().equalTo("name", "John").findFirst();
            return null;
        }


        public void close()
        {
            realm.close();
        }
    }
{% endhighlight %}


El proyecto está disponible para su descarga en mi [GitHub][repo].


[gb]:    https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/realm_android
[gdglima]: http://www.gdglima.com/
[volley]: http://developer.android.com/intl/es/training/volley/index.html
[gson]: https://github.com/google/gson
[parse]: https://parse.com/
[realm]: https://realm.io/

