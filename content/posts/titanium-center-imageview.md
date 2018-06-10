+++
title = "Center an ImageView inside a parent View in Titanium"
date = "2014-10-22"
meta = "true"
tags = [
    "titanium",
]
categories = [
    "Tutorials",
]
+++

As I was beginning my journey with [Titanium](http://www.appcelerator.com/titanium/), one of the hurdles I faced was centering an image. I wanted something that is similar to `background-size: cover;` in CSS where the image will be maxed out in the parent container while still retaining the image's aspect ratio. Since I can't find a built-in solution, I created one myself.

```javascript
/**
 * Center a Titanium.UI.ImageView inside a parent Titanium.UI.View
 *     similar to a `background-size: cover;` in CSS.
 *
 * Takes 3 parameters: imageRatio, parentView, and imageView
 *
 * imageRatio:
 *     an object with the ratio or actual dimension of the image to be placed in the ImageView.
 *     e.g. {width: 3, height: 2}
 *
 * parentView:
 *     an object that defines the container where the ImageView is placed. This sets the boundaries
 *     or clipping size for the ImageView. Can be a View or a simple object.
 *     e.g. {width: 500, height: 500}
 *
 * imageView:
 *     the ImageView object to be centered in a parent View.
 *     e.g. $.imgViewThumb
 *
 */
var centerImageView = function(imageRatio, parentView, imageView) {
    var h1, w1, h2, w2, x1, x2;

    h1 = parseFloat(parentView.height);
    w1 = parseFloat(parentView.width);
    h2 = parseFloat(imageView.height);
    w2 = parseFloat(imageView.width);

    if (imageRatio.width > imageRatio.height) {
        h2 = h1;
        w2 = (imageRatio.width * h2) / imageRatio.height;
    } else {
        w2 = w1;
        h2 = (imageRatio.height * w2) / imageRatio.width;
    }
    x1 = -((w2 * 0.5) - (w1 * 0.5));
    x2 = -((h2 * 0.5) - (h1 * 0.5));

    imageView.height = h2;
    imageView.width = w2;
    imageView.left = x1;
    imageView.top = x2;
};
```

The `centerImageView` function stretches the image's smaller side to fit the parent container, and recalculates the height and width so that it will retain it's aspect ratio.

```javascript
var win, parentView, imageView;
var initView;

// copy the centerImageView code here

initView = function() {
    win = Ti.UI.createWindow({
        backgroundColor: "#cccccc"
    });
    parentView = Ti.UI.createView({
        height: Ti.Platform.displayCaps.platformHeight,
        width: Ti.Platform.displayCaps.platformWidth,
        borderRadius: 0
    });
    imageView = Ti.UI.createImageView({
        image: "/images/my_image.jpg"
    });

    // we assume here that the image has a dimension of 600x400, therefore the ratio is 3:2.
    // then you can pass {width: 3, height: 2} or {width: 600, height: 400} as the first argument.
    // you can also call this after postlayout if you don't know the dimension of the image.
    // you can then pass the imageView itself as the first argument.
    centerImageView({
        width: 3,
        height: 2
    }, parentView, imageView);

    parentView.add(imageView);
    win.add(parentView);

    win.open();
};

initView();
```

As the saying goes, if there is a wall, we'll break it. If there isn't a path, we'll make one with our hands!
