# EXPORTING A FILE 

To export a file the option "Export data" in the menu will be used.

![Menu export](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/import-export-select.jpg)

This option allows the user to select different files to export. The steps will be the following:

## 1. Select type:

The user can select one type at the same time of the following options:

![Imagen select type](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/export-file-1.jpg)

## 2. Select Items of type:

The user can select one or more items of the selected type and click on the arrow to add them to the exported file.

![Imagen select items type](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/export-file-2.jpg)

## 3. Items ready to export:

All the selected files will be shown in the menu. The user can select other types, like the selected in step 1, to add more files.

![Imagen ready to export](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/export-file-3.jpg)

The recursive option allows the user to select one field and export all their children associated

* Example of recursive option:

We have defined the measurement linux_cpu with 4 metricss. If we use the recursive option when we select linux_cpu we will export all the childrens with it.

The following tree is how it works:
```
linux_cpu
│   linux_cpu_used_percent
│   linux_cpu_idle_percent
│   linux_cpu_system_percent
│   linux_cpu_user_percent
```
And this is the result we obtain when we export the file at the end:

![Img recursive](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/recursive.jpg)
## 4. Export:

![Imagen final expoort](https://github.com/toni-moreno/snmpcollector/blob/gh-pages/images/webUI/DataManagement/export-file-4.jpg)

* 'Tags' field is used to identify the exported data
