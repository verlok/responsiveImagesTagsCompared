# responsiveImagesTagsCompared

Comparing picture against img with srcset + sizes attributes

## Product image sizes and spacing by device & orientation

See spreadsheet [here](https://docs.google.com/spreadsheets/d/1BCeWGXOevUHlL8l9ti2i81C9BgSsHtybO9Z9WAYpfnQ/edit?usp=sharing)

## Product images sizes calculations

Product images sizes calculations has been done using [this pen](http://codepen.io/verlok/pen/JGXeyz?editors=110) and then [this pen](http://codepen.io/verlok/pen/adNQqX?editors=110).

## Conclusion

When the images have the same ratio over multiple media queries / resolutions, `img` with `srcset` and `sizes` wins.

The reason is that you have to write much less code to support much more devices and screen densities. Given the ability to use CSS' `calc()` function in the length expression of the `sizes` attribute, we can do even complex calculations to define the right image to use even in complex layouts.