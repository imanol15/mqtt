
# Implementación de un Bróker MQTT Seguro y Aplicación de Python

## Autor: Imanol Anda Garcia

Este proyecto muestra cómo montar un bróker MQTT seguro que utiliza autenticación basada en certificados de cliente y servidor, y cómo interactuar con él mediante una aplicación de Python y comandos de línea de comandos.

## Explicación de los Pasos Seguidos

- Configuración de un bróker MQTT utilizando Mosquitto con certificados SSL/TLS para asegurar la comunicación.
- Desarrollo de aplicaciones en Python (`mqtt_publisher.py` y `mqtt_subscriber.py`) que publican y suscriben a mensajes de forma segura utilizando los certificados generados.
- Instrucciones para el uso de comandos `mosquitto_pub` y `mosquitto_sub` desde la línea de comandos para interactuar con el bróker MQTT.

## Instrucciones de Uso

### Montar el Bróker MQTT

1. Instalar Mosquitto y configurar el archivo `mosquitto.conf` con los parámetros adecuados.
2. Generar los certificados necesarios utilizando el script `generar_certificados.sh`.
3. Iniciar el servicio Mosquitto con la nueva configuración.

### Utilizar la Aplicación de Python

1. Ejecutar `mqtt_publisher.py` para publicar mensajes en un tópico.
2. En una terminal diferente, ejecutar `mqtt_subscriber.py` para suscribirse al tópico y recibir mensajes.

### Interactuar desde la Línea de Comandos

- Publicar un mensaje:
  mosquitto_pub -h [BrokerIP] -p [Port] -t "[Topic]" -m "Hello, MQTT!" --cafile [PathToCAfile] --cert [PathToCertFile] --key [PathToKeyFile] -u "[Username]" -P "[Password]" --tls-version tlsv1.2
- Suscribirse a un tópico:
   mosquitto_sub -h [BrokerIP] -p [Port] -t "[Topic]" --cafile [PathToCAfile] --cert [PathToCertFile] --key [PathToKeyFile] -u "[Username]" -P "[Password]" --tls-version tlsv1.2
  
 ## Posibles Vías de Mejora 
  - Funcionalidades Adicionales:
    Ampliar la aplicación para soportar más temas y patrones de publicación/suscripción avanzados
  - Uso de Contenedores:
    Considerar la implementación de Mosquitto en contenedores Docker para facilitar el despliegue, la 
    escalabilidad y la gestión de la configuración.
  - Mejora de la Seguridad:
    Uso de diferentes clientes para realizar publicaciones.
 ## Problemas encontrados
  - Inicio de Servicio Mosquitto: Me encontré con un problema al intentar iniciar el servicio de Mosquitto      usando systemctl. El servicio no se iniciaba y no ofrecía detalles sobre la causa. Al ejecutar              Mosquitto en primer plano con mosquitto -c /etc/mosquitto/mosquitto.conf -v, pude identificar el            problema a través de los mensajes de error que se mostraban.
  - Configuración de SSL/TLS Incorrecta: Hubo complicaciones al configurar SSL/TLS para el servicio de          Mosquitto que podían ocasionar fallos en el inicio del mismo. Verifiqué que las rutas a los         
    certificados y claves en mosquitto.conf fueran correctas, que los archivos tuvieran los permisos 
    adecuados y que los certificados fueran válidos y estuvieran en el formato apropiado. Estos ajustes 
    solucionaron los problemas de configuración de SSL/TLS.
  - Problemas de Autenticación MQTT: Me encontré con un obstáculo significativo debido a errores de 
    autenticación cuando los clientes intentaban conectarse al bróker MQTT. A pesar de tener una 
    configuración de seguridad sólida con SSL/TLS, la autenticación de clientes seguía siendo un punto de 
    fallo.

    Para abordar esto, realicé una revisión de las credenciales de los clientes MQTT. Me percaté de que, 
    aunque los certificados estaban correctamente configurados, no había especificado en mis scripts de 
    Python las credenciales de usuario y contraseña necesarias para la autenticación con el bróker.

    La solución fue añadir el método username_pw_set en la configuración del cliente MQTT en mis scripts, 
    proporcionando así el nombre de usuario y la contraseña esperados por el bróker. Además, actualicé el 
    archivo de configuración mosquitto.conf para incluir la dirección donde se crearon los certificados, 
    reforzando la seguridad y permitiendo la autenticación exitosa de los clientes.

## Conclusión

El proyecto me ha permitido establecer un sistema de mensajería MQTT robusto y seguro, garantizando la privacidad y la integridad de los datos transmitidos. La capacidad de interactuar con el bróker tanto a través de una aplicación de Python como mediante comandos de línea de comandos proporciona una flexibilidad considerable para diferentes casos de uso.

## mosquitto.conf:

pid_file /run/mosquitto/mosquitto.pid

persistence true
persistence_location /var/lib/mosquitto/

log_dest file /var/log/mosquitto/mosquitto.log

include_dir /etc/mosquitto/conf.d

listener 8883
cafile /home/imanolanda/entregable_2/certificados/ca.crt
certfile /home/imanolanda/entregable_2/certificados/server.crt
keyfile /home/imanolanda/entregable_2/certificados/server.key
require_certificate true
allow_anonymous false
tls_version tlsv1.2

password_file /home/imanolanda/entregable_2/passwd





