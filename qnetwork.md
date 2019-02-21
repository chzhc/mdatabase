# QNetwork

[TOC]

1. 包含 Qt+=network 模组
2. QNetworkAccessManager 管理工具
3. QNetworkRequest 请求
4. QAbstractNetworkCache 缓存Cache
5. QNetworkReply  答复Reply

## QNetworkAccessManager 

The [QNetworkAccessManager](qnetworkaccessmanager.html) class allows the application to send network requests and receive replies

The Network Access API is constructed around one [QNetworkAccessManager](qnetworkaccessmanager.html) object, which holds the common configuration and settings for the requests it sends. It contains the proxy and cache configuration, as well as the signals related to such issues, and reply signals that can be used to monitor the progress of a network operation. One [QNetworkAccessManager](qnetworkaccessmanager.html) should be enough for the whole Qt application.

Once a [QNetworkAccessManager](qnetworkaccessmanager.html) object has been created, the application can use it to send requests over the network. A group of standard functions are supplied that take a request and optional data, and each return a [QNetworkReply](qnetworkreply.html) object. The returned object is used to obtain any data returned in response to the corresponding request.

A simple download off the network could be accomplished with:

```

```

```
  QNetworkAccessManager *manager = new QNetworkAccessManager(this);
```

```
  connect(manager, SIGNAL(finished(QNetworkReply*)),
```

```
          this, SLOT(replyFinished(QNetworkReply*)));
```

```

```

```
  manager->get(QNetworkRequest(QUrl("http://qt-project.org")));
```

```

```

[QNetworkAccessManager](qnetworkaccessmanager.html) has an asynchronous API. When the replyFinished slot above is called, the parameter it takes is the [QNetworkReply](qnetworkreply.html) object containing the downloaded data as well as meta-data (headers, etc.).

Note: After the request has finished, it is the responsibility of the user to delete the [QNetworkReply](qnetworkreply.html) object at an appropriate time. Do not directly delete it inside the slot connected to [finished](qnetworkaccessmanager.html#finished)(). You can use the [deleteLater](../qtcore/qobject.html#deleteLater)() function.

Note: [QNetworkAccessManager](qnetworkaccessmanager.html) queues the requests it receives. The number of requests executed in parallel is dependent on the protocol. Currently, for the HTTP protocol on desktop platforms, 6 requests are executed in parallel for one host/port combination.

A more involved example, assuming the manager is already existent, can be:

```

```

```
  QNetworkRequest request;
```

```
  request.setUrl(QUrl("http://qt-project.org"));
```

```
  request.setRawHeader("User-Agent", "MyOwnBrowser 1.0");
```

```

```

```
  QNetworkReply *reply = manager->get(request);
```

```
  connect(reply, SIGNAL(readyRead()), this, SLOT(slotReadyRead()));
```

```
  connect(reply, SIGNAL(error(QNetworkReply::NetworkError)),
```

```
          this, SLOT(slotError(QNetworkReply::NetworkError)));
```

```
  connect(reply, SIGNAL(sslErrors(QList<QSslError>)),
```

```
          this, SLOT(slotSslErrors(QList<QSslError>)));
```

```
 
```

Network and Roaming Support

With the addition of the [Bearer Management](bearer-management.html) API to Qt 4.7 [QNetworkAccessManager](qnetworkaccessmanager.html) gained the ability to manage network connections. [QNetworkAccessManager](qnetworkaccessmanager.html) can start the network interface if the device is offline and terminates the interface if the current process is the last one to use the uplink. Note that some platforms utilize grace periods from when the last application stops using a uplink until the system actually terminates the connectivity link. Roaming is equally transparent. Any queued/pending network requests are automatically transferred to the new access point.

Clients wanting to utilize this feature should not require any changes. In fact it is likely that existing platform specific connection code can simply be removed from the application.

Note: The network and roaming support in [QNetworkAccessManager](qnetworkaccessmanager.html) is conditional upon the platform supporting connection management. The [QNetworkConfigurationManager::NetworkSessionRequired](qnetworkconfigurationmanager.html#Capability-enum) can be used to detect whether [QNetworkAccessManager](qnetworkaccessmanager.html) utilizes this feature.

See also [QNetworkRequest](qnetworkrequest.html), [QNetworkReply](qnetworkreply.html), and [QNetworkProxy](qnetworkproxy.html).