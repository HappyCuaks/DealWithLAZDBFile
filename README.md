
# DealWithLAZDBFile - Step by Step guide

## Table of contents
* [Description](###Description)
* [Context](###Context)
* [Tools](###Tools)
* [Step by Step](###Step by Step)

##Description
This document explanins how I dealt with .laz.db file in Windows and visualized the point cloud inside.

##Context
I received the file that you can see in this repo called Batimetry.laz.db. However, the file had a weird .laz.db extension which I could not figure out how to handle. I asked colleagues, chatgpt and consulted forums, but anz of the tools (like pdal) were helping me to reach my objective: Visualize the point cloud.

##Tools
* [QGIS](https://docs.qgis.org/): Useful for plotting maps and work with them. In this case we can use it to view .las files
* [OSGeo4W](https://trac.osgeo.org/osgeo4w/): Contains packages that will be useful for working with point cloud files. Such as PDAL
* [LASTools](https://github.com/LAStools/LAStools): Needs to be build. However compiled binaries can be found. They provide a specific set of tools to work with point cloud files.
* [SQLiteStudio](https://sqlitestudio.pl/): This tools allow us to open, edit and browse through SQLite databases

###Step by Step
First I examined the contents of Batimetry.laz.db using SQLiteStudio. As I can see, there is one table called "data" that has relevant information, containing points xyz. Therefore, using SQLiteStudio, I exported this table into the file BatimetryData.csv. From all the data inside this file, I only needed the xyz data so I deleted the columns that I weren't going to use. Then, I created a .txt file by duplicating BatimetryData.csv and changing its extension to .txt. Using Sublime3 (although any text editor can do the job), I replaced the table separators by blank spaces. You can check the resulting file in BatimetryData.txt.
Once I had the .txt with xyz data, I proceeded to open a windows command prompt in the same directory where my files are. Using txt2las tool, I executed the next command:
```
~$>txt2las.exe -i BatimetryData.txt -o Batimetry.las -parse xyz -utm 14T
```
However, QGIS was not happy with this .las file as the xyz scale was a bit messed. Using OSGeo4W Shell, you can fix this issue and modify the scaling factors according to your requirements:
```
~$>pdal translate Batimetry.las BatimetryModified.las --writers.las.scale_x=.0001 --writers.las.scale_y=.0001 --writers.las.scale_z=.0001
```
This time, QGIS managed to open the file. Plotting what you can see in PlotImage.jpg.