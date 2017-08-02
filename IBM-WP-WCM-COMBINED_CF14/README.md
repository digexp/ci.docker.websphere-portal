# ci.docker.websphere-portal-cumulative-fixpack-14

The following instructions can be used to build an IBM WebSphere Portal Server V9.0 CF14 image.

* These instructions extend the IBM WebSphere Portal V9.0 image previously created and create a new image with CF14.

* Confirm the `portal:v90` image is listed in your local Docker image repository by running the command `docker images`.

#### Binary files required for Websphere Portal CF14

* Download [8.5-9.0-WP-WCM-Combined-CFPI73835-Server-CF14](https://www-945.ibm.com/support/fixcentral/swg/selectFixes?parent=ibm~WebSphere&product=ibm/WebSphere/WebSphere+Portal&release=9.0.0.0&platform=All&function=aparId&apars=PI73835) from FixCentral

Perform the following steps on your ftp or http server:
* Create a directory named `CF14`. 
* Extract the file `8.5-9.0-WP-WCM-Combined-CFPI73835-Server-CF14.zip` into the CF14 folder.
* Extract the file `WP8500CF14_Server.zip` into the CF14 folder.

After you extract the files, you should have the following directory structure:
```
├── CF14
   ├── 8500CF14
   ├── 8.5-9.0-HealthCheckTool-Readme.txt
   ├── 8.5-9.0-WP-WCM-Combined-CFPI73835-CF14_Readme.txt
   ├── 8.5-9.0-WP-WCM-Combined-CFPI73835-Server-CF14.zip
   ├── HealthCheckTool-Portal85and90.zip
   ├── repository.config
   ├── repository.xml
   ├── WP8500CF14_Server.zip
   ├── WP85CFRollback-Server-sample.xml
   └── WP85CFUpdate-Server-sample.xml
```


#### Building the IBM WebSphere Portal Server V9.0 CF14 image

This repository should already be cloned, navigate to the `ci.docker.websphere-portal/IBM-WP-WCM-COMBINED_CF14` directory.

Edit the `update90response.xml` file and adjust the repository location value.  Replace `{FTP MEDIA URL}` with the ftp or http server location of the downloaded installation files.

For example:

update90response.xml
```
  <server>
     <repository location='ftp://anonymous@your-ftp-server.example.com/pub/CF14'/>
  </server>
```

#### Build the image
Run the following command to build your image:
```
docker build -t portal:v90CF14 .
```

The `portal:v90CF14` image is a fully functional single node WebSphere Portal V9.0 on a WebSphere Application Server V9.0 installation.

#### Start the container so the WebSphere Portal Server is accessible

Run the following command to start the container:
```
docker run -p (your.ip.here):10039:10039 -i -t portal:v90CF14
```

#### Start Portal from within the container
Run the following command to start WebSphere Portal within the container:
```
/opt/IBM/WebSphere/wp_profile/bin/startServer.sh WebSphere_Portal
```

#### Access Portal from a browser
Log in to Portal from your browser.  For example, your URL should look like the following example:
```
    http://(your.ip.here):10039/wps/myportal

    Username: wpsadmin 
    Password: wpsadmin
```

A sample [docker-compose.yml](docker-compose.yml) has been provided that exposes additional ports for the WebSphere Portal Server, WebSphere Administration Server console, SOAP Connector, and the Configuration Wizard. This file can be used to start the portal:v90 image with the command `docker-compose up`. See the [Docker Compose](https://docs.docker.com/compose/) documentation for more details on starting docker images with Docker Compose.

For production or complex development environments based on IBM Digital Experience solutions WebSphere Portal Server and Web Content Manager on Docker, please contact [IBM Customer Engagement Services](https://www-01.ibm.com/software/commerce/services/customer.html). 
