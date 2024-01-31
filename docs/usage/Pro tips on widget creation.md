# Pro tips on widget creation
Previously, the creation of widgets has been covered. To help users being more confident in creating widgets, here are some details to master the widget creation.

## How to choose the appropriate widget for your use case?

We can classify the widgets in 3 different types:

- **Single dimension widgets:** use these widgets when you would like to display information about one single type of object (entity or relation).
    - **widgets: number, list, list (distribution), timeline, donuts, radar, map, bookmark, tree map**
    - use case example: view the amount of malware in platform (*number widget)*, view the top 10 threat actor group target a specific country (*distribution list widget*)…

- **Multi dimension widgets:** use these widgets if you would like to compare or have some insights about similar types of object (up to 5).
    - **widgets: line, area, heatmap, vertical bars**
    - use case example: view the amount of malware, intrusion sets, threat actor groups added in the course of last month in the platform (*line or area widget)*
    - Caution: these widgets need to use the same “type” of object to work properly. You always need to add relationships in the filter view if you have selected a “knowledge graph” perspective. If you have selected the knowledge graph entity, adding “Entities” (click on + entities) will not work, since you are not counting the same things.

- **Breakdown widgets:** Use this widget if you want to divide your data set into smaller parts to make it clearer and more useful for analysis.
    - **widgets: horizontal bars**
    - use case example: view the list of malware targeting a country breakdown by the type of malware