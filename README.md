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
* [Naming](#naming)
 * [Underscores](#underscores)
* [Methods](#methods)
* [Variables](#variables)
 * [Variable Qualifiers](#variable-qualifiers)
* [Properties](#properties)
 * [Property Attributes](#property-attributes)
 * [Private Properties](#private-properties)
* [Protocols](#protocols)
* [Categories](#categories)
* [Constants](#constants)
* [Literals](#literals)
* [Booleans](#booleans)
* [Bitmasks](#bitmasks)
* [Enumerated](#enumerated-types)
* [Conditionals](#conditionals)
* [Ternary Operator](#ternary-operator)
* [Case Statements](#case-statements)
* [CGRect Functions](#cgrect-functions)
* [Init & Dealloc](#init-and-dealloc)
* [Golden Path](#golden-path)
* [Error handling](#error-handling)
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

Length of the one line of code SHOULD be limited to **120** symbols to aid in visual clarity and organization. You can enable `Xcode → Preferences → Text Editing → Editing → Page guide at column` preference to assist you with this rule. What you can do if your code is not fit in 120 symbols:

**TO BE DISCUSSED**

Use `#warning` or `#pragma message` instead of `TODO` comments.

```objc
#pragma message "'someMethod' method should be refactored"

- (void)someMethod {

    // Do something
}
```

Use `#error` if you need to break a build process under some circumstances.

```objc
#ifndef API_VERSION
#error "API_VERISON is undefined!"
#endif
```

Use `#pragma mark` to categorize methods in functional groupings and protocol/delegate implementations following this general structure.

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

When they are needed, comments SHOULD be used to explain **why** a particular piece of code does something. Any comments that are used MUST be kept up-to-date or deleted.

Block comments are NOT RECOMMENDED, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

## Spacing

* Indentation MUST use 4 spaces. Never indent with tabs. Be sure to set these preferences in the `Xcode → Preferences → Text Editing → Indentation`
* Method braces and other braces (`if`/`else`/`switch`/`while` etc.) MUST open on the same line as the statement. Braces MUST close on a new line.
* There SHOULD be exactly one blank line after the opening bracket to aid in visual clarity and organization.

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

* There SHOULD be exactly one blank line between methods to aid in visual clarity and organization.
* Whitespace within methods MAY separate functionality, though this inclination often indicates an opportunity to split the method into several, smaller methods.
* Prefer using auto-synthesis. But if necessary, `@synthesize` and `@dynamic` MUST each be declared on new lines in the implementation.

## Naming

Apple naming conventions SHOULD be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**For example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *settBut;
```

A three letter prefix (e.g., `SDC`) MUST be used for class names and constants, however MAY be omitted for Core Data entity names. Constants MUST be camel-case with all words capitalized and prefixed by the related class name for clarity. A two letter prefix (e.g., `NS`) is [reserved for use by Apple](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/DefiningClasses/DefiningClasses.html#//apple_ref/doc/uid/TP40011210-CH3-SW12).

**For example:**

```objc
static const NSTimeInterval SDCFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Properties and local variables MUST be camel-case with the leading word being lowercase.

Instance variables MUST be camel-case with the leading word being lowercase, and MUST be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**For example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

### Underscores

When using properties, instance variables should always be accessed and mutated using `self.`. This means that all properties will be visually distinct, as they will all be prefaced with `self.`. 

An exception to this: inside initializers, the backing instance variable (i.e. _variableName) should be used directly to avoid any potential side effects of the getters/setters.

Local variables should not contain underscores.

## Methods

In method signatures, there SHOULD be a space after the method type (-/+ symbol). There SHOULD be a space between the method segments (matching Apple's style).  Always include a keyword and be descriptive with the word before the argument which describes the argument.

The usage of the word "and" is reserved.  It should not be used for multiple parameters as illustrated in the `initWithWidth:height:` example below.

**Preferred:**
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
- (void)sendAction:(SEL)aSelector to:(id)anObject forAllCells:(BOOL)flag;
- (id)viewWithTag:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width height:(CGFloat)height;
```

**Not Preferred:**

```objc
-(void)setT:(NSString *)text i:(UIImage *)image;
- (void)sendAction:(SEL)aSelector :(id)anObject :(BOOL)flag;
- (id)taggedView:(NSInteger)tag;
- (instancetype)initWithWidth:(CGFloat)width andHeight:(CGFloat)height;
- (instancetype)initWith:(int)width and:(int)height;  // Never do this.
```

## Variables

Variables SHOULD be named descriptively, with the variable’s name clearly communicating what the variable _is_ and pertinent information a programmer needs to use that value properly.

**For example:**

* `NSString *title`: It is reasonable to assume a “title” is a string.
* `NSString *titleHTML`: This indicates a title that may contain HTML which needs parsing for display. _“HTML” is needed for a programmer to use this variable effectively._
* `NSAttributedString *titleAttributedString`: A title, already formatted for display. _`AttributedString` hints that this value is not just a vanilla title, and adding it could be a reasonable choice depending on context._
* `NSDate *now`: _No further clarification is needed._
* `NSDate *lastModifiedDate`: Simply `lastModified` can be ambiguous; depending on context, one could reasonably assume it is one of a few different types.
* `NSURL *URL` vs. `NSString *URLString`: In situations when a value can reasonably be represented by different classes, it is often useful to disambiguate in the variable’s name.
* `NSString *releaseDateString`: Another example where a value could be represented by another class, and the name can help disambiguate.

Single letter variable names are NOT RECOMMENDED, except as simple counter variables in loops.

Asterisks indicating a type is a pointer MUST be “attached to” the variable name. **For example,** `NSString *text` **not** `NSString* text` or `NSString * text`, except in the case of constants (`NSString * const SDCConstantString`).

### Variable Qualifiers

When it comes to the variable qualifiers [introduced with ARC](https://developer.apple.com/library/ios/releasenotes/objectivec/rn-transitioningtoarc/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW4), the qualifier (`__strong`, `__weak`, `__unsafe_unretained`, `__autoreleasing`) SHOULD be placed between the asterisks and the variable name, e.g., `NSString * __weak text`. 

## Properties

Property definitions SHOULD be used in place of naked instance variables whenever possible. Direct instance variable access SHOULD be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information, see [Apple’s docs on using accessor methods in initializer methods and `dealloc`](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**For example:**

```objc
@interface SDCSection: NSObject

@property (strong, nonatomic, readonly) NSString *headline;

@end
```

**Not:**

```objc
@interface SDCSection : NSObject {

    NSString *headline;
}
```

### Private Properties

Private properties SHOULD be declared in class extensions (anonymous categories) in the implementation file of a class.

**For example:**

```objc
@interface SDCAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Protocols

In a [delegate or data source protocol](https://developer.apple.com/library/ios/documentation/General/Conceptual/CocoaEncyclopedia/DelegatesandDataSources/DelegatesandDataSources.html), the first parameter to each method SHOULD be the object sending the message.

This helps disambiguate in cases when an object is the delegate for multiple similarly-typed objects, and it helps clarify intent to readers of a class implementing these delegate methods.

**For example:**

```objc
- (void)tableView:(UITableView *)tableView didSelectRowAtIndexPath:(NSIndexPath *)indexPath;
```

**Not:**

```objc
- (void)didSelectTableRowAtIndexPath:(NSIndexPath *)indexPath;
```

## Categories

Categories are RECOMMENDED to concisely segment functionality and should be named to describe that functionality.

**For example:**

```objc
@interface UIViewController (SDCMediaPlaying)
@interface NSString (NSStringEncodingDetection)
```

**Not:**

```objc
@interface SDCAdvertisement (private)
@interface NSString (SDCAdditions)
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

static const CGFloat SDCImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"SOFTEQ Development Corporation"

#define thumbnailHeight 2
```

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals SHOULD be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**For example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## Booleans

Values MUST NOT be compared directly to `YES`, because `YES` is defined as `1`, and a `BOOL` in Objective-C is a `CHAR` type that is 8 bits long (so a value of `11111110` will return `NO` if compared to `YES`).

**For an object pointer:**

```objc
if (!someObject) {
    
}

if (someObject == nil) {
    
}
```

**For a `BOOL` value:**

```objc
if (isAwesome)
if (!someNumber.boolValue)
if (someNumber.boolValue == NO)
```

**Not:**

```objc
if (isAwesome == YES) // Never do this.
```

If the name of a `BOOL` property is expressed as an adjective, the property’s name MAY omit the `is` prefix but should specify the conventional name for the getter.

**For example:**

```objc
@property (assign, getter=isEditable) BOOL editable;
```

_Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE)._

## Bitmasks

When working with bitmasks, the `NS_OPTIONS` macro MUST be used.

**Example:**

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

**Example:**

```objc
typedef NS_ENUM(NSInteger, SDCAdRequestState) {
    
    SDCAdRequestStateInactive,
    SDCAdRequestStateLoading
};
```

## Conditionals

Conditional bodies MUST use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/SDCimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) can happen where the line “inside” the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

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

## Ternary Operator

The intent of the ternary operator, `?` , is to increase clarity or code neatness. The ternary SHOULD only evaluate a single condition per expression. Evaluating multiple conditions is usually more understandable as an if statement or refactored into named variables.

**For example:**
```objc
result = (a > b) ? (x) : (y);
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Case Statements

Braces are not required for case statements, unless enforced by the complier.  
When a case contains more than one line, braces should be added.

```objc
switch (condition) {

    case 1:
    
        // ...
        break;
        
    case 2: {
    
        // ...
        // Multi-line example using braces
        break;
    }
    
    case 3:
    
        // ...
        break;
        
    default: 
    
        // ...
        break;
}

```

There are times when the same code can be used for multiple cases, and a fall-through should be used.  A fall-through is the removal of the 'break' statement for a case thus allowing the flow of execution to pass to the next case value.  A fall-through should be commented for coding clarity.

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

When using an enumerated type for a switch, 'default' is not needed.   For example:

```objc
SDCLeftMenuTopItemType menuType = SDCLeftMenuTopItemMain;

switch (menuType) {

    case SDCLeftMenuTopItemMain:
    
        // ...
        break;
        
    case SDCLeftMenuTopItemShows:
    
        // ...
        break;
        
    case SDCLeftMenuTopItemSchedule:
    
        // ...
        break;
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## `CGRect` Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, code MUST use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**For example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## init and dealloc

`dealloc` methods SHOULD be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` methods SHOULD be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

```objc
- (instancetype)init {

    self = [super init]; // or call the designated initializer

        // Custom initialization
    }

    return self;
}
```

## Golden Path

When coding with conditionals, the left hand margin of the code should be the "golden" or "happy" path.  That is, don't nest `if` statements.  Multiple return statements are OK.

**Preferred:**

```objc
- (void)someMethod {
    
    if (![someOther boolValue]) {
    
        return;
    }
    
    //Do something important
}
```

**Not Preferred:**

```objc
- (void)someMethod {
    
    if ([someOther boolValue]) {
    
        //Do something important
    }
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

## Singletons

Singleton objects SHOULD use a thread-safe pattern for creating their shared instance.
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

```objc
// Frameworks
@import QuartzCore;

// Models
#import "SDCUser.h"

// Views
#import "SDCButton.h"
#import "SDCUserView.h"
```


## Xcode project

The physical files SHOULD be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created SHOULD be reflected by folders in the filesystem. Code SHOULD be grouped not only by type, but also by feature for greater clarity.

Target Build Setting “Treat Warnings as Errors” SHOULD be enabled. Enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang’s pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).
