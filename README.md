# BIMJSON REST API

Version 0.1, [GitHub repository](https://github.com/vdubya/BIMJSON)

## Introduction
BIMJSON is a format for encoding the exchange of Minimal Viable BIMs (Building Information Model) (MVB) through web services. The main objective is to create a clear and easy to understand structure with a high level of flexibility for data exchanges. BIMJSON geometry objects are an extension of [GeoJSON](http://geojson.org/). This API adheres to the [BIMJSON REST API Guidelines](https://github.com/vdubya/BIMJSON/blob/master/BIMJSON-REST-API-Guidelines.md). BIMJSON is a collaborative effort...please submit GitHub pull requests for fixes and features for the API and Guidelines.

####Inherited Features:
- GeoJSON objects must have a member with the name `"type"`. This member's value is a string determining the type of GeoJSON object.
- GeoJSON objects may have any number of members (name/value pairs).
- Every `"feature"` object must have a `"geometry"` member and a `"properties"` member.
- Geometry objects of any type other than `"GeometryCollection"` must have a member with the name `"coordinates"`.
- The default reference system for GeoJSON coordinates is a geographic coordinate reference system, using the WGS84 datum (specified in the latest GeoJSON specification of the [IETF](https://www.ietf.org) [Section 3](https://tools.ietf.org/html/draft-butler-geojson-06#section-3)).
- A commonly used identifier of a feature should be included as a member of the feature object with the name `"id"`. 

####Extended Features:
- In addition to the values possible in the GeoJSON geometry object, BIMJSON adds another geometry type `"ComplexPolygon"` that contains one or more `"arcs"` (round shapes) (more about this extension below)
- The feature type `"feature"` has been extended with the following feature types with the same requirements as the original `"feature"` type to describe the hierarchical level within a BIM:
  - `"Site"`
  - `"Building"`
  - `"Floor"`
  - `"Space"`
  - `"Component"`
- The `"Site"`, `"Building"`, `"Floor"`, `"Space"` and `"Component"` features shall include a member containing the unique identifier of the parent object with the names `"site_id"`, `"building_id"`, `"floor_id"`, `"space_id"` and `"component_id"`.
- The `"FeatureCollection"` of GeoJSON has been extended with with a type `"AttributeCollection"` for non-geometric data collections needed to represent the data in BIM. They may be of one of the following types:
  - `"System"`
  - `"Type"` (Component Types)
  - `"Zone"`
  - `"Contact"`
  - other non-geometric data collection types
- The `"geometry"` object of the `"Building"` feature type is always a `"Point"` (also called the "insertion point" of the building).
- The coordinates in the geometry object of the `"Floor"`, `"Space"`, and `"Component"` feature type may be relative to the point of the building geometry object. The `"x"`, `"y"`, and `"z"` values are in meters displacement from the WGS84 coordinates of the point of the building geometry object (the "insertion point").
- The `"arc"` geometry specified for BIMJSON can be segmented into straight lines whenever **ALL** geometry objects are specified by the WGS84 coordinate reference system. Note: the `"arc"` geometry requires a precision of at least 6 decimal point (in meters) which is why it is not recommended to use `"arcs`" with the WGS84 datum.
- When using **ALL** WGS84 coordinates in BIMJSON, the additional `"ComplexPolygon"` geometry type is not needed and should be avoided.

## BIMJSON objects

### 1. General
- BIMJSON always consists of a single object of the type `"FeatureCollection"` or `"AttributeCollection"` with `"features"` and `"attributes"` arrays containing the respective features.
- Each feature object (`"FeatureCollection"`) shall have a geometry member.
- Each feature or attribute object (`"FeatureCollection"` and `"AttributeCollection"`) shall have a property member, a link member, and any number of additional members (name/value pairs).

### 2. The Geometry Object
- The Minimal Viable BIM currently focuses on the "negative space" that is created by walls and not on the walls themselves. The "spaces" can consist of rooms, open office areas with many cubicles or of the cubicles themselves, open spaces such as roof areas, parking areas, sport fields, or any other defined polygonal area.
- The geometry object for a single building part consists of a single polygon which a `"profile"` containing at least 3 points and possible `"arcs"` (curves) and `"holes"`. `"Holes"` in turn can contain additional `"arcs"`. The `"arcs"` are defined by `"center"`, the `"start_index"` of the polygon point where the arc starts, and `"is_anticlockwise"` as the direction of the arc. The following sample code for the geometry of a single polygon is shown below in the graphic illustration.

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

For linear and point objects it is possible to use geometries of types `"point"` and `"line_string"`. They follow the same logic as `"polygon"` except `"holes"` do not apply, and they do not use the matching start and end point convention. 

### 3. The Placement Object
Each building part is placed within the context of the canvas that it belongs to.
- A `"site"` object is placed within the geographic coordinates of its Coordinate Reference System (CRS). BIMJSON uses the same `"CRS"` objects that GeoJSON use. The CRS can be a "named CRS" or a "linked CRS":
[CRS object in GeoJSON](http://geojson.org/geojson-spec.html#coordinate-reference-system-objects). 
The default (if nothing is specified) is the named CRS CRS84, i.e. basic WGS84 degrees (same as KML/Google Earth). This can be adjusted and specified accordingly.
- The `"angle"` is the anti-clockwise rotation of the site in relationship to North in degrees, or the clockwise rotation of the north arrow in relationship with the canvas.
- The `"building"` object is placed relative to the site origin. The `"X"`, `"Y"`, and `"Z"` values are all relative to this site origin. The `"angle"` is the counter-clockwise orientation of the building.
- The `"floor"` object is raised or lowered relative to the building elevation with the value of `"elevation_to_building"`. It is never displaced relative to the building origin in the X- and Y-axis. Therefore the floor origin can be considered the same as the building origin’s `"X"` and `"Y"` values but displaced by the value of `"elevation_to_building"`.
- All descendants of the floor object (`"slabs"`, `"spaces"`, and `"components"`) are placed relative to the `"floor"` origin.

### 4. The Type Object
- `"Type"` objects can be stored in a separate **types.json** file as a collection of identical components found in a building, or it can be a unique object in the `"components"` object in which case it is called `"component_type"`. Type objects contain attribute data of component instances of the same type, e.g. model number, manufacturer, etc. to keep the component data as normalized as possible. Since the `"component_types"` can be included in the component's objects, data from a data storage that is not fully normalized can be used.

### 5. The Component Object
- Components contain a placement object. The placement object contains an additional attribute `"mirror_y"` for mirrored objects. Objects mirrored over their X-axis are simply objects rotated by 180 degrees and mirrored over the Y-axis. Components contain the dimensions of the bounding box with the `"height"`, `"width"`, and `"depth"` attributes only. The term `"length"` was omitted on purpose due to its ambiguity relative to the coordinate system.
- The concept of data transfer for Minimal Viable BIM omits the inclusion of the full geometry of components. The objects can be handled by the respective applications through the mapping of the components to local or remote libraries. In addition, a link to the actual objects can be added in the link object of the component.

### 6. The Extrusion or Height Attribute
- The `"extrusion"` attribute is a special attribute of the `"slab"` object. It is usually a negative value extruding the slab polygon downward from the placement of the slab within the floor object.
- The `"height"` attribute of spaces define their height up to the ceiling. The `"height"` may be zero for open spaces without a ceiling (open fields, parking, roof, etc.)

### 7. The Zones and Systems Arrays
- The `"space"` object contains a `"zones"` array. Spaces can belong to one or many `"zones"` with specific attributes such as "circulation zones", "fire alarm zones", etc.
- The component object contains a `"systems"` array. Components can belong to a multitude of building systems such as HVAC, Electrical, etc.

### 8. The Link Object
- All BIMJSON objects may contain link objects which can be of many types. Some links might define other web services and APIs, while others simply link to files or images.
