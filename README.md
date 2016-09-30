# flow
a digitization-project tracking system

## goals
* develop and deploy a minimal application that supports digitization project tracking and reporting 
* reuse / enhance / extend existing infrastructure where possible
* develop new infrastructure where needed


## 10,000 foot view
* An `Archivist` generates a `Work Order File` and delivers it to the appropriate `Digitization Team`
* The `Digitization Team Manager` runs a script on the `Work Order File` that creates `Unit of Work` directories 
* Each `Unit of Work` directory contains a `Tracking URL`
* A `Digitization Team Member` processes the `Unit of Work` directory through the `Digitization Steps`
* As the `Unit of Work` is processed, `Monitoring Scripts` update the `Unit of Work` status via the `Tracking URL`
* The `Unit of Work` status is available via a web application: the `Flow Web UI`
* `Digitization Team Managers` and `Project Managers` use the `Flow Web UI` to track `Unit of Work` status


## 1,000 foot view
* An `Archivist` uses the ArchivesSpace UI to select the `Item`s they want digitized
* The `Archivist` generates a `Work Order File` using the ArchivesSpace work-order plugin 
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
* `Monitoring Script`s watch the `QC` and `Upload` directories and update `Unit of Work` status via the `Tracking URL`

## 100 foot view
* An `Archivist` uses the ArchivesSpace UI to select the `Item`s they want digitized
* The `Archivist` generates a `Work Order File` using the ArchivesSpace work-order plugin 
  * the `Work Order File` contains one line per `Item`
* The `Archivist` delivers the `Work Order File` to the appropriate `Digitization Team` [1]
* The `Digitization Team Manager` runs the `Unit of Work Generator` script that:
  * queries the R\* Back End (`rsbe`) API for a list of `partners`
  * asks the `Digitization Manager` to select the R* `partner` 
  * queries the R\* Back End (`rsbe`) API for a list of `collections` belonging to the selected `partner`
  * asks the `Digitization Manager` to select the `collection` 
  * processes the `Work Order File` line-by-line
    * for each line the script:
      * parses the line, extracting the relevant information like the `Component Unique Identifier (cuid)`, and `Archival Object URI` [3]
      * generates the `Digitization ID` by copying and/or transforming the `cuid`, e.g., converting `.` to `_` 
      * `POST`s a `JSON` request to the `rsbe` API to create a `source entity (se)` resource in the appropriate R\* `collection`
      * processes the `rsbe` response, which contains the `se URL` on successful `se` resource creation
      * creates the `Unit of Work` directory on the local machine named with the `Digitization ID` value
      * writes the `se URL` to a file named `tracking_url` in the `Unit of Work` directory
* The `Digitization Team Manager` assigns the `Unit of Work` to a `Digitization Team Member` [4]
* The `Digitization Team Member` digitizes the `Item`, placing the digital-object files into the `Unit of Work` directory
* The `Digitization Team Member` moves the `Unit of Work` directory to the `QC Directory`
* The `Digitization Team Manager` QCs the `Unit of Work` 
  * if the `Unit of Work` **passes** QC, the `Digitization Manager` moves the `Unit of Work` to the `Upload Directory`
  * if the `Unit of Work` **fails** QC, the `Digitization Manager` moves the `Unit of Work` to the `Rejected Directory` [2]
* `Monitoring Script`s watch the `QC` and `Upload` directories and update `Unit of Work` status via the `Tracking URL`

## enhancements to infrastructure
* add `work order uuid` to ArchivesSpace work-order plug-in

## references/action itesm
[1] [ ] Eric, is this correct? does the `Work Order` go to `Project Manager` first?  
[2] [ ] need to check with `Digitization Teams` to see if the use of a `Rejected Directory` is acceptable  
[3] [ ] TODO: analyze work order file to determine minimal required information, and which information should be part of the SE  
[4] I propose that we do *not* track this initially
