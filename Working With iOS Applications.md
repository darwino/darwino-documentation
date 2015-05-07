Building the iOS demo application requires a Mac with a valid iOS developer profile registered via Xcode's Accounts preference panel. To see if your computer has any active profiles, run:

	security find-identity -v -p codesigning

That should list any available signing profiles, and valid ones will contain "iPhone Developer" or "iOS Development".

Running via Maven
-----------------

To run the iOS applications via Maven (for example, via the command line), the [instructions for the RoboVM plugin](http://docs.robovm.com/maven/1.0.0-beta-03/) apply:

	mvn robovm:ipad-sim
    mvn robovm:iphone-sim
    mvn robovm:ios-device

Packaging via Maven
-------------------

Because building an iOS IPA package requires this specialized configuration, the RoboVM projects are configured to only attempt to do so in the `ipa` profile. For example:

	mvn -P ipa package

