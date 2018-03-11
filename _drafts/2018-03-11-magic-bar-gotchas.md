---
date: 2018-03-11
title: Magic Bar Gotchas
categories:
  - CaliCalo
  - ios
author_staff_member: ryan
featured_image: calicalo/magic-bar.jpg
image:
  path: /images/calicalo/magic-bar.jpg
---


Recently, we released a new version of CaliCalo with the Magic Bar. The Magic Bar helps users track their progress towards a 3500 calorie burn/surplus. Seems like a simple enough idea. The implementation was not so simple. This post discusses some of the gotchas we came across while implementing the Magic Bar

### Gotchas

- User settings impact
- Initial sync
- How often to allow user to sync and the impact of data we don't control
- Handling negative progress
- Using RxSwift
- Synchronizing animations
- Net calorie loading indicator vs Magic Bar loading indicator
- Reset/Hide Magic Bar
- Showing Magic Bar upon IAP