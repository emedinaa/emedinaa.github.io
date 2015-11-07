---
layout: post
title:  "MVP en Android"
date:   2015-10-08 20:23:27
categories: android patterns
---
MVP ( Model View Presenter) en Android.

Ejemplo de como implementar un Login de Usuario utilizando el patrón MVP.

{% highlight java %}
	Log.v(TAG, "MVP en Android");
{% endhighlight %}

* Requisitos

    - GSON , Librería Java para serializar y deserializar Objetos  a JSON y viceversa. [GSON][gson]
    - Volley, Librería Http para consumir servicios REST. [Volley][volley]
    - Parse REST API, Utilizo el servicio de Parse para el backend. [Parse][parse]  - [Parse REST API][parserest]

* Diagrama MVP
![Diagrama MVP]({{ site.url }}/assets/mvp_android.jpg)

* Diagrama Ejemplo Login
![Diagrama ejemplo MVP]({{ site.url }}/assets/mvp_diagrama_ejemplo.jpg)

* Model , En esta capa se definen las entidades que vamos a utilizar para el flujo de datos.
    - UserEntity & LoginResponse
 
{% highlight java %}
    package com.emedinaa.androidmvp.model.entity.response;

    import java.io.Serializable;

    /**
     - Created by emedinaa on 19/05/15.
     */
    public class LoginResponse implements Serializable {

        /*{
            "createdAt": "2015-05-19T20:11:22.931Z",
                "objectId": "fSVL0hhKgc",
                "sessionToken": "r:PxcekgeZiG3afFZGArdZdQ54w",
                "updatedAt": "2015-05-19T20:30:00.793Z",
                "username": "emedinaa"
        }*/

        private  String createdAt;
        private  String objectId;
        private  String sessionToken;
        private  String updatedAt;
        private  String username;

        public String getCreatedAt() {
            return createdAt;
        }

        public void setCreatedAt(String createdAt) {
            this.createdAt = createdAt;
        }

        public String getObjectId() {
            return objectId;
        }

        public void setObjectId(String objectId) {
            this.objectId = objectId;
        }

        public String getSessionToken() {
            return sessionToken;
        }

        public void setSessionToken(String sessionToken) {
            this.sessionToken = sessionToken;
        }

        public String getUpdatedAt() {
            return updatedAt;
        }

        public void setUpdatedAt(String updatedAt) {
            this.updatedAt = updatedAt;
        }

        public String getUsername() {
            return username;
        }

        public void setUsername(String username) {
            this.username = username;
        }

        @Override
        public String toString() {
            return "LoginResponse{" +
                    "createdAt='" + createdAt + '\'' +
                    ", objectId='" + objectId + '\'' +
                    ", sessionToken='" + sessionToken + '\'' +
                    ", updatedAt='" + updatedAt + '\'' +
                    ", username='" + username + '\'' +
                    '}';
        }
    }
{% endhighlight %}

* View , esta capa es la interfaz de usuario, donde se ejecutan las acciones y  muestran los datos. En nuestro caso (Android) serían las actividades y fragments.
    - LoginView & LoginActivity
 
{% highlight java %}
    package com.emedinaa.androidmvp.view;

    import android.content.Context;

    /**
     - Created by emedinaa on 21/08/15.
     */
    public interface LoginView {

        void showLoading(boolean state);
        void onRequestSuccess(Object object);
        void onRequestError(Object object);

        Context getContext();
    }
{% endhighlight %}

