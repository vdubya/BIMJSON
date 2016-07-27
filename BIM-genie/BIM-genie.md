# Minimal Viable BIM

**Version 0.1**
[GitHub repository](https://github.com/vdubya/BIMJSON/BIM-genie)

### sample Front End App @ BIM-genie.com
[BIM-genie.com](http://bim-genie.com)

#### Notes:
The UI of this App is kept simple with minimum formatting. The priority is given the reading, updating, adding, and deleting of assets (component data) to demonstrate the functioning of the APIs in BIM-cloud.com.
The App currently makes the web service requests for every page it loads and displays the API requests on the lower right of the page. Depending on the use case for front end apps, they could use AngularJS or other frameworks to improve the efficiency of data requests.

#### Features:

- The App retrieves data and displays at the bottom right the RESTful API requests made for the transactions. The focus is to demonstrate how the data can be displayed (GET), added and updated (POST), and deleted (DELETE) using those APIs in the BIMJSON format.
- Submit the UUID created by the Cloud Server to display the overall facility data. Since it is not intended that overall facility data is updated with this App, the UI displays all attributes associated with the facility (from COBie worksheets Facility, Floor, Document, and Attribute) in a raw format.
- From the right select either “view all assets” or “add new asset”.
Viewing all assets returns a complete list and displays the API requests made to display the page.
- Select the unique mark on the left to view the data associated with the asset or select to “Edit” or “Delete” an asset directly from the list. 
- Selecting a link to a space shows all assets within that single space. Alternatively, one can switch between the list of all assets to individual spaces with the pulldown at the top right.

#### Framework/Code:

The sample Front End App uses the free open source [web2py](http://web2py.com) framework with a few simple pages of code to display, delete, create, and edit facility data through HTTP exchanges with the BIM-Cloud APIs.

**BEFORE DOWNLOADING THE SOURCE CODE, PLEASE REVIEW THE [TEMPORARY LICENSE FILE](../LICENSE.md)**

Source code for both, Server and Front-End App, can be downloaded from here:
[Download Source Code](https://www.onuma.com/transfer/bimjson.zip)

**The zipped archive has the following structure:**

![folder](https://www.onuma.com/transfer/web2py-code.png)

Copy the full webservices folder into "applications" and modify routes.py for the BIM Cloud Server
**or**
copy the full inventory_list folder into "applications" and modify routes.py accordingly for the Front-End App

