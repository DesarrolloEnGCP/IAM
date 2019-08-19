# Laboratorio de IAM

## En este laboratorio revisaremos:
1) Como asignar propiedades a una configuración (proyecto y cuenta)
2) Como invitar usuarios (miembros) y asginarles un Rol
3) La relación entre un usuario y un rol (pueden ser muchos)
4) La relación entre un rol y sus permisos (para un recurso especifico: Storage)

## Listar configuraciones (todas)
```bash
gcloud config configurations list
```

## Obtener nombre de la configuración activa (y guardamos en variable config_activa)
```bash
export config_activa=$(gcloud config configurations list --filter="IS_ACTIVE=True" --format 'value(NAME)')
```

## Obtener lista de proyectos
```bash
gcloud projects list
```

## Obtener nombre del proyecto (y guardamos en variable proyecto)
```bash
export project=$(gcloud projects list --format 'value(PROJECT_ID)')
```

## Guardamos email de nuevo usuario administrador en la variable admin
```bash
export admin="admin1@instructor.ninja"
```
## Guardamos email de nuevo usuario developer en la variable dev
```bash
export dev="dev1@instructor.ninja"
```

## revisamos variables creadas
```bash
echo "configuracion activa: $config_activa, nombre del proyecto: $project, nuevo usuario: $admin y nuevo developer: $dev"
```
Este paso es MUY importante asegurate que se muestren correctamente las variables antes definidas, es decir que lo que se muestre en pantalla corresponda a algo como esto: 

configuracion activa: **NOMBRE CONFIGURACION**, nombre del proyecto: **NOMBRE DEL PROYECTO**, nuevo usuario: **email usuario admin** y nuevo developer: **email usuario administrador**

## Crear una nueva configuración para el administrador
```bash
gcloud config configurations create config-$project-admin
```

## Ver detalles de configuración activa(cambio a la creada recientemente)
```bash
gcloud config list
```
## Asignaremos el proyecto a la configuracion activa (dev1)
```bash
gcloud config set project $project
```

## Dar acceso a usuario administrador
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding $project --member user:$admin --role roles/editor
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy $project

## Autenticarse (cambiaremos de usuario al nuevo administrador)
```bash
gcloud auth login
```
You are already authenticated with gcloud when running
inside the Cloud Shell and so do not need to run this
command. Do you wish to proceed anyway?
Do you want to continue (Y/n)?

Go to the following link in your browser: https://accounts.google.com/o/oauth2/auth?

## Ver detalles de configuración (y comprobar que account ha cambiado al usuario con que hicimos login)
```bash
gcloud config list
```

## Crear una nueva configuración para el developer
```bash
gcloud config configurations create config-$project-dev
```

## Asignaremos el proyecto a la configuracion activa (dev1)
```bash
gcloud config set project $project
```
## Dar acceso a usuario developer
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding $project --member user:$dev --role roles/viewer
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy $project

## Autenticarse (cambiaremos de usuario a dev1@instructor.ninja)
```bash
gcloud auth login
```

## Ver configuración activa (Verificar que usuario es dev1@instructor.ninja)
```bash
gcloud config list
```

## Ver configuraciones creadas hasta ahora (PROJECT estara sin asginar para las configuraciones creadas)
```bash
gcloud config configurations list
```

## Comprobamos rol(es) del usuario Administrador
```bash
gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$admin"
```

## Comprobamos rol(es) del usuario Developer
```bash
gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$dev"
```

## Comparamos permisos para el Administrador
```bash
gcloud iam roles describe $(gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$admin" --format "value(bindings.role)") --flatten="includedPermissions[]" --format="table(includedPermissions)" | grep storage
```

## Comparamos permisos para el Developer
```bash
gcloud iam roles describe $(gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$dev" --format "value(bindings.role)") --flatten="includedPermissions[]" --format="table(includedPermissions)" | grep storage
```

## Cambiaremos a la configuración Developer
```bash
gcloud config configurations activate config-$project-dev
```

## Crearemos -intentaremos- un Bucket (repositorio de almacenamiento)
```bash
gsutil mb gs://bucket-$project
```
Como no tenemos permisos para crear buckets (solo viualizar) obtendremos el error:
Creating gs://bucket-**id-proyecto**/...
AccessDeniedException: 403 **email** does not have storage.buckets.create access to project **numero de proyecto**


## Cambiaremos a la configuración Administrador
```bash
gcloud config configurations activate config-$project-admin
```

## Crearemos un Bucket (repositorio de almacenamiento)
```bash
gsutil mb gs://bucket-$project
```
Comando ejecutado sin problemas: Creating gs://bucket-**id-proyecto**/...

## Crearemos un archivo de texto y lo subiremos al bucket
```bash
echo "hola mundo" > archivo.txt ; gsutil cp archivo.txt gs://bucket-$project
```

## Listaremos el contenido del bucket
```bash
gsutil ls gs://bucket-$project
```

## Cambiaremos a la configuración Developer
```bash
gcloud config configurations activate config-$project-dev
```

## Listaremos el contenido del bucket
```bash
gsutil ls gs://bucket-$project
```

## Crearemos un archivo de texto (2) y lo subiremos al bucket
```bash
echo "hola mundo" > archivo2.txt ; gsutil cp archivo2.txt gs://bucket-$project
```
Obtenemos el error:
Copying file://archivo2.txt [Content-Type=text/plain]...
AccessDeniedException: 403 **email** does not have storage.objects.create access to bucket-$project/archivo2.txt.

**Solo tenemos permiso para leer archivos en un bucket, no para crear Buckets, ni crear archivos en ellos**

## Cambiaremos a la configuración Administrador
```bash
gcloud config configurations activate config-$project-admin
```

## Obtener politica del Bucket
```bash
gsutil iam get gs://bucket-$project
```

## Damos Rol "Object Creator" a Developer
```bash
gsutil iam ch user:$dev:objectCreator gs://bucket-$project
```

## Obtener politica del Bucket, actualziada.
```bash
gsutil iam get gs://bucket-$project
```

## Cambiaremos a la configuración Developer
```bash
gcloud config configurations activate config-$project-dev
```

## Crearemos un archivo de texto (2) y lo subiremos al bucket
```bash
echo "hola mundo" > archivo-dev.txt ; gsutil cp archivo-dev.txt gs://bucket-$project
```
## Listaremos el contenido del bucket
```bash
gsutil ls gs://bucket-$project
```

## Cambiaremos a la configuración Administrador
```bash
gcloud config configurations activate config-$project-admin
```

## Quitamos Rol "Object Creator" a Developer
```bash
gsutil iam ch -d user:$dev:objectCreator gs://bucket-$project
```

## Cambiaremos a la configuración Developer
```bash
gcloud config configurations activate config-$project-dev
```

## Subiremos el mismo archivo al bucket, pero con otro nombre (en destino)
```bash
gsutil cp archivo-dev.txt gs://bucket-$project/archivo-dev-1.txt
```
