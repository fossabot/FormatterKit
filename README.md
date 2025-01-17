# FormatterKit
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fhaztheo%2FFormatterKit.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fhaztheo%2FFormatterKit?ref=badge_shield)


> This library is no longer being maintained.
> Since its initial release in 2011,
> Apple has filled in many of the gaps FormatterKit was created to fill
> in Foundation and other SDK frameworks:

| Deprecated FormatterKit API     | Apple SDK API                                                                          | Availability             |
| ------------------------------- | -------------------------------------------------------------------------------------- | ------------------------ |
| `TTTAddressFormatter`           | [`CNPostalAddressFormatter`][cnpostaladdressformatter]                                 | iOS 9.0+ / macOS 10.11+  |
| `TTTArrayFormatter`             | [`NSListFormatter`][nslistformatter]                                                   | iOS 13.0+ / macOS 10.15+ |
| `TTTNameFormatter`              | [`NSPersonNameComponentsFormatter`][nspersonnamecomponentsformatter]                   | iOS 9.0+ / macOS 10.11+  |
| `TTTLocationFormatter`          | [`NSMeasurementFormatter` + `NSUnitDistance` / `NSUnitAngle`][nsmeasurementformatter]  | iOS 10.0+ / macOS 10.12+ |
| `TTTOrdinalNumberFormatter`     | [`NSNumberFormatter` + `NSNumberFormatterOrdinalStyle`][nsnumberformatterordinalstyle] | iOS 9.0+ / macOS 10.11+  |
| `TTTTimeIntervalFormatter`      | [`NSRelativeDateTimeFormatter`][nsrelativedatetimeformatter]                           | iOS 13.0+ / macOS 10.15+ |
| `TTTUnitOfInformationFormatter` | [`NSMeasurementFormatter` + `NSUnitInformationStorage`][nsunitinformationstorage]      | iOS 13.0+ / macOS 10.15+ |

> You can continue to use FormatterKit in your projects,
> but we recommend switching to the corresponding Apple APIs
> as soon as it makes sense to do so.
>
> Going forward, we plan to spin out non-deprecated formatters
> into their own repositories for future development
> as well as any new projects.
> This repository will remain archived here
> for compatibility with existing installations.

---

`FormatterKit` is a collection of well-crafted `NSFormatter` subclasses for things like units of information, distance, and relative time intervals. Each formatter abstracts away the complex business logic of their respective domain, so that you can focus on the more important aspects of your application.

In short, use this library if you're manually formatting any of the following (with string interpolation or the like):

- **Addresses**: Create formatted address strings from components _(e.g. 221b Baker St / Paddington / Greater London / NW1 6XE / United Kingdom )_
- **Arrays**: Display `NSArray` elements in a comma-delimited list _(e.g. "Russell, Spinoza & Rawls")_
- **Colors**: RGB, CMYK, and HSL your ROY G. BIV in style. _(e.g. `#BADF00D`, `rgb(255, 100, 42)`)_
- **Location, Distance & Direction**: Show `CLLocationDistance`, `CLLocationDirection`, and `CLLocationSpeed` in metric or imperial units _(eg. "240ft Northwest" / "45 km/h SE")_
- **Names**: Display personal names in the correct format, according to the current locale and source language _(eg. "山田花子" for the Japanese first name "花子" (Hanako) and last name "山田" (Yamada))_
- **Ordinal Numbers**: Convert cardinal `NSNumber` objects to their ordinal in most major languages _(eg. "1st, 2nd, 3rd" / "1ère, 2ème, 3ème")_
- **Time Intervals**: Show relative time distance between any two `NSDate` objects _(e.g. "3 minutes ago" / "yesterday")_
- **Units of Information**: Humanized representations of quantities of bits and bytes _(e.g. "2.7 MB")_
- **URL Requests**: Print out `cURL` or `Wget` command equivalents for any `NSURLRequest` _(e.g. `curl -X POST "https://www.example.com/" -H "Accept: text/html"`)_

## Installation

### CocoaPods

You can install `FormatterKit` via CocoaPods,
by adding the following line to your `Podfile`:

```ruby
pod 'FormatterKit', '~> 1.9.0'
```

Run the `pod install` command to download the library
and integrate it into your Xcode project.

### Carthage

To use `FormatterKit` in your Xcode project using Carthage,
specify it in `Cartfile`:

```
github "FormatterKit/FormatterKit" ~> 1.9.0
```

