# d3-hexbin

Hexagonal binning is useful for aggregating data into a coarser representation for display. For example, rather than rendering a scatterplot of tens of thousands of points, bin the points into a few hundred hexagons to show the distribution. Hexbins can support a color encoding, area encoding, or both.

[<img alt="Hexagonal Binning (Color)" src="https://raw.githubusercontent.com/d3/d3-hexbin/master/img/color.jpg" width="420" height="219">](https://bl.ocks.org/mbostock/4248145)[<img alt="Hexagonal Binning (Area)" src="https://raw.githubusercontent.com/d3/d3-hexbin/master/img/area.jpg" width="420" height="219">](https://bl.ocks.org/mbostock/4248146)[<img alt="Bivariate Hexbin Map" src="https://raw.githubusercontent.com/d3/d3-hexbin/master/img/bivariate.jpg" width="420" height="219">](https://bl.ocks.org/mbostock/4330486)[<img alt="Dynamic Hexbin" src="https://raw.githubusercontent.com/d3/d3-hexbin/master/img/dynamic.jpg" width="420" height="219">](https://bl.ocks.org/mbostock/7833311)

## Installing

If you use NPM, `npm install d3-hexbin`. Otherwise, download the [latest release](https://github.com/d3/d3-hexbin/releases/latest). You can also load directly from [d3js.org](https://d3js.org), either as a [standalone library](https://d3js.org/d3-hexbin.v0.2.min.js). AMD, CommonJS, and vanilla environments are supported. In vanilla, a `d3_hexbin` global is exported:

```html
<script src="https://d3js.org/d3-hexbin.v0.2.min.js"></script>
<script>

var hexbin = d3.hexbin();

</script>
```

[Try d3-hexbin in your browser.](https://tonicdev.com/npm/d3-hexbin)

## API Reference

<a name="hexbin" href="#hexbin">#</a> d3.<b>hexbin</b>()

Constructs a new default [hexbin generator](#_hexbin).

<a name="_hexbin" href="#_hexbin">#</a> <i>hexbin</i>(<i>points</i>)

Bins the specified array of *points*, returning an array of hexagonal *bins*. For each point in the specified *points* array, the [*x*-](#hexbin_x) and [*y*-](#hexbin_y)accessors are invoked to compute the *x*- and *y*-coordinates of the point, which is then used to assign the point to a hexagonal bin. If either the *x*- or *y*-coordinate is NaN, the point is ignored and will not be in any of the returned bins.

Each bin in the returned array is an array containing the bin’s points. Only non-empty bins are returned; empty bins without points are not included in the returned array. Each bin has these additional properties:

* `x` - the *x*-coordinate of the center of the associated bin’s hexagon
* `y` - the *y*-coordinate of the center of the associated bin’s hexagon

These *x*- and *y*-coordinates of the hexagon center can be used to render the hexagon at the appropriate location in conjunction with [*hexbin*.hexagon](#hexbin_hexagon). For example, given a hexbin generator:

```js
var hexbin = d3.hexbin();
```

You could display a hexagon for each non-empty bin as follows:

```js
svg.selectAll("path")
  .data(hexbin(points))
  .enter().append("path")
    .attr("d", function(d) { return "M" + d.x + "," + d.y + hexbin.hexagon(); });
```

Alternatively, using a transform:

```js
svg.selectAll("path")
  .data(hexbin(points))
  .enter().append("path")
    .attr("transform", function(d) { return "translate(" + d.x + "," + d.y + ")"; })
    .attr("d", hexbin.hexagon());
```

This method ignores the hexbin’s [extent](#hexbin_extent); it may return bins outside the extent if necessary to contain the specified points.

<a name="bins_add" href="#bins_add">#</a> <i>bins</i>.<b>add</b>(<i>point</i>)

Adds a point and returns the bins.

<a name="bins_addAll" href="#bins_addAll">#</a> <i>bins</i>.<b>addAll</b>(<i>points</i>)

Adds points and returns the bins.

<a name="bins_remove" href="#bins_remove">#</a> <i>bins</i>.<b>remove</b>(<i>point</i>)

Removes the point (if it is referenced in its bin), and returns the bins. Empty bins are pruned.

<a name="bins_removeAll" href="#bins_removeAll">#</a> <i>bins</i>.<b>removeAll</b>(<i>points</i>)

Removes all the points from the bins, and returns the bins. Empty bins are pruned.


<a name="hexbin_hexagon" href="#hexbin_hexagon">#</a> <i>hexbin</i>.<b>hexagon</b>([<i>radius</i>])

Returns the SVG path string for the hexagon centered at the origin ⟨0,0⟩. The path string is defined with relative coordinates such that you can easily translate the hexagon to the desired position. If *radius* is not specified, the hexbin’s [current radius](#hexbin_radius) is used. If *radius* is specified, a hexagon with the specified radius is returned; this is useful for area-encoded bivariate hexbins.

<a name="hexbin_centers" href="#hexbin_centers">#</a> <i>hexbin</i>.<b>centers</b>()

Returns an array of [*x*, *y*] points representing the centers of every hexagon in the [extent](#hexagon_extent).

<a name="hexbin_mesh" href="#hexbin_mesh">#</a> <i>hexbin</i>.<b>mesh</b>()

Returns an SVG path string representing the hexagonal mesh that covers the [extent](#hexagon_extent); the returned path is intended to be stroked. The mesh may extend slightly beyond the extent and may need to be clipped.

<a name="hexbin_x" href="#hexbin_x">#</a> <i>hexbin</i>.<b>x</b>([<i>x</i>])

If *x* is specified, sets the *x*-coordinate accessor to the specified function and returns this hexbin generator. If *x* is not specified, returns the current *x*-coordinate accessor, which defaults to:

```js
function x(d) {
  return d[0];
}
```

The *x*-coordinate accessor is used by [*hexbin*](#_hexbin) to compute the *x*-coordinate of each point. The default value assumes each point is specified as a two-element array of numbers [*x*, *y*].

<a name="hexbin_y" href="#hexbin_y">#</a> <i>hexbin</i>.<b>y</b>([<i>x</i>])

If *y* is specified, sets the *y*-coordinate accessor to the specified function and returns this hexbin generator. If *y* is not specified, returns the current *y*-coordinate accessor, which defaults to:

```js
function y(d) {
  return d[1];
}
```

The *y*-coordinate accessor is used by [*hexbin*](#_hexbin) to compute the *y*-coordinate of each point. The default value assumes each point is specified as a two-element array of numbers [*x*, *y*].

<a name="hexbin_angle" href="#hexbin_angle">#</a> <i>hexbin</i>.<b>angle</b>([<i>angle</i>])

If *angle* is specified, sets the angle of the hexagonal grid to the specified number, in degrees. If *angle* is not specified, returns the current angle, which defaults to 0 (pointy-topped hexagons).

<a name="hexbin_translate" href="#hexbin_translate">#</a> <i>hexbin</i>.<b>translate</b>([<i>translate</i>])

If *translate* is specified, translates the hexagonal grid to the specified value [tx, ty]. If *translate* is not specified, returns the current translate, which defaults to [0, 0].

<a name="hexbin_radius" href="#hexbin_radius">#</a> <i>hexbin</i>.<b>radius</b>([<i>radius</i>])

If *radius* is specified, sets the radius of the hexagon to the specified number. If *radius* is not specified, returns the current radius, which defaults to 1. The width of each hexagon is *radius* × 2 × sin(π / 3) and the height of each hexagon is *radius* × 3 / 2.

<a name="hexbin_extent" href="#hexbin_extent">#</a> hexbin.<b>extent</b>([<i>extent</i>])

If *extent* is specified, sets the hexbin generator’s extent to the specified bounds [[*x0*, *y0*], [*x1*, *y1*]] and returns the hexbin generator. If *extent* is not specified, returns the generator’s current extent [[*x0*, *y0*], [*x1*, *y1*]], where *x0* and *y0* are the lower bounds and *x1* and *y1* are the upper bounds. The extent defaults to [[0, 0], [1, 1]].

<a name="hexbin_size" href="#hexbin_size">#</a> hexbin.<b>size</b>([<i>size</i>])

If *size* is specified, sets the [extent](#hexbin_extent) to the specified bounds [[0, 0], [*dx*, *dy*]] and returns the hexbin generator. If *size* is not specified, returns the generator’s current size [*x1* - *x0*, *y1* - *y0*], where *x0* and *y0* are the lower bounds and *x1* and *y1* are the upper bounds. The size defaults to [1, 1]. This is a convenience method for setting the extent. For example, these two statements are equivalent:

```js
hexbin.extent([[0, 0], [width, height]]);
hexbin.size([width, height]);
```

<a href="#hexbin_context" name="hexbin_context">#</a> <i>hexbin</i>.<b>context</b>([<i>context</i>]) [<>](https://github.com/d3/d3-hexbin/blob/master/src/hexbin.js "Source")

If *context* is specified, sets the current render context and returns the hexbin. If the *context* is null, hexbin.mesh and hexbin.hexagon will return SVG path strings; if the context is non-null, hexbin.mesh and hexbin.hexagon will instead call methods on the specified context to render geometry. The context must implement the following subset of the [CanvasRenderingContext2D API](https://www.w3.org/TR/2dcontext/#canvasrenderingcontext2d):

* *context*.moveTo(*x*, *y*)
* *context*.lineTo(*x*, *y*)

If a *context* is not specified, returns the current render context which defaults to null.


<a href="#hexbin_data" name="hexbin_data">#</a> <i>hexbin</i>.<b>data</b>([<i>points</i>]) [<>](https://github.com/d3/d3-hexbin/blob/master/src/hexbin.js "Source")

If *points* is specified, resets the hexbin with the new data points. If the *points* is null, returns the current data points, in a possibly different order.

