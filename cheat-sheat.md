**Dockerfile: ** "defines the contents of an image". Forráskód, az image definíciója.
**Image: ** A container definíciója. - env variables, jar file, jdk, tomcat, etc. a dockerfile buildelésének eredménye.
**Docker Container: ** Környezet, hasonló mint egy virtual machine. De containeren nincs op rendszer. A container a gépnek az os kernelén fut. A container egy process. Az image egy példánya, az imaget futtatod, és lesz belőle egy container.


**docker hub: **
hub.docker.com
olyan mint egy maven repo, itt vannak a docker imagek amik publicok és private-ok.
(ilyet lehet hostolni is, inkább githubhoz hasonlit)

**dockerfile commands documentation: **
	https://docs.docker.com/reference/dockerfile			


**image készítés: **
- lehet egy ubuntut indítani, belépni, felkonfolni és azt az állapotoot lementeni (kevésbé használt megoldás)
	docker container commit -a <"username"> <image name> <container id>  --> image létrehozás létező fellkonfolt containerből. nem szokás.
- a normális út a dockerfile készítéssel készül
	docker image build -t jdk-image-from-dockerfile . --> image buildelés dockerfileból. -t és a mögötte levő szöveg a név paraméter. a pont azt jelzi hogy ebből a mappából szed ki mindent. mindig a "Dockerfile" elnevezésúből fog buildelni.

**container futtatás/docker container run **
futtatáskor meg kell mondani mely portok legyenek publikusak
-p --> publish 
	-p 8080:8080 melyik portot hova irányítod, bal oldali külső, jobb a belső a dockeren belüli


#best practices:
 - Általában mindig használj ha tudsz verzió számot a dockerfilehoz
 
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
	docker container logs <container id> --> logok
	docker container logs -f <container id> --> logok, folyamat frissüléssel
	docker container exec -it <container id> bash --> be ssh-zás
	docker container commit -a <"username"> <image name> <container id>  --> image létrehozás létező fellkonfolt containerből. nem szokás.
	docker image build -t jdk-image-from-dockerfile . --> image buildelés dockerfileból. -t és a mögötte levő szöveg a név paraméter. a pont azt jelzi hogy ebből a mappából szed ki mindent. mindig a "Dockerfile" elnevezésúből fog buildelni.
```	


**kis egyéb: **
8080 -> port amin a Tomcat fut defaulton
80   ->  HTTP alapértelmezett portja, ezért ha a szerver ezen fut, nem kell portszámot írni az URL-be (pl. localhost:80 == localhost)
/ROOT.war --> ilyenkor nem kell domain név csak az ip address
java -jar test-program.jar --> jar futtatás 
ubuntu package keresés: apt-cache search jdk


