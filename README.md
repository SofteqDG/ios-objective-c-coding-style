# SOFTEQ Objective-C Style Guide

This style guide outlines the coding conventions of the iOS teams at SOFTEQ Development Corporation. 

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn’t mentioned here, it’s probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Language](#language)
* [Code Organization](#code-organization)
* [Dot Notation Syntax](#dot-notation-syntax)
* [Comments](#comments)
* [Spacing](#spacing)
* [Types](#types)
* [Naming](#naming)
 * [Prefixes](#prefixes)
 * [Underscores](#underscores)
* [Methods](#methods)
 * [Declaration](#declaration)
 * [Definition](#definition)
* [Variables](#variables)
* [Properties](#properties)
 * [Property Attributes](#property-attributes)
 * [Private Properties](#private-properties)
* [Blocks](#blocks)
 * [Block arguments](#block-arguments)
 * [Blocks as arguments](#blocks-as-arguments)
 * [Blocks as properties](#blocks-as-properties)
* [Protocols](#protocols)
* [Categories](#categories)
* [Constants](#constants)
* [Literals](#literals)
* [Booleans](#booleans)
* [Bitmasks](#bitmasks)
* [Enumerated](#enumerated-types)
* [Conditionals](#conditionals)
 * [Golden Path](#golden-path)
 * [Ternary Operator](#ternary-operator)
 * [Case Statements](#case-statements)
* [Error handling](#error-handling)
* [Init & Dealloc](#init-and-dealloc)
* [CGRect Functions](#cgrect-functions)
* [Singletons](#singletons)
* [Imports](#imports)
* [Xcode Project](#xcode-project)

## Language

US English MUST be used.

**For example:**

```objc
UIColor *myColor = [UIColor whiteColor];
```

**Not:**

```objc
UIColor *myColour = [UIColor whiteColor];
```

## Code Organization

Length of the one line with code SHOULD be limited to **120** symbols to aid in visual clarity and organization. You can enable `Xcode → Preferences → Text Editing → Editing → Page guide at column` preference to assist you with this rule. What you can do if your code does not fit in 120 symbols:

**TBD**

Use `#warning` or `#pragma message` directives instead of `TODO` comments. However be careful with `#warning` directive because it can be treated as a error depending on build settings.

**For example:**

```objc
#pragma message "'someMethod' method should be refactored"

- (void)someMethod {
    // Do something
}
```

Use `#error` directive if you need to break a build process under some circumstances.

**For example:**

```objc
#ifndef API_VERSION
#error "API_VERISON is undefined!"
#endif
```

Use `#pragma mark` directive to categorize methods in functional groupings and protocol/delegate implementations. There MUST be one blank line before and after `#pragma mark` directive or set of such directives.

**For example:**

```objc
#pragma mark - Lifecycle

- (void)dealloc {}
- (instancetype)init {}
- (void)viewDidLoad {}
- (void)viewWillAppear:(BOOL)animated {}
- (void)didReceiveMemoryWarning {}

#pragma mark - Custom Accessors & Mutators

- (void)setCustomProperty:(id)value {}
- (id)customProperty {}

#pragma mark - IBActions

- (IBAction)submitData:(id)sender {}

#pragma mark - Public

- (void)publicMethod {}

#pragma mark - Private

- (void)privateMethod {}

#pragma mark - Protocol conformance
#pragma mark - UITextFieldDelegate
#pragma mark - UITableViewDataSource
#pragma mark - UITableViewDelegate

#pragma mark - NSCopying

- (id)copyWithZone:(NSZone *)zone {}

#pragma mark - NSObject

- (NSString *)description {}
```

## Dot Notation Syntax

Dot notation is RECOMMENDED over bracket notation for getting and setting properties.

**For example:**

```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**

```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Comments

When they are needed, comments SHOULD be used to explain **why** a particular piece of code does something. Any comments that are used MUST be kept up-to-date or deleted. Comments CAN be added for code which is hard to understand. Any workarounds (especially with link to explanation) CAN be marked as `workaround: <link>` in the comment.

**For example:**
```objc
// workaround: http://stackoverflow.com/a/3927811
```

Block comments are NOT RECOMMENDED, as code SHOULD be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## Spacing

* Indentation MUST use 4 spaces. Never indent with tabs. Be sure to set these preferences in the `Xcode → Preferences → Text Editing → Indentation`
* There MUST be a blank line between the `@interface`, `@implementation`, `@protocol` statements and `@end` statement.
* There MUST be a blank line before and after the  `@interface`, `@implementation`, `@protocol`,  `@end` statements.

**For example:**

```objc
@class SDCDownloader;
@protocol SDCDownloaderDelegate;

@protocol SDCDownloaderDelegate <NSObject>

- (void)downloader:(SDCDownloader *)downloader didFinishDownloading:(NSURL *)fileURL;
- (void)downloader:(SDCDownloader *)downloader didResumeDownloading:(NSURL *)fileURL;

@end

@interface SDCDownloader : NSObject

@property (assign, nonatomic) NSInteger timeoutInterval;
@property (weak, nonatomic) id<SDCDownloaderDelegate> delegate;

@end
```

* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) MUST open on the same line as the statement. There MUST be a space before opening brace. Braces MUST close on a new line.
* There MUST be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods MAY separate functionality, though this inclination often indicates an opportunity to split the method into several, smaller methods.

**For example:**

```objc
- (void)someMethog {
	// Do something
	if (user.isHappy) {
		// Do something
	}
	else {
		// Do something else
	}
}

- (void)someMethod2 {
	// Do something
}
```

* Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` MUST each be declared on new lines in the implementation.

**For example:**

```objc
@implementation SDCDownloader
@synthesize delegate = _delegate;
@dynamic timeoutInterval;
@dynamic someProperty;

@end
```

## Types

`NSInteger` and `NSUInteger` SHOULD be used instead of `int`, `long`, etc per Apple's best practices and 64-bit safety. `CGFloat` is preferred over `float` for the same reasons. This future proofs code for 64-bit platforms.

All Apple types should be used over primitive ones. For example, if you are working with time intervals, use `NSTimeInterval` instead of `double` even though it is synonymous. This is considered best practice and makes for clearer code.

## Naming

Apple naming conventions SHOULD be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)). Long, descriptive method and variable names are good.

**For example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *settBut;
```

Properties and local variables MUST be camel-case with the leading word being lowercase. Instance variables MUST be camel-case with the leading word being lowercase, and MUST be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

### Prefixes

A minimum three letter prefix (e.g., `SDC`) MUST be used for constants and class/protocol names. A two letter prefix (e.g., `NS`) is [reserved for use by Apple](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW12). Constants MUST be camel-case with all words capitalized and prefixed by the related class name for clarity. 

**For example:**

```objc
static const NSTimeInterval SDCFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

There MUST be additional UI prefix right after an app- or organization-specific prefix for classes which are designed to work as a visual components (buttons, labels, charts and etc.)

**For example:**

```objc
@class SDCUIButton;
@class SDCUIHomeViewController;
@class SDCUINavigationController;
```

**Not:**

```objc
@class SDCButton;
@class HomeViewController;
@class SDCNavigationController;
```

Prefix MAY be omitted for Core Data entity names. However prefix MUST be used for all Core Data entity names if you decide to use it. 

### Underscores

When using properties, instance variables SHOULD always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers and dealloc the backing instance variable (i.e. _variableName) SHOULD be used directly to avoid any potential side effects of the getters/setters.

Local variables SHOULD NOT contain underscores.

## Methods

Your class interfaces SHOULD ONLY contain methods that need to be exposed publicly. All other code SHOULD be in the implementation file. All methods SHOULD be clearly named and MAY be commented so that documentation can be generated automatically.

### Declaration
In method signatures, there SHOULD be a space after the method type (-/+ symbol). There SHOULD NOT be a space between returned type and first method segment. There SHOULD be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument. 

The usage of the word "and" is reserved.  It SHOULD NOT be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**For Example:**

```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not:**

```objc
- (void)setT:(NSString *)text :(UIImage *)image;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

Use `NS_UNAVAILABLE` macro in the method signature if you need to "block" method calls on your interface.

**For example:**

```objc
- (instancetype)init NS_UNAVAILABLE;
```

Use `NS_REQUIRES_SUPER` macro in the method signature if the `super` method needs to be called by subclass. Once a method signature is appended with this macro, the compiler will produce a warning if `super` is not called by a subclass overriding the method.

**For example:**

```objc
- (void)someMethod NS_REQUIRES_SUPER;
```

### Definition

Method definitions SHOULD follow the same indentation rules as declaration, with the opening brace on the sane line as the method signature.

**For example:**

```objc
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height {
    // Do something
}
```

**Not:**

```objc
- (instancetype) initWithWidth:(CGFloat)width :(CGFloat)height
{
    // Do something
}
```

You CAN use `NSParameterAssert` macro for arguments that are critical for correct method execution.

**For example:**

```objc
- (void)parseData:(NSData *)data {
    NSParameterAssert(data);
    
    // Parsing logic
}
```

## Variables

Variables SHOULD be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**For example:**

* `NSString *title`: It is reasonable to assume a “title” is a string.
* `NSString *titleHTML`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively._
* `NSAttributedString *titleAttributedString`: A title, already formatted for display. _`AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context._
* `NSDate *now`: _No further clarification is needed._
* `NSDate *lastModifiedDate`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `NSURL *url` vs. `NSString *urlString`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `NSString *releaseDateString`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are NOT RECOMMENDED, except as simple counter variables in loops.

Asterisks indicating a type is a pointer MUST be “attached to” the variable name. **For example,** `NSString *text` **not** `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const SDCConstantString`).

## Properties

Property definitions SHOULD be used in place of naked instance variables whenever possible. Direct instance variable access SHOULD be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information, see [Apple’s docs on using accessor methods in initializer methods and `dealloc`](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface SDCSection : NSObject

@property (strong, nonatomic, readonly) NSString *headline;
@property (assign, nonatomic) NSInteger tag;

@end
```

**Not:**

```objc
@interface SDCSection : NSObject {
    NSString *headline;
    NSInteger tag;
}
```
### Property Attributes

Property attributes SHOULD be explicitly listed, and will help new programmers when reading the code.  
The order of property attributes should be:

1. storage (`weak`, `strong`, `assign`)
2. atomicity (`atomic`, `nonatomic`)
3. mutability (`readonly`, `readwrite`)
4. custom getter/setter

This order is consistent with automatically generated code when connecting UI elements from Interface Builder.

**For Example:**

```objc
@property (weak, nonatomic) IBOutlet UIView *containerView;
@property (strong, nonatomic) NSString *tutorialName;
@property (strong, nonatomic, readonly) NSString *currentDateString;
@property (assign, nonatomic, readonly, getter=isLoaded) BOOL loaded;
```

**Not:**

```objc
@property (nonatomic, weak) IBOutlet UIView *containerView;
@property (nonatomic) NSString *tutorialName;
```

Use `@dynamic` statement for properties which value should be calculated at runtime (calculated properties). `@dynamic` statement is a way to inform the system not to generate getters/setters for the property and a backing instance variable. You MUST write your own implementation of getter/setter for such properties depending on property attributes.

### Private Properties

Private properties SHOULD be declared in class extensions (anonymous categories) in the implementation file of a class.

**For example:**

```objc
@interface SDCAdvertisement ()

@property (strong, nonatomic) GADBannerView *googleAdView;
@property (strong, nonatomic) ADBannerView *iAdView;
@property (strong, nonatomic) UIWebView *adXWebView;

@end
```


## Blocks

Types of blocks SHOULD be defined using a `typedef`, so that the type can be easily referenced in other places. Like constants, you SHOULD define your blocks as close to the code where they are used, like in the header of the class that uses them. Block definitions SHOULD also omit their return type when possible as well as their arguments when they are void. Block arguments SHOULD always be declared in the `typedef`.

**For example:**

```objc
typedef void (^SDCCompletion) (id object, NSError *error);
SDCCompletionBlock block = ^(id object, NSError *error) { 
    // Do something.
};
```

**Not:**

```objc
void(^block)(id, NSError *) = ^void(id obj, NSError *error) { 
    // Do something.
};
```

### Block arguments

Block arguments SHOULD always be named so that they are filled in on autocomplete (Developers can rename the variables when using the block if they choose).

**For example:**

```objc
typedef void (^SDCCompletion) (id object, NSError *error);
```

**Not:**
```objc
typedef void (^SDCCompletion) (id, NSError *);
```

### Blocks as arguments

Block arguments SHOULD always be the last arguments for a method.

**For example:**

```objc
// in the header
- (void)doSomethingWithObject:(id)object success:(SDCSuccessCallback)success failure:(SDCFailureCallback))failure;

// in use:
[self doSomething:object success:^{ /* ... */ } failure:^{ /* ... */ }];
```

### Blocks as properties

Blocks SHOULD always be copied when used outside the lexical scope in which they are declared, e.g. asynchronous completion blocks. This goes for storage in a property or in a collection (i.e. NSArray, NSDictionary, etc.).

**For example:**

```objc
@interface SDCOperation : NSObject

@property (copy, nonatomic) SDCCompletion completion;

@end
```

## Protocols

Each protocol SHOULD be inherited from `NSObject` protocol unless you have a serious reason not to do it.
* There SHOULD be a space between a protocol/interface name and a list of inherited/conformed protocols.
* There SHOULD NOT be a space between property/variable type and a list of conformed protocols.
* There SHOULD NOT be a space between braces in the list of protocols.

**For example:**

```objc
@protocol SDCTimeProtocol <NSObject, NSCoding>

- (uint8_t)hours;
- (uint8_t)minutes;
- (uint8_t)seconds;

@end

@interface SDCAbsoluteTime : NSObject <SDCTimeProtocol>

@property (strong, nonatomic, readonly) id<SDCTimeProtocol> masterTime;

@end
```

**Not:**

```objc
@protocol SDCTimeProtocol<NSObject,NSCoding>

- (uint8_t)hours;
- (uint8_t)minutes;
- (uint8_t)seconds;

@end

@interface SDCAbsoluteTime : NSObject <SDCTimeProtocol >

@property (strong, nonatomic, readonly) id <SDCTimeProtocol> masterTime;

@end
```

In a [delegate or data source protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), the first parameter to each method MUST be the object sending the message. This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

**For example:**

```objc
@class SDCDownloader;

@protocol SDCDownloaderDelegate <NSObject>

- (void)downloader:(SDCDownloader *)downloader didFinishDownloading:(NSURL *)fileURL;
- (void)downloader:(SDCDownloader *)downloader didResumeDownloading:(NSURL *)fileURL;

@end
```

**Not:**

```objc
@class SDCDownloader;

@protocol SDCDownloaderDelegate <NSObject>

- (void)downloaderDidFinishDownloading:(NSURL *)fileURL;
- (void)downloaderDidResumeDownloading:(NSURL *)fileURL;

@end
```

## Categories

Categories are RECOMMENDED to concisely segment functionality and should be named to describe that functionality. 
* There SHOULD be a space between interface name and category name.
* There SHOULD NOT be a space between braces and a name of category.

**For example:**

```objc
@interface UIViewController (SDCMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Not:**

```objc
@interface SDCAdvertisement ( private )
@interface NSString(SDCAdditions)
```

Methods and properties added in categories MUST be named with an app- or organization-specific prefix. This avoids unintentionally overriding an existing method, and it reduces the chance of two categories from different libraries adding a method of the same name. (The Objective-C runtime doesn’t specify which method will be called in the latter case, which can lead to unintended effects.)

**For example:**

```objc
@interface NSArray (SDCAccessors)

- (id)sdc_objectOrNilAtIndex:(NSUInteger)index;

@end
```

**Not:**

```objc
@interface NSArray (SDCAccessors)

- (id)objectOrNilAtIndex:(NSUInteger)index;

@end
```

## Constants

Constants are RECOMMENDED over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants MUST be declared as `static` constants. Constants MAY be declared as `#define` when explicitly being used as a macro.

**For example:**

```objc
static NSString * const SDCAboutViewControllerCompanyName = @"SOFTEQ Development Corporation";

static const CGFloat SDCImageThumbnailHeight = 50.0f;
```

**Not:**

```objc
#define CompanyName @"SOFTEQ Development Corporation"

#define thumbnailHeight 2
```

Public constants of class MUST be declared as `extern` in the header file. You CAN use `FOUNDATION_EXTERN` macro for such constants.

**For example:**

```objc
// Declaration in the header file.
FOUNDATION_EXTERN const CGFloat SDCImageThumbnailHeight;

// Definition in the implementation file.
const CGFloat SDCImageThumbnailHeight = 50.0f;
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals SHOULD be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

* All number values (raw numbers, enum values, bools) MUST be wrapped in braces.
* Avoid making numbers a specific type unless necessary (for example, prefer 5 to 5.0, and 5.3 to 5.3f).
* The contents of array and dictionary literals SHOULD NOT have a space between opening and closing braces.
* Dictionary literals SHOULD NOT have a space between the key and the colon, and SHOULD have a single space between colon and value.

**For example:**

```objc
NSNumber *shouldUseLiterals = @(YES);
NSNumber *buildingZIPCode = @(10018);
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSArray *numbers = @[@(8), @(16), @(32), @(64), @(SDCSomeTypeValue), @(YES), @(NO)];
NSDictionary *productManagers = @{@"iPhone": @"Kate", @"iPad": @"Kamal", @"Mobile Web": @"Bill"};
```

**Not:**

```objc
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSArray *numbers = [NSArray arrayWithObjects:@8, @16, @(32), @(64), @(SDCSomeTypeValue), @YES, @NO];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
```

Longer or more complex literals SHOULD be split over multiple lines and aligned by the first value.

**For Example:**
```
NSArray *names = @[@"Kate",
                   @"Kamal",
                   @"Bill"];

NSDictionary *productManagers = @{@"iPhone": @"Kate",
                                  @"iPad": @"Kamal",
                                  @"Mobile Web": @"Bill"};
```

## Booleans

Values MUST NOT be compared directly to `YES`, because `YES` is defined as `1`, and a `BOOL` in Objective-C is a `CHAR` type that is 8 bits long (so a value of `11111110` will return `NO` if compared to `YES`).

**For an object pointer:**

```objc
if (!someObject) {
    // Do something
}

if (someObject == nil) {
    // Do something
}
```

**For a `BOOL` value:**

```objc
if (isAwesome)
if (!someNumber.boolValue)
```

**Not:**

```objc
if (someNumber.boolValue == NO)
if (isAwesome == YES) // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property’s name MAY omit the `is` prefix but CAN specify the conventional name for the getter.

**For example:**

```objc
@property (assign, nonatomic, getter=isEditable) BOOL editable;
```

_Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)._

## Bitmasks

When working with bitmasks, the `NS_OPTIONS` macro MUST be used.

**For example:**

```objc
typedef NS_OPTIONS(NSUInteger, SDCAdCategory) {
    SDCAdCategoryAutos      = 1 << 0,
    SDCAdCategoryJobs       = 1 << 1,
    SDCAdCategoryRealState  = 1 << 2,
    SDCAdCategoryTechnology = 1 << 3
};
```

## Enumerated Types

When using `enum`s, the new fixed underlying type specification MUST be used; it provides stronger type checking and code completion. The SDK includes a macro to facilitate and encourage use of fixed underlying types: `NS_ENUM()`.

**For example:**

```objc
typedef NS_ENUM(NSInteger, SDCAdRequestState) {
    SDCAdRequestStateInactive,
    SDCAdRequestStateLoading
};
```

## Conditionals

Conditional bodies MUST always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/SDCimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) can happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**For example:**

```objc
if (!error) {
    return success;
}
```

**Not:**

```objc
if (!error)
    return success;
```

or

```objc
if (!error) return success;
```

### Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**For example:**

```objc
- (void)someMethod {
    if (![someOther boolValue]) {
        return;
    }

    //Do something important
}
```

**Not:**

```objc
- (void)someMethod {
    if (![someOther boolValue]) {
        //Do something important
    }
}
```

### Ternary Operator

The intent of the ternary operator, `?` , is to increase clarity or code neatness. The ternary SHOULD only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables. The ternary MUST be wrapped in the round braces.

**For example:**

```objc
result = (a > b ? x : y);
```

or, you can use the `?:` operator to simplify ternary statements into a style similar to Ruby and Javascript's `||` operator behavior:

```objc
result = (a ?: b);
```

**Not:**

```objc
result = a > b ? x = c > d ? c : d : y;
```

### Case Statements

Braces are not required for `case` statements, unless enforced by complier.  Braces MUST be added for all `case` statements if they are required for at least one `case` statement. `default` statement MUST always be the last case and MUST always be included.

**For example:**

```objc
switch (condition) {
    case 1:
        // ...
        break;
        
    case 2:
        // ...
        break;  
          
    case 3:
        // ...
        break;
        
    default: 
        // ...
        break;
}

```

or

```objc
switch (condition) {
    case 1: {
        // ...
        break;
    }
        
    case 2: {
        // ...
        break;
    }  
          
    case 3: {
        // ...
        break;
    }
        
    default: {
        // ...
        break;
    }
}

```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the `break` statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through CAN be commented for coding clarity.

**For example:**

```objc
switch (condition) {
    
    case 1:
        // fall-through
    case 2:
        // code executed for values 1 and 2
        break;
    
    default: 
        // ...
        break;
}
```

## Error Handling

When methods return an error parameter by reference, code MUST switch on the returned value and MUST NOT switch on the error variable.

**For example:**

```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
    // Handle Error
}
```

**Not:**

```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
    // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## init and dealloc

`dealloc` methods SHOULD be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements.

 `init` methods SHOULD be placed directly below the `dealloc` methods of any class.
`init` methods SHOULD return `instancetype` in general case and SHOULD be structured like this:

**For example:**

```objc
- (instancetype)init {
    self = [super init]; // or call the designated initializer
    if (self) {
        // Custom initialization
    }
    return self;
}
```

## `CGRect` Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, code MUST use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat minX = CGRectGetMinX(frame);
CGFloat minY = CGRectGetMinY(frame);
CGFloat maxX = CGRectGetMaxX(frame);
CGFloat maxY = CGRectGetMaxY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat minX = frame.origin.x;
CGFloat minY = frame.origin.y; 
CGFloat maxX = frame.origin.x + frame.size.width;
CGFloat maxY = frame.origin.y + frame.size.height;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Singletons

Singleton objects SHOULD use a thread-safe pattern for creating their shared instance.

**For example:**

```objc
+ (instancetype)sharedInstance {
    static id sharedInstance = nil;
    
    static dispatch_once_t onceToken;
    dispatch_once(&onceToken, ^{
        sharedInstance = [[[self class] alloc] init];
    });
    
    return sharedInstance;
}
```
This will prevent [possible and sometimes frequent crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Imports

If there is more than one import statement, statements MUST be grouped [together](http://ashfurrow.com/blog/structuring-modern-objective-c). Groups MAY be commented.

Note: For modules use the [@import](http://clang.llvm.org/docs/Modules.html#using-modules) syntax.

**For example:**

```objc
// Frameworks
@import QuartzCore;

// Models
#import "SDCUser.h"

// Views
#import "SDCUIButton.h"
#import "SDCUIUserView.h"
```

## Xcode project

The physical files SHOULD be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created SHOULD be reflected by folders in the filesystem. Code SHOULD be grouped not only by type, but also by feature for greater clarity.

Target Build Setting “Treat Warnings as Errors” SHOULD be enabled. Enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
