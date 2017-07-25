<p align="center">
  <a href="https://github.com/luc-dion/FlexLayout"><img src="docs/images/flexlayout-logo-2.png" alt="FlexLayout" width="270"/></a>
</p>

<h1 align="center" style="color: #376C9D; font-family: Arial Black, Gadget, sans-serif; font-size: 3em">FlexLayout</h1>
 
<p align="center">
  <a href=""><img src="https://img.shields.io/cocoapods/p/FlexLayout.svg?style=flat" /></a>
  <a href="https://travis-ci.org/luc-dion/FlexLayout"><img src="https://travis-ci.org/luc-dion/FlexLayout.svg?branch=dev" /></a>
  <a href="https://codecov.io/gh/luc-dion/FlexLayout"><img src="https://codecov.io/gh/luc-dion/FlexLayout/branch/dev/graph/badge.svg"/></a>
  <a href='https://img.shields.io/cocoapods/v/FlexLayout.svg'><img src="https://img.shields.io/cocoapods/v/FlexLayout.svg" /></a>
  <!--a href="https://github.com/Carthage/Carthage"><img src="https://img.shields.io/badge/Carthage-compatible-4BC51D.svg?style=flat" /></a-->
  <a href="https://raw.githubusercontent.com/luc-dion/FlexLayout/master/LICENSE"><img src="https://img.shields.io/badge/license-New%20BSD-blue.svg?style=flat" /></a>
  <!--a href="https://github.com/luc-dion/FlexLayout/issues"><img src="https://img.shields.io/github/issues/luc-dion/FlexLayout.svg?style=flat" /></a-->
</p>

<br>