Then run the `carthage update` command to build the framework,
and drag the built FormatterKit.framework into your Xcode project.

## Demo

Build and run the `FormatterKit Example` project in Xcode to see an inventory of the available `FormatterKit` components.

---

## TTTAddressFormatter

Address formats vary greatly across different regions.
`TTTAddressFormatter` ties into the
[Address Book frameworks](https://developer.apple.com/documentation/addressbookui?language=objc)
to help your users find their place in the world.

For example, addresses in the United States take the form:

    Street Address
    City State ZIP
    Country

Whereas addresses in Japan follow a different convention:

    Postal Code
    Prefecture Municipality
    Street Address
    Country

> Requires that the `AddressBook` and `AddressBookUI` frameworks are linked
> with `#import` statements in `Prefix.pch`.
> `TTTAddressFormatter` isn't available on OS X.

### Example Usage

```objective-c
TTTAddressFormatter *addressFormatter = [[TTTAddressFormatter alloc] init];
NSLog(@"%@", [addressFormatter stringFromAddressWithStreet:street locality:locality region:region postalCode:postalCode country:country]);
```

## TTTArrayFormatter

Think of this as a production-ready alternative to
`NSArray -componentsJoinedByString:`.
`TTTArrayFormatter` comes with internationalization baked-in
and provides a concise API that allows you to configure for any edge cases.

### Example Usage

```objective-c
NSArray *list = [NSArray arrayWithObjects:@"Russel", @"Spinoza", @"Rawls", nil];
TTTArrayFormatter *arrayFormatter = [[TTTArrayFormatter alloc] init];
[arrayFormatter setUsesAbbreviatedConjunction:YES]; // Use '&' instead of 'and'
[arrayFormatter setUsesSerialDelimiter:NO]; // Omit Oxford Comma
NSLog(@"%@", [arrayFormatter stringFromArray:list]); // # => "Russell, Spinoza & Rawls"
```

## TTTColorFormatter

RGB, CMYK, and HSL your ROY G. BIV in style.
`TTTColorFormatter` provides string representations of colors.

### Example Usage

```objective-c
TTTColorFormatter *colorFormatter = [[TTTColorFormatter alloc] init];
NSString *RGB = [colorFormatter RGBStringFromColor:[UIColor orangeColor]];
```

## TTTLocationFormatter

When working with `CoreLocation`,
you can use your favorite unit for distance...
so long as your favorite unit is the meter.
If you want to take distance calculations and display them to the user,
you may want to use kilometers instead --- or maybe even miles
if you're of the [Imperial](http://en.wikipedia.org/wiki/Imperial_units) persuasion.

`TTTLocationFormatter` gives you a lot of flexibility in the display of
coordinates, distances, direction, speed, and velocity.
Choose Metric or Imperial, cardinal directions, abbreviations, or degrees,
and configure everything else (number of significant digits, etc.)
with the associated `NSNumberFormatter`.

### Example Usage

```objective-c
TTTLocationFormatter *locationFormatter = [[TTTLocationFormatter alloc] init];
CLLocation *austin = [[CLLocation alloc] initWithLatitude:30.2669444 longitude:-97.7427778];
CLLocation *pittsburgh = [[CLLocation alloc] initWithLatitude:40.4405556 longitude:-79.9961111];
```

#### Distance in Metric Units with Cardinal Directions

```objective-c
NSLog(@"%@", [locationFormatter stringFromDistanceAndBearingFromLocation:pittsburgh toLocation:austin]);
// "2,000 km Southwest"
```

#### Distance in Imperial Units with Cardinal Direction Abbreviations

```objective-c
[locationFormatter.numberFormatter setMaximumSignificantDigits:4];
[locationFormatter setBearingStyle:TTTBearingAbbreviationWordStyle];
[locationFormatter setUnitSystem:TTTImperialSystem];
NSLog(@"%@", [locationFormatter stringFromDistanceAndBearingFromLocation:pittsburgh toLocation:austin]);
// "1,218 miles SW"
```

#### Speed in Imperial Units with Bearing in Degrees

```objective-c
[locationFormatter setBearingStyle:TTTBearingNumericStyle];
NSLog(@"%@ at %@", [locationFormatter stringFromSpeed:25],[locationFormatter stringFromBearingFromLocation:pittsburgh toLocation:austin]);
// "25 mph at 310°"
```

#### Coordinates

```objective-c
[locationFormatter.numberFormatter setUsesSignificantDigits:NO];
NSLog(@"%@", [locationFormatter stringFromLocation:austin]);
// (30.2669444, -97.7427778)
```

## TTTNameFormatter

`TTTNameFormatter` formats names according to the internationalization standards
of the AddressBook framework, which determine, for example,
the display order of names and whether or not to delimit components with whitespace.

> `TTTNameFormatter` isn't available on OS X.

### Example Usage

```objective-c
TTTNameFormatter *nameFormatter = [[TTTNameFormatter alloc] init];
NSString *frenchName = [nameFormatter stringFromPrefix:nil firstName:@"Guillaume" middleName:@"François" lastName:@"Antoine" suffix:@"Marquis de l'Hôpital"];
NSLog(@"%@", frenchName);
// "Guillaume François Antoine Marquis de l'Hôpital"

NSString *japaneseName = [nameFormatter stringFromFirstName:@"孝和" lastName:@"関"];
NSLog(@"%@", japaneseName);
// "関孝和"
```

## TTTOrdinalNumberFormatter

`NSNumberFormatter` is great for [Cardinal numbers](http://en.wikipedia.org/wiki/Cardinal_number) (17, 42, 69, etc.), but it doesn't have built-in support for [Ordinal numbers](<http://en.wikipedia.org/wiki/Ordinal_number_(linguistics)>) (1st, 2nd, 3rd, etc.)

A naïve implementation might be as simple as throwing the one's place in a switch statement and appending "-st", "-nd", etc. But what if you want to support French, which appends "-er", "-ère", and "-eme" in various contexts? How about Spanish? Japanese?

`TTTOrdinalNumberFormatter` supports English, Spanish, French, German, Irish, Italian, Japanese, Dutch, Portuguese, Simplified Chinese and Swedish. For other languages, you can use the standard default, or override it with your own. For languages whose ordinal indicator depends upon the grammatical properties of the predicate, `TTTOrdinalNumberFormatter` can format according to a specified gender and/or plurality.

### Example Usage

```objective-c
TTTOrdinalNumberFormatter *ordinalNumberFormatter = [[TTTOrdinalNumberFormatter alloc] init];
[ordinalNumberFormatter setLocale:[NSLocale currentLocale]];
[ordinalNumberFormatter setGrammaticalGender:TTTOrdinalNumberFormatterMaleGender];
NSNumber *number = [NSNumber numberWithInteger:2];
NSLog(@"%@", [NSString stringWithFormat:NSLocalizedString(@"You came in %@ place!", nil), [ordinalNumberFormatter stringFromNumber:number]]);
```

Assuming you've provided localized strings for "You came in %@ place!", the output would be:

- English: "You came in 2nd place!"
- French: "Vous êtes arrivé à la 2e place !"
- Spanish: "Usted llegó en 2.o lugar!"

## TTTTimeIntervalFormatter

Nearly every application works with time in some way or another.
And when we display temporal information to users,
it's typically in relative terms to the present,
like "3 minutes ago", "10 months ago", or "last month".
`TTTTimeIntervalFormatter` defaults to a
smart, relative display of an `NSTimeInterval` value,
with options to extend that behavior to your particular use case.

### Example Usage

```objective-c
TTTTimeIntervalFormatter *timeIntervalFormatter = [[TTTTimeIntervalFormatter alloc] init];
[timeIntervalFormatter stringForTimeInterval:0]; // "just now"
[timeIntervalFormatter stringForTimeInterval:-100]; // "1 minute ago"
[timeIntervalFormatter stringForTimeInterval:-8000]; // "2 hours ago"

// Turn idiomatic deictic expressions on / off
[timeIntervalFormatter stringForTimeInterval:-100000]; // "1 day ago"
[timeIntervalFormatter setUsesIdiomaticDeicticExpressions:YES];
[timeIntervalFormatter stringForTimeInterval:-100000]; // "yesterday"

// Customize the present tense deictic expression for
[timeIntervalFormatter setPresentDeicticExpression:@"seconds ago"];
[timeIntervalFormatter stringForTimeInterval:0]; // "seconds ago"

// Expand the time interval for present tense
[timeIntervalFormatter stringForTimeInterval:-3]; // "3 seconds ago"
[timeIntervalFormatter setPresentTimeIntervalMargin:10];
[timeIntervalFormatter stringForTimeInterval:-3]; // "seconds ago"
```

## TTTUnitOfInformationFormatter

No matter how far abstracted from its underlying hardware an application may be,
there comes a day when it has to communicate the size of a file to the user.
`TTTUnitOfInformationFormatter` transforms a number of bits or bytes
into a humanized representation,
using either SI decimal or IEC binary unit prefixes.

### Example Usage

```objective-c
TTTUnitOfInformationFormatter *unitOfInformationFormatter = [[TTTUnitOfInformationFormatter alloc] init];
[unitOfInformationFormatter stringFromNumberOfBits:[NSNumber numberWithInteger:416]]; // "56 bytes"

// Display in either bits or bytes
[unitOfInformationFormatter setDisplaysInTermsOfBytes:NO];
[unitOfInformationFormatter stringFromNumberOfBits:[NSNumber numberWithInteger:416]]; // "416 bits"

// Use IEC Binary prefixes (base 2 rather than SI base 10; see http://en.wikipedia.org/wiki/IEC_60027)
[unitOfInformationFormatter setUsesIECBinaryPrefixesForCalculation:NO];
[unitOfInformationFormatter stringFromNumberOfBits:[NSNumber numberWithInteger:8660]]; // "8.66 Kbit"

[unitOfInformationFormatter setUsesIECBinaryPrefixesForCalculation:YES];
[unitOfInformationFormatter setUsesIECBinaryPrefixesForDisplay:YES];
[unitOfInformationFormatter stringFromNumberOfBits:[NSNumber numberWithInteger:416]]; // "8.46 Kibit"
```

## TTTURLRequestFormatter

`NSURLRequest` objects encapsulate all of the information made in a network request,
including url, headers, body, etc.
This isn't something you'd normally want to show to a user,
but it'd be nice to have a way to make it more portable for debugging.
Enter: `TTTURLRequestFormatter`.
In addition to formatting requests simply as `POST http://www.example.com/`,
it also generates `cURL` and `Wget` commands
with all of its headers and data fields intact to debug in the console.

### Example Usage

```objective-c
NSMutableURLRequest *request = [[NSMutableURLRequest alloc] initWithURL:[NSURL URLWithString:@"http://www.example.com/"]];
[request setHTTPMethod:@"POST"];
[request addValue:@"text/html" forHTTPHeaderField:@"Accept"];
[TTTURLRequestFormatter cURLCommandFromURLRequest:request];
```

    curl -X POST "https://www.example.com/" -H "Accept: text/html"

## Localizations

FormatterKit comes fully internationalized,
with `.strings` files for the following locales:

- Catalan (`ca`)
- Chinese (Simplified) (`zh_Hans`)
- Chinese (Traditional) (`zh_Hant`)
- Czech (`cs`)
- Danish (`da`)
- Dutch (`nl`)
- English (`en`)
- French (`fr`)
- German (`de`)
- Greek (`el`)
- Hebrew (`he`)
- Hungarian (`hu`)
- Indonesian (`id`)
- Italian (`it`)
- Korean (`ko`)
- Norwegian Bokmål (`nb`)
- Norwegian Nynorsk (`nn`)
- Polish (`pl`)
- Portuguese (Brazilian) (`pt_BR`)
- Russian (`ru`)
- Spanish (`es`)
- Swedish (`sv`)
- Turkish (`tr`)
- Ukranian (`uk`)
- Vietnamese (`vi`)

---

## License

FormatterKit is available under the MIT license.
See the LICENSE file for more info.

[cnpostaladdressformatter]: https://developer.apple.com/documentation/contacts/cnpostaladdressformatter?language=objc
[nslistformatter]: https://developer.apple.com/documentation/foundation/nslistformatter?language=objc
[nspersonnamecomponentsformatter]: https://developer.apple.com/documentation/foundation/NSPersonNameComponentsFormatter
[nsmeasurementformatter]: https://developer.apple.com/documentation/foundation/NSMeasurementFormatter
[nsnumberformatterordinalstyle]: https://developer.apple.com/documentation/foundation/nsnumberformatterstyle/nsnumberformatterordinalstyle?language=objc
[nsrelativedatetimeformatter]: https://developer.apple.com/documentation/foundation/NSRelativeDateTimeFormatter
[nsunitinformationstorage]: https://developer.apple.com/documentation/foundation/NSUnitInformationStorage


[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fhaztheo%2FFormatterKit.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fhaztheo%2FFormatterKit?ref=badge_large)