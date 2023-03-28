---
date: 2023-03-23
title: Visualizing Data with Swift Charts
categories:
  - iOS
  - Swift
  - SwiftUI
  - Charts
author_staff_member: aj
featured_image: swift-charts/swift-charts-grid.png
image:
  path: images/swift-charts/swift-charts.png
---

Software UI development is so often about visualizing the plethora of data we constantly have available on our devices. A simple chart can communicate so much at a quick glance.

![3 charts displaying random data](/images/swift-charts/swift-charts.png)

Now, with [Swift Charts](https://developer.apple.com/documentation/charts) we can make that a reality in our apps easier than ever. Take for example, a basic Line Chart like the one used in [CaliCalo](https://base11studios.com/clients/calicalo/) to display a user's Diet vs. Active calorie count over 7 days.

![Trends screen of CaliCalo depicting a line graph displayed on iPhone](/images/swift-charts/calicalo-trends-screen-render.png)

With this screen, we want to communicate some basic but pivotal data quickly. For the last 7 days - how have you been balancing your NET calories (calories consumed vs. calories burned)? We do that by plotting 2 lines - one for calories consumed (diet) and one for calories burned by daily totals.

By quickly scanning the graph a user is able to see how their lines overlap. Depending on their goals, this can help inform progress. 

- For a user trying to maintain their weight, the goal may be to have the lines match up as closely as possible.
- For a user trying to lose weight, they would be trying to consistently keep the 'Diet' line below the 'Burned' line. Opposite for the user trying to gain weight.

For either user, the line graph can help identify large disparities or inconsistencies that can help guide behavior or tracking.

## Swift Charts

Apple's [Swift Charts](https://developer.apple.com/documentation/charts) library offers a quicky and easy way to [start plotting data](https://developer.apple.com/documentation/charts/creating-a-chart-using-swift-charts). We'll use this library, along with SwiftUI, to build out our Trends chart.

### Brief Overview

A quick explainer of Swift Charts in the world of SwiftUI. Basically, you have a `Chart` view that takes a body that should be a list of 'marks'. Marks can be things like [`BarMark`](https://developer.apple.com/documentation/charts/barmark), [`LineMark`](https://developer.apple.com/documentation/charts/linemark), or [`PointMark`](https://developer.apple.com/documentation/charts/pointmark) that plot out the data depending on the type of chart you're making.

#### Make a Mark

Let's look at an extremely simple example. Here, we're charting calories-per-day using some brute force. Let's just create a `Chart` with some `LineMark`s.

```swift
import SwiftUI
import Charts

struct SimpleLineChartView: View {
    var body: some View {
        VStack {
            Chart {
                LineMark(
                    x: .value("Day", Calendar.current.date(from: .init(year: 2023, month: 1, day: 1)) ?? Date()),
                    y: .value("Calories", 2000.0)
                )
                LineMark(
                    x: .value("Day", Calendar.current.date(from: .init(year: 2023, month: 1, day: 2)) ?? Date()),
                    y: .value("Calories", 1800.0)
                )
                LineMark(
                    x: .value("Day", Calendar.current.date(from: .init(year: 2023, month: 1, day: 3)) ?? Date()),
                    y: .value("Calories", 2300.0)
                )
            }
        }
    }
}
```

The code above results in a simple utilitarian looking chart that plots our 3 points on a line.

![basic line chart with 3 points](/images/swift-charts/line-chart-1a.png)

Cool to see some data visualized! Let's look at a slightly more complicated example, where we map 2 lines. This time, we're plotting the 2 types of calories we care about in [CaliCalo](https://base11studios.com/clients/calicalo/) - Diet and Burned. To do this, we need to plot 2 points for every day.

#### Add a Modifier

