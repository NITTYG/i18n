# नोटीफीकेशंस (विंडोज, लिनक्स, मैकओएस)

## Overview

All three operating systems provide means for applications to send notifications to the user. The technique of showing notifications is different for the Main and Renderer processes.

For the Renderer process, Electron conveniently allows developers to send notifications with the [HTML5 Notification API](https://notifications.spec.whatwg.org/), using the currently running operating system's native notification APIs to display it.

To show notifications in the Main process, you need to use the [Notification](../api/notification.md) module.

## उदाहरण

### Show notifications in the Renderer process

Assuming you have a working Electron application from the [Quick Start Guide](quick-start.md), add the following line to the `index.html` file before the closing `</body>` tag:

```html
<script src="renderer.js"></script>
```

and add the `renderer.js` file:

```js
const myNotification = new Notification('Title', {
  body: 'Notification from the Renderer process'
})

myNotification.onclick = () => {
  console.log('Notification clicked')
}
```

After launching the Electron application, you should see the notification:

![Notification in the Renderer process](../images/notification-renderer.png)

If you open the Console and then click the notification, you will see the message that was generated after triggering the `onclick` event:

![Onclick message for the notification](../images/message-notification-renderer.png)

### Show notifications in the Main process

Starting with a working application from the [Quick Start Guide](quick-start.md), update the `main.js` file with the following lines:

```js
const { Notification } = require('electron')

function showNotification () {
  const notification = {
    title: 'Basic Notification',
    body: 'Notification from the Main process'
  }
  new Notification(notification).show()
}

app.whenReady().then(createWindow).then(showNotification)
```

After launching the Electron application, you should see the notification:

![Notification in the Main process](../images/notification-main.png)

## Additional information

हालाँकि सभी ऑपरेटिंग सिस्टम्स में कोड और उपयोगकर्ता अनुभव समान हैं, पर फिर भी कुछ सूक्ष्म अंतर है |

### विंडोज

* On Windows 10, a shortcut to your app with an [Application User Model ID][app-user-model-id] must be installed to the Start Menu. This can be overkill during development, so adding `node_modules\electron\dist\electron.exe` to your Start Menu also does the trick. Navigate to the file in Explorer, right-click and 'Pin to Start Menu'. You will then need to add the line `app.setAppUserModelId(process.execPath)` to your main process to see notifications.
* On Windows 8.1 and Windows 8, a shortcut to your app with an [Application User Model ID][app-user-model-id] must be installed to the Start screen. हालाँकि, उसका स्टार्ट स्क्रीन पर पिनड होना ज़रूरी नहीं है |
* विंडोज 7 पर, नोटीफीकेशंस एक कस्टम इम्प्लीमेंटेशन के द्वारा काम करती हैं जो कि नये सिस्टम्स पर मूल वाले की तरह दिखाई देता है |

Electron attempts to automate the work around the Application User Model ID. When Electron is used together with the installation and update framework Squirrel, [shortcuts will automatically be set correctly][squirrel-events]. Furthermore, Electron will detect that Squirrel was used and will automatically call `app.setAppUserModelId()` with the correct value. During development, you may have to call [`app.setAppUserModelId()`][set-app-user-model-id] yourself.

इसके अलावा, विंडोज 8 में नोटीफीकेशन की अधिकतम लम्बाई सीमा 250 अक्षरों की है, और विंडोज टीम की सलाह है कि इसे 200 अक्षरों तक ही सीमित रखा जाये | विंडोज 10 में यह सीमा हटा दी गयी है, पर विंडोज टीम ने डेवलपर्स को वाज़िब सीमा रखने की सलाह दी है | ऐपीआई को बहुत सारा टेक्स्ट (हजारों अक्षर) भेजने की कोशिश करने से अस्थिरता उत्पन्न हो सकती है |

#### उन्नत नोटीफीकेशंस

विंडोज के नवीनतम संस्करणों में उन्नत नोटीफीकेशंस भेजने की सुविधा मौज़ूद है, जिसमे कि कस्टम टेम्पलेटस, चित्र, और दुसरे लचीले तत्व शामिल हैं | उन नोटीफीकेशंस को भेजेने के लिए (मुख्य प्रक्रिया से या फिर रेंदेरेर प्रक्रिया से), यूजरलैंड मोड्यूल [electron-windows-notifications](https://github.com/felixrieseberg/electron-windows-notifications) का इस्तेमाल करें, जो कि `ToastNotification` और `TileNotification` ऑब्जेक्ट्स को भेजने के लिए मूल नोड ऐडओंन्स का इस्तेमाल करते हैं |

While notifications including buttons work with `electron-windows-notifications`, handling replies requires the use of [`electron-windows-interactive-notifications`](https://github.com/felixrieseberg/electron-windows-interactive-notifications), which helps with registering the required COM components and calling your Electron app with the entered user data.

#### शांत घंटे/ प्रेजेंटेशन मोड

To detect whether or not you're allowed to send a notification, use the userland module [electron-notification-state](https://github.com/felixrieseberg/electron-notification-state).

This allows you to determine ahead of time whether or not Windows will silently throw the notification away.

### मैकओएस

मैकओएस पर नोटीफीकेशंस भेजना काफी सरल है, पर आपको [नोटीफीकेशंस सम्बंधित एप्पल की ह्यूमन इंटरफ़ेस गाइडलाइन्स][apple-notification-guidelines] के बारे में ज्ञात होना चाहिये |

याद रखें कि नोटीफीकेशंस का आकार 256 बाय्टेस तक ही सीमीत है और अगर आप इस सीमा के बाहर जाते हैं, तो बाकी की नोटीफीकेशन काट दी जायेगी |

#### उन्नत नोटीफीकेशंस

मैकओएस के नवीनतम संस्करणों में नोटीफीकेशंस में एक इनपुट फील्ड भी शामिल है, जिससे कि उपयोगकर्ता एक नोटीफीकेशन को तुरंत रिप्लाई कर सकता है | नोटीफीकेशंस को इनपुट फील्ड के साथ भेजने के लिए, यूजरलैंड मोड्यूल [node-mac-notifier][node-mac-notifier] का इस्तेमाल करें |

#### परेशान न करें/ सेशन स्टेट

आपको एक नोटीफीकेशन भेजने की इजाज़त है या नहीं, यह जाँचने के लिए, यूजरलैंड मोड्यूल [electron-notification-state][electron-notification-state] का इस्तेमाल करें |

इससे आपको समय से पहले यह पता चल जायेगा कि नोटीफीकेशन को दिखाया जायेगा या नहीं |

### लिनक्स

नोटीफीकेशंस को भेजने के लिए `libnotify` का इस्तेमाल किया जाता है, जो कि [Desktop Notifications Specification][notification-spec] का अनुसरण करने वाले सभी डेस्कटॉप वातावरणों में नोटीफीकेशंस को दिखा सकता है, जिनमे शामिल हैं सिन्नामन, एनलाइटमेंट, यूनिटी, ग्नोम, केडीई आदि |

[apple-notification-guidelines]: https://developer.apple.com/macos/human-interface-guidelines/system-capabilities/notifications/

[node-mac-notifier]: https://github.com/CharlieHess/node-mac-notifier

[electron-notification-state]: https://github.com/felixrieseberg/electron-notification-state

[notification-spec]: https://developer.gnome.org/notification-spec/
[app-user-model-id]: https://msdn.microsoft.com/en-us/library/windows/desktop/dd378459(v=vs.85).aspx
[set-app-user-model-id]: ../api/app.md#appsetappusermodelidid-windows
[squirrel-events]: https://github.com/electron/windows-installer/blob/master/README.md#handling-squirrel-events