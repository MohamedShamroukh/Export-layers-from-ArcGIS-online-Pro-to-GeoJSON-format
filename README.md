# Export-layers-from-ArcGIS-online-Pro-to-GeoJSON-format
Export layers from ArcGIS Pro to GeoJSON format with ease. Bypasses common OBJECTID field compatibility issues.
# ArcGIS Pro Layer Exporter

A lightweight Python tool for exporting layers from ArcGIS Pro to GeoJSON format. Built to solve common field compatibility issues (ERROR 002809) encountered when exporting to other formats like GeoPackage.

Perfect for researchers and GIS professionals who need to:
- Share spatial data in web-friendly formats
- Prepare data for interactive visualisations
- Avoid OBJECTID field type compatibility errors
- Automate export workflows in ArcGIS Pro

## Features

✅ **Simple Configuration** - Just specify layer name and output folder  
✅ **Error-Free Exports** - Bypasses OBJECTID field compatibility issues  
✅ **Formatted Output** - Human-readable, indented GeoJSON  
✅ **Batch Processing** - Export multiple layers at once  
✅ **Auto-Detection** - Lists available layers if name not found  
✅ **Zero Dependencies** - Uses only ArcGIS Pro's built-in `arcpy`

## Requirements

- **ArcGIS Pro** 2.8 or higher
- **Python** with `arcpy` (included with ArcGIS Pro installation)

## Installation

### Option 1: Clone the Repository

