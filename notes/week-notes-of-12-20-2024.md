# Week Notes:
Week of December 20 - 26, 2024

## Work
#### Scroll Position
It’s still complex to detect a scroll position on iOS, when implementing it in lazy loading
```swift
let thresholdIndex = employerObservable.arrayObject.index(employerObservable.arrayObject.endIndex, offsetBy: -1)
if employerObservable.arrayObject.firstIndex(where: { $0.id == employerJob.id }) == thresholdIndex {
    loadMore()
}
```

And doing it in reverse feels a little more hacky.
```swift
.background(GeometryReader { geometry in
     let position = geometry.frame(in: .named("scrollPosition")).origin.y
     Color.clear.onChange(of: position) { position, _ in
     	let _ = print("Scroll position = \(position.rounded())")
     	if position.rounded() == 1.0 {
			loadMore()
     	}
     }
})
```

Compared this with Jetpack Compose, which is much more simpler and straightforward.
```kotlin
val lastIndex = messageViewModel.arrayObject.lastIndex
LazyColumn {
    itemsIndexed(messageViewModel.arrayObject.reversed()) { index, object ->
        MessageThreadRow(object = object)
        if (index == lastIndex) {
            LaunchedEffect(Unit, block = {
                // Get more data
            })
        }
    }
}

```

#### NSInternalInconsistencyException • UINavigationBar
This crash report has been around since last year, I have no clue what’s happening. My suspicion was correct, that this was from Apple because this crash is no longer around iOS 18 above.
```bash
Thread 0 - (TH_STATE_RUNNING)
0  CoreFoundation +0x83f1c  ___exceptionPreprocess
1  libobjc.A.dylib +0x172b4 _objc_exception_throw
2  Foundation +0x6de7f4     -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:]
3  UIKitCore +0x2e34f4      -[UINavigationBar layoutSubviews]
4  UIKitCore +0x10914       -[UIView(CALayerDelegate) layoutSublayersOfLayer:]
5  UIKitCore +0x167998      -[UINavigationBar layoutSublayersOfLayer:]
6  QuartzCore +0x7f268      CA::Layer::layout_if_needed(CA::Transaction*)
7  UIKitCore +0x59d58       -[UIView(Hierarchy) layoutBelowIfNeeded]
8  UIKitCore +0x102538      -[UINavigationController _positionNavigationBarHidden:edge:initialOffset:]
9  UIKitCore +0x164b18      -[UINavigationController _positionNavigationBarHidden:edge:]
10 UIKitCore +0x10a598      -[UINavigationController _updateBarsForCurrentInterfaceOrientationAndForceBarLayout:]
11 UIKitCore +0x2b9cc8      -[UIViewController viewDidMoveToWindow:shouldAppearOrDisappear:]
12 UIKitCore +0x2b73cc      -[UINavigationController viewDidMoveToWindow:shouldAppearOrDisappear:]
13 UIKitCore +0x2971c       -[UIView(Internal) _didMoveFromWindow:toWindow:]
14 UIKitCore +0x29560       -[UIView(Internal) _didMoveFromWindow:toWindow:]
15 UIKitCore +0x29560       -[UIView(Internal) _didMoveFromWindow:toWindow:]
16 UIKitCore +0x29560       -[UIView(Internal) _didMoveFromWindow:toWindow:]
17 UIKitCore +0x1a938       ___45-[UIView(Hierarchy) _postMovedFromSuperview:]_block_invoke
18 CoreAutoLayout +0x11f10  -[NSISEngine withBehaviors:performModifications:]
19 UIKitCore +0x1d7dc       -[UIView _postMovedFromSuperview:]
20 UIKitCore +0x1c298       -[UIView(Internal) _addSubview:positioned:relativeTo:]
21 UIKitCore +0x3b52c4      -[UITransitionView transition:fromView:toView:removeFromView:]
22 UIKitCore +0x3b4f38      -[UITransitionView transition:toView:]
23 UIKitCore +0x3b2dd8      -[UITabBarController transitionFromViewController:toViewController:transition:shouldSetSelected:]
24 UIKitCore +0x427a70      -[UITabBarController _setSelectedViewController:performUpdates:]
25 UIKitCore +0x426d0c      -[UITabBarController setSelectedViewController:]
26 UIKitCore +0x85cde0      -[UITabBarController _setSelectedViewControllerAndNotify:]
27 UIKitCore +0x85cc10      -[UITabBarController _tabBarItemClicked:]
28 UIKitCore +0x3fb4f4      -[UIApplication sendAction:to:from:forEvent:]
29 UIKitCore +0x785e94      -[UITabBar _sendAction:withEvent:]
30 UIKitCore +0x3fb4f4      -[UIApplication sendAction:to:from:forEvent:]
31 UIKitCore +0x3fb3cc      -[UIControl sendAction:to:forEvent:]
32 UIKitCore +0x3fb21c      -[UIControl _sendActionsForEvents:withEvent:]
33 UIKitCore +0x7871d0      -[UITabBar _buttonUp:]
34 UIKitCore +0x3fb4f4      -[UIApplication sendAction:to:from:forEvent:]
35 UIKitCore +0x3fb3cc      -[UIControl sendAction:to:forEvent:]
36 UIKitCore +0x3fb21c      -[UIControl _sendActionsForEvents:withEvent:]
37 UIKitCore +0x913a10      -[UIControl touchesEnded:withEvent:]
38 UIKitCore +0x33bf74      -[UIWindow _sendTouchesForEvent:]
39 UIKitCore +0x33ba1c      -[UIWindow sendEvent:]
40 UIKitCore +0x1bc920      -[UIApplication sendEvent:]
41 UIKitCore +0x1be114      ___dispatchPreprocessedEventFromEventQueue
42 UIKitCore +0x1c6e0c      ___processEventQueue
43 UIKitCore +0x1c5680      ___eventFetcherSourceCallback
44 CoreFoundation +0x56830  ___CFRUNLOOP_IS_CALLING_OUT_TO_A_SOURCE0_PERFORM_FUNCTION__
45 CoreFoundation +0x567c4  ___CFRunLoopDoSource0
46 CoreFoundation +0x54294  ___CFRunLoopDoSources0
47 CoreFoundation +0x53480  ___CFRunLoopRun
48 CoreFoundation +0x52cd4  _CFRunLoopRunSpecific
49 GraphicsServices +0x11a4 _GSEventRunModal
50 UIKitCore +0x40aae4      -[UIApplication _run]
51 UIKitCore +0x4bed94      _UIApplicationMain
52 SwiftUI +0x3f4290        0x18c418290 (0x18c4181ec + 164)
53 SwiftUI +0x3a085c        0x18c3c485c (0x18c3c47c8 + 148)
54 SwiftUI +0x3ac618        0x18c3d0618 (0x18c3d0598 + 128)
55 OnlineJobs +0xafd60      main (OnlineJobsApp.swift)
56 dyld +0x3d150            start
```

