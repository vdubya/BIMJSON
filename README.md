# BIMJSON REST API

**Version 0.1**
[GitHub repository](https://github.com/vdubya/BIMJSON)

## Draft v1

# Introduction

BIMJSON is a format for encoding the exchange of Minimal Viable BIMs (Building Information Model) (MVB) through web services. The main objective is to create a clear and easy to understand structure with a high level of flexibility for data exchanges. The objects for the building geometry are an extension of [GeoJSON](http://geojson.org/) with the addition of arcs, holes, and the extrusion of planar polygons for the 3-dimensional representation of building parts.

The main root objects consist of the basic hierarchical elements of a building:

- Site
- Building
- Space
- Component

These main objects may be expanded with additional objects to group specific features of a building into unique datasets.

# BIMJSON objects

## General

BIMJSON always consists of a single object with a name describing the values of that BIMJSON object. The "root" object contains an array to represent multiples of the main object.

Each object in this first level array contains the ID of object with predefined attributes, a "placement" member for building parts (sites, buildings, floor slabs, spaces and components/assets), a "geometry" member (sites, buildings, floor slabs, spaces), free form "properties", and "links" of different possible types.

## The Geometry Object

The Minimal Viable BIM currently focuses on the "negative space" that is created by walls and not on the walls themselves. The spaces can consist of rooms, open office areas with many cubicles or of the cubicles themselves, open spaces such as roof areas, parking areas, sport fields, or any other defined polygonal area.

The geometry object for a single building part consists of a single polygon which a "profile" containing at least 3 points and possible arcs (curves) and holes. Holes in turn can contain additional arcs. The arcs are defined by center, the index number of the polygon point where the arc starts, and the direction of the arc. The following sample code for the geometry of a single polygon is shown below in the graphic illustration.

**Example**

	{
	"geometry": {
		"type": "polygon",
		"profile": {
			"points": [
				[2.20, 0.50],
				[2.20, 1.10],
				[1.20, 1.10],
				[0.50, 1.10],
				[0.00, 1.10],
				[0.00, 0.00],
				[1.20, 0.00]
			],
			"arcs": [{
				"center": [1.94, -0.24],
				"start_index": 7,
				"is_anticlockwise": false
			}, {
				"center": [0.85, 0.96],
				"start_index": 2,
				"is_anticlockwise": true
			}],
			"holes": [{
				"points": [
					[0.20, 0.20],
					[0.50, 0.20],
					[0.50, 0.60],
					[0.20, 0.60]
				]
			}]
		}
	}
**Graphic Representation**


![Arc](https://www.onuma.com/transfer/ArcDiagram.png)

For linear and point objects it is possible to add other geometries of types point and line_string. They follow the same logic as the polygon.

## The Placement Object

Each building part is placed within the context of the canvas that it belongs to.

**A site object** is placed within the geographic coordinates of its Coordinate Reference System (CRS). BIMJSON uses the same "CRS" objects that GeoJSON is using: It can be a "named CRS" or a "linked CRS":
[CRS object in GeoJSON](http://geojson.org/geojson-spec.html#coordinate-reference-system-objects)
The default (if nothing is specified) is the named CRS CRS84, i.e. basic WGS84 degrees (same as KML/Google Earth). This can be adjusted and specified accordingly.

The "angle" is the anti-clockwise rotation of the site in relationship to North in degrees, or the clockwise rotation of the north arrow in relationship with the canvas.

**The building object** is placed relative to the site origin. The X, Y, and Z values are all relative to the longitude, latitude, and altitude of the site. The "angle" is the counter-clockwise orientation of the building relative to North.

**The floor object** is raised or lowered relative to the building elevation with the value of elevation_to_building. In the X- and Y-axis, it is never displaced relative to the building origin. Therefore the floor origin can be considered the same as the building origin’s X and Y values but displaced by the value of elevation_to_building.

**All descendants of the floor object** (slabs, spaces, and components) are placed relative to the floor origin.

## The Component Object

Components contain a placement object with a placement as well. The placement object contains an additional attribute "mirror_y" for mirrored objects. Objects mirrored over their X-axis are simply objects rotated by 180 degrees and mirrored over the Y-axis. Components contain the dimensions of the bounding box with the height, width, and depth attributes only. The term "length" was omitted on purpose due to its ambiguity relative to the coordinate system.
The concept of data transfer for Minimal Viable BIM omits the inclusion of the full geometry of components. The objects can be handled by the respective applications through the mapping of the components to local or remote libraries. In addition, a link to the actual objects can be added in the link object of the component.

## The Extrusion or Height Attribute

**The extrusion attribute** is a special attribute of the slab object. It is usually a negative value extruding the slab polygon downward from the placement of the slab within the floor object.

**The height attribute** of spaces define their height up to the ceiling. The height may be zero for open spaces without a ceiling (open fields, parking, roof, etc.)

## The Zones and Systems Arrays

The space object contains a **zones array**. Spaces can belong to one or many "zones" with specific attributes such as "circulation zones", "fire alarm zones", etc.

The component object contains a **systems array**. Components can belong to a multitude of building systems such as HVAC, Electrical, etc.

## The Link Object

All BIMJSON objects may contain link objects which can be of many types. Some links might define other web services and APIs, while others simply link to files or images.

## The Type Object

Type objects can be stored in a separate **types.json** file as a collection of identical component types found in a building, or it can be a unique object in the components object in which case it is called **component_type**. Type objects contain attribute data of component instances of the same type, e.g. model number, manufacturer, etc. to keep the component data as normalized as possible. Since the component_types can be included in the components objects, data from a data storage that is not fully normalized can be used.



















