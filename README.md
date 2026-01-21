# Practica GitBARE - Antonio Gat Fernández

Esta practica se podrá encontrar en [ut4.antoniogatfdezpruebas.es](https://ut4.antoniogatfdezpruebas.es/) y en el repositorio que he usado es: [github.com/antoniogatfdezzz/antoniobare](https://github.com/antoniogatfdezzz/antoniobare)


## Configuraciones iniciales

### Local y GitHub
1. Para empezar creamos un repositorio nuevo en GitHub y copiamos lo siguiente:
    ````
    echo "# antoniobare" >> README.md
    git init
    git add README.md
    git commit -m "first commit"
    git branch -M main
    git remote add origin git@github.com:antoniogatfdezzz/antoniobare.git
    git push -u origin main
    ````
2. En nuestro ordenador creamos una carpeta con: `mkdir antoniobare` y ejecutamos lo anterior dentro de ella (Se creará un `README.md`).
3. Ejecutaremos el siguiente comando en la terminal para conectar nuestro repositorio local con el repositorio de nuestro servidor: 
    ````
    git remote add prod ssh://HOST/home/ubuntu/N-carpeta
    
    # En mi caso:
    git remote add prod ssh://GitBARE/home/ubuntu/antoniobare
    ````

### Remoto (EC2 AWS)
1. Descargamos DOCKER usando los siguientes comandos:
   ````
   curl -fsSL https://get.docker.com -o get-docker.sh
   sudo sh get-docker.sh

   sudo groupadd docker
   sudo usermod -aG docker $USER
   newgrp docker
   `````
2. Creamos la red de Caddy
3. Creamos los Compose de Caddy y Nginx, ejecutamos amos usando `docker compose up` en cada carpeta de los contenedores. (Los encontrarás en esta carpeta).
4. Creamos la carpeta para el repositorio y dentro de esta ejecutamos lo siguiente: `git init --bare` y posteriormente: `git branch -M main`
5. También crearemos el `hooks/post-receive` ejecutando: `nano hooks/post-receive`. Dentro de este estará el siguiente código.
    ````
    #!/bin/bash

    GIT_DIR=/home/ubuntu/antoniobare
    GIT_WORK_TREE=/home/ubuntu/nginx/web/www

    if [ ! -d $GIT_WORK_TREE ]; then
            mkdir $GIT_WORK_TREE
    fi

    git --git-dir=$GIT_DIR --work-tree=$GIT_WORK_TREE checkout -f main
    cd $GIT_WORK_TREE
    git --git-dir=$GIT_DIR --work-tree=$GIT_WORK_TREE submodule update --init --recursive
    ````
    Finalmente le damos permisos de ejecución al hook usando: `chmod +x hooks/post-receive`


## Como subir nuevos archivos desde Local a GitHub y BARE

Crearemos un archivo en nuestro ordenador un `index.html` con el siguiente contenido:
````
<h1> Hola mundo, tu nombre </h1>

# En mi caso:
<h1> Hola mundo, Antonio Gat Fernández </h1>
````

### GitHub
1. En la terminal vamos hasta la carpeta del repositorio, en mi caso: `cd antoniobare`.
2.  Tras crear/modificar un documento ejecutamos :
    1.  `git add .` o `git add nombre-del-archivo`
    2.  `git commit -m"Mensaje Explicativo a la subida`
    3.  `git push` (Sube el/los archivos al repositorio de GitHub)
3.  Tras esto los cambios aparecerán en el repositorio de GitHub

### GitBARE (EC2 AWS)
1. En la terminal vamos hasta la carpeta del repositorio, en mi caso: `cd antoniobare`.
2.  Tras crear/modificar un documento ejecutamos :
    1.  `git add .` o `git add nombre-del-archivo`
    2.  `git commit -m"Mensaje Explicativo a la subida`
    3.  `git push prod main` (Sube el/los archivos a Git BARE de la EC2)
3. Tras esto los cambios aparecerán en el repositorio de [ut4.antoniogatfdezpruebas.es](https://ut4.antoniogatfdezpruebas.es/)