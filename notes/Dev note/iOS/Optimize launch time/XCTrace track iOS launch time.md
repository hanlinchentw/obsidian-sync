#ios #optimization 

### Launch time
When it comes to app launch time measurement, we usually talk about two types of launching:
- Cold Launch: A cold start refers to an app's starting **from scratch**. This means that until this start, the system's process creates the app's process. Cold starts happen in cases such as your app launching for the first time since the device **booted** or since the system **killed** the app.
- Hot Launch: A hot start of your app has lower overhead than a cold start. In a hot start, the system **brings your activity to the foreground**. If all of your app's activities are still resident in memory, then the app can avoid repeating object initialization, layout inflation, and rendering.
Ref: <a href="https://developer.android.com/topic/performance/vitals/launch-time">App startup time</a>

iOS enable developers to calculate cold start time. The time is calculated by capturing initial frame rendering (IFR). Before getting IFR, the process might come through several steps:
![[截圖 2025-04-29 下午4.55.13.png]]
### How to track launch time
1. <a href="https://github.com/Unity-Technologies/AppiumWebDriverAgent">WebDriverAgent</a>
> WebDriverAgent is a [WebDriver server](https://w3c.github.io/webdriver/webdriver-spec.html) implementation for iOS that can be used to remote control iOS devices. It allows you to launch & kill applications, tap & scroll views or confirm view presence on a screen. This makes it a perfect tool for application end-to-end testing or general purpose device automation.

By calling system's api to record frame by frame and track the changes between them, devs can infer app's launch time.
Pros:
- No need to resign ipa
Cons:
- Steep learning curve, as it's served for test automation.
- Unstable method, checking difference between frame 

2. Event tracking
Place two events, one for App's icon appear time, the other for one of the components in App's home page. Then, compare the time difference between two events.
Pros:
- Reliable
Cons:
- Require dev place these events

3. Use Instruments CLI
generate .trace file and analyze the file by using tools like TraceUtility. However, TraceUtility is deprecated.

Pros:
- Instruments: Apple's support
- CLI is easy to use
Cons:
- generated .trace is binary format, requiring GUI tool, like TraceUtility, to inspect it.
- TraceUtility is actually a iOS application which calls Instruments' private API. This makes TraceUtility only support Xcode 10 and older.

4. XCTrace CLI
Pros:
- Compare to Instruments CLI, it can output visualized file.
Cons:
- Apple developer account and profile are required to be the same bundle, i.e, bundle ID have to be the same.

### XCTrace usage

**Command: Generate .trace file**
```shell
xcrun xctrace record --template 'App Launch' --device ${UUID or device name} --output test.trace --launch ${App name}.app'
```
How to find UUID:
![[截圖 2025-04-29 下午5.19.42.png]]

**Analyze .trace**
```
xcrun xctrace export --input test.trace --toc
```
![[截圖 2025-04-29 下午5.21.49.png]]
This step is to get what schemas does .trace record. For example, we may want to  look into `schema="life-cycle-period"` which includes app's launch time. We can add it for --xpath.
Then, we can run the following command to see the table (life-cycle-period)
```shell
xcrun xctrace export --input test.trace --xpath '/trace-toc/run[@number="1"]/data/table[@schema="life-cycle-period"]' >> test.xml
```

To this stage, `test.xml` is already an accessible report. 
![[截圖 2025-04-29 下午5.33.35.png]]
But, instruments provides a pretty GUI for us to check the report.
![[截圖 2025-04-29 下午5.27.02.png]]

### Optimize application launch time
<a href="https://developer.apple.com/documentation/xcode/reducing-your-app-s-launch-time">Apple: Recuding your app's launch time</a>

### [Reduce dependencies on external frameworks and dynamic libraries](https://developer.apple.com/documentation/xcode/reducing-your-app-s-launch-time#Reduce-dependencies-on-external-frameworks-and-dynamic-libraries)

Before any of your code runs, the system must find and load your app’s executable and any libraries on which it depends.

The dynamic loader (`dyld`) loads the app’s executable file, and examines the Mach load commands in the executable to find frameworks and dynamic libraries that the app needs. It then loads each of the frameworks into memory, and resolves dynamic symbols in the executable to point to the appropriate addresses in the dynamic libraries.

Each additional third-party framework that your app loads adds to the launch time. Although `dyld` caches a lot of this work in a launch closure when the user installs the app, the size of the launch closure and the amount of work done after loading it still depend on the number and sizes of the libraries loaded. You can reduce your app’s launch time by limiting the number of 3rd party frameworks you embed.

### [Remove or reduce the static initializers in your code](https://developer.apple.com/documentation/xcode/reducing-your-app-s-launch-time#Remove-or-reduce-the-static-initializers-in-your-code)

Certain code in an app must run before iOS runs your app’s `main()` function, adding to the launch time. This code includes:

- C++ static constructors.
- Objective-C `+load` methods defined in classes or categories.
- Functions marked with the clang attribute `__attribute__((constructor))`.
- Any function linked into the `__DATA,__mod_init_func` section of an app or framework binary.

### [Move expensive tasks out of your app delegate](https://developer.apple.com/documentation/xcode/reducing-your-app-s-launch-time#Move-expensive-tasks-out-of-your-app-delegate)

Audit your initialization code to delay expensive work. The system calls methods of your app delegate during the launch cycle to give you time to perform required tasks. These methods execute synchronously on the main thread, and the launch cycle doesn’t finish until both methods return successfully. As a result, any expensive tasks you perform from the methods delay the completion of that launch cycle.

UIKit initializes an instance of your app delegate class (the class that conforms to the [`UIApplicationDelegate`](https://developer.apple.com/documentation/UIKit/UIApplicationDelegate) protocol) and sends it the [`application(_:willFinishLaunchingWithOptions:)`](https://developer.apple.com/documentation/UIKit/UIApplicationDelegate/application\(_:willFinishLaunchingWithOptions:\)) and [`application(_:didFinishLaunchingWithOptions:)`](https://developer.apple.com/documentation/UIKit/UIApplicationDelegate/application\(_:didFinishLaunchingWithOptions:\)) messages. UIKit sends these messages on the main thread, and time spent executing code in these methods increases your app’s launch time. Do only the work necessary to prepare your app’s initial display in these methods; defer other tasks to more appropriate times in the app’s life cycle.