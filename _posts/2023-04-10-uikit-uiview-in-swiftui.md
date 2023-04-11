---
date: 2023-04-09
title: Display a UIKit UIView inside a SwiftUI View
categories:
  - iOS
  - Swift
  - SwiftUI
  - UIKit
  - UIView
  - WKWebView
author_staff_member: ryan
featured_image: uikit-swiftui/loading.png
image:
  path: images/uikit-swiftui/loading.png
---

SwiftUI can display a UIKit UIView. Adapting UIViews keeps UIKit programming to a minimum by allowing SwiftUI views to supplement UIViews. For example, a `WKWebView` with an overlaid `ProgressView.`

![SwiftUI showing a WKWebview](/images/uikit-swiftui/loading.png)

## SwiftUI View

The WebView declares a `ZStack`, and places the `ProgressView` on top of the `WebViewSwiftUIAdapter` at the bottom of the page.

The `WebView` owns the `estimatedProgress` and is annotated with the `@State` property wrapper.

```swift
struct WebView: View {
    @State var estimatedProgress: Float = 0.0
    
    let url: URL
    
    var body: some View {
        ZStack(alignment: .bottomLeading) {
            WebViewSwiftUIAdapter(estimatedProgress: $estimatedProgress, url: url)
            if estimatedProgress < 1.0 {
                ProgressView(value: estimatedProgress)
                    .frame(maxWidth: .infinity)
                    .background(.white)
            }
            
        }
    }
}
```

## WebViewSwiftUIAdapter

The *SwiftUIAdapter* suffix can be used to identify any Swift UI views that wrap a `UIView`.

These adapters implement [UIViewRepresentable](https://developer.apple.com/documentation/swiftui/uiviewrepresentable){:target="_blank"}. `UIViewRepresentable` has two functions requiring implementation. SwiftUI calls `makeUIView` when it creates a view. SwiftUI calls `updateUIView` when it updates the view.

The `makeCoordinator` function returns a `Coordinator`. A `Coordinator` instance is of an `associatedtype` that allows communication between SwiftUI and UIKit.

```swift
struct WebViewSwiftUIAdapter: UIViewRepresentable {
    @Binding var estimatedProgress: Float
    let url: URL
    let webView = WKWebView()
    
    func makeCoordinator() -> WebViewCoordinator {
        WebViewCoordinator(self)
    }
    
    func makeUIView(context: Context) -> WKWebView {
        
        let request = URLRequest(url: url)
        webView.load(request)
        return webView
    }
    
    func updateUIView(_ webView: WKWebView, context: Context) {}
}
```

## WebViewCoordinator

The `WebViewCoordinator`  has a reference to the parent `WebViewSwiftUIAdapter`. The `WebViewCoordinator` can interact with the parent's `webView` and `estimatedProgress` properties.

The `WebViewCoordinator` subscribes to `estimatedProgress` updates from the `webView`. When the `webView` `estimatedProgress` changes, the `WebViewCoordinator` updates the parent's `estimatedProgress`. Since the parent's `estimatedProgress` is a `Binding` to the SwiftUI view's `State` property, SwiftUI updates the view.

```swift
class WebViewCoordinator: NSObject {
    let parent: WebViewSwiftUIAdapter
    var cancellable: AnyCancellable?
    
    init(_ parent: WebViewSwiftUIAdapter){
        self.parent = parent
        super.init()
        cancellable = parent.webView.publisher(for: \.estimatedProgress)
            .receive(on: RunLoop.main)
            .sink{ [weak self] in
                self?.parent.estimatedProgress = Float($0)
            }
        
    }
}
```