{% highlight java %}
    package com.emedinaa.androidmvp;

    import android.content.Context;
    import android.content.Intent;
    import android.support.v7.app.ActionBarActivity;
    import android.os.Bundle;
    import android.util.Log;
    import android.view.Menu;
    import android.view.MenuItem;
    import android.view.View;
    import android.widget.EditText;


    import com.android.volley.AuthFailureError;
    import com.android.volley.Request;
    import com.android.volley.RequestQueue;
    import com.android.volley.Response;
    import com.android.volley.VolleyError;
    import com.android.volley.toolbox.JsonObjectRequest;
    import com.android.volley.toolbox.Volley;
    import com.emedinaa.androidmvp.presenter.LoginPresenter;
    import com.emedinaa.androidmvp.view.LoginView;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.emedinaa.androidmvp.model.entity.response.LoginResponse;

    import org.json.JSONObject;

    import java.util.HashMap;
    import java.util.Map;


    public class LoginActivity extends ActionBarActivity  implements LoginView{

        private static final String TAG = "HomeActivity";
        private EditText eteUsername,etePassword;
        private View btnLogin,vLoading,tviSignIn;

        private String username, password;
        private LoginPresenter loginPresenter;

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_login);
            loginPresenter= new LoginPresenter(this);
            initiateUI();
        }

        private void initiateUI() {
            eteUsername = (EditText)findViewById(R.id.eteUsername);
            etePassword = (EditText)findViewById(R.id.etePassword);
            btnLogin = findViewById(R.id.btnLogin);
            vLoading = findViewById(R.id.vLoading);
            tviSignIn = findViewById(R.id.tviSignIn);
            vLoading.setVisibility(View.GONE);
            events();
        }

        private void events() {
            btnLogin.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {

                    if (validate()) {
                        login();
                    }
                }
            });
            tviSignIn.setOnClickListener(new View.OnClickListener() {
                @Override
                public void onClick(View view) {
                    gotoSignIn();
                }
            });
        }

        private boolean validate() {

            username = eteUsername.getText().toString().trim();
            password = etePassword.getText().toString().trim();

            eteUsername.setError(null);
            etePassword.setError(null);
            if(username.isEmpty())
            {
                eteUsername.setError(getString(R.string.msg_ingresar));
                return false;
            }
            if(password.isEmpty())
            {
                etePassword.setError(getString(R.string.msg_ingresar));
                return false;
            }
            return true;
        }

        private void gotoSignIn() {

        }

        private void login()
        {
            showLoading(true);
            loginPresenter.login(username,password);
        }

        @Override
        public void showLoading(boolean state) {
            int visibility= (state)?(View.VISIBLE):(View.GONE);
            vLoading.setVisibility(visibility);
        }

        @Override
        public void onRequestSuccess(Object object) {
            showLoading(false);
            gotoHome();
        }


        @Override
        public void onRequestError(Object object) {
            showLoading(false);
            //TODO mostrar mensaje de Error
        }

        private void gotoHome() {
            startActivity(new Intent(this,MainActivity.class));
            finish();
        }

        @Override
        public Context getContext() {
            return this;
        }

        @Override
        public boolean onCreateOptionsMenu(Menu menu) {
            return false;
        }

        @Override
        public boolean onOptionsItemSelected(MenuItem item)
        {
            return false;
        }
    }
{% endhighlight %}

* Presenter, en esta capa va la lógica de la app , actua sobre el Model y el View. En el ejemplo lo usaremos para realizar la conexión con parse, recibirá los datos del View "username" y "password" y luego mostrará respuesta del servidor en el View.

    - LoginPresenter
	
