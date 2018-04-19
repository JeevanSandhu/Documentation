# Host Angular 4 Apps on Microsoft IIS Server

### Use the Angular-CLI to create a production package
First decide whether the application will be located at the root in IIS (e.g. [http://server-name](http://server-name/)) or in a path below the root (e.g. [http://server-name/my-app](http://server-name/my-app))

If deploying to the root, run the Angular CLI _build_ command:
```
ng build --prod
```

If deploying to a path beneath the root, run the Angular CLI _build_ command with the _base-href_ option set to the name of application directory.
```
ng build --prod --base-href /myApp/
```

The _base-href_ option modifies the base element in the _index.html_, so it looks as follows. This is required for routing to work correctly.
> index.html
```html
<head>
	...
	<base href="/myApp">
	...
</head>
```

### Create an IIS web application

Add the web site, or application, or virtual directory to IIS and set the physical path to the location of the  _dist_  folder created by the Angular CLI.
![IIS New App](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_angular_on_iis/angular_iis1.jpg)
Any one of these three options shown below will work.
![IIS Directory](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_angular_on_iis/angular_iis2.jpg)

### Install the URL Rewrite Module into IIS

This next step is required to support deep-linking, which is the capability for the user to navigate directly to a page by typing the route into the address bar instead of using the Angular routing. Deep-linking causes a problem for IIS because the URL that the user attempts to access is not known to the server and therefore the user receives a 404 response. The solution is for the server to always return the root of the application, even if the user requests a path within the application.

Get the URL Rewrite Module installer here:  [https://www.iis.net/downloads/microsoft/url-rewrite](https://www.iis.net/downloads/microsoft/url-rewrite)

After installing you should see a new icon in the IIS Manager.
![IIS URL Rewrite Module](https://raw.githubusercontent.com/JeevanSandhu/Documentation/master/assets/host_angular_on_iis/angular_iis3.jpg)

### Add web.config file with a URL Rewrite rule

All requests to this web application that are not for files or folders should be directed to the root of the application. For an application or virtual directory under the default web site, the URL should be set to the alias, (e.g. “/MyApp/”). For a web site at the root of the server, the URL should be set to “/”.

```xml
<configuration>
<system.webServer>
 <rewrite>
    <rules>
      <rule name="Angular Routes" stopProcessing="true">
        <match url=".*" />
	  <conditions logicalGrouping="MatchAll">
	    <add input="{REQUEST_FILENAME}" matchType="IsFile" negate="true" />
	    <add input="{REQUEST_FILENAME}" matchType="IsDirectory" negate="true" />
 </conditions>
	  <action type="Rewrite" url="/MyApp/" />
 <!--<action type="Rewrite" url="/" />-->
 </rule>
    </rules>
  </rewrite>
</system.webServer>
</configuration>
```

### Troubleshooting

**_Problem:_**  Navigating directly to a route results in a 404.

**_Solution:_**  Make sure the  _web.config_  is correct and the URL property in the _web.config_ matches both the  _base href_  in the  _index.html_  and the web application or virtual directory alias if necessary.

**_Problem:_**  Navigating directly to a route results in a 500 or an error message related to the  _web.config_.

**_Solution:_**  Install the URL Rewrite module.

**_Problem:_**  Navigating directly to a route displays a broken page and network/console errors show 404 results for  _js_  and  _css_  requests.

**_Solution:_**  Verify that the  _base href_  in the  _index.html_  exactly matches the URL in the rewrite rule and that the slashes surrounding the name are not missing.
