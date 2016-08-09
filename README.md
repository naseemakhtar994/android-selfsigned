android-selfsigned
==================

[![Download](https://jitpack.io/v/onehilltech/android-selfsigned.svg)](https://jitpack.io/#onehilltech/android-selfsigned)
[![Build Status](https://travis-ci.org/onehilltech/android-selfsigned.svg)](https://travis-ci.org/onehilltech/android-selfsigned)
[![codecov.io](http://codecov.io/github/onehilltech/android-selfsigned/coverage.svg?branch=master)](http://codecov.io/github/onehilltech/android-selfsigned?branch=master)

A simple library for supporting self-signed certificates in Android

* Integrate with services that use **self-signed certificates**.
* **Preserve** existing security measures on the mobile device.
* Ideal for **prototyping and testing** using secure protocols.

**NOTE.** We strongly recommend that you purchase a certificate from a trusted authority 
when you move to production.

## Installation

#### Gradle

```
buildscript {
  repositories {
    maven { url "https://jitpack.io" }
  }
}

dependencies {
  compile com.github.onehilltech:android-selfsigned:x.y.z
}
```

## Getting Started

Manually define the list of hostnames/IP addresses that are using self-signed 
certificates. It is best to define the list as a resource so you can have
different list for different Gradle configurations:

```xml
<resources>
    <string-array name="hostnames">
        <!-- localhost on the Android emulator -->
        <item>10.0.2.2</item>
    </string-array>
</resources>
```

Define an `Application` class to initialize the `DefaultHostnameVerifier`, 
which is used by `HttpsURLConnection`.

```java
public class TheApplication extends Application 
{
  @Override
  public void onCreate ()
  {
    super.onCreate ();

    String [] hostnames = this.getResources ().getStringArray (R.array.hostnames);
    SelfSigned.getDefaultHostnameVerifier ().addAll (Arrays.asList (hostnames));
  }
}
```

Make sure you add the `TheApplication` class to `AndroidManifest.xml`.

```xml
<application
    android:name="[package].TheApplication"
    
    >
    
</application>
```

Add the public certificate to the application's assets. For example, if
the certificate is in a file named `server.crt`, then it must be added
to `main/assets/server.crt` (or the assets folder for the target configuration).


### android-volley

Use `VolleySelfTrust` to create a `RequestQueue` that is configured to use the
public certificate bundled as an asset:

```java
VolleySelfTrust.newRequestQueue (context, "server.crt")
```

Now, request executed on the returned `RequestQueue` that interact with an 
hostname/IP address defined in the resources above will not throw the usual 
security exceptions.