FlexLayout gently wraps [facebook/yoga](https://github.com/facebook/yoga) flexbox implementation in a concise, and chainable syntax.

<br>

### WARNING: This project is not ready yet and its still under development. Thanks for coming back later. 

<br>

### Requirements
* iOS 8.0+
* Xcode 8.0+
* Swift 3.0+

### Content

* [FlexLayout principles and philosophy](#introduction)
* [Usage example](#intro_usage_example)
* [Documentation](#documentation)
	* [Creation, modification and definition of flexbox containers](#create_modify_define_containers)
	* [Flexbox containers properties](#containers_properties)
	* [Flexbox items properties](#intems_properties)
	* [Absolute positionning](#absolute_positionning)
	* [Adjusting the size](#adjusting_size)
		* [Width, height and size](#width_height_size)
		* [minWidth, maxWidth, minHeight, maxHeight](#minmax_width_height_size)
		* [Aspect Ratio](#aspect_ratio)
	* 6. Margins <a name="margins"></a>
	* 7. Paddings <a name="paddings"></a>
	* 8. Borders <a name="borders"></a>
	* Accessing flexbox's UIView  <a name="accessing_flexbox_view"></a
* [Performance](#performance)
* [Comments, ideas, suggestions, issues, ....](#comments)
* [Installation](#installation)

<br>

## FlexLayout principles and philosophy <a name="introduction"></a>

* Flexbox layouting is simple, powerfull and fast.
* FlexLayout syntax is concise and chainable.
* FlexLayout/yoga is incredibly fast, its even faster than manual layout. See performances below.
* The source code structure matches the flexbox structure, making it easier to understand and modify. Flex containers are defined on one line, and its items (children) are imbricated. This makes the flexbox structure much more visual and easy to understand.
* FlexLayout is a companion of [PinLayout](https://github.com/mirego/PinLayout). They share a similar syntax and method names (ex: width, height, minWidth, maxWidth, marginTop, marginHorizontal, ...). PinLayout is a lower level layout framework, it is particularly useful for absolute positionning and animations. 

NOTE: FlexLayout wraps [facebook/yoga](https://github.com/facebook/yoga) implementation and expose all its features. So note that on this documentation we will refer to FlexLayout, but this also applies to Yoga.

<br>

## Usage sample <a name="intro_usage_example"></a>
###### Example:

![](docs/pinlayout-intro-example.png)

Two steps to use a flexbox container:

1. **Setup**: Initialize your flexbox structure. This need to be done once, probably in your UIView initializer.
2. **Layout**: The layout of the flexbox should be done from layoutSubviews() (or `willTransition(to: UITraitCollection, ...)` and `viewWillTransition(to: CGSize, ...)`). First layout your root flexbox container, then layout the flexbox children using FlexLayout method `layout()`.

```swift
init() {
   super.init(frame: .zero)
}

override func layoutSubviews() {
   super.layoutSubviews() 
    ...
    rootFlexboxView.layout()
}
``` 

## Variation from CSS flexbox
FlexLayout default properties are different from CSS flexbox. FlexLayout use the same default properties as Yoga/ReactNative flexbox.
....

* In many CSS methods and properties name, the keyword `flex` was added to control name conflicts. FlexLayout removed this keyword for being more concise and removed this unecessary keyword:

	| FlexLayout Name     | CSS Name | React Native Name |
	|---------------------|----------|-------------------|
	| **`direction`** | `flex-direction` | `flexDirection` |
	| **`wrap`** | `flex-wrap` | `flexWrap` |
	| **`grow`** | `flex-grow` | `flexGrow` |
	| **`shrink`** | `flex-shrink` | `flexShrink` |
	| **`basis`** | `flex-basis` | `flexBasis` |
	| **`start`** | `flex-start` | `flexStart` |
	| **`end`** | `flex-end` | `flexEnd` |


NOTE: **FlexLayout** doesn't support the flexbox `order` property**. The order is  determined by the flex container's `UIView.subviews` array. 


**FlexLayout additions**: 

* addItem()
* addContainer()
* define()
* layout()
* isIncludedInLayout()
* markAsDirty()
* intrinsicSize
* sizeThatFits()

<br>

# Documentation <a name="documentation"></a>

Flexbox is pretty easy and straightforward to use. The defining aspect of the flexbox is the ability to alter its items, width, height to best fill the available space on any display device. A flex container expands its items to fill the available free space or shrinks them to prevent overflow.

The flex layout is constituted of parent container referred as **flex container** and its immediate children which are called **flex items**. A flex item can also be a flex container, i.e. it is possible to add other flex items to it.

| Flexbox term     | Definition |
|---------------------|----------|-------------------|
| **`main-axis`** | The main axis of a flex container is the primary axis along which flex items are laid out.|
| **`cross-axis`** | The axis perpendicular to the main axis is called the cross axis. Its direction depends on the main axis direction
	
In the following sections we will see:

1. How to create, modifiy and defines flex containers and items.
2. Flexbox container's properties
3. Flexbox item's properties

<br>

## 1. Creation, modification and definition of flexbox containers <a name=" <a name="intro_usage_example"></a>

### addItem(:UIView)
- Applies to: `flex containers`
- Parameter: UIView
- Returns: FlexLayout interface of the newly added flex item.

This methods adds a flex item (UIView) to a flex container. Internally the methods adds the UIView has a subviews and enables flexbox.


###### Usage examples:
```swift
   view.flexbox.addItem(imageView).width(100).aspectRatio(1)
```
<br>

### addContainer()
- Applies to: `flex containers`
- Returns: FlexLayout interface of the newly created flex item.

This method is similar to `addItem()` except that it also creates the flex container's UIView. Internally the method creates a UIView, adds it has a subviews and finaly enables flexbox. This is useful to add a flex container easily when you don't need to refered to it later.

###### Usage examples:
```swift
	view.flexbox.addContainer().direction(.row).padding(10)
```
<br>

### define()
- Applies to: `flex containers`
- Parameter: Closure of type `(flexbox: FlexLayout) -> Void`

This method is used to structure your code so that it matches the flexbox structure. The method takes as parameter a closure with a single parameter called `flexbox`. This parameter is in fact the view's flexbox interface, it can be used to adds other flex items and containers. 

###### Usage examples:
```swift
	view.flexbox.addContainer().define { (flexbox) in
		flexbox.addItem(imageView).grow(1)
		
		flexbox.addContainer().direction(.row).define { (flexbox) in
			flexbox.addItem(titleLabel).grow(1)
			flexbox.addItem(priceLabel)
		}
	}
```

The same results can also be obtained without using the `define()` method, but the results is not as elegant:

```swift
   let columnContainer = UIView()
   columnContainer.flexbox.addItem(imageView).grow(1)
   view.flexbox.addItem(columnContainer)
		
   let rowContainer = UIView()
   rowContainer.flexbox.direction(.row)
   rowContainer.flexbox.addItem(titleLabel).grow(1)
   rowContainer.flexbox.addItem(priceLabel)
   columnContainer.flexbox.addItem(rowContainer)
```

**Avantages of using `define()`**:

- The source code structure matches the flexbox structure, making it easier to understand and modify.
	- Changing a flex item order, its just moving up/down its line/block that defines it.
	- Moving a flex item from one container to another is just moving line/block that defines it.
- The structure looks more similar to how HTML and React Native defines it.
- Inside the `define`'s closure, you can do whatever you want to fill the flexbox container, you can use `for` loops, iterates arrays of data, call functions, ...
 
 
### Accessing flex items UIView <a name="accessing_flexbox_view"></a>
It is possible to access the flex items's UIView using `flexbox.view`. This is particularly usefull when using `Flexbox.define()` method.

###### Example:
This example creates a flexbox container and sets its background color and add rounded corners.
![Flowers](docs/01-example-distance-superview-edge.png)

```swift
    flexbox.createBox().direction(.row).padding(20).alignItems(.center).define({ (flexbox) in
        flexbox.createBox().width(50).height(50).define({ (flexbox) in
            flexbox.view.backgroundColor = UIColor.red
            flexbox.view.layer.cornerRadius = 10
        }}
    })
``` 

Another possible solution:

```swift
    flexbox.createBox().direction(.row).padding(20).alignItems(.center).define({ (flexbox) in
        let container = UIView()
        container.backgroundColor = UIColor.red
        container.layer.cornerRadius = 10
        
        flexbox.addChild(container).width(50).height(50)
    })
``` 
<br>

### layout()
- Applies to: `flex containers`
- Values: `fitContainer` / `adjustWidth` / `adjustHeight`
- Default value: `fitContainer `

`func layout(mode: LayoutMode = . fitContainer)`

The method layout the flex container's children.

**Three different layout mode is available:**

1. fitContainer: This is the default mode when no parameter is specified. Children are layouted **inside** the container's size (width and height). 
2. adjustHeight: In this mode children are layouted **using only the container's width**. The container's height will be adjusted to fit the flexbox's children
3. adjustWidth: In this mode children are layouted **using only the container's height**. The container's width will be adjusted to fit the flexbox's children


### isIncludedInLayout()
- Applies to: `flex items`
- Parameter: Bool

It is possible to control dynamically if a flexbox's UIView is included or not in the flexbox layouting. 
When a flexbox's UIView is excluded, FlexLayout won't layout the view and its children views.

**Property & Method:**

* `isIncludedInLayout(: Bool)`

FlexLayout automatically includes the UIView when:
* The first time `UIView.flexbox` property is accessed
* When a child view is added to a flexbox container using `addChild(:UIView)`
* When a flexbox container is created using `createBox()`

<br>

### markAsDirty()
- Applies to: `flex items`
- Parameter: Bool

In the event that you need to another layout pass on a view you can mark it dirty via `flex.markAsDirty()`

### sizeThatFits()
- Applies to: `flex items`
- Parameter: CGSize

Returns the item size when layouted in the specified frame size.

###### Usage Example:
Get the size of view when layouted in a container with a width of 200 pixels.

```swift
    let layoutSize = viewA.flexbox.sizeThatFits(CGSize(width: 200, height: CGFloat.greatestFiniteMagnitude)
```

### intrinsicSize
- Applies to: `flex items`
- Parameter: None

Item natural size, considering only properties of the view itself. Independent of the item frame.

<br>

## 2. Flexbox containers properties  <a name="containers_properties"></a>
This section describe all flex container's properties.

### direction() 
- Applies to: `flex containers`
- Values: `column` / `columnReverse` / `row` / `rowReverse`
- Default value: `column`
- CSS name: `flex-direction` 

The `direction` property establishes the main-axis, thus defining the direction flex items are placed in the flex container.

The `direction` property specifies how flex items are laid out in the flex container, by setting the direction of the flex container’s main axis. They can be laid out in two main directions,  like columns vertically or like rows horizontally.

Note that row and row-reverse are affected by the layout direction (see `layoutDirection` property) of the flex container. If its text direction is LTR (left to right), row represents the horizontal axis oriented from left to right, and row-reverse from right to left; if the direction is rtl, it's the opposite.

NEW: This property is used to specify the direction of the flex container’s main axis, thus defining the direction flex items are placed in the flex container


| Value | Result | Description |
|---------------------|:------------------:|---------|
| **column** (default) 	| <img src="docs/images/flexlayout-direction-column.png" width="100"/>| Top to bottom |
| **columnReverse** | <img src="docs/images/flexlayout-direction-columnReverse.png" width="100"/>| Bottom to top |
| **row** | <img src="docs/images/flexlayout-direction-row.png" width="190"/>| Same as text direction |
| **rowReverse** | <img src="docs/images/flexlayout-direction-rowReverse.png" width="190"/>| opposite to text direction |


###### Usage examples:
```swift
	view.flexbox.direction(.column)  // Not required, defaut value. 
	view.flexbox.direction(.row)
```
<br/>

### wrap()
- Applies to: `flex containers`
- Values: `noWrap` / `wrap` / `wrapReverse`
- Default value: `noWrap`
- CSS name: `flex-wrap` 

The `wrap` property controls whether the flex container is single-lined or multi-lined, and the direction of the cross-axis, which determines the direction in which the new lines are stacked in.

Reminder: the cross axis is the axis perpendicular to the main axis. Its direction depends on the main axis direction.

NEW: By default the flex container fits all flex items into one line. Using this property we can change that. We can tell the container to lay out its items in single or multiple lines, and the direction the new lines are stacked in.

|                     	| direction(.column) | direction(.row) | Description|
|---------------------	|:------------------:|:---------------:|--------------|
| **noWrap** (default) 	| <img src="docs/images/flexlayout-wrap-column-nowrap.png" width="130"/>| <img src="docs/images/flexlayout-wrap-row-nowrap.png" width="180"/>| Single-line which may cause the container to overflow. NEW: Flex items are displayed in one row and by default they are shrunk to fit the flex container’s width |
| **wrap** | <img src="docs/images/flexlayout-wrap-column-wrap.png" width="130"/>| <img src="docs/images/flexlayout-wrap-row-wrap.png" width="180"/>| Multi-lines, direction is defined by `direction()`. NEW: Flex items are displayed in multiple rows if needed from left-to-right and top-to-bottom  |
| **wrapReverse**	| <img src="docs/images/flexlayout-wrap-column-wrapReverse.png" width="130"/>| <img src="docs/images/flexlayout-wrap-row-wrapReverse.png" width="180"/>| Multi-lines, opposite to direction defined by `direction()`. NEW: Flex items are displayed in multiple rows if needed from left-to-right and bottom-to-top |

###### Usage examples:
```swift
	view.flexbox.wrap(.nowrap)  // Not required, defaut value. 
	view.flexbox.wrap(.wrap)
```
<br/>

### justifyContent()
- Applies to: `flex containers`
- Values: `start` / `end` / `center` / `spaceBetween` / `spaceAround`
- Default value: `start`
- CSS name: `justify-content` 

The `justifyContent` property defines the alignment along the main-axis of the current line of the flex container. It helps distribute extra free space leftover when either all the flex items on a line have reached their maximum size. For example, if children are flowing vertically, justifyContent controls how they align vertically. 

NEW: justify-content property aligns flex items along the main axis of the current line of the flex container. It helps distribute left free space when either all the flex items on a line are inflexible, or are flexible but have reached their maximum size.

|                     	| direction(.column) | direction(.row) |
|---------------------	|:------------------:|:---------------:|
| **start** (default) 	| <img src="docs/images/flexlayout-justify-column-flexstart.png" width="140"/>| <img src="docs/images/flexlayout-justify-row-flexstart.png" width="160"/>|
| **end**	| <img src="docs/images/flexlayout-justify-column-flexend.png" width="140"/>| <img src="docs/images/flexlayout-justify-row-flexend.png" width="160"/>|
| **center** 	| <img src="docs/images/flexlayout-justify-column-center.png" width="140"/>| <img src="docs/images/flexlayout-justify-row-center.png" width="160"/>|
| **spaceBetween** 	| <img src="docs/images/flexlayout-justify-column-spacebetween.png" width="140"/>| <img src="docs/images/flexlayout-justify-row-spacebetween.png" width="160"/>|
| **spaceAround** 	| <img src="docs/images/flexlayout-justify-column-spacearound.png" width="140"/>| <img src="docs/images/flexlayout-justify-row-spacearound.png" width="160"/>|

###### Usage examples:
```swift
	view.flexbox. justifyContent(.start)  // defaut value. 
	view.flexbox. justifyContent(.center)
```
<br/>

### alignItems()
- Applies to: `flex containers`
- Values: `stretch` / `start` / `end` / `center` / `baseline`
- Default value: `stretch `
- CSS name: `align-items` 

The `alignItems` property defines how flex items are laid out along the cross axis on the current line. Similar to justifyContent but for the cross-axis (perpendicular to the main-axis). For example, if children are flowing vertically, alignItems controls how they align horizontally. 

NEW: The align-items property defines how flex items are laid out along the cross axis on the current line. You can think of it as the justify-content version for the cross-axis.

|                     	| direction(.column) | direction(.row) |
|---------------------	|:------------------:|:---------------:|
| **stretch** (default) 	| <img src="docs/images/flexlayout-align-column-stretch.png" width="140"/>| <img src="docs/images/flexlayout-align-row-stretch.png" width="160"/>|
| **start**	| <img src="docs/images/flexlayout-align-column-flexStart.png" width="140"/>| <img src="docs/images/flexlayout-align-row-flexStart.png" width="160"/>|
| **end**	| <img src="docs/images/flexlayout-align-column-flexEnd.png" width="140"/>| <img src="docs/images/flexlayout-align-row-flexEnd.png" width="160"/>|
| **center** 	| <img src="docs/images/flexlayout-align-column-center.png" width="140"/>| <img src="docs/images/flexlayout-align-row-center.png" width="160"/>|
| **baseline** 	| | <img src="docs/images/flexlayout-align-row-baseline.png" width="160"/>|

<br/>

### alignSelf()
- Applies to: `flex containers`
- Values: `auto` / `stretch` / `start` / `end` / `center` / `baseline`
- Default value: `auto`
- CSS name: `align-self` 

The `alignItems` property controls how a child aligns in the cross direction, overriding the alignItems of the parent. For example, if children are flowing vertically, alignSelf will control how the flex item will align horizontally. 

The `auto` value means use the flex container `alignItems` property. See `alignItems` for documentation of the other values.

NEW: This align-self property allows the default alignment (or the one specified by align-items) to be overridden for individual flex items. Refer to align-items explanation for flex container to understand the available values.

<br/>

### alignContent()
- Applies to: `flex containers`
- Values: `start` / `end` / `center` / `stretch` / `spaceBetween` / `spaceAround`
- Default value: `start`
- CSS name: `align-content`  

The align-content property aligns a flex container’s lines within the flex container when there is extra space in the cross-axis, similar to how justifyContent aligns individual items within the main-axis.

Note, `alignContent` has no effect when the flexbox has only a single line.

NEW: The align-content property aligns a flex container’s lines within the flex container when there is extra space in the cross-axis (similar to how justify-content aligns individual items within the main-axis)

|                     	| direction(.column) | direction(.row) |
|---------------------	|:------------------:|:---------------:|
| **start** (default) 	| <img src="docs/images/flexlayout-alignItems-column-flexStart.png" width="140"/>| <img src="docs/images/flexlayout-alignItems-row-flexStart.png" width="160"/>|
| **end**	| <img src="docs/images/flexlayout-alignItems-column-flexEnd.png" width="140"/>| <img src="docs/images/flexlayout-alignItems-row-flexEnd.png" width="160"/>|
| **center** 	| <img src="docs/images/flexlayout-alignItems-column-center.png" width="140"/>| <img src="docs/images/flexlayout-alignItems-row-center.png" width="160"/>|
| **stretch**	| <img src="docs/images/flexlayout-alignItems-column-stretch.png" width="140"/>| <img src="docs/images/flexlayout-alignItems-row-stretch.png" width="160"/>|
| **spaceBetween** | <img src="docs/images/flexlayout-alignItems-column-spaceBetween.png" width="160"/> | <img src="docs/images/flexlayout-alignItems-row-spaceBetween.png" width="160"/>|
| **spaceAround** | <img src="docs/images/flexlayout-alignItems-column-spaceAround.png" width="160"/> | <img src="docs/images/flexlayout-alignItems-row-spaceAround.png" width="160"/>|

<br/>

# 3. Flexbox items properties <a name="intems_properties"></a>
This section describe all flex items's properties.

## grow 
- Applies to: `flex items`
- Value type: CGFloat
- Default value: 0
- CSS name: `flex-grow` 

The `grow` property defines the ability for a flex item to grow if necessary. It accepts a unitless value that serves as a proportion. It dictates what amount of the available space inside the flex container the item should take up.

For example, if all items have `grow` set to 1, every child will set to an equal size inside the container. If you were to give one of the children a value of 2, that child would take up twice as much space as the others.

NEW: This property specifies the flex grow factor, which determines how much the flex item will grow relative to the rest of the flex items.


## shrink
- Applies to: `flex items`
- Value type: CGFloat
- Default value: 1
- CSS name: `flex-shrink` 

It specifies the "flex shrink factor", which determines how much the flex item will shrink relative to the rest of the flex items in the flex container when there isn't enough space on the main-axis.

When omitted, it is set to 1 and the flex shrink factor is multiplied by the flex `basis` when distributing negative space.

NEW: The flex-shrink specifies the flex shrink factor, which determines how much the flex item will shrink relative to the rest of the flex items

## basis
- Applies to: `flex items `
- Value type: CGFloat
- Default value: 0
- CSS name: `flex-basis` 

This property takes the same values as the width and height properties, and specifies the initial size of the flex item, before free space is distributed according to the grow and shrink factors.

NEW: This property takes the same values as the width and height properties, and specifies the initial main size of the flex item

## 4. Absolute positionning: left / top / right / bottom / start / end  <a name="absolute_positionning"></a>
- Applies to: `flex items`
- Parameter: CGFloat

    public func left(_ value: CGFloat) -> Flexbox {
    public func top(_ value: CGFloat) -> Flexbox {
    public func right(_ value: CGFloat) -> Flexbox {
    public func bottom(_ value: CGFloat) -> Flexbox {
    public func start(_ value: CGFloat) -> Flexbox {
    public func end(_ value: CGFloat) -> Flexbox {
    

## 5. Adjusting the size  <a name="adjusting_size"></a> 

### Width and height and size <a name="width_height_size"></a>

FlexLayout has methods to set the view’s height and width.

**Methods:**

* `width(_ width: CGFloat)` / `width(percent: Percent)`  
The value specifies the view's width in pixels. Value must be non-negative.
* `height(_ height: CGFloat)` / `height(percent: Percent)`  
The value specifies the view's height in pixels. Value must be non-negative.
* `size(_ size: CGSize)` / `size(_ percent: Percent)`  
The value specifies view's width and the height in pixels. Values must be non-negative.
* `size(_ sideLength: CGFloat)`  
The value specifies the width and the height of the view in pixels, creating a square view. Values must be non-negative.


###### Usage examples:
```swift
	view.flexbox.width(100)
	view.flexbox.width(of: view1)
	
	view.flexbox.height(200)
	
	view.flexbox.size(of: view1)
	view.flexbox.size(250)
```

### minWidth, maxWidth, minHeight, maxHeight <a name="minmax_width_height_size"></a>

FlexLayout has methods to set the view’s minimum and maximum width, and minimum and maximum height. 

**Methods:**

* `minWidth(_ width: CGFloat)` 
The value specifies the view's minimum width of the view in pixels. Value must be non-negative.

* `maxWidth(_ width: CGFloat)`  
The value specifies the view's maximum width of the view in pixels. Value must be non-negative.

* `minHeight(_ height: CGFloat)`
The value specifies the view's minimum height of the view in pixels. Value must be non-negative.

* `maxHeight(_ height: CGFloat)`
The value specifies the view's maximum height of the view in pixels. Value must be non-negative.
   
###### Usage examples:
```swift
	view.flexbox.maxWidth(200)
	view.flexbox.width(of: view1).maxWidth(250)
	
	view.flexbox.maxHeight(100)
	view.flexbox.height(of: view1).maxHeight(200)
```

### Aspect Ratio <a name="aspect_ratio"></a>
...

<br/>


## 6. Margins <a name="margins"></a>
FlexLayout ....

**Methods:**

* `marginTop(_ value: CGFloat)`
* `marginLeft(_ value: CGFloat)`
* `marginBottom(_ value: CGFloat)`
* `marginRight(_ value: CGFloat)`
* `marginHorizontal(_ value: CGFloat)`
* `marginVertical(_ value: CGFloat)`
* `margin(_ value: CGFloat) `
* `margin(_ vertical: CGFloat, _ horizontal: CGFloat)`
* `margin(_ top: CGFloat, _ horizontal: CGFloat, _ bottom: CGFloat)`
* `margin(_ top: CGFloat, _ right: CGFloat, _ bottom: CGFloat, _ left: CGFloat) `

###### Usage examples:
```swift
	view.flexbox.margin(20)
	view.flexbox.marginBottom(20)
	view.flexbox.marginHorizontal(20)
	view.flexbox.margin(10, 12, 0, 12)
```

<br>

## 7. Paddings <a name="paddings"></a>
FlexLayout ...

**Methods:**

* `paddingTop(_ value: CGFloat)`
* `paddingLeft(_ value: CGFloat)`
* `paddingBottom(_ value: CGFloat)`
* `paddingRight(_ value: CGFloat)`
* `paddingHorizontal(_ value: CGFloat)`
* `paddingVertical(_ value: CGFloat)`
* `padding(_ value: CGFloat) `
* `padding(_ vertical: CGFloat, _ horizontal: CGFloat)`
* `padding(_ top: CGFloat, _ horizontal: CGFloat, _ bottom: CGFloat)`
* `padding(_ top: CGFloat, _ right: CGFloat, _ bottom: CGFloat, _ left: CGFloat) `


###### Usage examples:
```swift
	view.flexbox.padding(20)
	view.flexbox.paddingBottom(20)
	view.flexbox.paddingHorizontal(20)
	view.flexbox.padding(10, 12, 0, 12)
```

<br>

## 8. Borders <a name="borders"></a>

**Methods:**

* `borderLeft(_ value: CGFloat)`
* `borderTop(_ value: CGFloat)`
* `borderRight(_ value: CGFloat)`
* `borderBottom(_ value: CGFloat)`
* `borderStart(_ value: CGFloat)`
* `borderEnd(_ value: CGFloat)`
* `border(_ value: CGFloat)`

<br>


# FlexLayout's Performance <a name="performance"></a>

FlexLayout's performance has been measured using the nice and simple LayoutKit benchmark. FlexLayout has been added to this benchmark to compare its performance. 

As you can see in the following chart, FlexLayout's performance is as fast as manual layouting, and **between 12x and 16x faster than auto layout**, for all types of iPhone (5/6/6S/7)

These results also means that ** FlexLayout is faster than any layout frameworks that is built over auto layout**. 

[See here more complete details, results and explanation of the benchmark](docs/Benchmark.md).

<p align="center"> Tested on a iPhone 6 iOS 10.3.2</p>
<p align="center">
  <img src="docs/Benchmark/Benchmark-iPhone6.png" alt="FlexLayout Performance" width=600/>
</p>


<br/>

# Links
* THE flexbox CSS reference: [A Complete Guide to Flexbox](https://css-tricks.com/snippets/css/a-guide-to-flexbox/)
* [Interresting Yoga tutorial](https://www.raywenderlich.com/161413/yoga-tutorial-using-cross-platform-layout-engine?utm_source=raywenderlich.com+Weekly&utm_campaign=e7e557ef6a-raywenderlich_com_Weekly_Issue_125&utm_medium=email&utm_term=0_83b6edc87f-e7e557ef6a-414921561)
* https://yoksel.github.io/flex-cheatsheet
* https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Flexible_Box_Layout/Advanced_layouts_with_flexbox

# Installation <a name="installation"></a>

### CocoaPods

To integrate FlexLayout into your Xcode project using CocoaPods, specify it in your `Podfile`:

```ruby
    pod 'FlexLayout'
```

Then, run `pod install`.

<br/>

### Contributing, comments, ideas, suggestions, issues, .... <a name="comments"></a>
For any **comments**, **ideas**, **suggestions**, simply open an [issue](https://github.com/luc-dion/FlexLayout/issues). 

For **issues**, please have a look at [Yoga's issues](https://github.com/facebook/yoga/issues). Your issue may have been already reported. If not, it may be a FlexLayout issue. In this case open an issue and we'll let you know if the issue is related to Yoga's implementation. 

If you find FlexLayout interresting, thanks to **Star** it. You'll be able to retrieve it easily later.

If you'd like to contribute, you're welcome!

<br>


## Changelog
FlexLayout recent history is available in the are documented in the [CHANGELOG](CHANGELOG.md).

<br>

## License
BSD 3-Clause License