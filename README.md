# StepperViewSwift

#### Swift iOS component for Step Indications

[![iOS](https://img.shields.io/badge/ios-14%2B-brightgreen)](#)
[![Swift](https://img.shields.io/badge/swift-5-green)](#)
[![License](https://img.shields.io/badge/License-MIT%20license-blue)](https://github.com/azizHamadi/StepperViewSwift/blob/master/LICENSE)

**StepperViewSwift** is a fully customizable control to give people more ways to create a stepper.

## Overview

**StepperViewSwift** is the stepper UI framework. This release includes a dynamic stepper model, including the ability to create your own stepper, use auto-size stackviews and tableviews instead of sizing cells, 100% fast, and more. The result is a much simpler, easier to use, more powerful and easier to maintain stepper. This framework is currently used by the [BubbleYou](https://apps.apple.com/id/app/bubbleyou/id1559384388) iOS application in the quiz, voting and survey module.

<p align="center">
  <img src="https://github.com/azizHamadi/StepperViewSwift/blob/readme-version-1/Images/defaultStepperView.gif?raw=true" alt="screenshot" height="591" width="289" />
</p>

- [Requirements](#requirements)
- [Usage](#usage)
  - [Initialization](#initialization)
  - [Customization](#customization)
  - [Implementation](#implementation)
    - [Default stepper](#default-stepper)
    - [Custom stepper](#custom-stepper)
    - [Common function](#common-function)
- [Installation](#installation)
- [Contribution](#contribution)
- [Contact](#contact)
- [License](#license-mit)


## Requirements

- iOS 14+
- Xcode 12+
- Swift 5+

## Usage

### Initialization

You can initialize the stepper by using **code** or **storyboard**

### Storyboard
Go to **.storyboard** and add a **blank UIView**, set the view class to 'Stepper' in the Identity Inspector

Make sure that you select the
'StepperViewSwift' in the 'Module' field

<p align="center">
  <img src="https://github.com/azizHamadi/StepperViewSwift/blob/readme-version-1/Images/Custom%20class.png?raw=true" alt="screenshot" />
</p>

### Code
```swift
import StepperViewSwift

let stepper = Stepper(frame: CGRect(x: 0, y: 0, width: 300, height: 500))
// configure datasource & delegate
// you can configure the size of the 'stepper' circle, line thickness, color of the circles...

view.addSubview(stepper)
```

## Customization

You can customize the color, stepper size and selected icon color from the inspector as well by code 

### Inspector

<p align="center">
  <img src="https://github.com/azizHamadi/StepperViewSwift/blob/readme-version-1/Images/stepperViewConfig.gif?raw=true" alt="screenshot" />
</p>

### Code 

```swift
// widthLinearStepperView is the size of the vertical line view of the stepper
stepperView.widthLinearStepperView = 5

// stepperSize is the stepper circle size
stepperView.stepperSize = 60

// stepperRadius is the corner radius of the stepper circle
stepperView.stepperRadius = 15

// stepperColor is the stepper circle color
stepperView.stepperColor = .red

// borderStepperColor is the border color of the stepper and line view circle
stepperView.borderStepperColor = .black

// iconStepperView is the stepper circle icon
stepperView.iconStepperView = UIImage(named: "iconStepper")!

// iconColorStepperView is the icon color of the stepper circle
stepperView.iconColorStepperView = .systemIndigo

// isStepperInteraction to enable/disable interaction with stepper circles
// default: isStepperInteraction = true -> hide and show stepper content when clicking on stepper circles
stepperView.isStepperInteraction = false
```
You can change the font of the title and description as well as their colors, customize the color of checkbox and radiobox

<p align="center">
  <img src="https://github.com/azizHamadi/StepperViewSwift/blob/readme-version-1/Images/stepperParam.jpg?raw=true" alt="screenshot" height="469" width="516" />
</p>

```swift
stepperView.titleFont = UIFont(name: "Futura", size: 17)!
stepperView.titleColor = .red
stepperView.actionIconColor = .red
```

## Implementation

**StepperViewSwift** offers you 2 types of stepper: 

- Default stepper
- Custom stepper

### Default stepper

*Default stepper* use the enumeration `StepperModel`

#### StepperModel
A `StepperModel` is a enumeration defined with these properties:
- ViewModel
- RequestModel

##### ViewModel

`ViewModel` is a structure used to create the stepper and which contains the following properties:
- *title:* stepper title must not be null
- *description:* a description of the stepper
- *type:* must not be null and contains 5 types: text, textarea, checkbox, radiobox and ranking (drag and drop)
- *resourceConfig:* to configure the title and the placeholder of the input, it contains: 
  - *responseTitle:* Additional information
  - *responsePlaceholder:* a placeholder for the input field
- *maxChecked:* limit selections with a checkbox type stepper
- *responseList:* list of choices for ranking, checkbox and radiobox type steppers, it contains:
  - *responseText:* a response title
  - *image:* an image of the response
  - *imageType:* image type can be system, assets or presignedUrl 
  - *checked:* response checked or not

```swift
StepperModel.ViewModel(title: "title",
                      description: "description",
                      type: .textarea,
                      resourceConfig: ResourceConfig(responseTitle: "Short answer*",
                                                    responsePlaceholder: "placeholder text"))
```

##### RequestModel

`RequestModel` is a structure used to retrieve stepper data and contains the following properties:
- *type:* must not be null and contains 5 types: text, textarea, checkbox, radiobox and ranking (drag and drop)
- *responseText:* text and textarea type stepper response
- *responseList:* list of selected choices for steppes of type classification, checkbox and radiobox

#### defaultDataSource

`defaultDataSource` is a protocol used to generate the default stepper view

```swift
// Conforming to the DefaultStepperDataSource
stepperView.defaultDataSource = self

// data: contains the list of StepperModel.ViewModel which will be displayed
func numberOfRowsInStepper() -> Int {
    return data.count
}
func stepper(dataForRowAtIndexPath indexPath: NSIndexPath) -> StepperModel.ViewModel? {
    return data[indexPath.row]
}
```

#### defaultDelegate

`defaultDelegate` is a protocol used to retrieve data entered in the stepper

`resultStepper` function called when switching from stepper to another

```swift
// Conforming to the DefaultStepperDelegate
stepperView.defaultDelegate = self

extension ExampleViewController: DefaultStepperDelegate {
    func resultStepper(dataAtIndexPath indexPath: NSIndexPath, data: StepperModel.RequestModel?) {
        print(data)
    }
}
```

### Custom stepper

*Custom stepper* use your custom view

#### CustomStepperDataSource

To generate a custom stepper, you first need to subclass `CustomStepperDataSource`

```swift
// Conforming to the CustomStepperDataSource
stepperView.customDataSource = self

// titles: contains the list of title which will be displayed
func numberOfRowsInStepper() -> Int {
    return titles.count
}

func stepper(cellForRowAtIndexPath indexPath: NSIndexPath) -> UIView? {
    let customStepperView = ExampleStepperItem()
    // you can create or return existing view
    // customize the view
    return customStepperView
}

func titleStepper(cellForRowAtIndexPath indexPath: NSIndexPath) -> String? {
    return titles[indexPath.row]
}
```

#### CustomStepperDelegate

`CustomStepperDelegate` is a protocol used to retrieve the view in the stepper

`resultStepper` function called when switching from stepper to another

```swift
// Conforming to the CustomStepperDelegate
stepperView.customDelegate = self

func resultStepper(viewAtIndexPath indexPath: NSIndexPath, view: UIView?) {
    if let customStepperView = view as? MyView {
        print("Index of custom stepper view : \(indexPath.row)")
    }
}
```
You can return the view of stepper manually

```swift
let customViewAtIndexPath = stepperView.customViewFromStepper(indexPath: IndexPath(row: 0, section: 0))
```

### Common function

```swift
//You can switch from one stepper to another manually
// to move to the next stepper
stepperView.nextStepper()

// to move to the previous stepper
stepperView.previousStepper()

// check if it's the last stepper
stepperView.isLastStepper()

// number of stepper on stepper view
stepperView.numberOfStepper()

// reload Data of stepper view
stepperView.reloadData()

// index of the selected stepper
stepperView.selectedIndex()
```

## Installation

#### CocoaPods

Install CocoaPods if not already available:

``` bash
$ [sudo] gem install cocoapods
$ pod setup
```
Go to the directory of your Xcode project, and Create and Edit your *Podfile* and add _StepperViewSwift_:

``` bash
$ cd /path/to/MyProject
$ pod init
$ open -a Xcode Podfile
source 'https://github.com/CocoaPods/Specs.git'

# Comment the next line if you don't want to use dynamic frameworks
use_frameworks!

pod 'StepperViewSwift'
```

Install into your project:

``` bash
$ pod install
```

Open your project in Xcode from the .xcworkspace file (not the usual project file):

``` bash
$ open MyProject.xcworkspace
```

You can now `import StepperViewSwift` framework into your files.

## Contribution

Contributions are welcomed and encouraged *♡*.

## Contact

Aziz Hamadi
 - [LinkedIn](https://tn.linkedin.com/in/aziz-hamadi)

## License (MIT)

StepperViewSwift is available under the MIT license. See the [LICENSE](https://github.com/azizHamadi/StepperViewSwift/blob/readme-version-1/LICENSE) file for more info.
