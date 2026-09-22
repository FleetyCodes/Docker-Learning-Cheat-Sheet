# Notes:

**Dockerfile:** "defines the contents of an image". Forráskód, az image definíciója.  
**Image:** A container definíciója. - env variables, jar file, jdk, tomcat, etc. a dockerfile buildelésének eredménye.  
**Docker Container:** Környezet, hasonló mint egy virtual machine. De containeren nincs op rendszer. A container a gépnek az os kernelén fut. A container egy process - 'a service  in its own right'. 
		Az image egy példánya, az imaget futtatod, és lesz belőle egy container. Egy container, egy service, egy microservice.
**docker network:** egy virtuális hálózat amin keresztül a containerek látják egymást és kommunikálhatnak egymással. Érdemes modulokat (FE, BE, DB) külön konténerekben futtatni, és ezáltal tudnak kommunikálni egymással.
**docker volumes:** perzisztens adattárolás, container leállítás után is megmaradnak a definiált adatok
**docker compose: ** a simple text file which contains the configuration. production tool is Kubernetes, docker compose is a local dev and test tool.  doksi: https://docs.docker.com/reference/compose-file/  

**docker hub:**
hub.docker.com
olyan mint egy maven repo, itt vannak a docker imagek amik publicok és private-ok.
(ilyet lehet hostolni is, inkább githubhoz hasonlit)

**dockerfile commands documentation:**
	https://docs.docker.com/reference/dockerfile			


**image készítés:**
- lehet egy ubuntut indítani, belépni, felkonfolni és azt az állapotot lementeni (kevésbé használt megoldás)
	docker container commit -a <"username"> <image name> <container id>  --> image létrehozás létező felkonfolt containerből. nem szokás.
- a normális út a dockerfile készítéssel készül
	docker image build -t jdk-image-from-dockerfile . --> image buildelés dockerfileból. -t és a mögötte levő szöveg a név paraméter. a pont azt jelzi hogy ebből a mappából szed ki mindent. mindig a "Dockerfile" elnevezésúből fog buildelni.

**container futtatás/docker container run**
futtatáskor meg kell mondani mely portok legyenek publikusak
-p --> publish 
	-p 8080:8080 melyik portot hova irányítod, bal oldali külső, jobb a belső a dockeren belüli


## Best practices:
 - Általában mindig használj ha tudsz verzió számot a dockerfilehoz
 - Dockerfile-ban maintainer LABEL legyen, többit nem igazán használják
 
## Maven plugin:
 - https://dmp.fabric8.io/ (io.fabric8) --> Ez a plugin integrálja a dockert. "mvn clean package docker:build" (execute mavel goal) commanddal, a POM-ban megadott Dockerfile-ból (<dockerFileDir>${project.basedir}/src/main/docker/</dockerFileDir>) buildel jar-t majd másolja a target\docker alá. 
											Innen a Dockerfile a COPY paranccsal be tudja emelni az image build context-jébe.

