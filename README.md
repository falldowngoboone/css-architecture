# CSS Architecture

Heavily influenced by Object Oriented CSS (OOCSS), Scalable and Modular Architecture for CSS (SMACSS), and Inverted Triangle CSS (ITCSS).

## TL;DR

Group styles according to their specificity and reach. The least specific styles with the greatest reach should be at the very top of the cascade, the most specific, granular styles should be at the bottom:

1. Settings
2. Tools
3. Generic
4. Elements
5. Objects
6. Components
7. Utilities

Group these layers into AT MOST three separate stylesheets: `base.css`, `pages/*.css`, and, optionally, `override.css`.

Use BEMIT as guidance for a naming methodology.

Moving to a JavaScript framework (e.g React or even a polyfilled Web Components library like [Lit](https://lit.dev)) would allow CSS to become more modular, but a lot of the methodology laid out in this documentation would still be helpful for cross-cutting styles.

## Considerations

When considering how to architect CSS, it's important to understand the challenges involved in large-scale CSS projects. Understanding large-scale CSS challenges ensures that your CSS architecture solution addresses the correct concerns.

The number one concern in large-scale CSS projects is managing specificity. Specificity should only increase the further down you go in a page's style cascade. Unmanaged specificity creates the need for hacks like unnecessary nesting, qualifying selectors, or `!important` declarations, which only create even more problems for future CSS. Good CSS architecture creates a steady CSS [specificity graph](https://csswizardry.com/2014/10/the-specificity-graph/) curve and keeps the CSS readable, scalable and maintainable.

Another concern in large-scale CSS projects is efficiency. Websites can be comprised of tens of thousands of pages grouped into a handful of sections. These sections will share several common styles but will also require styles unique to their respective section. Good CSS architecture delivers only the styles that are required for each page/section, as well as effectively abstracts related style declarations for reuse.

## Organization

Styles can be efficiently grouped into three or more layers, in ascending level of specificity:

1. Styles used throughout the site, usually with a low level of specificity
2. Styles used in a specific area of the site
3. Styles that override granular aspects of the other styles (utilities)

The second layer tends to be the most specific, meaning that different areas of the site will require a different second layer. The first layer is global by definition, and the last layer lends itself to reuse even though it is the most specific. Therefore, it's best to build out these layers as three separate stylesheets.

Using the layered stylesheet approach allows the base (first) and override (last) layers, which could potentially contain the bulk of the site's style definitions, to be cached once for reuse throughout the site. I have found this to be the most efficient stylesheet organization.

The alternative would be to shove everything into a single, page-specific stylesheet. The only advantage this strategy yields is a single payload for every page. However, in the former approach, the only time three stylesheets would be requested would be the first page visit. Each additional page/section visit would leverage browser cache and only require a single stylesheet request, which would be considerably smaller than a single, combined stylesheet.

Now that we've seen how a layered stylesheet approach is beneficial to delivering styles efficiently, let's examine what I propose for each layer in more detail.

### `base.css`

This stylesheet contains the most used and least-specific styles. In order:

1. All `@font-face` rules
2. A light CSS-reset (I am a fan of Jeremy Thomas's [minireset.css](https://github.com/jgthms/minireset.css))
3. Light element styles
4. Layout and other non-cosmetic primitives
5. Component styles used throughout the site (e.g. site navigation styles)

### Page-specific styles (`pages/**/*.css`)

This stylesheet contains page- and/or section-specific styles.

Let's assume we are creating a stylesheet for the PDP. The PDP includes a Tabs and Accordion component we have created, as well as a carousel component provided by a third-party vendor. The third-party vendor component also requires local overrides to better adhere to site styling. The `pages/pdp.css` stylesheet might include the following, in order:

1. Tabs styles
2. Accordion styles
3. The vendor's carousel styles
4. Our local overrides for the vendor carousel

These are all specific to the needs of the PDP. The PLP might include different components but the same third-party vendor carousel. You can simply copy the local overrides you have made here over to the PLP's page-specific stylesheet (however, if this carousel becomes something that's used in three or more places, you may think about abstracting it into a new component with component styles for reuse).

### `override.css` (optional)

This layer would traditionally contain theme definitions and utility overrides, but it is completely optional. Theming can be done easily today [with CSS custom properties](https://csswizardry.com/2016/10/pragmatic-practical-progressive-theming-with-custom-properties/) in the global `base.css` and page stylesheets. Utility classes, as we have used them, are great for rapid prototyping, but they can lead to a bulk of unnecessary CSS that can better be managed through well thought out component styles and modifications.

## Resources

- https://developer.mozilla.org/en-US/docs/Web/CSS/Cascade
- https://developer.mozilla.org/en-US/docs/Web/CSS/Specificity
- https://csswizardry.com/2014/10/the-specificity-graph/
- https://css-tricks.com/one-two-three/
- https://developer.mozilla.org/en-US/docs/Learn/CSS/Building_blocks/Organizing