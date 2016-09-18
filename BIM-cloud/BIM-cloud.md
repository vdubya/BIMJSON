# Minimal Viable BIM

**Version 0.1**

### sample Cloud Server with JSON web service APIs
[BIM-cloud.com](https://bim-cloud.com)

#### Features:

- Import a valid COBie file or the USACE Facility Data Workbook to BIM-cloud.com (sample files included)
- Each import generates a self-contained, serverless, open source database. It’s name is the UUID (GUID) generated for the project by the importer.
- Once imported, the Cloud Server displays the UUID which can be used by front end application to access the project with RESTful web services.
- After the import, the APIs are displayed to access the project.
- The JSON response is in compact format. Use a browser extension like Chrome's "JSONView" or copy/paste the displayed data into a JSON viewer/validator such as:
	- http://jsonlint.com
	- https://jsonformatter.curiousconcept.com
	- or any other JSON formatter

#### Framework/Code:

The prototype Cloud Server uses the free open source [web2py](http://web2py.com) framework with a few simple pages of code to import the 2 sample file formats (COBie + USACE Workbook) and to generate the RESTful web service APIs.

Source code for the BIM-Cloud Server can be downloaded from here:
[Download Source Code](https://www.onuma.com/transfer/bimcloud.zip)

**The zipped archive has the following structure:**

![folder](https://www.onuma.com/transfer/bim-cloud.png)

Copy the full webservices folder into "applications" and modify routes.py for the BIM Cloud Server

#### License:
MIT License

Copyright (c) 2016 Onuma, Inc.

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

#### Sample Files:
Note: for the current prototype, the files have to be saved as XLSX files.

[USACE workbook](https://www.onuma.com/transfer/USACE_Facility_Data_Workbook2.xlsx)

[COBie](https://www.onuma.com/transfer/Clinic_COBie.xlsx)







