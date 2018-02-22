o-brand [![Circle CI](https://circleci.com/gh/Financial-Times/o-brand/tree/master.svg?style=svg)](https://circleci.com/gh/Financial-Times/o-brand/tree/master)
=================

Tools to "brand" Origami components. `o-brand` is intended as an internal tool and **must not** be depended on directly outside of Origami components.

- [Terms](#terms)
- [Usage](#usage)
	- [Sass](#sass)
- [Contact](#contact)
- [Licence](#licence)

## Terms

### Brand

A brand is a collection of configuration to tailor a component for specifc usecases. A branded Origami component is able to output the correct visual style and features for a requested brand.

Examples of brands include:

- Master: FT branding for public ft.com sites and affiliates.
- Internal: A minimal style with subtle branding for internal products, tools, and documentation.
- Whitelabel: Base, structural styles only.

### Variants

A branded component may contain a number of variants e.g. a button component may provide a "big" and "small" variant. Variants may modify the appearance or functionality of a component. They may override default brand styles, e.g. change the colour of a button. Variants may also require new markup e.g. to add a breadcrumb to a header component. Variants must be optional and build upon a fully functional branded component.

E.g.
Brand variants modify a fully functional foundation.
```scss
	.o-example {
		content: 'component';
	}
```
```html
	<div class="o-example"></div>
```
Variants may override styles.
```scss
	.o-example--inverse {
		content: 'example variant "inverse"';
	}
```
```html
	<div class="o-example o-example--inverse"></div>
```

Variants may require additional markup.
```scss
	.o-example__extra {
		content: 'example feature variant "extra"';
	}
```
```html
	<div class="o-example o-example--inverse">
		<div class="o-example__extra"></div>
	</div>
```

## Usage

Mixins within `o-brand` help configure components to support brands. There is no configuration in `o-brand`. It provides the mechanisms for components to apply their own brand support.

The following mixins and functions are provided to brand a component.

- oBrandDefine
- oBrandConfigureFor
- oBrandGet

### Defining Brand Configuration

A component must first define the configuration for its supported brands. To do that use the mixin `oBrandDefine`. The default brand `master` must be defined.

To define a component brand pass a `variables` and `settings` map. Variables contain key/value pairs which can be retrieved later. Variant variables are nested within the `variables` map.

Settings indicate what variants should be supported by the brand.

The below shows defining a brand `master` for the component `o-example`. We define a default variables `component-content` which is different for variant `inverse` and compound variant `inverse demo`. We indicate the `master` brand supports these two variants in the `settings` map.

```scss
@include oBrandDefine('o-example', 'master', (
    'variables': (
        component-content: 'default value',
        'inverse': (demo
            component-content: '"inverse" variant value'
        ),
        ('inverse', 'demo'): (
            component-content: '"inverse " variant value'
        )
    ),
    'settings': (
        'demo': true,
        'inverse': true,
        'extra': true
    )
));
```

### Using Brand Configuration

### Retrieve A Variable

To retrieve a variable previousy defined for a brand use the function `oBrandGet`. This gets the value for the requested component variable.

Building on the "define" example above:
```scss
.o-example {
	content: oBrandGet('o-example', 'component-content'); // default value
}
```

### Retrieve A Variable For A Variant

To retrieve a variable for a variant use `oBrandConfigureFor`. This mixin accepts a content block and variant. This indicates styles within the content block are intended for the passed variant.

Building on the "define" example above:
```scss
@include oBrandConfigureFor('o-example', 'inverse') {
	.o-example--inverse {
		content: oBrandGet('o-example', 'component-content'); // "inverse" variant value
	}
}
```

To retrieve a variable for a compound variant pass a list:
```scss
@include oBrandConfigureFor('o-example', ('inverse', 'demo')) {
	.o-example--inverse.o-example--demo {
		content: oBrandGet('o-example', 'component-content'); // "inverse demo" variant value
	}
}
```

Nesting is also supported:
```scss
@include oBrandConfigureFor('o-example', 'inverse') {
	.o-example--inverse.o-example--demo {
		@include oBrandConfigureFor('o-example', 'demo') {
			content: oBrandGet('o-example', 'component-content'); // "inverse demo" variant value
		}
	}
}
```

### Output Styles Only If A Brand Supports A Variant

Not all brands will share variants. Support is defined in the `settings` map as demonstrated above. To only output styles if the variant is supported wrap variant styles in `oBrandConfigureFor`.

```scss
@include oBrandConfigureFor('o-example', 'b2b') {
	.o-example--b2b {
		// The brand defined above does not support a "b2b" variant.
		// CSS here will not be output.
		content: '"b2b" variant not available for this brand.';
	}
}
@include oBrandConfigureFor('o-example', 'extra') {
	.o-example__extra {
		// The brand defined above does support an "extra" variant.
		// CSS here is output.
		content: '"extra" variant available for this brand.';
	}
}
```

---

## Contact

If you have any questions or comments about this component, or need help using it, please either [raise an issue](https://github.com/Financial-Times/o-brand/issues), visit [#ft-origami](https://financialtimes.slack.com/messages/ft-origami/) or email [Origami Support](mailto:origami-support@ft.com).

----

## Licence

This software is published by the Financial Times under the [MIT licence](http://opensource.org/licenses/MIT).
