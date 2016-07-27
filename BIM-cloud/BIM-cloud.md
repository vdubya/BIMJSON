# Minimal Viable BIM

**Version 0.1**
[GitHub repository](https://github.com/vdubya/BIMJSON/BIM-cloud)

### sample Cloud Server for BIMJSON web services
[BIM-cloud.com](https://bim-cloud.com)

#### Features:

- Import a valid COBie file or the USACE Facility Data Workbook to BIM-cloud.com (sample files included)
- Each import generates a self-contained, serverless, open source database. It’s name is the UUID (GUID) generated for the project by the importer.
- Once imported, the Cloud Server displays the UUID which can be used by front end application to access the project with RESTful web services.
- After the import, the APIs are displayed to access the project.
- The JSON response is in compact format. Copy/paste the displayed data into a JSON viewer/validator such as:
	- http://jsonlint.com
	- https://jsonformatter.curiousconcept.com
	- or any other JSON formatter

#### Framework/Code:

The prototype Cloud Server uses the free open source [web2py](http://web2py.com) framework with a few simple pages of code to import the 2 sample file formats (COBie + USACE Workbook) and to generate the RESTful web service APIs.

**BEFORE DOWNLOADING THE SOURCE CODE, PLEASE REVIEW THE [TEMPORARY LICENSE FILE](../LICENSE.md)**

Source code for both, Server and Front-End App, can be downloaded from here:
[Download Source Code](https://www.onuma.com/transfer/bimjson.zip)

**The zipped archive has the following structure:**

![folder](https://www.onuma.com/transfer/web2py-code.png)

Copy the full webservices folder into "applications" and modify routes.py for the BIM Cloud Server
**or**
copy the full inventory_list folder into "applications" and modify routes.py accordingly for the Front-End App

#### Sample Files:
Note: for the current prototype, the files have to be saved as XLSX files.

[USACE workbook](USACE_Facility_Data_Workbook2.xlsx)

[COBie](Clinic_COBie.xlsx)







