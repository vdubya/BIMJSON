# BIMJSON REST API

Version 0.2, [GitHub repository](https://github.com/vdubya/BIMJSON)

## Introduction
BIMJSON is a format for encoding the exchange of Minimal Viable BIMs (Building Information Model) (MVB) through web services. The main objective is to create a clear and easy to understand structure with a high level of flexibility for data exchanges. BIMJSON geometry objects are an extension of [GeoJSON](http://geojson.org/). This API adheres to the [BIMJSON REST API Guidelines](https://github.com/vdubya/BIMJSON/blob/master/BIMJSON-REST-API-Guidelines.md). BIMJSON is a collaborative effort...please submit GitHub pull requests for fixes and features for the API and Guidelines.

#### Inherited Features:
- GeoJSON objects must have a member with the name `"type"`. This member's value is a string determining the type of GeoJSON object.
- GeoJSON objects may have any number of members (name/value pairs).
- Every `"feature"` object must have a `"geometry"` member and a `"properties"` member.
- Geometry objects of any type other than `"GeometryCollection"` must have a member with the name `"coordinates"`.
- The default reference system for GeoJSON coordinates is a geographic coordinate reference system, using the WGS84 datum (specified in the latest GeoJSON specification of the [IETF](https://www.ietf.org) [Section 3](https://tools.ietf.org/html/draft-butler-geojson-06#section-3)).
- A commonly used identifier of a feature should be included as a member of the feature object with the name `"id"`. 

#### Extended Features:
- In addition to the values possible in the GeoJSON geometry object, BIMJSON adds another geometry type `"ComplexPolygon"` that contains one or more `"arcs"` (round shapes) (more about this extension below)
- The feature type `"Feature"` has been extended with the following feature types with the same requirements as the original type to describe the hierarchical level within a BIM:
  - `"Site"`
  - `"Building"`
  - `"Floor"`
  - `"Space"`
  - `"Component"`
- The first `"properties"` attribute specifies the above `"featureType"`
- The `"Site"`, `"Building"`, `"Floor"`, `"Space"` and `"Component"` features shall include a property containing the unique identifier of the parent object with the names `"site_id"`, `"building_id"`, `"floor_id"`, `"space_id"` and `"component_id"`.
- The `"FeatureCollection"` of GeoJSON has been extended with with a type `"AttributeCollection"` for non-geometric data collections needed to represent the data in BIM. They may be of one of the following `"attributeType"`:
  - `"System"`
  - `"ComponentType"`
  - `"Zone"`
  - `"Contact"`
  - possibly other non-geometric data collection types
- The `"geometry"` object of the `"Building"` feature type is always a `"Point"` (also called the "insertion point" of the building).
- The coordinates in the geometry object of the `"Floor"`, `"Space"`, and `"Component"` feature type may be relative to the point of the building geometry object. The `"x"`, `"y"`, and `"z"` values are the displacement in meters from the WGS84 coordinates of the point of the building geometry object (the "insertion point").

## BIMJSON objects

### 1. General
- BIMJSON always consists of a single object of the type `"FeatureCollection"` or `"AttributeCollection"` with `"features"` and `"attributes"` arrays containing the respective features.
- Each feature object (`"FeatureCollection"`) shall have a geometry member.
- Each feature or attribute object (`"FeatureCollection"` and `"AttributeCollection"`) shall have a property member.

### 2. The Geometry Object
- The Minimal Viable BIM currently focuses on the "negative space" that is created by walls and not on the walls themselves. The "spaces" can consist of rooms, open office areas with many cubicles or of the cubicles themselves, open spaces such as roof areas, parking areas, sport fields, or any other defined polygonal area.
- The geometry object for a single building part contains a geometry type with a `"coordinates"` array OR it constains a `"GeometryCollection"` with multiple geometries.
- GeoJSON `"Polygons"` contain an array of coordinates for a polygon without a hole or multiple arrays of coordinates where the first array is the outer ring of the polygon followed by arrays for the holes.
- The outer ring of `"coordinates"` for `"Floors"` and `"Spaces"` and the `"coordinates"` for the point geometry of `"Components"` always consist of dimensions for all three axes. `"Holes"` (inner rings) and `"arcs"` only contain `"X"` and `"Y"` values.
- A BIMJSON `"ComplexPolygon"` contains an array of coordinates followed by an array of `"arc"` objects for curved edges. This may be followed by an array of `"hole"` objects which in turn may contain `"arc"` objects as well. The `"arcs"` are defined by `"center"`, the `"start_index"` of the polygon point where the arc starts, and `"is_anticlockwise"` as the direction of the arc. The following examples show the GeoJSON `"Polygon"` with a hole and the BIMJSON `"ComplextPolygon"`. A graphic illustration for the constructions of arcs is shown below.
- The outer and inner ring of Polygons, MultiPolygons, and ComplexPolygons shall always be closed, i.e. the first point must be repeated at the end of the coordinates.

**Example `"Polygon"` with hole**

	"geometry": {
		"type": "Polygon",
		"coordinates": [
			[
				[15.3523, 45.0032, 5.2000],
				[15.3523, 27.3254, 5.2000],
				[-15.3523, 27.3254, 5.2000],
				[-15.3523, 45.0032, 5.2000],
				[15.3523, 45.0032, 5.2000]
			],
			[
				[10.3523, 40.0032],
				[10.3523, 20.3254],
				[-10.3523, 20.3254],
				[-10.3523, 40.0032],
				[10.3523, 40.0032]
			]
		]
	}


**Example `"ComplexPolygon"` with curves**

	"geometry": {
		"type": "ComplexPolygon",
		"coordinates": [
			[2.20, 0.50, 5.2000],
			[2.20, 1.10, 5.2000],
			[1.20, 1.10, 5.2000],
			[0.50, 1.10, 5.2000],
			[0.00, 1.10, 5.2000],
			[0.00, 0.00, 5.2000],
			[1.20, 0.00, 5.2000],
			[2.20, 0.50, 5.2000]
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
			"coordinates": [
				[0.20, 0.20],
				[0.50, 0.20],
				[0.50, 0.60],
				[0.20, 0.60],
				[0.20, 0.20]
			]
		}]
	}
**Graphic Representation**


![Arc](https://www.onuma.com/transfer/ArcDiagram.png)

For linear and point objects it is possible to use geometries of types `"point"` and `"line_string"`. 

### 3. The Coordinate System
- A `"Site"` and `"Building"` objects are always longitude/latitude coordinates based on the WGS84 datum (the default of GeoJSON, KML, and others)
- The coordinates in the geometry object of the `"Floor"`, `"Space"`, and `"Component"` feature type may be relative to the point of the building geometry object. The `"x"`, `"y"`, and `"z"` values are the displacement in meters from the WGS84 coordinates of the point of the building geometry object (the "insertion point").
- In additon to the use of the **orthogonal local coordinate system**, applications which support BIMJSON exchanges may also use **a GeoJSON flavor by using transformations** to convert all local coordinates into WGS84 coordinates. Whenever the coordinates are transformed to make them useful in a GIS environment the following rules shall be applied: 
  - The `"arc"` geometry specified for BIMJSON shall be segmented into straight lines (Note: the `"arc"` geometry requires a precision of at least 6 decimal point in meters which is why it is not recommended to use `"arcs`" with the WGS84 datum.)
  - The additional `"ComplexPolygon"` geometry type used to allow arcs is not needed and shall be avoided.

### 4.1 BIMJSON "root" Objects
- Each BIMJSON "root" object (outermost level) is either an object of type `"FeatureCollection"` or of type `"AttributeCollection"` containing an array of "features" or "attributes" respectively.
- Each `""feature"` or `"attribute"` object must have the following members:
  - `"type"`: an attribute specifying the level in the building hierarchy or the type of data.
  - `"id"`: an identifier attribute which can be used by the other objects to create a relationship.
  - `"date_created"` and `"date_modified"` attributes in a complete ISO 8601 format (1994-11-05T13:15:30Z or 1994-11-05T08:15:30-05:00)
  - `"name"` attribute
  - `"category"` attribute
  - `"properties"`: single object containing NULL or any number of property attributes and objects
  - `"links"`: an array of objects containing links to files, web services, comments, etc. Each "link" object contains a `"type"`, a `"name"`, and a `"url"` atttrbute and a `"properties"` object.


### 4.2. FeatureCollection
- A `"FeatureCollection"` is of `"featureType"` `"Site"`, `"Building"`, `"Floor"`, `"Space"`, or `"Component"` in this hierarchical sequence.
- Except fo the `"Site"`, each of these objects contains the foreign key id of its parent object in the following format: [parent]_id (e.g. site_id, building_id, etc.).
- Each of these objects must have a member with the name `"geometry"`.
- Each of these objects has a fixed set of attibutes in the `"properties"` and may have other member objects with additional building data.

### 4.3. AttributeCollection
- An `"AttributeCollection"` of type `"System"`, `"Zone"`, `"Contact"`, `"ComponentType"` and possibley others contain non-geometric building data.

### 5.1 The Site Feature
- The coordinates in the `"Site"` object are always longitude/latitued coordinates based on the WGS84 datum.
- The object has the standard members and attributes of the `"FeatureCollection"`.

### 5.2 The Building Feature
- The `"geometry"` object of the `"Building"` is of type `"Point"` with world coordinates based on the WGS84 datum.
- This `"Point"` serves as the point of origin of the orthogoanal local coordinate system within the building for BIMJSON objects.
- In addition to the standard members of a `"FeatureCollection"`, the `"Building"` object contains the following `"properties"`:
  - `"view_angle"`: the clockwise rotation of the view-port in radians to allow the building and its children to be displayed in an orthogonal layout (i.e. the building is rotated anti-clockwise to fit the orthogonal view-port). This angle does not have any impact on the local coordinates. **It's for viewing purposes only**.

### 5.3 The Floor Feature
- The `"geometry"` object of the `"Floor"` creates one or more polygons for one or more floor slabs. In the case of multiple slabs on a single floor, the `"geometry"` object shall be either a `"MultiPolygon"` object or a `"GeometryCollection"` containing an array of geometries for the various slabs.
- For the standard BIMJSON object, the coordinates are orthogonal metric using the local building point as origin. The outer ring of the coordinates shall have three dimensions per point (`"x"`, `"y"`, and `"z"`).
- each polygon shall have an negative extrusion for the thickness of the slab and a properties object.
- In addition to the standard members of a `"FeatureCollection"`, `"Floor"` objects shall have 3 additional `"properties"`:
  - `"is_ground_floor"`
  - `"elevation_to_building"` (floor elevation relative to building geometry)
  - `"height"` (floor-to-floor height)

### 5.4 The Space Feature
- The `"geometry"` object of the `"Space"` consists of a single geometry of either the `"Polygon"` type for spaces without curved edges or the `"ComplexPolygon"` type if curves are required.
- For the standard BIMJSON object, the coordinates are orthogonal metric using the local building point as origin. The outer ring of the coordinates shall have three dimensions per point (`"x"`, `"y"`, and `"z"`).
- In addition to the standard members of a `"FeatureCollection"`, `"Space"` objects shall have 2 additional `"properties"` and a additional object:
  - `"number"`
  - `"height"` (room/space height)
  - `"label_placement"` object with `"z"` and `"y"` coordinates.
- `"Space"` objects may have an array member called `"zones"` containing objects with at least a `"zone_id"` as a foreign key to the objects in the `"AttributeColletion"` of type `"Zone"`. For data exchanges, these objects may also contain all the attributes and objects from the related `"Zone"` type BIMJSON object.

### 5.5 The Component Feature
- The `"geometry"` object of the `"Component"` is a `"Point"` type with coordinates for all three axes. In addition, it contains an `"angle"` attribute with the counterclockwise rotation of the object in radians and a `"mirror_y"` attribute that can be set to `"true"` or `"false"`. Note: Mirroring over the X-axis is not needed since the combination of a rotation with a mirroring over the Y-axis leads to the same result.
- For the standard BIMJSON object, the coordinates are orthogonal metric using the local building point as origin.
- In addition to the standard members of a `"FeatureCollection"`, `"Component"` objects shall have 7 additional `"properties"`:
  - `"unique_mark"`: the plan identifier of the object (also called Mark or Symbol).
  -  `"height"`, `"width"`, and `"depth"` for the bounding box of the components.
  - `"serial number"`
  - `"installation_date"` in a ISO 8601 format (e.g. 1997-07-16)
  - `"bar_code"`
- `"Component"` objects may have an array member called `"systems"` containing objects with at least a `"system_id"` as a foreign key to the objects in the "AttributeColletion" of type `"System"`. For data exchanges, these objects may also contain all the attributes and objects from the related `"System"` type BIMJSON object.
- `"Component"` objects may have a single object member called `"component_type"` containing at least a `"component_type_id"` as a foreign key to the objects in the `"AttributeColletion"` of type `"ComponentType"`. For data exchanges, this object may also contain all the attributes and objects from the related `"ComponentType"` BIMJSON object.

### 6.1. The ComponentType Attribute
- The non-geometric data of identical components in a building is stored in an `"AttributeCollection"` of `"featureType"` `"ComponentType"`
- As an alternative to being transmitted in a separate **compnent_types.json** file, a single object of this type can be included with each "Component" feature.
- In addtion to the standard members of an `"AttributeCollection"`, `"ComponentType"` objects shall have 2 additional attributes:
  - `"model_number"`
  - `"manufacturer_id"` (a unique ID in contacts.json)

### 6.2. The System Attribute
- The non-geometric data of building systems (electical, plumbing, HVAC, etc.) in a building is stored in an `"AttributeCollection"` of `"featureType"` `"System"`
- As an alternative to being transmitted in a separate **sytems.json** file, an array of objects of this type can be included with each `"Component"` feature.
- The object has the standard members and attributes of the `"AttributeCollection"`.

### 6.3. The Zone Attribute
- The non-geometric data of zones (fire zones, HVAC zones, circulation zones, departments, functional areas, etc.) in a building is stored in an `"AttributeCollection"` of `"featureType"` `"Zone"`
- As an alternative to being transmitted in a separate **zones.json** file, an array of objects of this type can be included with each `"Space"` feature.
- The object has the standard members and attributes of the `"AttributeCollection"`.

### 6.4. The Contact Attribute
- The non-geometric data of contacts associated with a building (planners, owners, contractors, manufacturers, warrantors, etc.) is stored in an `"AttributeCollection"` of `"featureType"` ``"Contact"``
- In addtion to the standard members of an `"AttributeCollection"`, `"ComponentType"` objects shall have 3 additional attributes:
  - `"www"` (a unique web address or email)
  - `"phone"` (main phone number of contact)
