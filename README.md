Purpose
--------------

FXKeychain is a lightweight wrapper around the Apple keychain APIs that exposes the commonly used functionality whilst hiding the horrific complexity and ugly interface of the underlying APIs.

FXKeychain treats the keychain like a simple dictionary that you can set and get values from. For most purposes you can get by using the defaultKeychain, however it is also possible to create new keychain instances if you wish to namespace your keychain by service, or share values between apps using an accessGroup.


Supported iOS & SDK Versions
-----------------------------

* Supported build target - iOS 6.0 / Mac OS 10.8 (Xcode 4.5.2, Apple LLVM compiler 4.1)
* Earliest supported deployment target - iOS 5.0 / Mac OS 10.7
* Earliest compatible deployment target - iOS 4.3 / Mac OS 10.6

NOTE: 'Supported' means that the library has been tested with this version. 'Compatible' means that the library should work on this iOS version (i.e. it doesn't rely on any unavailable SDK features) but is no longer being tested for compatibility and may require tweaking or bug fixes to run correctly.


ARC Compatibility
------------------

FXKeychain requires ARC. If you wish to use FXKeychain in a non-ARC project, just add the -fobjc-arc compiler flag to the FXKeychain.m class. To do this, go to the Build Phases tab in your target settings, open the Compile Sources group, double-click FXKeychain.m in the list and type -fobjc-arc into the popover.

If you wish to convert your whole project to ARC, comment out the #error line in FXKeychain.m, then run the Edit > Refactor > Convert to Objective-C ARC... tool in Xcode and make sure all files that you wish to use ARC for (including FXKeychain.m) are checked.


Installation
---------------

To use FXKeychain, just drag the class files into your project and add the Security framework. You can use the [FXKeychain defaultKeychain] shared instance, or create new instance as and when you need them.


Properties
------------------
    
FXKeychain has the following properties. They are all immutable once the keychain has been created.
        
    @property (nonatomic, copy, readonly) NSString *service;
    
The service property is used to distinguish between multiple apps or services on a given device or within the same app. On Mac OS and the iOS simulator, services are shared between apps, so it's a good idea to use something unique for the service, such as the application bundle ID, or the same value as the accessGroup if you wish to share a service between multiple apps.
    
    @property (nonatomic, copy, readonly) NSString *accessGroup;

The accessGroup value is used for sharing a keychain between multiple iOS apps from the same vendor. See Apple's documentation for acceptable values to use for the accessGroup. Leave this value nil if you do not intend to share the keychain between apps. On Mac OS, the keychain is already shared between apps, so this property has no effect.


Methods
----------------

    + (instancetype)defaultKeychain;
    
This method returns a shared default keychain instance, which uses the app's bundle ID for the service to avoid namespace collisions with other apps on Mac OS or the iOS simulator.
    
    - (id)initWithService:(NSString *)service
              accessGroup:(NSString *)accessGroup;
              
This method creates a new FXKeychain instance with the specified parameters. Each FXKeychain can contain as many key/value pairs as you want, so you may only need a single FXKeychain per application. Each FXKeychain is uniquely identified by the service parameter; see the Properties description for how to use this. You can specify nil for the service, in which case it will act as "wildcard" selector and calls to objectForKey: will return the first value found within any service stored in the keychain. The accessGroup parameter is used for setting up shared keychains that can be accessed by multiple different apps; leave this as nil if you do not require that functionality.
    
    - (BOOL)setObject:(id<NSCoding>)object forKey:(id<NSCopying>)key;
    - (BOOL)setObject:(id<NSCoding>)object forKeyedSubscript:(id<NSCopying>)key;
    
These methods will save the specified object in the keychain. The object can be of any class that implements the NSCoding protocol. Values of type NSString will be stored as UTF8-encoded data, and are intercompatible with other keychain solutions. Any other object type will be stored as NSCoded data. Passing a value of nil as the object will remove the key from the keychain. The second form of this method is functionally identical to the first, but is included to support the modern objective C keyed subscripting syntax.
    
    - (BOOL)removeObjectForKey:(id<NSCopying>)key;
    
This method deletes the specified key from the keychain.
    
    - (id)objectForKey:(id<NSCopying>)key;
    - (id)objectForKeyedSubscript:(id<NSCopying>)key;

This method returns the value for the specified key from the keychain. If the key does not exist it will return nil. The second form of this method is functionally identical to the first, but is included to support the modern objective C keyed subscripting syntax.