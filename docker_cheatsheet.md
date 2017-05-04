https://hackernoon.com/docker-containerd-standalone-runtimes-heres-what-you-should-know-b834ef155426

__DOCKER__

> root of a docker container = image define in a docker file ( define how the 
image is built), à partir de ce docker file on peut INSTANCIER une image 
localement ou la prendre d'un registre d'image, 
le plus souvent on prend une image de base et on la surcharge pour notre app


__DOCKER_FILE__

#comments
ENV = variable d'environement qui peuvent être utilisé dans le fichier ( données
	hardcodées), peuvent être overriden pendant le build à la console
	
RUN //define commands that will be run during the buil process
	/!\ every run command result in a new layer image, so a new layer is applied 
	to the image

WORKDIR // cd or change directory

ADD // take a local file location and add this file to the image during the 
	build process /!\ si c'est un fichier compressé -> local file wil be extracted
	automatically -> remote URI not => Run command to extract it
	
__VOLUMES__
> classic virtualization -> write locally and then do post-processing
> Docker -> volume = storage location for data, data are separated from the app
