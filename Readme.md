# Sass styleguide

* Opinionated sass / css style guide for teams

Managging css using sass or any pseudo compiling language can be brittle, leads to css bloat or specifity issues.
If you're looking for an opiniationted surely effective way to manage your css for an application  here's the full guide

When you write a module using any methodology, the key thing is to make code as predictable as possible, avoid surprising the next developer who's going to use it (wtf moments)

## Table of contents

  1. [Code management](#code-management)
  1. [Folder structure](#folder-structure)
  1. [Formatting](#Formatting)
  1. [Name spacing](#namespacing)
  1. [Variables](#naming-variable)
  1. [Naming your selectors](#naming-your-selectors)
  1. [Color palette](#color-palette)
  1. [Nesting](#nesting)
  1. [Writing key value pairs](#writing-key-value-pairs)
  1. [Naming your mixins](#naming-your-mixins)
  1. [Generate your rtl css](#rtl)
  1. [References](#references)

## Code management

When it comes to writing code, always try to be disciplined to avoid ending up with a [CSS bloat](http://www.slideshare.net/stubbornella/css-bloat) and other issues, be kind to your future self and your team.

- If something is not used, remove it
- Separate your quick fixes and hacks from the stable code
- Make sure you separate CSS dependencies from the code you are writing
- Avoid performance bottlenecks like deep nesting and duplication
- Use meaningful names for classes and other selectors
- Keep track of the bugs and improvements of the code, use an [issue tracker](https://github.com/mystratallc/sw4/issues)

## Folder structure

For the sake of separating concerns, it's recommended to kick start with a folder structure that make sense, always keep in mind that this is not dictated in any shape or form but when it comes to file naming better stick to a naming pattern like {a-z}-{a-z}.sass for compiling files and _{a-z}-{a-z}.sass for non compiling SASS files (partials).

The following tree is a waterfall model for generating a single CSS output file

```bash
└── sass
    ├── _config.sass
    ├── _base.sass
    ├── _branding.sass
    ├── _shame.sass
    ├── components
    └── mixins
        └── _placeholder-reset.sass
        └── _media-queries.sass
        └── _rems2pixels.sass
    └── modules
    └── vendors
        └── _normalize.sass
└── css
     └── sw4-ltr.css
     └── sw4-rtl.css
```

* Project configuration

`_config.sass` is the SASS project configuration file that have information like paths ect..

* Branding

`_branding.sass` is where the branding related information lives like color palettes and fonts.
Better be consistent and avoid code repetition

* Mixins

`/mixins` is where the SASS mixins live, it's better to keep all the mixins in a separate and a single
folder instead of creating them next to your modules.

* Quick fixes

In the perfect world, there is no quick fixes, but unfortunately from time to time we are going to need to add them
`_shame.sass` is a partial for quick fixes and hacks, the usage of this file should be moderated, if you
have not enough time for writing the CSS fix or the hack you can think about the solution later but most importantly get the feature out of the door but make sure you add a comment explaining to the next person what the hack is doing.

* The base file

The reason why `_base.sass` exist is to import every other file as a partial then compile it to different css files.

Other solutions exists like flipping the files using a [gulp](http://gulpjs.com/) [task](https://www.npmjs.com/package/gulp-rtlcss) but if gulp is not part of the code base then we can use this strategy.

* Final files

`sw4-*.css` are the final CSS files (English and Arabic)

## Formatting

To avoid headeaches with different styling preferences, choose to write .sass files not the sass .scss format.
It dictates the way people write code

You might want to use .editorconfig in your favorite editor to help with a consistent formatting across the code base.
[editor config](http://editorconfig.org/)

## Namespacing

Prefixing the entire CSS code will help in debugging and mostly to avoid specificity issues (clashing with third-party libraries).
It's recommended to wrap the code by a [single name space per project](https://gabri.me/blog/global-scope-namespacing-css).
In the end you will be able to identify easily your overrides and your code from those libraries.

[Specifics on css specificity by css-tricks](http://css-tricks.com/specifics-on-css-specificity/)

```sass
// Namespacing the project modules (assuming being written in a config partial)
$ns: 'az-'
// naming the module dynamically
$module: '#{$ns}-forms'

// Module is CSS class
.$module
    border: 1px solid $color--gray
    margin-top: 10px
    ....
```
Output to
```css
.az-forms {
    border: 1px solid #EBECED;
    margin-top: 10px;
    }
```

## Variables

Always in lower cases and separated by dash avoid using underscore

```sass
// Bad
$myVariable: red
// Bad
$MY-VARIABLE: red
// Bad
$my_variable: red
// Good
$my-variable: red
// Bad
$color--Red:
// Good
$color--red: red

```

## Naming your selectors
When you want to choose a selector first of all
- Avoid using ID's, you shouldn't style anything with an ID selector. (if your .js files uses those slectors or you emplemented achors, good luck in refractoring your code)
- Stick to the name convention  .selector-name

*Why avoiding id ?*
Because you don't want to css bloat, you want to reuse the styles as much as you can.
Id's are meant to be used once per page, id's will tightly couple the css with the application business logic, the next maintainer wouldn't be able to change that confidently unless he make sure he won't remove anything else with the css.

Naming of the selectors need to be consistent and meaningful, you can follow these methodologies below that resolves the naming conventions, this is very useful for bug tracking and feature bug free where you avoid overrides and clashes with other CSS files.

* [SMACSS approach](https://smacss.com/)
* [BEM approach](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)

Beside these methodologies, the [sassy way](http://thesassway.com/advanced/modular-css-naming-conventions) is a good naming convention for big projects

## Color palette

Never use color codes directly within your code, always define your color in file and keep reusing them as variables

exp: _color.sass

```sass

// Blue
$color--diamond: #412453

// Gray scale
$color--bleech: #412453
$color--cloud: #ebeced

```

Within _toolbar.sass

```sass
@import _colors
.toolbar
	  background-color: $color--bleach
```

## Nesting

Avoid deep level nesting, 3 levels might be okay, more nesting will put the browser on overkill mode.
[How CSS gets evaluated](https://smacss.com/book/selectors#evaluated)

A cool technique to avoid that is to consider using the [Block element modifier approach](http://csswizardry.com/2013/01/mindbemding-getting-your-head-round-bem-syntax/)


* BEM and the @root level sass

Sass offers the opportunity to write clean modular code using the @root level in conjunction with one of the methodologies (SMACSS or BEM), it help to write flat CSS.

Example without the @root level feature

```sass
.module
    .element
        .modifier
            ...
// CSS output
.module >.element >.modifier {
    .....
    }
```

Example with the @root level feature

```sass
// General syntax BEM
.module
    &__element
        &--modifier

// CSS output
.module__element--modifier
    ...
```
The downside of this, global search became hard, cause the class name is generated based on a parent namespace, so you might be careful
with the namings.


## Naming your mixins
you can use a shorter version for naming your mixins

```sass
// default version
@mixin calculateSomething($a, $b)
    $result  = $a / $b + 'px'

// Short version
=calculateSomething($a, $b)
    $result  = $a / $b + 'px'

// Long version
@include calculateSomething(20, 30)

// Short version
+calculateSomething(20, 30)
```

## Writing key value pairs

- when writing nested classes, always write the parent key value proprieties first then write the sibling classes and the rest of the code

```sass
// Discouraged
.module
    .my-class
        border-radius: 2px
        border: 1px solid $color--gray
    background-color: $color--white
    ....

// Recommended
.module
     background-color: $color--white // key value pair of .module
     ....
    .my-class
        border-radius: 2px
        border: 1px solid $color--gray
```
**Why?**: Because when you have so many nested classes media queries and so on, it became very difficult to read and figure out which key value pair attributes belong to which class

- Always try to mention overrides and when something will is pulled out from the code then it's override need to be pulled out too

```sass
// Good example
.module
   margin-left: 10px
   .element
        margin: 0 //override inherited margin from .module
```
**Why?**: Because if you leave overrides it will be difficult to figure out and remember if you really need those key values proprieties pair.

- When you write a class, try to sort all key value proprieties pairs (F9 on Sublime/linux and F5 on Sublime/mac) by alphabetical order
```sass
.module
    // Not sorted by alphabetical order
    padding: 10px
    border: 1px solid $color--gray
    font-size: 12px
    color: $color--blue
    border-radius: 4px
    margin: 10px

.module
    // Sorted proprieties key value pairs
    border-radius: 4px
    border: 1px solid $color--gray
    color: $color--blue
    font-size: 12px
    margin: 10px
    padding: 10px
```
**Why?**: It's easier to read and useful to avoid duplication from merges and code conflict resolutions.


## RTL

Use a configuration file for ltr / rlt then generate always the rtl file based on the ltr.
Never attempt to write twice as much the css for this.

Your structure should look like:

``bash
└── sass
    ├── _ltr-settings.sass
    ├── _rtl-settings.sass
    ├── layout-rtl.sass
    ├── layout-ltr.sass
    └── _layout.sass
```

Within the `_ltr-settings.sass`

```sass
// Considered being 'left'
$leading: left !default
// trailing is considered as 'right'
$trailing: right !default
// direction is left to right by default
$dir: ltr !default
```
Within the `_rtl-settings.sass`

```sass
// Considered being 'right'
$leading: right !default
// trailing is considered as 'left'
$trailing: left !default
// direction is left to right by default
$dir: rtl !default
```

Your main file `_layout.sass` (you only maintain once both ltr/rtl)
You're going to generate two files based on a single file
The configuration is going to cascade

```sass
$ns-#{$container}
  // generating left
	float: $leading
	margin-#{$leading}: 10px
	margin-#{$trailing}: 10px
```

In `_layout-rtl.sass`
```sass
@import _ltr-settings
@import _layout
```

In `_layout-rtl.sass`

```sass
@import _rtl-settings
@import _layout
```


## References
[OOCSS](http://oocss.org/)

[SASS](http://sass-lang.com/)

[SMACSS](https://smacss.com/)

[CSS specificity](http://cssspecificity.com/)

[Naming conventions less names more fun](https://medium.com/@drublic/css-naming-conventions-less-rules-more-fun-12af220e949b)
