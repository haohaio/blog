---
title: 'Flutter 布局组件简介'
entitle: flutter-layout-widgets
author: haohaio
avatar: /images/favicon.png
authorLink: 'https://haohaio.github.io'
authorAbout: 'https://haohaio.github.io'
authorDesc: 写写代码，记记笔记
categories: 前端
timestamp: 1559011391
comments: true
date: 2019-05-28 10:43:11
tags:
- Flutter
keywords: Flutter
description: Flutter 布局组件简介
photos:
- /img/tags/flutter.png
---

Flutter 是使用 Layout Widgets 来进行布局的，今天我们就来了解一下相关的布局组件。

## Single-child layout widgets

### Container

> A convenience widget that combines common painting, positioning, and sizing widgets.

#### 属性

- key：container 唯一标识符，用于查找更新。
- alignment：控制 child 的对齐方式，如果 container 或者 container 父节点尺寸大于 child 的尺寸，这个属性设置会起作用，有很多种对齐方式。
- padding：内边距。
- color：用来设置 container 背景色。
- decoration：绘制在child后面的装饰，设置了decoration的话，就不能设置color属性，否则会报错，此时应该在 decoration 中进行颜色的设置。
- foregroundDecoration：绘制在child前面的装饰。
- width：container 的宽度，设置为 `double.infinity` 可以强制在宽度上撑满。不设置，则根据 child 和父节点两者一起布局。
- height：container 的高度，设置为 `double.infinity` 可以强制在高度上撑满。
- constraints：添加到 child 上额外的约束条件。
- margin：外边距。
- transform：设置 container 的变换矩阵，类型为 Matrix4。
- child：container 中的内容 widget。

我们可以用 Container 来设置间隔、背景、边框、圆角、对齐等。

### Padding

> A widget that insets its child by the given padding.

我们可以用 Padding 来设置间隔。（Container 中 的 margin 以及 padding 属性都是利用 Padding 控件去实现的。）

### Align

#### 属性

- key
- alignment：对齐方式
- widthFactor：child 的宽度倍数
- heightFactor：child 的高度倍数
- child

> A widget that aligns its child within itself and optionally sizes itself based on the child's size.

我们可以用 Align 来设置对齐。若不设 widthFactor 和 heightFactor, Align 会尽量的扩展自己的尺寸。

### Center

> A widget that centers its child within itself.

Center 继承自 Align，只不过是将 alignment 设置为 Alignment.center，其他属性例如 widthFactor、heightFactor，布局行为，都与 Align 完全一样。

### FittedBox

> Scales and positions its child within itself according to fit.

我们可以用FittedBox来处理缩放。FittedBox 如果外部有约束的话，按照外部约束调整自身尺寸。如果没有外部约束条件，则跟 child 尺寸一致，指定的缩放以及位置属性将不起作用。

### AspectRatio

> A widget that attempts to size the child to a specific aspect ratio.

AspectRatio 的作用是调整 child 到设置的宽高比。AspectRatio首先会在布局限制条件允许的范围内尽可能的扩展，widget的高度是由宽度和比率决定的，类似于BoxFit中的contain，按照固定比率去尽量占满区域。如果在满足所有限制条件过后无法找到一个可行的尺寸，AspectRatio最终将会去优先适应布局限制条件，而忽略所设置的比率。

### ConstrainedBox

> A widget that imposes additional constraints on its child.

ConstrainedBox 的 作用就是给 child 添加约束（constraints）。

### Baseline

> A widget that positions its child according to the child's baseline.

我们可以用 Baseline 来处理字符对齐。

### FractionallySizedBox

> A widget that sizes its child to a fraction of the total available space. For more details about the layout algorithm, see RenderFractionallySizedOverflowBox.

child 可以通过 FractionallySizedBox 在其可用区域里取百分比尺寸。

### IntrinsicHeight

> A widget that sizes its child to the child's intrinsic height.

IntrinsicHeight 的作用是调整 child 到固定的高度。

### IntrinsicWidth

> A widget that sizes its child to the child's intrinsic width.

IntrinsicWidth 的作用是调整 child 到固定的宽度。

### LimitedBox

> A box that limits its size only when it's unconstrained.

LimitedBox 的作用是对 child 的最大宽高进行限制。

### Offstage

> A widget that lays the child out as if it was in the tree, but without painting anything, without making the child available for hit testing, and without taking any room in the parent.

Offstage 的作用是通过一个参数，来控制 child 是否显示。

### OverflowBox

> A widget that imposes different constraints on its child than it gets from its parent, possibly allowing the child to overflow the parent.

OverflowBox 的作用是允许 child 超出 parent 的范围显示。

### SizedBox

> A box with a specified size.

SizedBox 的作用就是设置具体的宽高。

### SizedOverflowBox

> A widget that is a specific size but passes its original constraints through to its child, which will probably overflow.

SizedOverflowBox 是 SizedBox 与 OverflowBox 的结合体。

### Transform

> A widget that applies a transformation before painting its child.

Transform 就是对 child 做矩阵变换的，即平移、旋转、缩放等操作。

### CustomSingleChildLayout

> A widget that defers the layout of its single child to a delegate.

CustomSingleChildLayout提供了一个控制child布局的delegate，我们可以通过这个控件去封装一些基础的控件（尺寸），供他人使用。

## Multi-child layout widgets

### Row & Column

> A widget that displays its children in a horizontal array.

类似于 CSS3 中的 Flex 布局。可结合 Expanded 使用，设置 children 中各 widget 的 flex 值。

### Stack

> A widget that positions its children relative to the edges of its box.

我们可以用 Stack 来做需要叠加显示的布局。

### IndexedStack

> A Stack that shows a single child from a list of children.

IndexedStack 继承自 Stack，它的作用是显示第 index 个 child，其他 child 都是不可见的。所以 IndexedStack 的尺寸永远是跟最大的子节点尺寸一致。

### GridView

> A scrollable, 2D array of widgets.

GridView 就是一个滚动的多列列表。

### Flow

> A widget that implements the flow layout algorithm.

Flow 可以用来在定制化流式布局，但是写起来比较复杂，但胜在灵活性以及其高效。

### Table

> A widget that uses the table layout algorithm for its children.

Table 可以用来做表格展示。

### Wrap

> A widget that displays its children in multiple horizontal or vertical runs.

Wrap 可以用来处理自动换行的布局。Flow 可以很轻易的实现 Wrap 的效果，从效率上讲，Flow 肯定会比 Wrap 高，但是 Wrap 使用起来会方便一些。

### ListBody

> A widget that arranges its children sequentially along a given axis.

ListBody 的作用是按给定的轴方向，按照顺序排列子节点。不常直接使用，一般都会配合 ListView 或者 Column 等控件使用

### CustomMultiChildLayout

> A widget that uses a delegate to size and position multiple children.

CustomMultiChildLayout 的作用是通过 delegate 实现 多节点的自定义布局（尺寸、位置）。

### LayoutBuilder

> Builds a widget tree that can depend on the parent widget's size.

### ListView

> A scrollable, linear list of widgets.

ListView 可以用来做列表展示。

### Expanded

> A widget that expands a child of a Row, Column, or Flex.

配合 Row & Column 使用，设置 child 的 flex 值。