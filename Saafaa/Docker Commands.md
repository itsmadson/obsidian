
**Main GeoServer Docker:**
	┌──(madson㉿kali)-[~/Documents/Saafaa]
	- └─$ docker run --rm -d  -p 80:8080 docker.osgeo.org/geoserver:2.28.x
	29e74f27b85075ebae7de4d77ac6688d5456555fa4dd4dbfcd20f47ec38000da
	
	
	┌──(madson㉿kali)-[~/Documents/Saafaa]
	└─$ docker pull docker.osgeo.org/geoserver:2.28.x

**Kartoza Docker**
	- [ ] docker run --rm -d -p 80:8080 --name geoserver docker.arvancloud.ir/kartoza/geoserver
	- [ ] https://github.com/kartoza/docker-geoserver
	- [ ] http://localhost:8080/geoserver


**PostGIS commands**
	sudo docker exec -it postgis psql -U gisuser -d mydata  bash
	
