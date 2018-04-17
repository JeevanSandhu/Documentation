# Hosting Flask API on Microsoft IIS Server

## Pre-requisites:
1.  Enable IIS on Windows
Enable Internet Information Services (IIS) in Turn Windows Features On or Off.
* Make sure that `IIS > Web Management Tools > IIS Management Console` is checked.
* Also `IIS > World Wide Web Services > Common HTTP` check `Default Document`, `Directory Browsing`, `HTTP Errors`, `Static Content`
2. Install Python 3.* and add it to the Environment Path
3. Install wfastcgi
Run `pip install wfastcgi` in a terminal
	* Open a terminal (cmd with Admin)
	* Type `wfastcgi-enable` to enable wfastcgi. Save the path that is given for use later.
![Enable WFastCGI](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis1.png)
	* To disable wfastcgi type `wfastcgi-disable`

## IIS Setup
1. Open IIS Manager
2. Under `ServerName > Sites`, right-click on `Default Web Site` and select Add Application
![Add Application in Default Website](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis2.png)
	* Add Alias as the subsite you want (ex: alias="test" for 10.10.10.10/test)
	* Select the path for the project directory (where `app.py` resides)
	* Click OK
![Add Alias and Path for Application](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis3.png)
3. Select the newly created app under the Default Web Site
	* Double-click on Handler Mappings
![Handler Mappings](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis4.png)
	* On the right-side panel, select Add module mapping
![Add Module Mapping](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis5.png)
		* Set `Request Path` to `*`
		* Select `Module` to `FastCgiModule`
		* Set `Executable (optional)` to the path that was saved earlier when enabling wfastcgi
		* Set any name for the handler
![Module Mapping Details](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis6.png)
		* Click on Request Restrictions and under the Mapping tab uncheck the box for Invoke Handler only if request is mapped to : ...
![Request Restrictions](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis7.png)
		* Click OK
	* Click OK
	* A dialog box `Add Module Mapping` which says `Do you want to create a FastCGI application for this executable?` will be shown. Click Yes.
![Add Entry ti FastCGI Collection](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis8.png)
4. Select `Application Pools` under the ServerName
	* Click on `Add Application Pool` in the right-side panel
		* Name: App name
		* .NET CLR Version: .NET CLR Version v4.0.30319
		* Managed pipeline mode: Integrated
		* Start applicaiton pool immediately: Checked
		* Click OK
![Application Pool](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis9.png)
	* Select the newly created applcation pool, right-click and select Advanced Settings
		* Under the Process Model Section, change the Identity from ApplcationPoolIdentity to Local System
![Advanced Settings](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis10.png)
5. Click on the ServerName
	* Double-click on the FastCgiSettings under IIS Section
![FastCGI Settings](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis12.png)
	* Double-click the entry for the python path of your project
![Python Path](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis13.png)
		* Under the General Settings, click on the three dots of the `Environment Variables`
![Edit FastCGI Settings](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis14.png)
		* Create 2 new entries with the following:
			* Name: `PYTHONPATH` & Value: `C:\inetpub\wwwroot\test` (the path where your `app.py` resides)
			* Name: `WSGI_HANDLER` & Value: `app.app` (if your flask python file is called test, then use `test.app`)
			* Click OK
![Environment Variables](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_flask_on_iis/iis15.png)
		* Click OK
6. Restart the server for the changes to take place
