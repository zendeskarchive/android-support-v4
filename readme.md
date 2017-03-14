This is a fork of support-v4 library version 23.1.1. `upstream` contains the original support-v4 code, `master` branch includes our changes in implementation (to fix some issues that affected us) and build scripts (to allow project compilation outside of AOSP).

Here's a detailed list of implementation changes between `upstream` and `master`, unless noted otherwise, the issues are *not* fixed in newer versions of support lib:

### [Backport of framework SimpleCursorAdapter fixes to support lib](https://github.com/futuresimple/android-support-v4/commit/e609f5d5a7051890744fb8c9d2b86c25379d1d0c)
Already fixed in [upstream](https://android.googlesource.com/platform/frameworks/support/+/91af1489f851fe8c4b015b519e2ce215189878b1)
    
### [Added `ViewPager.setCurrentItem(int item, int scrollDuration)` method](https://github.com/futuresimple/android-support-v4/commit/8b950d949c3b405b51f98c519d5654f03664136e)

### [`CursorLoader.onAbandon()` implementation](https://github.com/futuresimple/android-support-v4/commit/2d812385b695911c2261501fc0445457af3e3a11)
Prevents unnecessary data reload. Does not change the correctness of Loader implementation, might have a microscopic performance impact ;)

### Changes to observer registration in `CursorLoader` ([part 1](https://github.com/futuresimple/android-support-v4/commit/2145477a4ff73fd1018f14fb515af8c67b1da670), [part 2](https://github.com/futuresimple/android-support-v4/commit/896346795770e70dfe38c73b96be0d9014b19885))
Ensures the initial load is delivered as soon as possible, which seems to be the CursorLoaders author intention. Without this change the observer is registered in background thread and the change notification might be triggered before the result is delivered. In most pathological case it might cause infinite reloading.

### [Disallow using non-static anonymous class `Loaders` in `LoaderManager`](https://github.com/futuresimple/android-support-v4/commit/26973820453aeb53f68ea4efaafa1d9b5fe89bd6)
Supplements the existing logic for disallowing non-static member Loaders.

### [Fix cancellation issue in `LoaderManager`](https://github.com/futuresimple/android-support-v4/commit/5c96b3562d59baf9468bd0f2d183d9529a7af145)
There is a race condition that might cause result delivery loss. A better looking fix was done in support-v4 [upstream](https://android.googlesource.com/platform/frameworks/support/+/47ab4c24dac8eff1adbe4cc216cbf9c6318993a3)

### Fixed `onActivityResult()` dispatching to child fragments (part [1](https://github.com/futuresimple/android-support-v4/commit/0c8d6c857a35d9622abbadcff9b703620bd717fe), [2](https://github.com/futuresimple/android-support-v4/commit/39602bea5de7744302bae87d3431b2750b09b0ab) and [3](https://github.com/futuresimple/android-support-v4/commit/cb80af8abb8029ec71d4f306d49f78a761735ed5))
Already fixed in latest [support lib](https://android.googlesource.com/platform/frameworks/support/+/a76a4339be2f27431a167381187c3f0b0d08f1b7%5E%21/#F0).

### [Fixed `onRequestPermissionResult` dispatching to child fragments](https://github.com/futuresimple/android-support-v4/commit/015019c9ee237e4d9244f1326c0a78851808b903)
Fixed in support lib ([1](https://android.googlesource.com/platform/frameworks/support/+/86f3b80ddf7f9aa5c5b7afe77217cb75632d62a2), [2](https://android.googlesource.com/platform/frameworks/support/+/0157c98cff15bc790b4837cb96cb240afc4839ed))

### Fix invalid `Fragment` state restoring ([part 1](https://github.com/futuresimple/android-support-v4/commit/1cb001df6d6724abb37b74f4e9409967a0f63605), [part 2](https://github.com/futuresimple/android-support-v4/commit/85f5595faa603cf5c42fac1dac80c6e52801133a)).
If I remember correctly, this is an intermingled state machines extravaganza, a bug on the joint of `LoaderManager`, `FragmentManager` and `FragmentStatePagerAdapter`. To give the support lib authors justice, I suspect that this bug might have been a result of our misuse of aforementioned APIs. On the other hand a well designed API should prevent or withstand any kind of abuse.

Unfortunately, the detailed memories of my trip through this abbyss has already faded, so it's hard to tell if this bug was somehow addressed in the recent versions of support lib.

# The future of this fork
Is not bright. Maintaining it before The Great Support Lib Split was already a pain in the ass, and I expect the merge with the current upstream to be at least twice as painful.

From my perspective the only issue that have to be addressed/checked before switching to latest upstream version is the `Fragment` state restoration crap. The other changes have either negligible impact or were already fixed in the upstream.
