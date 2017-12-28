# SLA 3D Printer Control File Standard

The purpose of this repository is to document a file standard for conveying 3D print information to a SLA 3D printer for how to process each individual image of a print. Traditionally SLA 3D printers only expose a single image per layer, and have the same layer thickness for each layer. Control files following the format we outline in this repository will allow each layer to be comprised of multiple images, with the ability to customize what each image's exposure time is, as well as customizing each layer's thickness.

Overall, following this file standard gives a much higher degree of control over how a design is printed compared to traditional 3D printing methods.

### Example API

For convenience, a python class that contains a basic API for interfacing with the file format has been included in the repository (see `ControlFile.py`). It should be straightforward to incorporate into a preexisting python project, however, due to the inherent variability between 3D printer setups, values in the main dictionary that relate directly to the 3D printing mechanism itself have intentionally been left undefined so that the standard can be adapted to any 3D printing mechanism that offers the required operational flexibility.

## File format and explanation

This information can also be found in `example.json`
```
{
  "Design": {
    "Purpose": "String with short statement about design's purpose.",
    "Description": "String containing description of design to be printed with this JSON file. Could be multi-line by using '\n' to separate lines.",
    "Resin": "Resin that this design is intended to be used with. Example: PEGDA with 2% NPS and 1% Irgacure 819.",
    "3D printer": "3D printer that this design is intended to be printed on.",
    "Design file": "<path/filename> (OpenSCAD or other 3D CAD file containing design)",
    "STL file": "<path/filename>",
    "Slicer": "Specify which slicer was used to create png images from STL file.",
    "Date": "Date file was sliced."
  },
  "Header": {
    "Comment": "This section contains information about the schema and the directory where to find png files,
    which is specified relative to the directory in which this json file resides.If the json file
    is in the same directory as the png images, this would be `.`",
    "Schema version": "0.1",
    "png directory": "images",
  },
  "Printer settings": {
    "Comment": "This section contains general printer settings. Generally speaking, this can be broken up into the settings for the optical engine,
    the build stage and the separation mechanism.Depending on the 3 D printer,
    some of these settings maybe part of a single mechanism which share the same settings
    (e.g.the build stage and the separation mechanism).It is up to the user to define how partition these settings,
    based on what makes sense for their 3 D printer. Below is an example of the general
    case.

    Also these settings can be redefined on a per layer basis by defining the setting name in the specfic layer.Because there
    is no way to define what sub category a setting belongs to inside of layer,
    it is important that all settings have unique key names in relation to the 'Default layer settings'
    and any of the sub categories under 'Printer settings'",
    "Optical Engine":
      {
        "Power setting": "100",
        ...
        etc.
      },
      "Build Stage": {
        "Movement Speed": "400",
        ...
        etc.
      },
      "Separation Mechanism": {
        "Movement Speed": "400",
        ...
        etc.
      }
  },
  "Layers": {
    "Comment:": "This section contains information for each of the layers to be exposed.
    In the example below: burn in layers 0 - 3,
    channel in layers 7 - 9 with multiple doses,
    channel ceiling in layers 10 - 12 with multiple doses,
    different layer thickness and multiple doses in layers 13 - 15.
    The idea is that general settings are specified and are used for all layers except when they are
    overridden when a particular layer contains any of those particular settings.",
    "Default layer settings": {
      "Comment": "Unless otherwise defined in the layer, these are the values that are to be used for each layer.",
      "Layer thickness (microns)": "10",
      "Layer exposure time (ms)": "400",
      "Number of duplications": "0"
    },
    "0": {
      "png files": ["path/filename000.png"],
      "exposure times (ms)": ["20000"],
      "custom thickness": "20",
      "Comment": "This layer has defined a custom thickness, which will impact the distance that the build platform moves."
    },
    "1": {
      "png files": ["path/filename000.png"],
      "exposure times (ms)": ["10000"],
      "custom duplications": "2",
      "Comment": "This layer needs to be reproduced twice beyond the initial exposure."
    },
    "2": {
      "png files": ["path/filename000.png"],
      "exposure times (ms)": ["5000"],
      "Power Setting": "200",
      "Comment": "This layer requires the LED power setting to be higher than the default."
    },
    "3": {
      "png files": ["path/filename000.png"],
      "exposure times (ms)": ["1000"]
    },
    "4": {
      "png files": ["path/filename001.png"]
    },
    "5": {
      "png files": ["path/filename002.png"]
    },
    "6": {
      "png files": ["path/filename003.png"]
    },
    "7": {
      "png files": ["path/filename004.png", "path/filename004a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel layer 1"
    },
    "8": {
      "png files": ["path/filename005.png", "path/filename005a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel layer 2"
    },
    "9": {
      "png files": ["path/filename006.png", "path/filename006a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel layer 3"
    },
    "10": {
      "png files": ["path/filename007.png", "path/filename007a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel ceiling layer 1"
    },
    "11": {
      "png files": ["path/filename008.png", "path/filename008a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel ceiling layer 2"
    },
    "12": {
      "png files": ["path/filename009.png", "path/filename009a.png"],
      "exposure times (ms)": ["400", "200"],
      "Comment": "Channel ceiling layer 3"
    },
    "13": {
      "Layer thickness (microns)": "5",
      "png files": ["path/filename010.png", "path/filename010a.png"],
      "exposure times (ms)": ["250", "150"]
    },
    "14": {
      "Layer thickness (microns)": "7.5",
      "png files": ["path/filename011.png", "path/filename011a.png"],
      "exposure times (ms)": ["300", "175"]
    },
    "15": {
      "Layer thickness (microns)": "8",
      "png files": ["path/filename012.png", "path/filename012a.png"],
      "exposure times (ms)": ["350", "190"]
    },
    "16": {
      "png files": ["path/filename013.png"]
    },
    "17": {
      "png files": ["path/filename014.png"]
    },
    "18": {
      "png files": ["path/filename015.png"]
    }
  }
}

```

<a rel="license" href="http://creativecommons.org/licenses/by/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by/4.0/88x31.png" /></a><br />This work is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by/4.0/">Creative Commons Attribution 4.0 International License</a>.
