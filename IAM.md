# Cloud SDK

## Laboratorio de IAM

En este laboratorio revisaremos:
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

## Cambiaremos a la configuración admin
```bash
gcloud config configurations activate $config_activa
```

## Dar acceso a usuario administrador
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding $project --member user:$admin --role roles/editor
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy $project

## Dar acceso a usuario developer
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding $project --member user:$dev --role roles/viewer
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy $project

## Comprobamos rol(es) del usuario Administrador
```bash
gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$admin"
```

## Comprobamos rol(es) del usuario Developer
```bash
gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$dev"
```

## Comparamos permisos para "Storage" de Administrador
```bash
gcloud iam roles describe $(gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$admin" --format "value(bindings.role)") --flatten="includedPermissions[]" --format="table(includedPermissions)" | grep storage
```

## Comparamos permisos para "Storage" de Developer
```bash
gcloud iam roles describe $(gcloud projects get-iam-policy $project --flatten="bindings[].members" --filter="bindings.members:user:$dev" --format "value(bindings.role)") --flatten="includedPermissions[]" --format="table(includedPermissions)" | grep storage
```