# CMD futtatásra bevett parancsok:
```
	docker container run hello-world  --> init image, container létrehozás. smoke test hogy a Docker daemon fut
	docker image pull <name of image> --> image letöltés
		pl.: docker image pull virtualpairprogrammers/fleetman-webapp
	docker container run -p 8080:8080 virtualpairprogrammers/fleetman-webapp --> futtatás -> ha nem volt pullolva le is pullolja
	docker container ls --> list of running docker containers
	docker-machine ip --> a docker toolbox ip-je, ha nem windows lenne, a localhosthoz, eléréshez
	docker container stop <id vagy azonosito eleje amitől unique>
	docker ps --> list of running docker containers
	docker ps -a --> list of all docker containers
	docker container run -it ubuntu --> folyamatos futás
	docker container start <id> --> olyat lehet futtatni, ami már futott egyszer, és ilyenkor a háttérben elfut
	docker container rm --> container törlés a listából, ami már futott
	docker container prune --> mindent töröl ami nem fut--> memória takaritas
	docker container run -d -p 8080:8080 virtualpairprogrammers/fleetman-webapp --> háttérben futtatás a minusz d commanddal
		-d --> detached
	docker container logs <container id> --> logok
	docker container logs -f <container id> --> logok, folyamat frissüléssel
	docker container exec -it <container id> bash --> be ssh-zás || docker container exec -it <container name> sh
		docker container commit -a <"username"> <image name> <container id>  --> image létrehozás létező fellkonfolt containerből. nem szokás.
	docker image build -t jdk-image-from-dockerfile . --> image buildelés dockerfileból. -t és a mögötte levő szöveg a név paraméter. a pont azt jelzi hogy ebből a mappából szed ki mindent. mindig a "Dockerfile" elnevezésúből fog buildelni.
	docker image tag <id to tag> <tag to give>
	docker login --> belépés docker hubra
	docker image push fleetman-webapp
	docker container run -e MYSQL_ROOT_PASSWORD=password -d mysql:5 --> -e: env var
	
	
	CONTAINER NETWORKING:
		docker network ls -> network list
			- bridge -> ez a default network
		docker network create <network name>
		
		docker container run --network my-docker-network --name database_container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman -d mysql:5
	
		--network my-docker-network --> csatlakoztatás docker networkhöz
		----name database_container --> konténer elnevezése, spring.datasource.url-be ez fog kelleni
		docker container run -d -p 80:8080 --network my-docker-network --name fleetman-webapp --rm fleetman-webapp
		--rm --> leállás után törlődik a container	
		
	DOCKER VOLUMES
		docker container inspect <id or name of container> --> container info
		docker volume ls -> volume lista
		docker volume prune --> volume-ok törlése
		
		docker container run -v mydata:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:5
			--> -v mydata:/var/lib/mysql --> volume létrehozása, a "mydata" lesz itt a neve
		docker volume inspect mydata --> mydata nevű volume inspectálás	
		
		docker container run -v /home/valamieleres/:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:5
			--> linux 
		docker container run -v //c/felhasználók/Laci/work/mydatabase:/var/lib/mysql -d -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=fleetman mysql:5
			--> windows verzio
			Ha nincs elérés akkor létrehozza. Fizikailag kiírja ide a fileokat.
			
	DOCKER COMPOSE
		docker-compose -v --> docker compose version check
		docker-compose up --> startup, restartolja a servicet ahol change volt
		docker-compose down --> teljes leállás
```	

## Dockerfile commands:
	**CMD-ből csak egy lehet ! **
	EXPOSE 8080 --> jelzés milyen port mapping kelhet. dokumentációs jelleg.
	RUN rm -rf ./webapps/* --> előtakarítás
	COPY target/fleetman-0.0.1-SNAPSHOT.war /usr/local/tomcat/webapps/ROOT.war --> másolás
	ENV JAVA_OPTS="-Dspring.profiles.active=docker-demo" --> env var megadás
	VOLUME /var/lib/mysql --> megtartja az itt definiált adatokat ha leáll a container. ezek a host gépen lesznek tárolva.
		

**kis egyéb:**  
- 8080 -> port amin a Tomcat fut defaulton  
- 80   ->  HTTP alapértelmezett portja, ezért ha a szerver ezen fut, nem kell portszámot írni az URL-be (pl. localhost:80 == localhost)  
- /ROOT.war --> ilyenkor nem kell domain név csak az ip address  
- java -jar test-program.jar --> jar futtatás   
- ubuntu package keresés: apt-cache search jdk  
- vm argument: -Dspring.profiles.active=development
- build: .\mvnw.cmd clean package -DskipTests
- cd .. --> visszaugrás
- java -D"spring.profiles.active"=development -jar .\fleetman-0.0.1-SNAPSHOT.jar --> run jar
- mysql -ppassword -> belépés "password" jelszóval a db-be
	show databases; show tables; (db commands, mindig kell a ; a végére!)
- settings.xml --> C:\Users\Username\.m2