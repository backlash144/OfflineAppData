Offline Data Policy and Sync
===================

Here I am going to explain how can we utilize local cache and make mobile apps more responsive, and allows us to handle various concerns around network connectivity on a mobile device. Although I am familiar with Android platform but the discussed approaches can be applicable for any other mobile platforms.


Cache Policies
-------------
Policies explained here are based on ideas not code implementation for applications having different nature of data.
> **Example:**

> - Weather forecast data it may change every 15 minutes interval depending upon its implementation.

> - Personal content keeping apps like cloud drives do not care about frequent data change can follow a different cache approach

 1. **NO CACHE** 
This policy will not use any caching, and will execute every request online.
Use this policy if your application is dependant on data that is shared between multiple users and always needs to be up to date.

 2. **CACHE ONLY**
 This policy will only retrieve data from the cache, and will not use any network connection.

 3. **CACHE FIRST**
This policy will first attempt to retrieve data from the cache. If the data has been cached, it will be returned. If the data does not exist in the cache, the data will be retrieved from API server and the cache will be updated.
> Use this policy if your application can display data that doesn't change very often but you still want local updates.

 4. **CACHE FIRST NO REFRESH**
This policy will first attempt to retrieve data from the cache. If the data has been cached, it will be returned. If the data does not exist in the cache, the data will be retrieved from API server but the cache will not be updated with the new results.

 > Use this policy if you want to set default results, however if a request is made that cannot return these defaults a live request will be made (without modifying those default values)

 5. **NETWORK FIRST**
This policy will execute the request on the network, and will store the result in the cache. If the online execution fails, the results will be pulled from the cache.
>Use this policy if you application wants the latest data but you still want responsiveness if a connection is lost

 6. **BOTH**
This policy will first retrieve an element from the cache, and then it will attempt to execute the request on line. This caching policy will make two calls to the API server, either onSuccess or onFailure for both executing on the cache as well as executing online.

 >Use this policy if you want more responsiveness without sacrificing the consistency of data with your backend.


Offline Policies
-------------
Program your offline policies allowing you to ensure your app is responsive and data is up to date even without a reliable network connection.

You can set an Offline Policy on any implemented cache mechanism to allow your app to maintain a local copy of your entities, which will then be persisted with your backend once a connection is restored. Offline policy should locally persist entities as well as maintain a queue of requests performed while offline.

In android it can be achieved via background services. Other platforms can persist offline queues in separate cache files or by using local storage.

>**Think:**
While connected to a network, query q returns entities 1, 2, and 3. Offline policy automatically stores the query and the results.

>When a connection is lost, entity 3 is updated with a save request.

>While still offline, query q will now return entities 1, 2, and the updated entity 3
If entity 4 is added that meets the criteria for the query, it will not be returned with query q until a connection has been restored.

 1. **ONLINE FIRST** 
This policy will attempt to execute the request online first, and if that is successful will update the local store with the results. If the request fails due to connectivity issues, then the request will be executed against the local store. If it fails for any other reason such as an Authorization Error, the on Failure callback will be called to break the pattern in case of failure.

 >Use this policy if your application's data is constantly changing on the backend, but you want to support offline mode.

 2. **LOCAL FIRST** This policy will attempt to execute the request against the local store first. If the request is a Get, and the data cannot be found in the local store, then an online request will be attempted. If that suceeds, the store will be updated, and onSuccess will be called. If that fails, then onFailure will be called. For save requests, the local store will be updated and the entity will be returned through the onSuccess callback.

 >Use this policy if each user has their own data, and updates are not constantly required from your backend.

 3. **ALWAYS ONLINE** 
 This policy will not use any local storage or queuing, and will execute every request online. If no network connection is available, errors will be returned through the onFailure callback.

 >Use this policy if your application is fully dependent on data in the backend, and data cannot be stored locally.



Memory Policy
-------------
This policy relies on time and invalidates resource set based on last time pulled from backend. During caching a time of expiry can be set for each resource set while successful data pull from backend.

 - Refer to an implementation 
 https://github.com/NYTimes/Store/blob/develop/store/src/main/java/com/nytimes/android/external/store/base/impl/MemoryPolicy.java#L11


All above explained policies can be implemented independently or can be programmed by mixing different policies totally depends upon the nature of system and data.

Thanks for reading.

