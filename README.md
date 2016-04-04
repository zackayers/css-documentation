# css-documentation

# **CSS**

**[Philosophy](#markdown-header-philosophy)** | **[Syntax](#markdown-header-syntax)** | **[File Structure](#markdown-header-file-structure)** | **[Standards](#markdown-header-standards)**

--------------------------------------------------------------------------------

# Philosophy

* **Embrace the Cascade** – Don't fight the natural cascading of styles to child elements; contextualize what needs contextualizing, but build upon inheritance.
* **Be Only As Specific as Necessary** – Limit specificity to only what's required, and nothing more.
* **Always Style the Basic Elements** – Determine what the core styles are, and make sure those elements are styled without context-specificty. Especially important for sites with a WYSIWYG admin component.
* **Limit to 4 Levels of Nesting** – Anything more than 4 levels is likely more specific than necessary.
* **Utilize Preprocessors** – Variables, mixins, color functions, nesting, and partials make CSS much more maintainable.

--------------------------------------------------------------------------------

# Syntax

## New Lines

Use new lines to keep code readable, and make finding selectors and copying and pasting properties easier.

* Every rule on its own line
* Multiple selectors on their own lines
* Closing curly brace on its own line, at same level as selectors

Good:
```css
.thing,
.other-thing {
	background: blue;
	color: red;
}
```

Bad:
```css
.thing, .other-thing {
	background: blue; color: red; }
```


## Ordering

* Keep all element rules before nested children to keep properties close to their selector
* Group like-properties when possible (e.g. margin and padding, position and top/left/bottom/right, font-weight/size/style) for readability and easier future editing

Good:
```sass
.thing {
	position: absolute;
	top: 7px;
	left: 0;
	background: red;
	font-weight: bold;
	font-style: italic;

	.thing-child {
		/* ... */
	}
}
```

Bad:
```sass
.thing {
	top: 7px;
	font-weight: bold;
	background: red;

	.thing-child {
		/* ... */
	}

	left: 0;
	font-style: italic;
	position: absolute;
}
```


## Shorthand Properties

Use shorthand properties only when you need to set or reset *all* values of a property.

Avoid shorthand that repeats all properties of a child or variant class:
```sass
.news-item {
	margin: 1em 0;

	&.has-icon {
		// Bad:
		margin: 1em 0 1em 5em;

		// Good:
		margin-left: 5em;
	}
}
```

Be mindful of optional properties that might be unintentionally reset by shorthand:

```sass
.cool-hero {
	background-color: #000;
}
/* ... */
.some-page {
	.cool-hero {
		background: url( images/my-image.jpg ); // background-color is now transparent
	}
}
```

## Comments

Make sure your future self knows what the heck your current self was thinking when you made that `z-index: 743`. But, future-you knows how to read code too.

> The code should tell you how, the comments should tell you why

Bad:
```sass
.thing {
	color: blue; // Make it blue
	font-weight: bold; // Bold
	z-index: 743;
}
```

Good:
```sass
.thing {
	color: blue;
	font-weight: bold;
	z-index: 743; // Tuck between main slider at 740, and overlay at 745
}
```

### Block Comments

Organize single files into logical sections, and separate them with a block comment.

* 80-character width
* All-caps for text
* Text lines up with 4-space tab
* Exclamation point creates a "bookmark" in Coda

```sass
// -----------------------------------------------------------------------------
//! BLOCK COMMENT
// -----------------------------------------------------------------------------
```

### Inline Comments

Use inline comments above a rule or after a property value, depending on its relevance.

```sass
// Size variant
.size-large {
	font-size: 1.2em;
	font-weight: normal; // Larger size antialiases too bold
}
```

## Media Queries and Responsive Mixins


Nest responsive rules where they are most relevant, while avoiding repetition. Media queries (or a responsive mixin) should go at the end of all other properties and selectors at a given nesting level.

Good:
```sass
.thing {
	font-weight: bold;

	.thing-child {
		font-style: italic;
	}

	@media screen and (max-width: 900px) {
		// Responsive stuff

		.thing-child {
			// More responsive stuff
		}
	}
}
```

Bad:
```sass
.thing {
	@media screen and (max-width: 900px) {
		// Responsive stuff
	}

	.thing-child {
		@media screen and (max-width: 900px) {
			// More responsive stuff
		}

		font-style: italic;
	}

	font-weight: bold;
}
```

--------------------------------------------------------------------------------

# File Structure

To keep things tidy, break out like-styles into partial files and `@import` them into a main file. Think of the main file as purely a table of contents.


## Multiple Processed Stylesheets

When dealing with multiple pre-processed stylesheets (e.g. WordPress theme, WordPress admin color scheme, and WordPress TinyMCE editor), store variables and shared core styles in their own partials and `@import` them into each base file versus redeclaring them in each file.


--------------------------------------------------------------------------------

# Standards

## SCSS for Preprocessing


Use SCSS for CSS preprocessing, unless an existing environment is already using LESS.

A few articles outlining the reasoning:

* [SASS vs. LESS](https://css-tricks.com/sass-vs-less/) _CSS Tricks_
* [Less vs Sass? It's time to switch to Sass](http://www.zingdesign.com/less-vs-sass-its-time-to-switch-to-sass/) _Zing Design_
* [SASS vs LESS](http://www.hongkiat.com/blog/sass-vs-less/) _Hongkiat_


## Name Things for Reuse

* Be descriptive – not prescriptive – to keep the focus on modularization
* Points for clever and memorable but not at the expense of description
* Context-dependent children should inherit the parent's naming as a prefix to contextualize context-specific classes and reduce specificity

Example:
```sass
.thing { }
.thing-heading { }
.thing-heading-button { }
```

### Element States

The dynamic nature and interactivity of the web means things are constantly changing states. Active, inactive; open, closed; expanded, collapsed.

Use the `.is-` prefix to style specific states of an element.

Example:
```sass
.thing {
	display: none;

	&.is-expanded {
		display: block;
	}
}
```


## Recognize Variations

When thinking modularly, think about what aspects of a thing might be (or become) variable, and style them as such. This makes future development and codebase expansion easier and more efficient.

Example:
```sass
/* It's always padded nicely, and bold... */
.button {
	display: inline-block;
	padding: 0.5em 1em;
	font-weight: bold;
}
/* ...but sometimes it's red... */
.button-red {
	background: red;
	color: white;
}
/* ...and sometimes blue. */
.button-blue {
	background: lightblue;
	color: darkblue;
}
```

Some variations have specific properties that vary among several options. If these are easily repeatable, throw them into a `@mixin` and get the added benefit of preprocessing functionality.

Example:
```sass
@mixin button-color($background-color, $text-color) {
	background: $background-color;
	color: $text-color;
	border: 1px solid darken($background-color, 5%);
	/* No need for a color picker, just make the border 5% darker than the background */
}

.button-red {
	@include button-color(#900, #FFF);
}

.button-blue {
	@include button-color(#69F, #036);
}
```


## Never Use `!important`

Think long and hard before using `!important` and be prepared to defend your decision. It can easily wreak havoc on a codebase, and make debugging CSS issues a nightmare. Once there's one `!important`, you'll inevitably have to add more.


## Inherit Box Sizing

When resetting the box model to the more useful `border-box`, set it generically on the root element, and declare inheritance on all children. This adds flexibility to use an alternate box model on an element, and have that model cascade to its children.

```sass
html {
	box-sizing: border-box;

	*,
	*:before,
	*:after {
		box-sizing: inherit;
	}
}
```

## Style the Basic Elements First

At its core, the CSS should be styling the _content_. The awesome things you do with the markup to turn the content into a _website_ should be the exceptions to the rule, not the rule itself.

This is even more important when building for WYSIWYG editors. Make sure all of the buttons clients can click in the editor do something meaningful to the styling.

Bad:
```sass
ul {
	list-style: none;
	margin: 0;
	padding: 0;
	/* Yay, now my nav menu doesn't need this! */
}
.page-content ul {
	list-style: disc;
	margin: 1em 0;
	padding: 0 0 0 1em;
	/* Oh. Now I'll undo that initial reset so these lists actually look like lists. And remember to put a .page-content div on every page where I want lists to look like lists. */
}
```

Good:
```sass
ul {
	list-style: disc;
	margin: 1em 0;
	padding: 0 0 0 1em;
	/* Lists are lists */
}
.nav-menu {
	list-style: none;
	margin: 0;
	padding: 0;
	/* The thing that's not really a list gets the special reset styling */
}
```


## Keep Key Selectors Specific

The more general your key selector, the more likely it is that future code changes will be erroneously affected. Generic elements (e.g. `<div>`, `<span>`) should never be key selectors.

Bad:
```css
.thing span {
	color: orange;
	/* Is it orange because it's a span? */
}
```

Good:
```css
.thing-accent {
	color: orange;
	/* Or is it orange because it's an accent in .thing? */
}
```


## Avoid Unnecessary Nesting

The more specific your key selector, the more likely it is that it can stand on its own. If it can't, only style the context-dependent properties.

Bad:
```sass
.thing {
	.thing-heading {
		.thing-heading-accent {
			/* ... */
		}
	}
}
```

Good
```css
.thing { }
.thing-heading { }
.thing-accent { }
```


## Don't Use IDs

Think _hard_ before using an ID in your selector. It almost always breaks the cascade and forces extraneous specificity on everything inside it.

Example:
```html
<div id="modal">
	<div class="cool-p-style">
		<p>Lorem ipsum dolor</p>
	</div>
</div>
```
```css
.cool-p-style p {
	font-size: 1.2em;
	color: green;
}

#modal p {
	font-size: 1.5em;
	color: red;

	/* Good luck having this be green and 1.2em */
}
```


## Mind the Depth

Try to keep things no more than 4 levels deep, including pseudo-selectors (`:before`, `:hover`, etc). Keeping your key selectors specific will help with this.

Good:
```css
.nav-menu {
	/* ... */
}
.nav-menu-item {
	/* ... */
}
.nav-menu-dropdown {
	/* ... */
}
```
Bad:
```sass
.nav-menu {
	> li {
		> a {
			&:hover {
				/* ... */
			}
		}
	}

	ul {
		li {
			a {
				&:hover {
					/* ... */
				}
			}
		}

		ul {
			li {
				a {
					&:hover {
						/* Where am I? Who are you? */
					}
				}
			}
		}
	}
}
```
