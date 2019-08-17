# Cloud SDK

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

## Autenticarse (cambiaremos de usuario al nuevo administrador)
```bash
gcloud auth login
```

## Ver detalles de configuración (y comprobar que account ha cambiado al usuario con que hicimos login)
```bash
gcloud config list
```

## Dar acceso a usuario administrador
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding $project --member user:$admin --role roles/editor
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy $project

## Crear una nueva configuración para el developer
```bash
gcloud config configurations create config-$project-dev
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

## Asignaremos el proyecto a la configuracion activa (dev1)
```bash
gcloud config set project $project
```

## Cambiaremos a la configuración admin
```bash
gcloud config configurations activate config-$project-admin
```

## Asignaremos el proyecto a la configuracion activa (admin)
```bash
gcloud config set project $project
```

## Ver configuraciones creadas hasta ahora (PROJECT estara ASIGNADO para todas las configuraciones)
```bash
gcloud config configurations list
```

