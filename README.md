# @sanity/image-url

Quickly generate image urls from Sanity image records.

This helper will by default respect any crops/hotspots specified in the Sanity content provided to it. The most typical use case for this is to give it a sanity image and specify a width, height or both and get a nice, cropped and resized image according to the wishes of the content editor and the specifications of the front end developer.

In addition to the core use case, this library provides a handy builder to access the rich selection of processing options available in the Sanity image pipeline.

## Getting started

    npm install --save @sanity/image-url

## Usage

The most common way to use this library in your project is to configure it by passing it [your configured sanityClient](https://www.npmjs.com/package/@sanity/client). That way it will automatically be preconfigured to your current project and dataset:

```js
import React from 'react'
import myConfiguredSanityClient from './sanityClient'
import imageUrlBuilder from '@sanity/image-url'

const builder = imageUrlBuilder(myConfiguredSanityClient)

function urlFor(source) {
  return builder.image(source)
}
```

Then you can use the handy builder syntax to generate your urls:

```jsx
<img src={urlFor(author.image).width(200).url()} />
```

This will ensure that the author image is alway 200 pixels wide, automatically applying any crop specified by the editor and cropping towards the hot-spot she drew. You can specify both width and height like this:

```jsx
<img src={urlFor(movie.poster).width(500).height(300).url()}>
```

There are a large number of useful options you can specify, like e.g. blur:

```jsx
<img src={urlFor(mysteryPerson.mugshot).width(200).height(200).blur(50).url()}>
```

Note that the `url()` function needs to be the final one in order to output the url as a string.

## Builder methods

### `image(source)`

Specify the image to be rendered. Accepts either a Sanity `image` record, an `asset` record, or just the asset id as a string. In order for hotspot/crop processing to be applied, the `image` record must be supplied, as well as both width and height.

### `dataset(dataset)`, `projectId(projectId)`

Usually you should preconfigure your builder with dataset and project id, but even when you did, these let you temporarily override them if you need to render assets from other projects or datasets.

### `width(pixels)`

Specify the width of the rendered image in pixels.

### `height(pixels)`

Specify the height of the rendered image in pixels.

### `size(width, height)`

Specify width and height in one go.

### `focalPoint(x, y)`

Specify a center point to focus on when cropping the image. Values from 0.0 to 1.0 in fractions of the image dimensions. When specified, overrides any crop or hotspot in the image record.

### `blur(amount)`, `sharpen(amount)`, `invert()`

Apply image processing.

### `rect(left, top, width, height)`

Specify the crop in pixels. Overrides any crop/hotspot in the image record.

### `format(name)`

Specify the image format of the image. 'jpg', 'pjpg', 'png', 'webp'

### `auto(mode)`

Specify transformations to automatically apply based on browser capabilities. Supported values:

- `format` - Automatically uses WebP if supported

### `orientation(angle)`

Rotation in degrees. Acceptable values: 0, 90, 180, 270

### `quality(value)`

Compression quality, where applicable. 0-100

### `forceDownload(defaultFileName)`

Make this an url to download the image. Specify the file name that will be suggested to the user.

### `flipHorizontal()`, `flipVertical()`

Flips the image.

### `crop(mode)`

Specifies how to crop the image. When specified, overrides any crop or hotspot in the image record. See the [documentation](https://www.sanity.io/docs/reference/image-urls#crop-object-object) for details.

### `fit(value)`

Configures the fit mode. See the [documentation](https://www.sanity.io/docs/reference/image-urls#fit-object-object) for details.

### `dpr(value)`

Specifies device pixel ratio scaling factor. From 1 to 3.

### `saturation(value)`

Adjusts the saturation of the image. Currently the only supported value is `-100` - meaning it grayscales the image.

### `ignoreImageParams()`

Ignore any specifications from the image record (i.e. crop and hotspot).

### `url()`, `toString()`

Return the url as a string.

### `pad(value)`

Specify the number of pixels to pad the image.

### Deprecated: `minWidth(pixels)`, `maxWidth(pixels)`, `minHeight(pixels)`, `maxHeight(pixels)`

Specifies min/max dimensions when cropping.

**Deprecated**: You usually want to use `width`/`height` with a fit mode of `max` or `min` instead.

## Custom CDN domains

You can specify a custom `baseUrl` in the builder options in order to override the default (`https://cdn.sanity.io`):

```js
import imageUrlBuilder from '@sanity/image-url'

const builder = imageUrlBuilder({
  baseUrl: 'https://my.custom.domain',
  projectId: 'abc123',
  dataset: 'production',
})
const urlFor = (source) => builder.image(source)

urlFor('image-928ac96d53b0c9049836c86ff25fd3c009039a16-200x200-png')
  .auto('format')
  .fit('max')
  .width(720)
  .toString()

// output: https://my.custom.domain/images/abc123/production/928ac96d53b0c9049836c86ff25fd3c009039a16-200x200.png?auto=format&fit=max&w=720
```

If you already have a configured client instance:

```js
import imageUrlBuilder from '@sanity/image-url'
import myConfiguredClient from './mySanityClient'

const builder = imageUrlBuilder({
  ...myConfiguredClient.config(),
  baseUrl: 'https://my.custom.domain',
})
```

## License

MIT © [Sanity.io](https://www.sanity.io/)
