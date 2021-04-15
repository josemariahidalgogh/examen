# ACTIVIDAD Nº 2

## TÍTULO DE LA ACTIVIDAD: Haciendo persistente la aplicación GuestBook 

## TEXTO DE LA ACTIVIDAD

En este ejercicio vamos a volver a desplegar nuestra aplicación GuestBook, que realizamos en [Actividad 5.3: Despliegue de la aplicación GuestBook](../modulo5/actividad3.md) y en la [Actividad 6.1: Acceso de la aplicación GuestBook](../modulo6/actividad1.md) para añadirle persistencia a la base de datos redis.

Por lo tanto necesitaremos solicitar un volumen, que se asociará de forma dinámica.

## Creando el despliegue de redis para que guarde la información en un directorio

Si estudiamos la documentación de la imagen redis en [Docker Hub](https://hub.docker.com/_/redis), para que la información de la base de datos se guarde en un directorio `/data` del contenedor hay ue ejecutar con docker:

$ docker run --name some-redis -d redis redis-server --appendonly yes

Es decir, hay que crear el contenedor ejecutando el proceso `redis-server` con los argumentos `--appendonly yes`.

Por lo tanto tenemos que cambiar el fichero de definición del despliegue de redis, [`redis-deployment.yaml`](/files/guestbook/plantilla-redis-deployment.yaml) de la siguiente manera:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis
  labels:
    app: redis
    tier: backend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
      tier: backend
  template:
    metadata:
      labels:
        app: redis
        tier: backend
    spec:
      volumes:
        - name: volumen-redis
          persistentVolumeClaim:
            claimName: xxxxxxxxxxxx
      containers:
        - name: contenedor-redis
          image: redis
          command: ["redis-server"]
          args: ["--appendonly", "yes"]
          ports:
            - name: redis-server
              containerPort: 6379
          volumeMounts:
            - mountPath: "xxxxxxxxxxxx"
              name: volumen-redis
```
Hemos usado el parámetro `command` para ejecutar el proceso, y el parámetro `args` para indicar los argumentos.

**Nota**: Los valores `xxxxxxxxxxxx` tendrás que rellenarlos durante la realización de la actividad.

## Pasos a seguir

Realiza los siguientes pasos:

1. Crea un fichero yaml para definir un recurso *PersistantVolumenClaim* que se llame `pvc-redis`, para solicitar un volumen de 3Gb.
2. Crea el recurso y comprueba que se ha asociado un volumen de forma dinámica a la solicitud.
3. Modifica el fichero del despliegue de redis, modificando las `xxxxxxxxxxxx` por los valores correctos: el nombre del *PersistantVolumenClaim* y el directorio de montaje en el contenedor.
4. Crea el despliegue de redis. El despliegue de la aplicación `guestbook` y la creación de los servicios de acceso se hacen con los ficheros que ya utilizamos anteriormente: [`guestbook-deployment.yaml`](files/guestbook/guestbook-deployment.yaml), [`guestbook-srv.yaml`](files/guestbook/guestbook-srv.yaml) y [`redis-srv.yaml`](files/guestbook/redis-srv.yaml).
5. Accede a la aplicación y escribe algunos mensajes.
6. Comprobemos la persistencia: elimina el despliegue de redis, vuelve a crearlo y vuelve acceder desde el navegador y comprueba que los mensajes no se han perdido.

Para superar la actividad deberás entregar en un fichero comprimido los siguientes pantallazos:

1. Pantallazo con la definición del recurso *PersistantVolumenClaim*.
2. Pantallazo donde se visualice los recursos `pv` y `pvc` que se han creado.
3. Pantallazo donde se vea el fichero yaml modificado para el despliegue de redis.
4. Pantallazo donde se vea el acceso a la aplicación con los mensajes escritos.
5. Pantallazo donde se vea que se ha eliminado y se ha vuelto a crear el despliegue de redis y que se sigue sirviendo la aplicación con los mensajes.

## RECURSOS

* Conexión a internet
* Los ficheros del [Ejemplo 2: Gestión dinámica de volúmenes](ejemplo2.md).

## ¿ES OBLIGATORIO HACER ESTA ACTIVIDAD PARA SUPERAR EL CURSO? (S/N)

Si

## ¿ES UNA ACTIVIDAD INDIVIDUAL O DE GRUPO?

Individual

## ¿ES UNA ACTIVIDAD CALIFICABLE?

Si

### ¿Tiene que ser calificada por el tutor/a? (S/N) 

Si

### ¿Es de calificación automática?

No

### ¿Es calificada por el resto de compañeros/as del curso? (S/N)

No

## EVALUACIÓN

* Se entregan los documentos; contienen lo solicitado y los contenidos son originales.

## ¿ES NECESARIO TENER TERMINADA ALGUNA ACTIVIDAD O RECURSO ANTERIOR? Indique cuáles.

No

## TIEMPO ESTIMADO PARA REALIZAR LA ACTIVIDAD

1 hora