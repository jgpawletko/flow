# flow
a digitization-project tracking system

## goals
* develop and deploy a minimal application that supports digitization project tracking and reporting 
* reuse / enhance / extend existing infrastructure where possible
* develop new infrastructure where needed


## 10,000 foot view
* An `Archivist` generates a `Work Order File` and gives it to the appropriate `Digitization Team`
* The `Digitization Team Manager` runs a script on the `Work Order File` that creates `Unit of Work` directories 
* Each `Unit of Work` directory contains a `Tracking URL`
* A `Digitization Team Member` processes the `Unit of Work` directory through the `Digitization Steps`
* As the `Unit of Work` is processed, `Monitoring Scripts` update the `Unit of Work` status via the `Tracking URL`
* The `Unit of Work` status is available via a web application: the `Flow Web UI`
* `Digitization Team Managers` and `Project Managers` use the `Flow Web UI` to track `Unit of Work` status


## 5,000 foot view
* An `Archivist` selects the `Item`s they want digitized and generates a `Work Order File` using the ArchivesSpace work-order plugin 
* The `Archivist` delivers the `Work Order File` to the appropriate `Digitization Team` [1]
* The `Digitization Team Manager` runs the `Unit of Work Generator` script that:
  * creates a `Unit of Work` directory on the local machine for each `Item`
  * places the `Tracking URL` for the `Unit of Work` in the `Unit of Work` directory
* The `Digitization Team Manager` assigns the `Unit of Work` to a `Digitization Team Member`
* The `Digitization Team Member` digitizes the `Item`, placing the digital-object files into the `Unit of Work` directory
* The `Digitization Team Member` moves the `Unit of Work` directory to the `QC Directory`
* The `Digitization Team Manager` QCs the `Unit of Work` 
  * if the `Unit of Work` **passes** QC, the `Digitization Manager` moves the `Unit of Work` to the `Upload Directory`
  * if the `Unit of Work` **fails** QC, the `Digitization Manager` moves the `Unit of Work` to the `Rejected Directory` [2]
* `Monitoring Scripts` watch the `QC` and `Upload` directoriesdetect the addition of a `Unit of Work` directory to the `QC` directory and updates the
* 
  * asks the `Digitization Manager` to select the R* `partner` and `collection`
  * processes the `Work Order File` line-by-line
    * for each line the script:
      * `POST`s a `JSON` request to the `rsbe` API to create a `source entity resource (se)`
      * `rsbe` creates an `se` resource and returns the `se` URL
      * creates a directory with the `cuid` generates directories with the proper names


## human-centric process steps
* `Archivist` generates a `Work Order` using the ArchivesSpace work-order plugin 
* `Archivist` delivers `Work Order` to appropriate digitization team
* The `Digitization Team Manager` runs a `Work-order-processing script` that:
  * asks the `Digitization Manager` to select the R* `partner` and `collection`
  * processes the `Work Order File` line-by-line
    * for each line the script:
      * `POST`s a `JSON` request to the `rsbe` API to create a `source entity resource (se)`
      * `rsbe` creates an `se` resource and returns the `se` URL
      * creates a directory with the `cuid` generates directories with the proper names

## enhancements to infrastructure
* add `work order uuid` to ArchivesSpace work-order plug-in

[1] [ ] Eric, is this correct? does the `Work Order` go to `Project Manager` first?
[2] [ ] need to check with `Digitization Teams` to see if the use of a `Rejected Directory` is acceptable
