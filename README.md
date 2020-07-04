## Overview

PHP Remove Unused CSS is a tool to remove unused CSS from your website using PHP. Developed by Momentum 81 - https://momentum81.com.

```diff
- IMPORTANT! MAKE SURE YOU READ THE GUIDE FIRST OR YOU MAY INADVERTENTLY OVERWRITE YOUR CSS
```

The main idea is to first compile your CSS into as few files as possible, then you would remove the extra classes using this package, then use this (or another) package to minify the CSS.

Often this is done with JS however that can raise issues if you want to work this into a pure PHP development flow.

## Installation

Installation via composer:

```
composer require momentum81/php-remove-unused-css
```

## Example

``` php
$removeUnusedCss = new \Momentum81\PhpRemoveUnusedCss\RemoveUnusedCssBasic();

$removeUnusedCss->whitelist('.fab', '.far', '.fal')
    ->styleSheets(public_path('**/*.css'))
    ->htmlFiles(resource_path('**/*.blade.php'))
    ->setFilenameSuffix('.refactored.min')
    ->minify()
    ->refactor()
    ->saveFiles();
```

## Classes

The are two main ways of using the package:

* Basic
* Complete (In Development, not yet available)

### Basic Class

The basic class is created using `RemoveUnusedCssBasic`. This is essentially a 'dumb' system that won't traverse the DOM in any way and will just include a selector if it's lowest level appears in the CSS.

That said, this can still provide some significant savings in file size, especially when you're using a package like Bootstrap.

``` php
$removeUnusedCss = new \Momentum81\PhpRemoveUnusedCss\RemoveUnusedCssBasic();
```

The basic class only weakly matches, lets look at the following HTML:

```html
<div>
    <span class="hello">Hello World</span>
</div>
```

The following CSS Classes would match and be kept, despite the `.hello` class being used in the HTML not being inside a parent element using the class `.test`:

```css
.test .hello {}
.test .hello::after {}
```

### Complete Class

In Development. This method attempts to be smarter and where possible traverse the DOM as much as it can (When using a templating system this is infinitely more difficult if your views are not cached, so the system can only do so well here).


### Available Methods

| Method | Description |
| :--- | :--- |
| `whitelist(...$selectors)` | Here you can provice multipleCSS selectors to whitelist, ensuring they remain in the CSS even if they are not present in the HTML. |
| `styleSheets(...$styleSheets)` | Here you can provide glob compatible absolute paths to the stylesheets you want to refactor. For example in Laravel, you could use `public_path('**/*.css')`. |
| `htmlFiles(...$htmlFiles)` | Here you can provide glob compatible absolute paths to the HTML files (Can be any text file type, not just `.html`) that you want to scan for selectors to keep in your refactored CSS. For example in Laravel, you could use `resource_path('**/*.blade.php')`. |
| `setFilenameSuffix($string)` | By default the platform will overwrite the stylesheets it finds (When saving as files), here you can provide a suffix for the file name - this will get appended before the file type. So `stylesheet.css` could become `stylesheet.refactored.min.css`. The default value is `.refactored.min`. IMPORTANT: If you do not specify this method, your original files will be OVERWRITTEN if you use `saveFiles()`! |
| `minify($bool)` | If specified, this will perform minification on the CSS before it's saved/returned, using the package `matthiasmullie/minify` (https://github.com/matthiasmullie/minify) |
| `skipComment($bool)` | Specify if we should skip the comment at the top of the CSS file |
| `comment($string)` | Specify custom text for the comment at the top of the CSS file - use the variable `:time:` for the date and time to be added in its place. Don't forget to add your opening and closing tags for the comment: `/* and */`. |
| `refactor()` | This method performs the refactoring. |
| `saveFiles()` | This will save the new (Or overwritten) files |
| `returnAsText()` | This will return the refactored CSS as an array of files with text rather than writing them to real files. |
