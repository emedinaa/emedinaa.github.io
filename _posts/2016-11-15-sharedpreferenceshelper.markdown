---
layout: post
title:  "SharedPreferences Helper"
date:   2016-11-015 20:23:27
categories: android helper sharedpreferences
---

# SharedPreferencesHelper
SharedPreferencesHelper maneja las preferencias locales compartidas en nuestra Aplicación Android.

Shared Preferences(SP) es una de las opciones de persistencia de datos en Android que te permite almacenar  en tuplas, es decir ``` <Key, Value> ```, elementos  primitivos como String, Boolean,Double o  Integer. Por ejemplo para guardar el email o id del usuario al autenticarse , el puntaje obtenido o alguna opción seleccionada que necesitemos usar luego en nuestra App.

## El problema

He visto en algunas oportunidades que en el Activity o Fragment invocan el sharedpreferences , realizan operaciones como guardar , editar o eliminar algún valor . Este  código suelto, repetitivo , difícil de encontrar,  sobre todo cuando necesitamos hacer cambios, con el tiempo nos podrá generar errores. Además, sería una responsabilidad adicional que agregaríamos a la vista, lo cual no es correcto.

Un ejemplo : 
```
  private void saveEmail(String email){
    SharedPreferences sharedPref = getActivity().getPreferences(Context.MODE_PRIVATE);
    SharedPreferences.Editor editor = sharedPref.edit();
    editor.putString("myEmail", email);
    editor.commit();
  }
```

## Una solución

Seria genial tener un clase  con la responsabilidad de manejar las operaciones del sharedpreferences y que podamos probar con test cases. A este clase la llamaré SharedPreferencesHelper. 

¿Qué responsabilidades tendría?
  - Limpiar el SP
  - Agregar algún ``` <Key, Value> ```
  - Editar algún ``` <Key, Value> ```
  - Obtener algún ``` <Key, Value> ```

Creamos un interfaz que defina el comportamiento del SP Helper
```
  package com.emedinaa.sharedpreferences.storage;

  import com.emedinaa.sharedpreferences.entity.User;

  /**
   * Created by eduardomedina on 15/11/16.
   */
  public interface SharedPreferencesHelper {

      void saveEmail (String email);
      String email();

      void saveUser(User user);
      User user();

      void clear();
  }

```

Luego la implementación
```
  package com.emedinaa.sharedpreferences.storage;

  import android.content.SharedPreferences;

  import com.emedinaa.sharedpreferences.entity.User;
  import com.emedinaa.sharedpreferences.utils.GsonHelper;

  /**
   * Created by eduardomedina on 15/11/16.
   */
  public class DefaultSharedPreferencesHelper implements SharedPreferencesHelper {

      private  final String MY_SHARED_PREFERENCES = "com.emedinaa.sharedpreferences";
      private  final String KEY_EMAIL = MY_SHARED_PREFERENCES+".session.email";
      private  final String KEY_USER = MY_SHARED_PREFERENCES+".session.user";

      private final SharedPreferences sharedPreferences;
      private final GsonHelper gsonHelper;

      public DefaultSharedPreferencesHelper(GsonHelper gsonHelper,SharedPreferences sharedPreferences) {
          this.gsonHelper= gsonHelper;
          this.sharedPreferences = sharedPreferences;
      }

      @Override
      public void saveEmail(String email) {
          SharedPreferences.Editor editor = editor();
          editor.putString(KEY_EMAIL, email);
          editor.apply();
      }

      @Override
      public String email() {
          String  email= sharedPreferences.getString(KEY_EMAIL,"");
          return email;
      }

      @Override
      public void saveUser(User user) {
          SharedPreferences.Editor editor = editor();
          editor.putString(KEY_USER, gsonHelper.objectToJSON(user).toString());
          editor.apply();
      }

      @Override
      public User user() {
          String  userStr= sharedPreferences.getString(KEY_USER,"");
          User user= gsonHelper.jsonToObject(userStr,User.class);
          return user;
      }

      @Override
      public void clear() {
          SharedPreferences.Editor editor = editor();
          editor.clear();
          editor.apply();
      }

      private  SharedPreferences.Editor editor() {
          return sharedPreferences.edit();
      }
  }

```
Ejemplos de como usar el Helper

- Guardar y obtener el Email
```
    private void preferencesEmail() {
        SharedPreferences sharedPreferences= getSharedPreferences(MY_SHARED_PREFERENCES, Context.MODE_PRIVATE);
        GsonHelper gsonHelper= new GsonHelper();

        sharedPreferencesHelper= new DefaultSharedPreferencesHelper(gsonHelper,sharedPreferences);
        sharedPreferencesHelper.saveEmail("emedinaa@gmail.com");
        String email=sharedPreferencesHelper.email();

        Log.v(TAG, "email "+email);
    }
```
Output 
```
  V/MainActivity: email emedinaa@gmail.com
```


- Guardar y obtener una Entidad 
```
    private void preferencesGson() {
        SharedPreferences sharedPreferences= getSharedPreferences(MY_SHARED_PREFERENCES, Context.MODE_PRIVATE);
        GsonHelper gsonHelper= new GsonHelper();

        sharedPreferencesHelper= new DefaultSharedPreferencesHelper(gsonHelper,sharedPreferences);
        User user= new User();
        user.setId(100);
        user.setName("Eduardo Medina");
        user.setEmail("emedinaa@gmail.com");

        sharedPreferencesHelper.saveUser(user);
        User userSp= sharedPreferencesHelper.user();

        Log.v(TAG, "userSp "+userSp);
    }
```
Output 
```
  V/MainActivity: userSp User{id=100, name='Eduardo Medina', email='emedinaa@gmail.com'}
```

## Conclusión

Siempre es sano crear helpers que nos ayuden en tareas rutinarias,con responsabilidades definidas , que podamos probar y mantener el código ordenado y limpio. No recomiendo usar statics, ya saben… , es la solución fácil y podría ocultar dependencias que perjudican cuando hacemos testing.

References :

- [https://developer.android.com/training/basics/data-storage/shared-preferences.html](https://developer.android.com/training/basics/data-storage/shared-preferences.html)

- [https://developer.android.com/reference/android/content/SharedPreferences.html](https://developer.android.com/reference/android/content/SharedPreferences.html)



El proyecto está disponible para su descarga en mi [GitHub][repo].


[gb]:    https://github.com/emedinaa
[web]:   http://emedinaa.github.io/
[androiddevperu]: https://medium.com/@androiddevperu
[repo]: https://github.com/emedinaa/sharedpreferenceshelper


