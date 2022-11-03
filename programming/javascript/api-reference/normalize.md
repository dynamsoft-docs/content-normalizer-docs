---
layout: default-layout
title: Dynamsoft Document Normalizer JavaScript API - Detect and Normalize APIs
description: This page shows the Detect and Normalize APIs of Dynamsoft Document Normalizer JavaScript SDK.
keywords: DocumentNormalizer, api reference, javascript, js, Detect and Normalize
needAutoGenerateSidebar: true
needGenerateH3Content: true
noTitleIndex: true
breadcrumbText: Detect and Normalize APIs
---

# Detect and Normalize

## Detect and Normalize a Still Images

| API Name | Description |
|---|---|
| [detectQuad()](#detectquad) | Detects quadrilaterals from an image. |
| [normalize()](#normalize) | Normalizes the source image based on the settings in options. |

## Detect and Normalize Video Frame Images

| API Name | Description |
|---|---|
| [setImageSource()](#setimagesource) | Sets an image source for continous scanning. |
| [onQuadDetected](#onquaddetected) | This event is triggered when a new quadrilateral is detected. |
| [confirmQuadForNormalization()](#confirmquadfornormalization) | Confirms which quadrilateral will be used for later normalization. |
| [normalizeWithConfirmedQuad()](#normalizewithconfirmedquad) | Normalizes the image whith one confirmed quadrilateral. |
| [startScanning()](#startscanning) | Opens the camera and starts continuous scanning of incoming images. |
| [pauseScanning()](#pausescanning) | Pauses continuous scanning but keep the video stream. |
| [resumeScanning()](#resumescanning) | Resumes continuous scanning. |
| [stopScanning()](#stopscanning) | Stops continuous scanning and closes the video stream. |

## detectQuad

Detects quadrilaterals from an image.

```js
detectQuad: (source: Blob | DSImage | DCEFrame | HTMLImageElement | HTMLCanvasElement | string) => Promise<Array<DetectedQuadResult>>;
```

**Parameters**

`source` : specifies the image to detect. The supported image data type include `Blob` , `DSImage` , `DCEFrame` , `HTMLImageElement`, `HTMLCanvasElement` and a string which means an URL.

**Return value**

A promise resolving to an array of [`DetectedQuadResult`](./interfaces/detected-quad-result.md) object that contains all the quad results found in this image.

**Code snippet**

```js
let quads1 = await normalizer.detectQuad(blob);
let quads2 = await normalizer.detectQuad(url);
let quads3 = await normalizer.detectQuad(htmlImageElement);
let quads4 = await normalizer.detectQuad(htmlCanvasElement);
```

**See also**

* [Blob](https://developer.mozilla.org/en-US/docs/Web/API/Blob)
* [DSImage](./interface/dsimage.md)
* [DCEFrame](https://www.dynamsoft.com/camera-enhancer/docs/programming/javascript/api-reference/interface/dceframe.html?ver=latest)
* [HTMLImageElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLImageElement)
* [HTMLCanvasElement](https://developer.mozilla.org/en-US/docs/Web/API/HTMLCanvasElement)

## normalize

Normalizes the source image based on the settings in options.

```js
normalize: (source: Blob | DSImage | DCEFrame | HTMLImageElement | HTMLCanvasElement | string, options?: { quad?: Quadrilateral }) => Promise<NormalizedImageResult>;
```

**Parameters**

`source` : specifies the image to normalize. The supported image data type include `Blob` , `DSImage` , `DCEFrame` , `HTMLImageElement`, `HTMLCanvasElement` and a string which means an URL.

`options` : specifies how normalization should be done. If "quad" is passed, it means that the image should be cropped according to the quad, and the resulting image should also be "deskewed" and "perspective corrected". More options to be added for the normalization in the future.

**Return value**

A promise resolving to a [`NormalizedImageResult`](./interfaces/normalized-image-result.md) object that contains the normalized result of this image.

**Code snippet**

```js
let quads = await normalizer.detectQuad(blob);
let normalizedImageResult = await normalizer.normalize(sourceImage, { quad: quads[i].location });
```

## setImageSource

Sets an image source for continous scanning.

```typescript
setImageSource(imageSource: ImageSource, options?: object): Promise<void>;
```

**Parameters**

`imageSource` : specifies the image source.

`options` : options to help with the usage of the [`ImageSource`](./interfaces/imagesource.md) object. At present, it only contains one property `resultsHighlightBaseShapes` that accepts `Dynamsoft.DCE.DrawingItem` as its value to help with the highlighting of text regions as shown in the code snippet below. More properties will be added as needed in the future.

**Return value**

A promise that resolves when the operation succeeds.

**Code snippet**

```javascript
let normalizer = await Dynamsoft.DDN.DocumentNormalizer.createInstance();
let enhancer = await Dynamsoft.DCE.CameraEnhancer.createInstance();
let options = {
    resultsHighlightBaseShapes: Dynamsoft.DCE.DrawingItem
};
await normalizer.setImageSource(enhancer, options);
normalizer.onQuadDetected = async (results, sourceImage) => {
    //...
}
await normalizer.startScanning(true);
```

## onQuadDetected

This event is triggered when a new quadrilateral is detected.

```js
onQuadDetected: (results: Array<DetectedQuadResult>, sourceImage: Blob | DSImage | DCEFrame | HTMLImageElement | HTMLCanvasElement | string) => void;
```

**Parameters**

`results` : an array of [`DetectedQuadResult`](./interfaces/detected-quad-result.md) that hold the detected quad results.

`sourceImage` : specifies the image to detect. The supported image data type include `Blob` , `DSImage` , `DCEFrame` , `HTMLImageElement`, `HTMLCanvasElement` and a string which means an URL.

**Code snippet**

```javascript
normalizer.onQuadDetected = async (results, sourceImage) => {
    for (let result of results) {
        let normalizedImageResult = await normalizer.normalize(sourceImage, { quad: result.location });
    }
}
```

## confirmQuadForNormalization

Confirms which quadrilateral will be used for later normalization.

```js
confirmQuadForNormalization: () => void;
```

**Code snippet**

```javascript
normalizer.confirmQuadForNormalization();
```

## normalizeWithConfirmedQuad

Normalizes the image whith one confirmed quadrilateral.

```js
normalizeWithConfirmedQuad: () => Promise<NormalizedImageResult>;
```

**Return value**

A promise resolving to a [`NormalizedImageResult`](./interfaces/normalized-image-result.md) object that contains the normalized result of this image.

**Code snippet**

```javascript
let normalizedImageResult = await normalizer.normalizeWithConfirmedQuad();
let resultImg = await normalizedImageResult.saveToFile("example.png");
```

## startScanning

Open the camera and starts continuous scanning of incoming images.

```typescript
startScanning(appendOrShowUI?: boolean): Promise<void>;
```

**Parameters**

`appendOrShowUI` : this parameter specifies how to handle the UI that comes with the bound CameraEnhancer instance. When set to true, if the UI doesn't exist in the DOM tree, the CameraEnhancer instance will append it in the DOM and show it; if the UI already exists in the DOM tree but is hidden, it'll be displayed. When not set or set to false, it means not to change the original state of that UI: if it doesn't exist in the DOM tree, nothing shows up on the page; if it exists in the DOM tree, it may or may not show up depending on its original state.

**Return value**

A promise that resolves when the operation succeeds.

**Code Snippet**

```js
let normalizer = await Dynamsoft.DDN.DocumentNormalizer.createInstance();
let enhancer = await Dynamsoft.DCE.CameraEnhancer.createInstance();
let options = {
    resultsHighlightBaseShapes: Dynamsoft.DCE.DrawingItem
};
await normalizer.setImageSource(enhancer, options);
normalizer.onQuadDetected = async (results, sourceImage) => {
    for (let result of results) {
        let normalizedImageResult = await normalizer.normalize(sourceImage, { quad: result.location });
    }
}
normalizer.startScanning(true);
```

## pauseScanning

Pause continuous scanning but keep the video stream.

**Parameters**

`options` : Options to configure how the pause works. At present, it only contains one property `keepResultsHighlighted` which, when set to **true**, will keep the text found on the frame (at the time of the pause) highlighted.

```typescript
pauseScanning(options?: object): void;
```

## resumeScanning

Resumes continuous scanning.

```typescript
resumeScanning(): void;
```

## stopScanning

Stops continuous scanning and closes the video stream.

```typescript
stopScanning(hideUI?: boolean): void;
```

**Parameters**

`hideUI` : this parameter specifies how to handle the UI that comes with the bound CameraEnhancer instance. When set to true, if the UI doesn't exist in the DOM tree or it exists but is hidden, nothing is done; if the UI already exists in the DOM tree and is shown, it'll be hidden. When not set or set to false, it means not to change the original state of that UI: if it doesn't exist in the DOM tree, nothing happens; if it exists in the DOM tree, it may or may not be hidden depending on its original state.

**Code Snippet**

```js
normalizer.stopScanning(true);
```