```bash
git clone https://github.com/YOUR_USERNAME/arcgis-layer-exporter.git
cd arcgis-layer-exporter
Option 2: Download Script Only
Download export_layer.py and place it in your project folder.

Quick Start
1. Basic Single Layer Export
Open your ArcGIS Pro project and edit the configuration in export_layer.py:

python
# --- User Configuration ---
input_layer_name = "Domestic"  # Your layer name from Contents pane
output_folder = r"C:\Your\Output\Path"
Then run the script from:

ArcGIS Pro Python window

Python Command Prompt (with ArcGIS Pro environment activated)

Your preferred IDE (configured with ArcGIS Pro Python)

2. Run from ArcGIS Pro Python Window
python
exec(open(r'C:\path\to\export_layer.py').read())
3. Batch Export Multiple Layers
Use the provided example:

python
from export_layer import export_layer_to_geojson

layers = ["Domestic", "International", "Commercial"]
output_folder = r"C:\Your\Output\Path"

for layer in layers:
    export_layer_to_geojson(layer, output_folder)
Usage Examples
Example 1: Export Single Layer
python
import arcpy
from export_layer import export_layer_to_geojson

# Export a layer
result = export_layer_to_geojson(
    layer_name="Building_Footprints",
    output_path=r"C:\GIS_Data\Exports"
)

if result:
    print(f"Exported to: {result}")
Example 2: Export All Layers in Active Map
python
import arcpy
from export_layer import export_layer_to_geojson

aprx = arcpy.mp.ArcGISProject("CURRENT")
active_map = aprx.activeMap
output_folder = r"C:\Exports"

for layer in active_map.listLayers():
    if layer.isFeatureLayer:
        export_layer_to_geojson(layer.name, output_folder)
Example 3: Export with Custom Naming
python
import arcpy
import os
from export_layer import export_layer_to_geojson

# Export and rename
layer_name = "Domestic"
temp_output = r"C:\Temp"

result = export_layer_to_geojson(layer_name, temp_output)

if result:
    # Rename with timestamp
    from datetime import datetime
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    new_name = f"{layer_name}_{timestamp}.geojson"
    new_path = os.path.join(temp_output, new_name)
    os.rename(result, new_path)
    print(f"Saved as: {new_path}")
Configuration Options
Parameter	Type	Description	Example
input_layer_name	str	Layer name from Contents pane (case-sensitive)	"Domestic"
output_folder	str	Destination folder path (created if doesn't exist)	r"C:\Data\Exports"
Output Format
File Type: GeoJSON (.geojson)
Coordinate System: Preserves original layer CRS
Formatting: Human-readable with indentation
Naming: Automatically uses layer name (e.g., LayerName.geojson)

Sample Output Structure
json
{
  "type": "FeatureCollection",
  "features": [
    {
      "type": "Feature",
      "geometry": {
        "type": "Point",
        "coordinates": [-1.5243, 52.7681]
      },
      "properties": {
        "name": "Example Location",
        "category": "Domestic"
      }
    }
  ]
}
Troubleshooting
❌ Layer Not Found Error
Error Message:

text
Error: Layer named 'X' not found in the active map.
Solutions:

Check layer name spelling (case-sensitive)

Ensure layer is in the active map (not just project)

Use the listed available layers from error output

Verify layer is a feature layer (not raster or group layer)

❌ Permission Denied
Error Message:

text
PermissionError: [Errno 13] Permission denied
Solutions:

Close any programs with the output file open (QGIS, text editors, etc.)

Check you have write permissions for the output folder

Try a different output location (e.g., Desktop)

Run ArcGIS Pro as administrator

❌ Geometry Errors
Error Message:

text
ERROR 000117: Invalid geometry
Solutions:

Use ArcGIS Pro's Check Geometry tool

Run Repair Geometry tool before export

Check for null geometries in your layer

❌ No Active Map
Error Message:

text
Error: No active map found
Solutions:

Open a map in your ArcGIS Pro project

Click on the map to make it active

Ensure you're running the script with a project open

Why GeoJSON Over GeoPackage?
This tool exports to GeoJSON instead of GeoPackage to avoid ERROR 002809: Field OBJECTID is of an unsupported type which commonly occurs when exporting directly to GeoPackage format.

Advantages of GeoJSON
Feature	GeoJSON	GeoPackage
Web mapping support	✅ Excellent	⚠️ Requires conversion
Human-readable	✅ Yes	❌ Binary format
JavaScript libraries	✅ Native support	⚠️ Limited
Field compatibility	✅ No OBJECTID issues	❌ Common errors
File size	⚠️ Larger (text)	✅ Compressed
Multiple layers	❌ One per file	✅ Multiple layers
Best for: Web visualisations, Leaflet/Mapbox, research sharing, GitHub repositories

Not ideal for: Very large datasets (>100MB), multi-layer packages

Advanced Usage
Create an ArcGIS Pro Toolbox
Create a new Python Toolbox (.pyt)

Add this as the tool script:

python
import arcpy

class ExportToGeoJSON(object):
    def __init__(self):
        self.label = "Export Layer to GeoJSON"
        self.description = "Export a feature layer to GeoJSON format"
        
    def getParameterInfo(self):
        # Layer parameter
        param0 = arcpy.Parameter(
            displayName="Input Layer",
            name="input_layer",
            datatype="GPFeatureLayer",
            parameterType="Required",
            direction="Input"
        )
        
        # Output folder parameter
        param1 = arcpy.Parameter(
            displayName="Output Folder",
            name="output_folder",
            datatype="DEFolder",
            parameterType="Required",
            direction="Input"
        )
        
        return [param0, param1]
    
    def execute(self, parameters, messages):
        input_layer = parameters.valueAsText
        output_folder = parameters.valueAsText
        
        output_name = f"{arcpy.Describe(input_layer).baseName}.geojson"
        output_path = os.path.join(output_folder, output_name)
        
        arcpy.conversion.FeaturesToJSON(
            input_layer,
            output_path,
            format_json="FORMATTED",
            geoJSON="GEOJSON"
        )
        
        messages.addMessage(f"Exported to: {output_path}")
Integration with Other Tools
Export and automatically upload to web services:

python
from export_layer import export_layer_to_geojson
import requests

# Export
geojson_path = export_layer_to_geojson("MyLayer", r"C:\Temp")

# Upload to web service
if geojson_path:
    with open(geojson_path, 'r') as f:
        data = f.read()
    
    response = requests.post(
        'https://your-api.com/upload',
        files={'file': data}
    )
