# ci.docker.websphere-portal
The following instructions can be used to build an IBM WebSphere Portal Enable V9.0 docker image running on CentOS7 from the installation binary files that you have already downloaded from IBM.
The downloaded files create a WebSphere Portal and Web Content Manager V9.0 container running on the Derby database with a single Dockerfile.

Download the binary files from [Passport Advantage](http://www-01.ibm.com/software/passportadvantage/pao_customer.html)

| Binary files required for WebSphere Portal Enable V9.0                    | Part Number |         
| ------------------------------------------------------------------------- | ----------- | 
| IBM WebSphere Portal Enable Setup V9.0 Multiplatform Multilingual Part 1  | CNH1VML     |
| IBM WebSphere Portal Enable Setup V9.0 Multiplatform Multilingual Part 2  | CNH1WML     | 
| IBM WebSphere Portal Enable Setup V9.0 Multiplatform Multilingual Part 3  | CNH1XML     |  
| IBM WebSphere Portal Enable Setup V9.0 Multiplatform Multilingual Part 4  | CNH1YML     | 


### Pre-requisites
1) Install git or github on the operating system that you plan to use to build the WebSphere Portal image.

2) Install Docker on the operating system that you plan to use to build the WebSphere Portal image.  
See the [Docker installation documentation](https://docs.docker.com/engine/installation/) for details. 

3) Configure docker to meet at least the minimum WebSphere Portal Enable installation requirements.  
As a starting point for a development system, this requirement should at least be 4GB of RAM, 2 CPUs, and 20GB of disk space.  
 See the [WebSphere Portal Enable 9.0 - Detailed system requirements](https://www.ibm.com/software/reports/compatibility/clarity-reports/report/html/softwareReqsForProduct?deliverableId=A969290097A811E6A121FF7B62CD6B8A&osPlatform=Linux) for additional details.  
 
    To determine your system settings, open a command prompt and run the `docker info` command.

4) Select a suitable storage-driver for docker.  
 In Docker versions prior to 1.13, the default storage-driver for some OSes was 'aufs' which is known to have problems when building the WebSphere Portal Enable image.  
 
     It is recommended that you review the Docker documenation [Select a Storage Driver](https://docs.docker.com/engine/userguide/storagedriver/selectadriver/) to choose a suitable storage-driver for your OS prior to building this image.  
 
     To determine which storage-driver is in use, run the `docker info` command and look at the `Storage Driver:` output.  
 
     In our testing, we had success using overlay2 for Linux, Mac, and Windows and devicemapper for Linux.


### Building the IBM WebSphere Portal Enable V9.0 image 

#### Copy the Portal binary images
The WebSphere Portal binary images must be copied to an ftp, http, or https server. See the IBM Installation Manager documentation on [Repositories](https://www.ibm.com/support/knowledgecenter/SSDV2W_1.8.5/com.ibm.silentinstall12.doc/topics/r_repository_types.html) for a list of valid ways to access the installation media. 

Once the installation media is downloaded, you should have the following zip files:
```
WS_PORTAL_ENABLE_SU_V9.0_PART1_MP.zip  
WS_PORTAL_ENABLE_SU_V9.0_PART2_MP.zip  
WS_PORTAL_ENABLE_SU_V9.0_PART3_MP.zip  
WS_PORTAL_ENABLE_SU_V9.0_PART4_MP.zip
```

Extract each of these files to the same directory.  After you extract the files, you should have the following directory structure:
```
├── SETUP
    ├── IIM
    ├── launchpad
    ├── products
    │   ├── IFPI59896
    │   |    └── 8.5.0.0-WP-Server-IFPI59896.zip
    │   │── JDK803
    │   ├── WASND90
    │   ├── WP8500CF13_Server
    │   ├── WP85_Enable
    │   ├── WP85_Server
    │   ├── WP90_Enable
    │   └── WP90_Portal
    ├── quickstart
    ├── sample-responsefiles
    ├── setup64.exe
    ├── setup64.ini
    ├── setup.sh
    ├── WP90_Enable.id
    └── WP90.setup.id
```

#### Clone this repository.
Use git or github to clone this repository.

#### Edit the provided Portal installation response files

Edit the `Portal85BaseResponse.xml`, `Portal85CF13Response.xml`, and `Portal90Response.xml` files and adjust each of the repository location values.  Replace all instances of `{FTP MEDIA URL}` with the ftp or http server location of the downloaded installation files.

For example:

Portal85BaseResponse.xml
```
<server>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/IFPI59896/8.5.0.0-WP-Server-IFPI59896.zip'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP8500CF13_Server'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP90_Portal'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP90_Enable'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/JDK803'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WASND90'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP85_Server'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP85_Enable'/>
</server>
  ```

Portal85CF13Response.xml
```
<server>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP85_Server'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP8500CF13_Server'/>
</server>
```

Portal90Response.xml
```
<server>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP90_Portal'/>
  <repository location='ftp://anonymous@your-ftp-server.example.com/pub/SETUP/products/WP90_Enable'/>
</server>
```

#### Build the image

Run the following command to build your image:
```
docker build --build-arg URL={FTP MEDIA URL} -t portal:v90 .
```

The `portal:v90` image is a fully functional single node WebSphere Portal V9.0 on a WebSphere Application Server V9.0 installation.

* This command should take around two hours to complete.

#### Start the container so that the WebSphere Portal Server is accessible

Run the following command to start the container:
```
docker run -p (your.ip.here):10039:10039 -i -t portal:v90
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
