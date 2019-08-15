# Cloud SDK

## Listar configuraciones (todas)
```bash
gcloud config configurations list
```

## Obtener nombre de la configuración activa (y guardamos en archivo config_activa)
```bash
echo $(gcloud config configurations list --filter="IS_ACTIVE=True" --format 'value(NAME)') > config_activa ; cat config_activa
```

## Obtener lista de proyectos
```bash
gcloud projects list
```

## Crear una nueva configuración (reemplazar ID del proyecto)
```bash
gcloud projects get-iam-policy *ID_DEL_PROYECTO*
```

## Crear una nueva configuración (reemplazar ID del proyecto)
```bash
gcloud config configurations create config-*ID_DEL_PROYECTO*-admin
```

## Ver detalles de configuración (activa...cambio por la creada recientemente)
```bash
gcloud config list
```

## Autenticarse (cambiaremos de usuario)
```bash
gcloud auth login
```

## Ver detalles de configuración (y comprobar que account ha cambiado al usuario con que hicimos login)
```bash
gcloud config list
```

## Dar acceso a usuario: dev1@instructor.ninja (*Deben reemplazarlo por otro usuario de propiedad de UDs*)
*Si aparece el error: "Email addresses and domains must be associated with an active Google Account or Google Apps account." Es por que el usuario no tiene una "cuenta de google" activa y asociada el email ingresado*
*Para crear una (para cualquier email sea o no de Google) Google Account: https://accounts.google.com/SignUp*
```bash
gcloud projects add-iam-policy-binding *ID_DEL_PROYECTO* --member user:dev1@instructor.ninja --role roles/viewer
```
Un resultado sin error de la ejecución de este comando es obtener la configuración de la politica completa, es decir el equivalente a ejecutar: gcloud projects get-iam-policy *ID_DEL_PROYECTO*

## Crear una nueva configuración (reemplazar ID del proyecto)
```bash
gcloud config configurations create config-*ID_DEL_PROYECTO*-dev1
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
gcloud config set project *ID_DEL_PROYECTO*
```

## Cambiaremos a la configuración admin
```bash
gcloud config configurations activate config-*ID_DEL_PROYECTO*-admin
```

## Asignaremos el proyecto a la configuracion activa (admin)
```bash
gcloud config set project *ID_DEL_PROYECTO*
```

## Ver configuraciones creadas hasta ahora (PROJECT estara ASIGNADO para todas las configuraciones)
```bash
gcloud config configurations list
```

