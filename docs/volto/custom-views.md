---
html_meta:
  "description": ""
  "property=og:description": ""
  "property=og:title": ""
  "keywords": ""
---

(custom-views-label)=

# Custom Views

## Full View

In this chapter we are going to move the full view to a separate view.
In Plone there is a view called `All content` with the view id `full_view`.
We start by creating a file called: `components/FullView/FullView.jsx`.
In this file we will put the content of our created full view in the previous chapter.
We will rename this summary view to full view.

```jsx
/**
 * Full view component.
 * @module components/theme/View/FullView
 */

import React from 'react';
import PropTypes from 'prop-types';
import { Helmet } from '@plone/volto/helpers';
import { Link } from 'react-router-dom';
import { Container, Image } from 'semantic-ui-react';
import { FormattedMessage } from 'react-intl';

/**
 * Full view component class.
 * @function FullView
 * @param {Object} content Content object.
 * @returns {string} Markup of the component.
 */
const FullView = ({ content }) => (
  <Container className="view-wrapper">
    <Helmet title={content.title} />
    <article id="content">
      <header>
        <h1 className="documentFirstHeading">{content.title}</h1>
        {content.description && (
          <p className="documentDescription">{content.description}</p>
        )}
      </header>
      <section id="content-core">
        {content.items.map(item => (
          <article key={item.url}>
            <h2>
              <Link to={item.url} title={item['@type']}>
                {item.title}
              </Link>
            </h2>
            {item.image && (
              <Image
                clearing
                floated="right"
                alt={item.image_caption ? item.image_caption : item.title}
                src={item.image.scales.thumb.download}
              />
            )}
            {item.description && <p>{item.description}</p>}
            {item.text &&
              item.text.data && (
                <p dangerouslySetInnerHTML={{ __html: item.text.data }} />
              )}
          </article>
        ))}
      </section>
    </article>
  </Container>
);

/**
 * Property types.
 * @property {Object} propTypes Property types.
 * @static
 */
FullView.propTypes = {
  /**
   * Content of the object
   */
  content: PropTypes.shape({
    /**
     * Title of the object
     */
    title: PropTypes.string,
    /**
     * Description of the object
     */
    description: PropTypes.string,
    /**
     * Child items of the object
     */
    items: PropTypes.arrayOf(
      PropTypes.shape({
        /**
         * Title of the item
         */
        title: PropTypes.string,
        /**
         * Description of the item
         */
        description: PropTypes.string,
        /**
         * Text of the item
         */
        text: PropTypes.string,
        /**
         * Url of the item
         */
        url: PropTypes.string,
        /**
         * Image of the item
         */
        image: PropTypes.object,
        /**
         * Image caption of the item
         */
        image_caption: PropTypes.string,
        /**
         * Type of the item
         */
        '@type': PropTypes.string,
      }),
    ),
  }).isRequired,
};

export default FullView;
```

Next we will add the view to the components.
We can do this by adding the following lines to `components/index.js`.

```jsx
import FullView from './FullView/FullView';

export { FullView };
```

## Registering The View

To register the view we will edit the `config.js` file.
The `views` configuration options contains all the views.
This object contains an object called `layoutViews` which registers all the layout views.
We will add the `full_view` to this object.

```jsx
import { FullView } from './components';

export const views = {
  ...defaultViews,
  layoutViews: {
    ...defaultViews.layoutViews,
    full_view: FullView,
  },
};
```

## Exercise

Create the `Album View` that shows the images in a grid.
You can use the `Card` class from `semantic-ui`.

````{admonition} Solution
:class: toggle

`components/AlbumView/AlbumView.jsx`

```jsx
/**
 * Album view component.
 * @module components/theme/View/AlbumView
 */

import React from 'react';
import PropTypes from 'prop-types';
import { Helmet } from '@plone/volto/helpers';
import { Link } from 'react-router-dom';
import { Card, Container, Image } from 'semantic-ui-react';

/**
 * Album view component class.
 * @function AlbumView
 * @param {Object} content Content object.
 * @returns {string} Markup of the component.
 */
const AlbumView = ({ content }) => (
  <Container className="view-wrapper">
    <Helmet title={content.title} />
    <article id="content">
      <header>
        <h1 className="documentFirstHeading">{content.title}</h1>
        {content.description && (
          <p className="documentDescription">{content.description}</p>
        )}
      </header>
      <section id="content-core">
        <Card.Group>
          {content.items.map(item => (
            <Card key={item.url}>
              {item.image && (
                <Image
                  alt={item.image_caption ? item.image_caption : item.title}
                  src={item.image.scales.thumb.download}
                />
              )}
              <Card.Content>
                <Card.Header>
                  <Link to={item.url} title={item['@type']}>
                    {item.title}
                  </Link>
                </Card.Header>
              </Card.Content>
            </Card>
          ))}
        </Card.Group>
      </section>
    </article>
  </Container>
);

/**
 * Property types.
 * @property {Object} propTypes Property types.
 * @static
 */
AlbumView.propTypes = {
  /**
   * Content of the object
   */
  content: PropTypes.shape({
    /**
     * Title of the object
     */
    title: PropTypes.string,
    /**
     * Description of the object
     */
    description: PropTypes.string,
    /**
     * Child items of the object
     */
    items: PropTypes.arrayOf(
      PropTypes.shape({
        /**
         * Title of the item
         */
        title: PropTypes.string,
        /**
         * Description of the item
         */
        description: PropTypes.string,
        /**
         * Url of the item
         */
        url: PropTypes.string,
        /**
         * Image of the item
         */
        image: PropTypes.object,
        /**
         * Image caption of the item
         */
        image_caption: PropTypes.string,
        /**
         * Type of the item
         */
        '@type': PropTypes.string,
      }),
    ),
  }).isRequired,
};

export default AlbumView;
```

`components/index.js`

```jsx
/**
 * Add your components here.
 * @module components
 * @example
 * import Footer from './Footer/Footer';
 *
 * export {
 *   Footer,
 * };
 */

import AlbumView from './AlbumView/AlbumView';
import FullView from './FullView/FullView';

export { AlbumView, FullView };
```

`config.js`

```jsx
/**
 * Add your config changes here.
 * @module config
 * @example
 * export const settings = {
 *   ...defaultSettings,
 *   port: 4300,
 *   listBlockTypes: {
 *     ...defaultSettings.listBlockTypes,
 *     'my-list-item',
 *   }
 * }
 */

import {
  settings as defaultSettings,
  views as defaultViews,
  widgets as defaultWidgets,
  tiles as defaultTiles,
} from '@plone/volto/config';

import { AlbumView, FullView } from './components';

export const settings = {
  ...defaultSettings,
};

export const views = {
  ...defaultViews,
  layoutViews: {
    ...defaultViews.layoutViews,
    album_view: AlbumView,
    full_view: FullView,
  },
};

export const widgets = {
  ...defaultWidgets,
};

export const tiles = {
  ...defaultTiles,
};
```
````
