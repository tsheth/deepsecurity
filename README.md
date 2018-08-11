## Deepsecurity libraries for Azure python Function
Deep security library for python 2.7.x for Azure python function

Default python version in azure function is 2.7.8 which has few SSL limitations. In order to use 3rd party libraries, Python function version need to be upgraded to 2.7.14.
## Create Azure function with Python 2.7.14
Create a Function App
First, create a new Function App. 

# Create a New Function App

Confirm the function app is up and running.  Then click the + sign next to functions to add a function to the app. 

![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/createFunc.png)
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/addFunctionToApp.png)

# Create a New Function

The center pane will ask for a scenario and language to assist with a premade function. Since we are using python for our language, a custom function must be selected to proceed.
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/customFunc.png)

# Enable Experimental Languages

After selecting Python, only two options (HTTP trigger and Queue trigger) can be selected. For this demo, I will select HTTP trigger. I left the defaults for this example.
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/enableExperimentalLang.png)

# HTTP Trigger
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/newHTTPtrigger.png)

# Update Python Version
Now that we have a function in the app, the python version needs to be updated. The python version that is installed is old and conflicted with my scripts. This may not be the case for your scripts but if you need to update to a specific version of python, this will assist in that process. My scripts were written for Python 2.7. I need to fix my scripts to support Python 3.6 but that will come at a later time. To get started, We need to access the Kudu tool. Click the Function App name on the left, then Platform features at the top, and then “Advanced tools (Kudu)” near the bottom of the center pane.
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/accessKudu.png)

# Azure App's Kudu

To update the Python version, click the Site extensions at the top.

![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/siteExtSearchPython.png)

Click the Gallery tab. Then type in Python in the search. The results will provide multiple versions of Python available to be installed. Pick your desired version. 

![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/installPython.png)

I need Python version 2.7.14 x64. Click the + sign to install the extension into your environment. The install icon will show an animated loading icon while it is installing. Once it is finished, a X icon will be present in the upper right of the tile. Take not of the path where this version of python is installed. It will be needed later.

Now that our desired version of Python has been installed, the Handler Mappings need to be updated. Go back to the Function App’s Platform Features page. Then select “Application settings.”

# Application Settings

A new tab is shown in the center pane. Scroll to the bottom to the Handler Mappings section. A new mapping needs to be added. Click “Add new handler mapping” and enter the relevant settings for “fastCgi” handler mapping for the version of Python you installed. The path is shown on the tile when you installed the different version. My handler settings were as followed:
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/appSettings.png)

fastCgi ->D:\home\python27\python.exe -> D:\home\python27\wfastcgi.py 
![alt text](http://www.get-vm.com/blog/wp-content/uploads/2018/04/handlerMapping.png)

Python fastCgi Handler Mapping

Scroll to the top of the Application Settings page and click Save.
Check version in azure function with

```
import platform
print("Python == ", platform.python_version())
```

Following link shows the process to upgrading the version.  Thanks Zach for detailed description  
http://www.get-vm.com/blog/2018/04/11/python-modules-azure-functions/



## install xmltodict in azure function

 Go back to Kudu console (CMD) and install xmltodict. 
 ```
 D:\home>pip install xmltodict
 ```
 
## clone deep security libraries (modified) for Azure funcation.

 Navigate to your funcation path
```
 D:\home\site\wwwroot\{azure function name}> git clone https://github.com/tsheth/deepsecurity.git
 ```
 
 NOTE: {azure function name} is the function name you gave while creating custom function.
 
 ## Create test function in run.py to test the connectivity
 
 ```
from __future__ import print_function

\\standard library
import json
import os
import urllib2

\\3rd party dependencies
import deepsecurity

import platform
print("Python == ", platform.python_version())
   
ds_username = "{deep security API username}"
ds_password = "{DSM password}"
ds_hostname = "{Deep security DSM host name}"
ds_port = 443  //Port can be custom port number
ds_ignore_ssl_validation = "1" //if using DSM instead of DSaaS
ds_tenant = None

print("testing server")
try:
    DSM = deepsecurity.dsm.Manager(username=ds_username, password=ds_password, tenant=ds_tenant, hostname=ds_hostname, port=ds_port, ignore_ssl_validation=ds_ignore_ssl_validation)
    DSM.sign_in()
    DSM.computers.get()
    DSM.policies.get()
    print("Signed into Deep Security")
    print(DSM.policies.get())
    computer = DSM.computers[<Deep security host ID>]
    computer.scan_for_recommendations()
    print("Vulnerability scan completed with IPS rule assignment")
except Exception, ex:
    print("Could not successfully sign into Deep Security. Threw exception: {}".format(ex))
  
 ```


 
