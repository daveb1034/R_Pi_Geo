## Raspberry Pi GeoServer

### Configuring and Starting up 
 THe first stage is to instal the raspbrian image. I chose to do this using Noobs.
 
 Follow the install guide at the [Raspberry Pi](https://www.raspberrypi.org/downloads/noobs/ "Raspberry Pi Downloads") download page.

Configure the Raspberry Pi to allow a remote desktop connection from windows PC using Adam Riley's [Blog post](http://www.raspberrypiblog.com/2012/10/how-to-setup-remote-desktop-from.html "RDP to Pi").

### Installing Postgresql 9.4

open a terminal on the Raspberry Pi and type 


```
sudo apt-get install postgresql-9.4
```

When prompted type y to continue and let the system install postgresql. THis will take a few minutes.

additionally you should run:

```
sudo apt-get install postgresql-contrib
```

This prevents a warning in pgadmin3 reference missing server instrumentation.

Now install pgadmin3 using the command

```
sudo apt-get install pgadmin3
```

I had some dramas connecting to the server after install so i ran the following in the terminal:

```
sudo -u postgres psql postgres

alter user postgres with password 'postgres';
```

If you still get the server instrumentation missing warning click the Fix It! button on the dialog.

Now postgis needs to be installed using 
```
sudo apt-get install postgis
```

The above software can be installed using a singal command

```
sudo apt-get install postgresql-9.4 postgresql-contrib pgadmin3 postgis
```

## Download and Configure Geoserver

This part of the post is inspired by [this](http://blog.sortedset.com/gis-tiny-box-geoserver-raspberry-pi/ "GIS in a tiny box") blog post.
The first few sections take you through setting up and accessing the Raspberry Pi.

To run Geoserver we will install Tomcat version 8 using the following

```
sudo apt-get install tomcat8
```

To check that the installing was successful open the web browser and enter the following address:

```
http://xxx.xxx.xxx.xxx:8080
```
replacing the xxx.xxx.xxx.xxx with the ip address of your Raspberry Pi.

To download Geoserver use wget. The current version as at Nov 2015 is 2.8.1. THe downloaded file needs to be unzipped and moved into the Tomcat webapps folder.

```
wget http://sourceforge.net/projects/geoserver/files/GeoServer/2.8.1/geoserver-2.8.1-war.zip

unzip geoserver-2.8.1-war.zip

sudo service tomcat8 stop

sudo mv geoserver.war /var/lib/tomcat8/webapps

sudo service tomcat8 start
```

The final step is to test Geoserver is up and running. Navigate to the following address:

```
http://xxx.xxx.xxx.xxx:8080/geoserver/
```

This should load the Geoserver landing page.

You can log in to the admin using the default user as admin and password as geoserver.

Select the Layer Preview tab on the left of the page and then select the OpenLayers link for topp:states USA Population.

Using the address above you can access the web services from any device on your network.


### Remote Access

Configure PGSql for remote access so other workstations on the network can access the database from programs such as QGIS.
- This was achieved by modyifying the pg_hba.conf and add the following
```
host all all 192.168.1.0/24 md5
```

This provides access to all devices on the above network. NOTE: this is not secure so do not use in a production environment.

Then edit the postgresql.conf file and change
```
listen_addresses='localhost'
```
to
```
listen_addresses='*'
```
If that fails just enter the ip addresses of the machines to grant access.
Finally for the changes to take effect restart the postgresql service.

### Next Steps?

1. Load data into postgis database using pgadmin3.
2. View and style data in QGIS.
3. Create an SLD and test adding a service in geoserver.