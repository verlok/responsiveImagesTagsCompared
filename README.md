# responsiveImagesTagsCompared

We have a responsive website layout and we want to have flexible responsive images, optimized for the most common resolutions. Do we need to use the `picture` tag, or the `img` tag with its `srcset` and `sizes` attributes can do the job?

Here I'm trying to find out if the `img` tag used with its `srcset` and `sizes` attributes has some limitations compared to the `picture` tag in a full responsive, non full-width page layout.

## The images layout

Images in this layout are as follows:

| Viewport Width        | Images width     | Container width          |
|-----------------------|------------------|--------------------------|
| 0 to 767px            | 1/2 of container | 100% with no padding     |
| From 768px to 1023px  | 1/3 of container | 100% with 30px padding   |
| From 1024px to 1279px | 1/4 of container | 100% with 30px padding   |
| From 1280px up        | 1/4 of container | 1280px with 30px padding |

## The problem I thought it existed

When using the `img` tag with `srcset` and `sizes`, we can specify how wide your images will look at different media queries, but as I could found so few articles that explains it, I thought we could only specify the widths using the viewport width (`vw`) unit. But I was wrong...

## The solution I found

Contrarily of what I thought, we can specify the `img` `sizes` using **any length**, using the `vw` unit in the simplest cases, and **using the CSS `calc()` function** in the most complex ones.

> If you need a more extensive explication, please read [srcset and sizes](https://ericportis.com/posts/2014/srcset-sizes/) by Eric Portis, in my opinion the best article about using `img` with `srcset` and `sizes`.

Experiments about images sizes calculations has been done using [this pen](http://codepen.io/verlok/pen/JGXeyz?editors=110) which uses media queries and CSS to specify `img` widths, and then [this pen](http://codepen.io/verlok/pen/adNQqX?editors=110) which specifies widths directly inside the `img` tag.

### Applying it to the layout

| Viewport Width        | Images width     | Container width          | Image width in CSS          |
|-----------------------|------------------|--------------------------|-----------------------------|
| 0 to 767px            | 1/2 of container | 100% with no padding     | `50vw`                      |
| From 768px to 1023px  | 1/3 of container | 100% with 30px padding   | `calc(( 100vw - 60px ) / 3)`|
| From 1024px to 1279px | 1/4 of container | 100% with 30px padding   | `calc(( 100vw - 60px ) / 4)`|
| From 1280px up        | 1/4 of container | 1280px with 30px padding | `305px` :)                  |

## Calculating optimized image sizes based on spacing and device / orientation

We want our images to be optimized at most common resolutions, we need to:

* Define which are the most common screen resolutions / densities
* Decide which are the resolutions / densities that we want to optimize for
* Calculate what the image sizes will be at these resolutions / densities
* Scale our images to those image sizes
* Markup the `img`s in our HTML
    * List all the image sizes in the `srcset` attribute, using the `w` descriptor
    * List all the image widths in the `sizes` attribute, as defined in the table above

To define the screen resolutions and decide which ones to optimize for, we can use any tool like Google Analytics to get some information about our users and the devices they mostly use.

To see the calculations I made and get the image sizes to use, see [this spreadsheet](https://docs.google.com/spreadsheets/d/1BCeWGXOevUHlL8l9ti2i81C9BgSsHtybO9Z9WAYpfnQ/edit)

To recap, the resulting image sizes are the following:

| Device & orientation    | Screen width | Img width (css px) | Img height (css px) | Screen density | Img width (px) | Img height (px) |
|-------------------------|--------------|--------------------|---------------------|----------------|----------------|-----------------|
| iPhone 4/5/5s           | 320          | 160                | 186                 | 2              | **320**        | **372**             |
| iPhone 6                | 375          | 187                | 217                 | 2              | **374**        | **435**             |
| iPhone 5/5s landscape   | 568          | 284                | 330                 | 2              | **568**        | **660**             |
| iPhone 6 landscape      | 667          | 333                | 387                 | 2              | **666**        | **774**             |
| iPad / mini             | 768          | 236                | 274                 | 2              | **472**        | **549**             |
| iPad / mini landscape   | 1024         | 241                | 280                 | 2              | **482**        | **560**             |
| PC with 1280w up        | 1280         | 305                | 355                 | 1              | **305**        | **355**             |
| PC with 1280w and HiDPI | 1280         | 305                | 355                 | 2              | **610**        | **709**             |
| PC with more than 1280w | 1920         | 305                | 355                 | 1              | **305**        | **355**             |

## `picture` vs `img`

Picture markup:

```html
<picture>
    <source media="(max-width: 320px)"
            srcset="http://placehold.it/320x372 2x">
    <source media="(max-width: 375px)"
            srcset="http://placehold.it/374x435 2x">
    <source media="(max-width: 568px)"
            srcset="http://placehold.it/568x660 2x">
    <source media="(max-width: 667px)"
            srcset="http://placehold.it/666x774 2x">
    <source media="(max-width: 768px)"
            srcset="http://placehold.it/472x549 2x">
    <source media="(max-width: 1024px)"
            srcset="http://placehold.it/241x280 1x, http://placehold.it/482x560 2x">
    <source media="(min-width: 1280px)"
            srcset="http://placehold.it/305x355 1x, http://placehold.it/610x709 2x">
    <img src="http://placehold.it/305x355" alt="A product image">
</picture>
```

Img markup:

```html
<img src="http://placehold.it/305x355"
         srcset="http://placehold.it/241x280 241w, http://placehold.it/305x355 305w, http://placehold.it/320x372 320w, http://placehold.it/374x435 374w, http://placehold.it/472x549 472w, http://placehold.it/482x560 482w, http://placehold.it/568x660 578w, http://placehold.it/610x709 610w, http://placehold.it/666x774 666w"
         sizes="(min-width: 1280px) 305px, (min-width: 1024px) calc((100vw - 60px) / 4), (min-width: 768px) calc((100vw - 60px) / 3), 50vw"
         alt="A product image">
```

`picture` markup turns out to be more verbose, and we're still not supporting single density displays under 768px.

`img` markup is shorter and non repetitive, and lets the browser calculate what is the better image to use considering resolution AND pixel density.

## Conclusion

`img` with `srcset` and `sizes` wins.

The reason is that you have to write much less code to support much more devices and screen densities. Given the ability to use CSS' `calc()` function in the length expression of the `sizes` attribute, we can do even complex calculations to define the right image to use even in complex layouts.

Note that `picture` and `img` are comparable only when all the images have the same ratio over multiple media queries. If the images ratio change to adapt images to devices ratios, the `picture` tag is the only way to go.

Cheers!
