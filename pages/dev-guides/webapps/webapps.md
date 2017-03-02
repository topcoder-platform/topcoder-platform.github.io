# React Web App Development Guide

## Style Guide

We follow AirBnB [JavaScript style guide](https://github.com/airbnb/javascript) and [React styleg uide](https://github.com/airbnb/javascript/tree/master/react).

You should lint your code with ESLint to align with the requirements. From the terminal do

```
$ npm run lint
```

or

```
$ yarn run lint
```

Please do not create pull requests if your code has linter errors.

[CSS modules and SCSS Style guide](css-modules.md)

## Unit tests

> Under development

We would like you to do unit tests for all your code.

## Configuration

TBD

## CI / CD
Migrate from Travis to CircleCI (TBD)

## Architecture

### Organization
As any big project ours is organized in a way that allows scale and feature separation. It is best that each item has its own place, and everybody can follow and find the code she is looking for.

Connect App follows the following structure:

```
root
|
|--config/						(all webpack configuration files)
|--examples/					(example code)
|--node_modules/				(all node dependencies)
|--src/							(all source file)
	|--actions/					(system actions)
	|--api/
	|--assets/					(all icons and images)
	|--components/				(global components)
	|--config/					(app configurations)
	|--containers/				(app pages)
	|--helpers/					(source file)
	|--reducers/				(redux reducers)
	|--styles/					(all the app styles)
		|--_helpers/			(helper files included in main.scss)
		|--themes/				(where all themes are stored)
			|--connect/			(the default theme)
		|--vendors/				(all 3rd party styles in a separate folder)
		|--main.scss			(the compiled stylesheet)
	|--index.html
	|--index.jsx
|
|--readme.md
|--package.json

```

Each container (page) should replicate appropriate structure to include all components, reducers they may need. Imagine that we have a project list container that displays all Connect projects in a list view. Each list card would be the main building block, and each card would use some of the global components. Based on the feature separation each component should be accompanied by its logic (*as .js file*) and style (*as .scss file*) with the same name.

```
root
|
|--containers/
	|--project-listing/
		|--actions/
		|--components/
			|--card/
				|--card.jsx
				|--card.js
				|--card.scss
			|--grid_header/
			|--grid_footer/
		|--reducers/
	|--projectListing.jsx
	|--projectListing.js
	|--projectListing.scss
```

### Components

We have 3 types of components:

- **Dumb components** — stateless components that don't manage data. They are used multiple times and have a very basic DOM and style;
- **Complex components** - they manage the data and use stateless components to render data
- **Containers** — thin abstraction layer that manages context. In essence those are the pages in our app.

For more information read [Thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html)

#### Dumb components

We whould think of those as small blocks of HTML that are repeated multiple times. There should be no logic into a dumb component. All content should be passed by the smart parent as props, and managed by the parent.

```javascript
const DumbComponent = (props) => {
  return (<div />);
}
```

Always use props. Period. Read [Typechecking With PropTypes](https://facebook.github.io/react/docs/typechecking-with-proptypes.html)

#### Smart components

Smart containers should build the view with dumb components to avoid repetition and also manage all the data for the dumb components.


#### Containers

The reason you want to use containers that pass down the data is because you want to avoid having to connect every view to a store when dealing with Flux/Redux. (from [here](https://medium.com/@nesbtesh/react-best-practices-a76fd0fbef21#.v87936dd1))


#### Optimize repo size
from [this article](https://lacke.mn/reduce-your-bundle-js-file-size/)

Reduce the repo size by always explicitly loading only the things you need, not the whole bundle.

Bloated

```javascript
import { concat, sortBy, map, sample } from 'lodash';
```

Lean

```javascript
import concat from 'lodash/concat';
import sortBy from 'lodash/sortBy';
import map from 'lodash/map';
import sample from 'lodash/sample';
```

We still don't have Webpack 2 and tree shaking. Until we upgrade our Webpack, follow this guide.


#### Resources

<https://medium.com/@nesbtesh/react-best-practices-a76fd0fbef21#.v87936dd1>
<https://engineering.siftscience.com/best-practices-for-building-large-react-applications/>
<https://facebook.github.io/react/docs/react-component.html>




### Component styling

CSS Modules provides a novel way to treat the plain old CSS — during build time all the class names of the component and namespaced by webpack (depending on your configuration). This allows us to reuse the same generic `.classname` in many components and depending on position in the DOM they'll be compiled to something like `.u234432-classname-2344324bngfdsdfgsdfg`. This solves the global contamination of namespaces.

For brevity, CSS modules introduce a new keyword `composes: … ` that allows you to reuse styles from the global lists, so you don't need to write the same code twice. It behaves similar to `extends` in Sass. For further details read the [Css Modules repo](https://github.com/css-modules/css-modules).

Connect uses React and to help with the CSS Modules implementation we use a NPM plugin [CSS React Modules](https://github.com/gajus/react-css-modules).

All styles should be in the SCSS format (.scss) and follow CSS Modules. In order for the system to properly compile your styles, use `.m.scss` suffix for your components.

In the case of 3rd party component being used in Connect, you have to provide a styling alternative in a dedicated folder the `~/scr/styles/vendors/` that matches the name of the component.


## Application themes

> NOTE: Under development.

We want to create runtime/context dependable styling for our apps. There are cases where the components would need to apply different styles based on the user, organisation or subdomain. The best solution so far seems to be <https://github.com/javivelasco/react-css-themr>.

Topcoder has its own library of components that we ship with default css. In the next version (2.0) of `tc-react-components` we will upgrade the styles to use CSS Modules and thus be able to pick different styles through style override.


### SVG and images

Scalabel Vector Graphics (SVG) is amazing, and we love it. The best way to use it in our app is through [React SVG loader](https://github.com/boopathi/react-svg-loader). This converts our .SVG images into React components that we can modify in the code through passing the appropriate `.classname`. This helps to keep the icons and images in format that can be easily maintained by the designer, limits manual labor and potential errors.

All .svg images should reside under `~/src/assets/icons`. Any .svg placed in that folder by default would be available for you as a React component upon `import`. Any .svg outside of this folder should use `.inline.svg` suffix for webpack to be able to pick it up.

Example:

```javascript
// Load the .svg file as react component
import IconDashboard from "icons/dashboard.svg"

...

<li>
	<Link {...getLinkProps()}>
		// Use the new react component as inline SVG and customize it through CSS
		<IcondDashboard className="active"/> Dashboard
	</Link>
</li>
```

Bonus: [Why inline SVG is Best SVG](https://youtu.be/af4ZQJ14yu8) - cool video

> *Note for the designers:* When you export your amazing designs into SVG, please remove any classnames and other styles, use only the standard properties (fill) to add color.
