# ci.docker.websphere-portal-cumulative-fixpack-15

The following instructions can be used to build an IBM WebSphere Portal Server V9.0 CF15 image.

* These instructions extend the IBM WebSphere Portal V9.0 image previously created and create a new image with CF15.

* Confirm the `portal:v90` image is listed in your local Docker image repository by running the command `docker images`.

* If you have upgraded your IBM WebSphere Portal V9.0 image to CF14, your existing image name should be `portal:v90CF14`.

#### Binary files required for Websphere Portal CF15

* Download [8.5-9.0-WP-WCM-Combined-CFPI83476-Server-CF15](https://www-945.ibm.com/support/fixcentral/swg/selectFixes?parent=ibm%7EWebSphere&product=ibm/WebSphere/WebSphere+Portal&release=9.0.0.0&platform=All&function=aparId&apars=PI83476) from FixCentral

Perform the following steps on your ftp or http server:
* Create a directory named `CF15`. 
* Extract the file `8.5-9.0-WP-WCM-Combined-CFPI83476-Server-CF15.zip` into the CF15 folder.
* Extract the file `WP8500CF15_Server.zip` into the CF15 folder.

After you extract the files, you should have the following directory structure:
```
├── CF15
   ├── 8500CF15
   ├── 8.5-9.0-HealthCheckTool-Readme.txt
   ├── 8.5-9.0-WP-WCM-Combined-CFPI83476-CF15_Readme.txt
   ├── 8.5-9.0-WP-WCM-Combined-CFPI83476-Server-CF15.zip
   ├── HealthCheckTool-Portal85and90.zip
   ├── repository.config
   ├── repository.xml
   ├── WP8500CF15_Server.zip
   ├── WP85CFRollback-Server-sample.xml
   └── WP85CFUpdate-Server-sample.xml
```


#### Building the IBM WebSphere Portal Server V9.0 CF15 image

This repository should already be cloned, navigate to the `ci.docker.websphere-portal/IBM-WP-WCM-COMBINED_CF15` directory.

Edit the `update90response.xml` file and adjust the repository location value.  Replace `{FTP MEDIA URL}` with the ftp or http server location of the downloaded installation files.

For example:

update90response.xml
```
  <server>
     <repository location='ftp://anonymous@your-ftp-server.example.com/pub/CF15'/>
  </server>
```

If you are upgrading from IBM WebSphere Portal Server or Web Content Management V9.0CF14, you will need to edit the `Dockerfile` so it references the upgraded image.

For example:

Dockerfile
```
FROM portal:v90CF14
```

#### Build the image
Run the following command to build your image:
```
docker build -t portal:v90CF15 .
```

The `portal:v90CF15` image is a fully functional single node WebSphere Portal V9.0 on a WebSphere Application Server V9.0 installation.

#### Start the container so the WebSphere Portal Server is accessible

Run the following command to start the container:
```
docker run -p (your.ip.here):10039:10039 -i -t portal:v90CF15
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

A sample [docker-compose.yml](docker-compose.yml) has been provided that exposes additional ports for the WebSphere Portal Server, WebSphere Administration Server console, SOAP Connector, and the Configuration Wizard. This file can be used to start the portal:v90CF15 image with the command `docker-compose up`. See the [Docker Compose](https://docs.docker.com/compose/) documentation for more details on starting docker images with Docker Compose.

For production or complex development environments based on IBM Digital Experience solutions WebSphere Portal Server and Web Content Manager on Docker, please contact [IBM Customer Engagement Services](https://www-01.ibm.com/software/commerce/services/customer.html). 