## Personal Projects
#### Gradle Exception
An old Android project was throwing this error, it seems you just need to upgrade it to the latest Gradle version. I just don’t understand why refuse to update Gradle or Android Plugin frequently. This project was using a Gradle version 4 years ago.
```gradle
Caused by: org.gradle.api.GradleException: Cannot use @TaskAction annotation on method DataBindingGenBaseClassesTask.writeBaseClasses() because interface org.gradle.api.tasks.incremental.IncrementalTaskInputs is not a valid parameter to an action method.
	at org.gradle.api.internal.project.taskfactory.DefaultTaskClassInfoStore.createTaskAction(DefaultTaskClassInfoStore.java:124)
```

#### Android Text-to-Speech
I wrote a quick text-to-speech Android app for my son, which was recommended by our therapist. I haven’t tried it in Jetpack Compose, but I came up with this and it worked.
```kotlin
Scaffold(modifier = Modifier.fillMaxSize()) { innerPadding ->
    var text by remember { mutableStateOf("") }
    val context = LocalContext.current
    val tts = remember {
        mutableStateOf<TextToSpeech?>(null)
    }
    DisposableEffect(context) {
        val textToSpeech = TextToSpeech(context) { status ->
            if (status == TextToSpeech.SUCCESS) {
                tts.value?.language = Locale.US
            }
        }
        tts.value = textToSpeech
        onDispose {
            textToSpeech.stop()
            textToSpeech.shutdown()
        }
    }
    Column(
        modifier = Modifier
            .padding(innerPadding)
            .fillMaxSize(),
        horizontalAlignment = Alignment.CenterHorizontally
    ) {
        OutlinedTextField(
            value = text,
            onValueChange = {
                text = it
            },
            singleLine = false,
            modifier = Modifier.fillMaxWidth(),
            maxLines = 20
        )
        Button(onClick = {
            if (tts.value?.isSpeaking == true) {
                tts.value?.stop()
            } else {
                tts.value?.speak(text, TextToSpeech.QUEUE_FLUSH, null, null)
            }
        }, modifier = Modifier
            .fillMaxWidth(0.5f)
            .padding(top = 32.dp)) {
            Text(text = "Speak")
        }
    }
}

```
It’s amazing what you can do with just a few lines. No need to create `main_activity.xml` like the old Android view days.

#### Interesting Reads
