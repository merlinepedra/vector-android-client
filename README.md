# Free Market Android Client

## Getting Started

These instructions will get you a copy of the project up and running on your local machine for development and testing purposes.

### Prerequisites

* android >= 6.0
* Orbot application

### Installations

* Manually install apk to android or download source code and compile

### Compilation

* Install Android Studio and compile project

## Architecture

### Components

Application uses variation of MVC architecture with core components definded as:

* Activities - controllers - classes handle user input and render application pages
* Adapters - data display classes of various elements, in particular RecyclerView
* API - classes that describe the model of interaction with the REST API
* Config - сlasses containing static variables
* FragmentAdapters - helper classes to help display Fragments
* Fragments - classes that display fragments of different data on application pages on activities
* Helpers - classes that help process and store data from different sources
* Models - POJO objects 1-1 mppings of undelying JSON APS
* Services - handle notifications
* Stores - classes for interacting with API and storing results
* Tools - some functions allow to process data
* ViewModels - objects to help describe UI elements

#### Activities

Application supports masquerading -- hiding activities in case of app restore and showing disguised calculator. Here's the code doing that.

```java
@Override
    protected void onRestart() {
        super.onRestart();
        checkSecuredActivity(this);
    }
```

We redefine onRestart, to check whether the activity was restored or the usual transition to our application. If the app is opened from the outside, we redirect to ActivityCalc where required to enter a password for access.

For this mechanism to work immediately after setContentView call to save information about the last working Activity.

```java
ActivityStart.workData.setCurrentActivity(this.getClass().toString());
```

It is also necessary to initialize before setContentView in each activity call

```java
setupNonScreenShot(this);
```

This prohibits taking screenshots of our app.

To initialize the bottom menu, call the method

```java
initBottomMenu(this,"current_activity"); // if this item is not present, then specify an empty string.
```

Add to Layout

```xml
<include layout="@layout/menu_bottom" />
```

To initialize the dark theme and styles call

```java
applyTheme(this,null,cnt);
```

##### Initialize Drawer Activity

Create layout

```xml
<?xml version="1.0" encoding="utf-8"?>
<androidx.drawerlayout.widget.DrawerLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/drawer_layout"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    tools:openDrawer="start"
    android:background="@color/black">
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    <!-- content here -->

        <include layout="@layout/menu_bottom" />
    </RelativeLayout>
    <include
        layout="@layout/app_bar_activity_main"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

    <com.google.android.material.navigation.NavigationView
        android:id="@+id/nav_view"
        android:layout_width="wrap_content"
        android:layout_height="match_parent"
        android:layout_gravity="start"
        android:fitsSystemWindows="true"
        app:headerLayout="@layout/nav_header_activity_main"
        app:menu="@menu/activity_main_drawer" />
</androidx.drawerlayout.widget.DrawerLayout>
```

Inside the onCreate function called

```java
setupDrawer(cnt,this);
```

#### API

```market.free.vector.api.RESTPAPIClient``` - Client is responsible for ineracting with server via REST API. It implements a secure client of ```info.guardianproject.netcipher.client.StrongOkHttpClientBuilder``` ands uses okhttp3 library for underlying operations. All fetched data returned in models.

```market.free.vector.api.VectorAPIClient``` subclasses ```market.free.vector.api.RESTPAPIClient``` and implements Vector REST API calls.

#### Models

Each model is POJO object that is direct reflection of Server-Side JSON model. Models are annotated with [GSON](https://github.com/google/gson) for parsing.

Refer to code for list of activities. All calsses have JAVADOC strings.

#### Stores

Classes that help to save data for caching and provide up to date information using API. All data is stored in memory using class SelfExpiringHashMap allows you to create caches for the time period. If there is no cache data for a particular request, the request is made to the API and cached again.

All stores are defined as singleton objects and kept in memory all application lifecycle.

## Storyboard

1. ActivityCalc - security check and run init activity
2. ActivityStart - initialize OrbotConnection 
3. ActivitySearch - first activity after init, for search and navigation
4. Other activities

## License
 
The MIT License (MIT)

Copyright (c) 2015 Chris Kibble

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

