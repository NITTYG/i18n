# Mac App Store Submission Guide

منذ v0.34.0، يسمح إلكترون بتقديم تطبيقات حزمة إلى متجر تطبيقات ماك (MAS). يوفر هذا الدليل معلومات عن: كيفية إرسال التطبيق الخاص بك و القيود على بناء MAS.

**Note:** Submitting an app to Mac App Store requires enrolling in the [Apple Developer Program][developer-program], which costs money.

## كيفية إرسال التطبيق الخاص بك

الخطوات التالية تقدم طريقة بسيطة لإرسال التطبيق الخاص بك إلى متجر تطبيقات Mac . However, these steps do not ensure your app will be approved by Apple; you still need to read Apple's [Submitting Your App][submitting-your-app] guide on how to meet the Mac App Store requirements.

### الحصول على شهادة

لإرسال تطبيقك إلى متجر تطبيقات Mac ، يجب عليك أولاً الحصول على شهادة من Apple. You can follow these [existing guides][nwjs-guide] on web.

### احصل مُعرّف الفريق

قبل تسجيل تطبيقك، تحتاج إلى معرفة معرف الفريق عن حسابك. لتحديد موقع معرف فريقك، قم بتسجيل الدخول إلى [مركز مطوري Apple](https://developer.apple.com/account/)، وانقر فوق العضوية في الشريط الجانبي. يظهر معرف فريقك في قسم معلومات العضوية تحت اسم الفريق.

### توقيع التطبيق الخاص بك

بعد الانتهاء من أعمال الإعداد، يمكنك حزم التطبيق الخاص بك من خلال متابعة [توزيع التطبيق](application-distribution.md)، ثم انتقل إلى توقيع التطبيق الخاص بك.

أولاً، يجب عليك إضافة مفتاح `ElectronTeamID` إلى معلومات التطبيق `الخاصة بك. قائمة`، التي تحتوي على معرف فريقك كقيمة:

```xml
<plist version="1.0">
<dict>
  ...
  <key>ElectronTeamID</key>
  <string>TEAM_ID</string>
</dict>
</plist>
```

ثم تحتاج إلى إعداد ثلاثة ملفات للاستحقاقات.

`child.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.inherit</key>
    <true/>
  </dict>
</plist>
```

`parent.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
  <dict>
    <key>com.apple.security.app-sandbox</key>
    <true/>
    <key>com.apple.security.application-groups</key>
    <array>
      <string>TEAM_ID.your.bundle.id</string>
    </array>
  </dict>
</plist>
```

`loginhelper.plist`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0. td">
<plist version="1.0">
  <dict>
    <key>com.apple.Security pp-sandbox</key>
    <true/>
  </dict>
</plist>
```

يجب عليك استبدال `TEAM_ID` بمعرف فريقك، واستبدال `معرف your.bundle.id` بمعرف الحزمة في التطبيق الخاص بك.

ثم قم بالتوقيع على التطبيق الخاص بك بالنص البرمجي التالي:

```sh
#!/bin/bash

# Name of your app.
APP="YourApp"
# The path of your app to sign.
APP_PATH="/path/to/YourApp.app"
# المسار إلى الموقع الذي تريد وضع الحزمة الموقعة.
RESULT_PATH="~/Desktop/$APP.pkg"
# اسم الشهادات التي طلبتها.
APP_KEY="تطبيق مطور Mac الخاص بالطرف الثالث: اسم الشركة (APIDENTITY)"
INSTALLER_KEY="مثبت مطور Mac الخاص بالطرف الثالث: اسم الشركة (APIDENTITY)
# مسار الملفات اللوحية الخاصة بك.
CHILD_PLIST="/path/to/child.plist"
PARENT_PLIST="/path/to/parent.plist"
LOGINHELPER_PLIST="/path/to/loginhelper.plist"

FRAMEWORKS_PATH="$APP_PATH/Contents/Frameworks"

codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Electron Framework"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libffmpeg.dylib"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework/Versions/A/Libraries/libnode.dylib"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/Electron Framework.framework"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/Contents/MacOS/$APP Helper"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$FRAMEWORKS_PATH/$APP Helper.app/"
codesign -s "$APP_KEY" -f --entitlements "$LOGINHELPER_PLIST" "$APP_PATH/Contents/Library/LoginItems/$APP Login Helper.app/Contents/MacOS/$APP Login Helper"
codesign -s "$APP_KEY" -f --entitlements "$LOGINHELPER_PLIST" "$APP_PATH/Contents/Library/LoginItems/$APP Login Helper.app/"
codesign -s "$APP_KEY" -f --entitlements "$CHILD_PLIST" "$APP_PATH/Contents/MacOS/$APP"
codesign -s "$APP_KEY" -f --entitlements "$PARENT_PLIST" "$APP_PATH"

productbuild --component "$APP_PATH" /Applications --sign "$INSTALLER_KEY" "$RESULT_PATH"
```

If you are new to app sandboxing under macOS, you should also read through Apple's [Enabling App Sandbox][enable-app-sandbox] to have a basic idea, then add keys for the permissions needed by your app to the entitlements files.

Apart from manually signing your app, you can also choose to use the [electron-osx-sign][electron-osx-sign] module to do the job.

#### تسجيل وحدات أصلية

الوحدات الأصلية المستخدمة في التطبيق الخاص بك تحتاج أيضا إلى توقيع. في حالة استخدام electron-osx-sign، تأكد من تضمين المسار إلى الثنائية التي تم بناؤها في قائمة الحجج :

```sh
electron-osx-sign YourApp.app YourApp.app/contents/Resources/app/node_modules/nativemodule/build/release/nativemodule
```

لاحظ أيضًا أن الوحدات الأصلية قد تحتوي على ملفات وسيطة تم إنتاجها والتي يجب عدم تضمينها لأنها تحتاج أيضًا إلى توقيع). If you use [electron-packager][electron-packager] before version 8.1.0, add `--ignore=.+\.o$` to your build step to ignore these files. الإصدارات 8.1.0 و في وقت لاحق تجاهل هذه الملفات بشكل افتراضي.

### رفع تطبيقك

After signing your app, you can use Application Loader to upload it to iTunes Connect for processing, making sure you have [created a record][create-record] before uploading.

### إرسال تطبيقك للمراجعة

After these steps, you can [submit your app for review][submit-for-review].

## المحدودية لبناء MAS

من أجل تلبية جميع المتطلبات لصندوق الرمل التطبيقي، تم تعطيل الوحدات النمطية التالية في إنشاء MAS:

* `crashReporter`
* `تحديث تلقائي`

وقد تم تغيير السلوكيات التالية:

* قد لا يعمل التقاط الفيديو لبعض الآلات.
* قد لا تنجح بعض ميزات الوصول.
* لن تكون التطبيقات على علم بتغييرات DNS.

Also, due to the usage of app sandboxing, the resources which can be accessed by the app are strictly limited; you can read [App Sandboxing][app-sandboxing] for more information.

### الاستحقاقات الإضافية

اعتمادًا على تطبيقات إلكترون التي تستخدمها تطبيقك، قد تحتاج إلى إضافة مستحقات إضافية إلى والد `الخاص بك. قائمة` ملف لتتمكن من استخدام هذه التطبيقات من إنشاء Mac App Store الخاص بك.

#### الوصول إلى الشبكة

تمكين اتصالات الشبكة الصادرة للسماح للتطبيق الخاص بك بالاتصال بالخادم:

```xml
<key>com.apple.security.network.client</key>
<true/>
```

تمكين اتصالات الشبكة الواردة للسماح لتطبيقك بفتح مقبس الاستماع للشبكة :

```xml
<key>com.apple.security.network.server</key>
<true/>
```

See the [Enabling Network Access documentation][network-access] for more details.

#### dialog.showOpenDialog

```xml
<key>com.apple.security.files.user-selected.read-فقط</key>
<true/>
```

See the [Enabling User-Selected File Access documentation][user-selected] for more details.

#### dialog.showSaveDialog

```xml
<key>com.apple.security.files.user-selected.read-write</key>
<true/>
```

See the [Enabling User-Selected File Access documentation][user-selected] for more details.

## خوارزميات التشفير المستخدمة من قبل إلكترون

اعتماداً على البلدان التي تقوم فيها بإصدار التطبيق الخاص بك، قد تكون مطلوبة لتوفير معلومات عن خوارزميات التشفير المستخدمة في برنامجك الخاص بك. See the [encryption export compliance docs][export-compliance] for more information.

يستخدم إلكترون تتبع خوارزميات التشفير:

* AES - [NIST SP 800-38A](https://csrc.nist.gov/publications/nistpubs/800-38a/sp800-38a.pdf)، [NIST SP 800-38D](https://csrc.nist.gov/publications/nistpubs/800-38D/SP-800-38D.pdf)، [RFC 3394](https://www.ietf.org/rfc/rfc3394.txt)
* HMAC - [FIPS 198-1](https://csrc.nist.gov/publications/fips/fips198-1/FIPS-198-1_final.pdf)
* ECDSA - ANS X9.62–2005
* ECDH - ANS X9.63–2001
* HKDF - [NIST SP 800-56C](https://csrc.nist.gov/publications/nistpubs/800-56C/SP-800-56C.pdf)
* PBKDF2 - [RFC 2898](https://tools.ietf.org/html/rfc2898)
* RSA - [RFC 3447](http://www.ietf.org/rfc/rfc3447)
* SHA - [FIPS 180-4](https://csrc.nist.gov/publications/fips/fips180-4/fips-180-4.pdf)
* بلوسمك - https://www.schneier.com/cryptography/blowfish/
* CAST - [RFC 2144](https://tools.ietf.org/html/rfc2144)، [RFC 2612](https://tools.ietf.org/html/rfc2612)
* DES - [FIPS 46-3](https://csrc.nist.gov/publications/fips/fips46-3/fips46-3.pdf)
* DH - [RFC 2631](https://tools.ietf.org/html/rfc2631)
* DSA - [ANSI X9.30](https://webstore.ansi.org/RecordDetail.aspx?sku=ANSI+X9.30-1%3A1997)
* EC - [SEC 1](http://www.secg.org/sec1-v2.pdf)
* IDEA - "في تصميم وأمن الكتل العلمية" كتاب X. Lai
* MD2 - [RFC 1319](https://tools.ietf.org/html/rfc1319)
* MD4 - [RFC 6150](https://tools.ietf.org/html/rfc6150)
* MD5 - [RFC 1321](https://tools.ietf.org/html/rfc1321)
* MDC2 - [ISO/IEC 10118-2](https://wiki.openssl.org/index.php/Manual:Mdc2(3))
* RC2 - [RFC 2268](https://tools.ietf.org/html/rfc2268)
* RC4 - [RFC 4345](https://tools.ietf.org/html/rfc4345)
* RC5 - http://people.csail.mit.edu/rivest/Rivest-rc5rev.pdf
* RIPEMD - [ISO/IEC 10118-3](https://webstore.ansi.org/RecordDetail.aspx?sku=ISO%2FIEC%2010118-3:2004)

[developer-program]: https://developer.apple.com/support/compare-memberships/
[submitting-your-app]: https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/SubmittingYourApp/SubmittingYourApp.html
[nwjs-guide]: https://github.com/nwjs/nw.js/wiki/Mac-App-Store-%28MAS%29-Submission-Guideline#first-steps
[enable-app-sandbox]: https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html
[create-record]: https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/CreatingiTunesConnectRecord.html
[electron-osx-sign]: https://github.com/electron-userland/electron-osx-sign
[electron-packager]: https://github.com/electron/electron-packager
[submit-for-review]: https://developer.apple.com/library/ios/documentation/LanguagesUtilities/Conceptual/iTunesConnect_Guide/Chapters/SubmittingTheApp.html
[app-sandboxing]: https://developer.apple.com/app-sandboxing/
[export-compliance]: https://help.apple.com/app-store-connect/#/devc3f64248f
[user-selected]: https://developer.apple.com/library/mac/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW6
[network-access]: https://developer.apple.com/library/ios/documentation/Miscellaneous/Reference/EntitlementKeyReference/Chapters/EnablingAppSandbox.html#//apple_ref/doc/uid/TP40011195-CH4-SW9