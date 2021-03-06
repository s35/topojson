# TopoJSON

**TopoJSON** is an extension of GeoJSON that encodes topology. Rather than representing geometries discretely, geometries in TopoJSON files are stitched together from shared line segments called *arcs*. This technique is similar to [Matt Bloch’s MapShaper](http://www.cartogis.org/docs/proceedings/2006/bloch_harrower.pdf
) and the [Arc/Info Export format, .e00](http://indiemaps.com/blog/2009/02/e00parser-an-actionscript-3-parser-for-the-arcinfo-export-topological-gis-format/).

TopoJSON eliminates redundancy, allowing related geometries to be stored efficiently in the same file. For example, the shared boundary between California and Nevada is represented only once, rather than being duplicated for both states. A single TopoJSON file can contain multiple feature collections without duplication, such as states and counties. Or, a TopoJSON file can efficiently represent both polygons (for fill) and boundaries (for stroke) as two feature collections that share the same arc mesh.

As a result, TopoJSON is substantially more compact than GeoJSON. The above shapefile of U.S. counties is 2.2M as a GeoJSON file, but only [436K](https://bl.ocks.org/mbostock/4090870) as a boundary mesh, a reduction of 80.4% even without simplification. TopoJSON can also be more efficient to render since shared control points need only be projected once. To further reduce file size, TopoJSON uses fixed-precision delta-encoding for integer coordinates rather than floats. This is similar to rounding coordinate values (e.g., [LilJSON](https://github.com/migurski/LilJSON)), but with greater precision. Like GeoJSON, TopoJSON files are easily modified in a text editor and amenable to gzip compression.

Lastly, encoding topology has numerous useful applications for maps and visualization. It allows [topology-preserving shape simplification](https://github.com/topojson/topojson-simplify), which ensures that adjacent features remain connected after simplification; this applies even across feature collections, such as simultaneous consistent simplification of state and county boundaries. Topology can also be used for [Dorling](http://www.ncgia.ucsb.edu/projects/Cartogram_Central/types.html) or [hexagonal cartograms](http://pitchinteractive.com/latest/tilegrams-more-human-maps/), as well as other techniques that need shared boundary information such as [automatic map coloring](https://bl.ocks.org/4188334).

## Installing

If you use NPM, `npm install topojson`. Otherwise, download the [latest release](https://github.com/topojson/topojson/releases/latest). You can also load directly from [unpkg](https://unpkg.com). AMD, CommonJS, and vanilla environments are supported. In vanilla, a `topojson` global is exported:

```html
<script src="https://unpkg.com/topojson@2"></script>
<script>

var topology = topojson.topology({foo: geojson});

</script>
```

[Try topojson in your browser.](https://tonicdev.com/npm/topojson)

## API Reference

<a name="topology" href="#topology">#</a> topojson.<b>topology</b>(<i>objects</i>[, <i>quantization</i>])

… This is a destructive operation!

## Command-Line Reference

<a name="geo2topo" href="#geo2topo">#</a> <b>geo2topo</b> [<i>options…</i>] &lt;name=file&gt;… [<>](https://github.com/topojson/topojson/blob/master/bin/geo2topo "Source")

Converts one or more GeoJSON objects to an output topology. For example, to convert the us-states-10m.json GeoJSON FeatureCollection to a TopologyJSON topology with the “states” object in us-10m.json:

```
geo2topo states=us-states-10m.json > us-10m.json
```

For convenience, you can omit the object name and specify only the file *name*; the object name will be the basename of the file, with the directory and extension removed. For example, to convert the states.json GeoJSON FeatureCollection to a TopologyJSON topology with the “states” object in us-10m.json:

```
geo2topo states.json > us-10m.json
```

<a name="geo2topo_help" href="#geo2topo_help">#</a> geo2topo <b>-h</b>
<br><a href="#geo2topo_help">#</a> geo2topo <b>--help</b>

Output usage information.

<a name="geo2topo_version" href="#geo2topo_version">#</a> geo2topo <b>-V</b>
<br><a href="#geo2topo_version">#</a> geo2topo <b>--version</b>

Output the version number.

<a name="geo2topo_newline_delimited" href="#geo2topo_newline_delimited">#</a> geo2topo <b>-n</b>
<br><a href="#geo2topo_newline_delimited">#</a> geo2topo <b>--newline-delimited</b>

Output [newline-delimited JSON](http://ndjson.org/), with one feature per line.

<a name="geo2topo_in" href="#geo2topo_in">#</a> geo2topo <b>-i</b> <i>file</i>
<br><a href="#geo2topo_in">#</a> geo2topo <b>--in</b> <i>file</i>

Specify the input TopoJSON file name. Defaults to “-” for stdin.

<a name="geo2topo_list" href="#geo2topo_list">#</a> geo2topo <b>-l</b>
<br><a href="#geo2topo_list">#</a> geo2topo <b>--list</b>

List the names of the objects in the input topology, and then exit. For example, this:

```
geo2topo -l < us-10m.json
```

Will output this:

```
counties
states
land
```
