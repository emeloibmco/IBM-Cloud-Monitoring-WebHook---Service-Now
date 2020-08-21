# IBM-Cloud-Monitoring-WebHook---Service-Now

A continuación se presenta una guía para realizar la configuración integración de Monitoring with Sysdig con Servicenow para la implementación de webhooks, esto teniendo en cuenta que Sysdig Monitor y Sysdig Secure admiten el envío de una notificación de alerta a un destino (un sitio web, una aplicación personalizada, etc.) para el cual agente de Sysdig no tiene una integración nativa. 

### Indice
1. [Pre-requisitos](#Pre-requisitos-)
2. [Configuración de Monitoring with Sysdig](#Configuración-de-Monitoring-with-Sysdig-)
3. [Configuración de Servicenow](#Configuración-de-Servicenow-)
4. [Prueba y envío de webhooks](#Prueba-y-envío-de-webhooks-)
5. [Referencias](#Referencias)

## Pre-requisitos 📋

_1. Tener una cuenta de ServiceNow configurada y funcionando._

_2. Si es necesario, consulte la documentación para desarrolladores de ServiceNow [aquí](https://developer.servicenow.com/dev_app.do#!/documentation?v=london)._

_3. Contar con una instancia activa en Monitoring With Sysdig **IBM Cloud**._

## Configuración de Monitoring with Sysdig: 🚀

### Haga 'login' a IBM Cloud.

_Inicialmente debe acceder a IBM Cloud desde el siguiente link:_

```
https://cloud.ibm.com/
```
### Acceda a su instancia de Monitoring with Sysdig 📦


_1.	Dentro de la interfaz de Monitoring with Sysdig vaya configuración y haga clic sobre **Notification channels** y en **Add Notification Channel** seleccione **webhooks**._



<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/1.png">
</p>
_2. Ingrese las opciones de configuración del canal de webhook:_

**URL**: la URL de destino a la que se enviarán las notificaciones. Nombre del canal: agregue un nombre significativo, como "Ansible", "Webhook.Site", etc.

**Habilitado**: activar / desactivar las opciones de notificación: activar lasnotificaciones cuando se resuelven o reconocen las alertas.

**Notificación de prueba**: alterne para recibir una notificación de que la URL configurada está funcionando.

_3. De compartido con: elija si desea aplicar este canal globalmente ( Todos los equipos ) o a un equipo específico en el menú desplegable._

_4.Haga clic en Save._

Cuando se crea el canal, puede usarlo en cualquier alerta que cree.

Luego, cuando se active la alerta, la notificación se enviará como POST en formato JSON a su punto final de webhook. (Consulte Salida de alerta, a continuación).

Con fines de prueba, puede utilizar un sitio de terceros para crear un punto final temporal y ver exactamente qué enviará una alerta de Sysdig en cualquier notificación específica.

## Configuración de Servicenow 📦

_Para instalar corectamente Knative en el cluster se deben seguir los siguientes pasos:_ 

_1. Inicie sesión en [ServiceNow](https://developer.servicenow.com/dev.do#!/home) (entrada de desarrollador) y cree una API REST con script:_

<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/2.png">
</p>

_2.Haga clic y envíe el formulario con lo siguiente: **New**

Nombre: ID de API de SysdigAlert : sysdigalert_
_3.Regrese al Scripted REST APIsy abra el recurso recién creado.

Baje hasta el área de la lista relacionada, seleccione , y haga clic . Esto creará un nuevo recurso API REST con script. **Resources New**_.


_4.Complete el campo, por ejemplo, Demo. **Name**
<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/3.png">
</p>

_5.Desplácese hacia abajo **Security** y desmarque la casilla de verificación que requiere autenticación.


<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/4.png">
</p>

_6.Cambie HTTP methodde GET a POST.

### Agregar el código a la nueva API

Una vez realizado los pasos anteriores deberá agregar el script que permitirá obtener los post que envia monitoring.

Ahora dale al recurso el código para ejecutar.Los objetos predeterminados con los que trabajar en un recurso API REST con script son responsey request.

El recurso creado ya tendrá algún código de ejemplo:

```
(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {  
  
    // implement resource here  
  
})(request, response);  
```
7. Cambie este código predeterminado a:
```
(function process(/*RESTAPIRequest*/ request, /*RESTAPIResponse*/ response) {  
  
 gs.info(request.body.dataString);  
  
})(request, response); 
```
8.Tenga en cuenta que la siguiente ruta de recursos a este recurso recién creado ahora está visible: / api / snc / sysdigalert.
La URL de este recurso sería https://yourInstance.service-now.com/<resource_Path

<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/5.png">
</p>

Haga clic en Enviar / Actualizar en este recurso.

## Prueba y envío de webhooks

Ahora que se crea el punto final de API personalizado en ServiceNow, puede configurar las alertas de Sysdig para usar un webhook personalizado para activar la integración de ServiceNow.

**API URL**: la URL del nombre de su instancia

**Nombre**: ServiceNow (o el nombre que desee para este webhook de alerta de Sysdig)

**Notificar cuando esté bien**: opcional

**Notificar cuando se resuelva**: Opcional

**Notificación de prueba** : Utilice este interruptor y / o configure una alerta de prueba como se describe en la siguiente sección.

<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/6.png">
</p>

Para probar si esta integración de ServiceNow está configurada y funciona correctamente, puede configurar una alerta de prueba para que se active o **activar test notification** _(recomendado)_.

Por ejemplo, puede crear una alerta para el uso de la CPU:

<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/7.png">
</p>

En ServiceNow, navegue hasta System log para ver un webhook activado de muestra. **System Log > All**

<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/8.png">
 </p>
 
## Referencias

1. [Configure a Webhook Channel](https://docs.sysdig.com/en/configure-a-webhook-channel.html)
 
 