{% highlight java %}
    package com.emedinaa.androidmvp.presenter;

    import android.util.Log;
    import android.view.View;

    import com.android.volley.AuthFailureError;
    import com.android.volley.Request;
    import com.android.volley.RequestQueue;
    import com.android.volley.Response;
    import com.android.volley.VolleyError;
    import com.android.volley.toolbox.JsonObjectRequest;
    import com.android.volley.toolbox.Volley;
    import com.emedinaa.androidmvp.R;
    import com.emedinaa.androidmvp.model.entity.response.LoginResponse;
    import com.emedinaa.androidmvp.view.LoginView;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;

    import org.json.JSONObject;

    import java.util.HashMap;
    import java.util.Map;

    /**
     - Created by emedinaa on 21/08/15.
     */
    public class LoginPresenter {

        private static final String TAG ="LoginPresenter" ;
        private LoginView loginView;
        private RequestQueue queue;
        private LoginResponse loginResponse;

        public LoginPresenter(LoginView loginView) {
            this.loginView = loginView;
        }

        public void login(String username, String password)
        {
            queue = Volley.newRequestQueue(loginView.getContext());

            final String url = loginView.getContext().getString(R.string.url_login)+"?username="+username+"&password="+password;
            final String appID= loginView.getContext().getString(R.string.app_id);
            final String key= loginView.getContext().getString(R.string.rest_key);

            Log.i(TAG, "url " + url);

            JsonObjectRequest jsonObjReq = new JsonObjectRequest(Request.Method.GET,
                    url, null,
                    new Response.Listener<JSONObject>()
                    {

                        @Override
                        public void onResponse(JSONObject response)
                        {
                            Log.i(TAG, "response "+response.toString());
                            GsonBuilder gsonb = new GsonBuilder();
                            Gson gson = gsonb.create();

                            loginResponse=null;
                            try
                            {
                                loginResponse= gson.fromJson(response.toString(),LoginResponse.class);
                                if(loginResponse!=null)
                                {
                                    Log.i(TAG, "loginResponse "+loginResponse.toString());
                                    loginView.onRequestSuccess(response);
                                }

                            }catch (Exception e)
                            {
                                loginView.onRequestError(e);
                            }

                        }
                    }, new Response.ErrorListener() {

                @Override
                public void onErrorResponse(VolleyError error)
                {
                    Log.i(TAG, "Error: " + error);
                    loginView.onRequestError(error);
                }
            })
            {
                @Override
                public Map<String, String> getHeaders() throws AuthFailureError {
                    Map<String, String>  params = new HashMap<String, String>();
                    params.put("X-Parse-Application-Id", appID);
                    params.put("X-Parse-REST-API-Key", key);

                    return params;
                }
            };
            queue.add(jsonObjReq);
        }

        public void login(Object object)
        {

        }
    }
{% endhighlight %}
* Configurar Android Manifest
    - Permisos y declaración de actividades
	
{% highlight xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <manifest xmlns:android="http://schemas.android.com/apk/res/android"
        package="com.emedinaa.androidmvp" >

        <uses-permission android:name="android.permission.INTERNET" />

        <application
            android:allowBackup="true"
            android:icon="@mipmap/ic_launcher"
            android:label="@string/app_name"
            android:theme="@style/AppTheme" >
            <activity
                android:name=".LoginActivity"
                android:label="@string/app_name"
                android:screenOrientation="portrait">
                <intent-filter>
                    <action android:name="android.intent.action.MAIN" />

                    <category android:name="android.intent.category.LAUNCHER" />
                </intent-filter>
            </activity>
            <activity
                android:name=".MainActivity"
                android:label="@string/title_activity_main"
                android:screenOrientation="portrait">
            </activity>
        </application>

    </manifest>
{% endhighlight %}

* Credenciales, conexión con parse. 
    - REST API Key y Application ID

{% highlight xml %}
    <?xml version="1.0" encoding="utf-8"?>
    <resources>
        <string name="app_id">q27nkZnG7EwIBZpu4ElO2S5YcBEKikn9xfmxAzT8</string>
        <string name="rest_key">H7f0mZTNju0ln9jO2igL2ECv3ttE0S3PFNZ0FlOP</string>
        <string name="url_login">https://api.parse.com/1/login</string>
        <string name="url_user">https://api.parse.com/1/users</string>
    </resources>
{% endhighlight %}


* Referencias
	* MVP en Android: cómo organizar la capa de presentación [link][limecreativelabs]

El proyecto está disponible para su descarga en mi [GitHub][repo].


[gb]:    https://github.com/emedinaa
[web]:   http://www.eduardomedina.me/
[androidpe]: https://www.facebook.com/groups/androidpe/
[repo]: https://github.com/emedinaa/android-mvp
[gdglima]: http://www.gdglima.com/
[volley]: http://developer.android.com/intl/es/training/volley/index.html
[gson]: https://github.com/google/gson
[parse]: https://parse.com/
[parserest]: https://parse.com/docs/rest/guide
[limecreativelabs]: http://www.limecreativelabs.com/mvp-android/

