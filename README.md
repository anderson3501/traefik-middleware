# traefik-middleware

TALLER Gateway de servicios con Traefik
1. Diagrama simple de la solución (Traefik, API (x2), Neo4j) y redes.

![Diagrama traefik](https://github.com/user-attachments/assets/456d7171-9eda-4ce1-92b2-8a842c5c93a3)

2. Los host cofigurados para el enrrutamineto de traefik son:

    ops.localhost: Usado para acceder al dashboard de Traefik.

    api.localhost: Usado para acceder a la API de la aplicación.
3.  Comprobaciones
  3.1. API en api.localhost responde a /health
    <img width="923" height="229" alt="image" src="https://github.com/user-attachments/assets/c832ca66-7106-416c-a671-8521f7570f74" />
  3.2. Dashboard accesible solo en ops.localhost/dashboard/ y con autenticación
    <img width="1456" height="922" alt="image" src="https://github.com/user-attachments/assets/bd298413-bbb8-455b-91c2-25690b93b23b" />

  3.3. Servicios y Routers visibles en el Dashboard
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6f061fc4-5eb5-40e1-aebe-02ea9be40760" />
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/62942c06-62e3-466b-b041-f030b3bfcf39" />
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/6a63e2b2-b599-4b27-b002-ef5498a17f20" />
  <img width="1920" height="1080" alt="image" src="https://github.com/user-attachments/assets/c8fdd55e-430d-430a-ab37-a53c80880ecb" />

  3.4. Rate-limit o stripPrefix aplicado y verificado: Aca se demuestra la efectividad del reateLimit con un buvle de 300 peticiones donde no pudo responde 133 que esta en falied por el rate de 100 que le interpusimos  
    daniel-reyes@daniel-reyes-VirtualBox:~/traefik-middleware$ ab -n 200 -c 100 http://api.localhost/
  This is ApacheBench, Version 2.3 <$Revision: 1903618 $>
  Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
  Licensed to The Apache Software Foundation, http://www.apache.org/

  Benchmarking api.localhost (be patient)
  Completed 100 requests
  Completed 200 requests
  Finished 200 requests


  Server Software:        nginx/1.29.1
  Server Hostname:        api.localhost
  Server Port:            80

  Document Path:          /
  Document Length:        617 bytes

  Concurrency Level:      100
  Time taken for tests:   0.551 seconds
  Complete requests:      200
  Failed requests:        133
   (Connect: 0, Receive: 0, Length: 133, Exceptions: 0)
  Non-2xx responses:      200
  Total transferred:      81356 bytes
  HTML transferred:       43600 bytes
    Requests per second:    363.02 [#/sec] (mean)
  Time per request:       275.466 [ms] (mean)
  Time per request:       2.755 [ms] (mean, across all concurrent requests)
  Transfer rate:          144.21 [Kbytes/sec] received

  Connection Times (ms)
              min  mean[+/-sd] median   max
  Connect:        0    7   7.5      2      19
  Processing:     2   98  78.4    110     292
  Waiting:        1   96  78.7    109     291
  Total:          2  105  83.1    121     307

  Percentage of the requests served within a certain time (ms)
  50%    121
  66%    136
  75%    155
  80%    164
  90%    232
  95%    281
  98%    300
  99%    301
 100%    307 (longest request)
daniel-reyes@daniel-reyes-VirtualBox:~/traefik-middleware$ 
<img width="866" height="783" alt="image" src="https://github.com/user-attachments/assets/29134c9b-39f0-43a3-8f6d-d9ff735e012f" />

3.5.Balanceo entre 2 réplicas evidenciado: Aca se mostro como se ivan alternando las dos replicas del backend para responder las peticiones y es evidenciable en el hostname que es diferente entre si 
daniel-reyes@daniel-reyes-VirtualBox:~/traefik-middleware$ for i in {1..10}; do curl http://api.localhost/v1/personas; echo ""; done
{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "3c53294fcb50",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "b0d5d25da71f",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "3c53294fcb50",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "b0d5d25da71f",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "3c53294fcb50",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "b0d5d25da71f",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "3c53294fcb50",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "b0d5d25da71f",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "3c53294fcb50",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

{
  "data": [
    {
      "edad": 26,
      "id": "34189ec4-61dc-4f5c-aadb-5c556049a5b8",
      "nombre": "Daniel"
    },
    {
      "edad": 69,
      "id": "2753c95a-a34c-4c28-82ce-2a0e6b808059",
      "nombre": "Camila"
    }
  ],
  "has_next": false,
  "has_prev": false,
  "hostname": "b0d5d25da71f",
  "page": 1,
  "per_page": 50,
  "total_pages": 1,
  "total_records": 2
}

daniel-reyes@daniel-reyes-VirtualBox:~/traefik-middleware$ 

<img width="866" height="783" alt="image" src="https://github.com/user-attachments/assets/b1ec6f02-7f27-43f8-9ac8-a6b344c550a9" />

Bonus Parcial 

Paginar de error 404  personalizada 
<img width="1797" height="1012" alt="image" src="https://github.com/user-attachments/assets/d8163f24-da2c-489d-b4bb-79de57e7cf4a" />

4. Breve reflexión técnica:

¿Qué aporta Traefik frente a mapear puertos directamente?

Traefik nos da mas control ya que detecta los nuevos sevicios y los enruta segun los labels que configuremos, una mejor seguridad mediante los middlewares, permite distribuir el trafico de peticiones, esto mejora la escalabilidad  y la disponibilidad de la api, mientras que al mapear los puertos nos exponemos al exponer los puertos, esto da menor seguridad para la api

¿Qué middlewares usarían en producción y por qué?

Algunos de los middlewares que usaría en producción son: 
- IpAllowList ya que esta acepta o rechaza las solicitudes que se hacen según la ip del cliente que las realiza
- BasicAuth esta es importante para realizar las autenticaciones y que sea mas fácil de proteger las rutas ya que solo accedera quien tenga las credenciales
- RedirectScheme ese fuerza la dirección http a https y la convierte en mas segura
- Headers este middleware administra las solicitudes y los permite modificar o eliminar si es necesario 
- StripPrefix esta es para cuando tenemos muchas rutas de microservicios y los enruta bajo un mismo dominio quitando un prefijo de la ruta original

Riesgos de dejar el dashboard “abierto” y cómo mitigarlos.

al dejar el dashboard abierto podemos exponer la posible información sensible que tengamos, como de claves credenciales o las bases de datos de los usuarios y empleados, algún atacante poddria realizar cambios que no sean deseados en nuestra api y tambine manipular las rutas y encontrar mas vulnerabilidades de las que aprovecharse

para poder mitigar esto es necesario usar los middlewares de autentificación como authbasic para dar acceso solo a ip confiables, y evitar exponer las rutas sensibles, también mantener nuestro traefik y middlewares siempre actualizadas

## Integrantes
## Daniel ALejandro Reyes Leon
## Anderson Eduardo Carvajal Oliveros



