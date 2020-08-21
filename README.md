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


_1.	Dentro de la interfaz de Monitoring with Sysdig vaya configuración y haga clic sobre **Notification channels*._



<p align="center">
<img width="696" alt="7" src="https://github.com/emeloibmco/IBM-Cloud-Monitoring-WebHook---Service-Now/blob/master/imagenesw/1.png">
</p>

_•	Diríjase a la sección de **clústers** y dar clic en el que se desea acceder._

_•	Se da clic en el botón **Actions...** y luego en la sección que dice **Terminal Web**._

![WhatsApp Image 2020-06-09 at 11 30 23 AM](https://user-images.githubusercontent.com/60628267/84174858-bc304700-aa44-11ea-99d7-02065ad676cc.jpeg)

 
_La terminal que se abre al terminar el paso anterior, es una terminal similar a la que maneja un sistema operativo como Ubuntu._

### Instalación de Knative en el cluster de Kubernetes (IKS) 📦

**NOTA: En este punto de la guía debemos estar en la consola web del cluster**
_Para instalar corectamente Knative en el cluster se deben seguir los siguientes pasos:_ 

_1. Habilite el complemento Knative gestionado en el clúster, esto lo puede hacer mediante el siguiente comado:_

```
ibmcloud ks cluster addon enable knative --cluster <cluster_name_or_ID> -y
```
_La salida de este comado debe ser:_

```
Enabling add-on knative for cluster knative...
OK
```

_2. Verifique que Istio se ha instalado correctamente. Todos los pods correspondientes a los servicios de Istio deben estar en el estado **Running**, y para poder verificarlo debemos utilizar el siguiente comando:_

```
kubectl get pods --namespace istio-system
```
_La salida de este comado debe ser:_

```
NAME                                       READY     STATUS      RESTARTS   AGE
istio-citadel-748d656b-pj9bw               1/1       Running     0          2m
istio-egressgateway-6c65d7c98d-l54kg       1/1       Running     0          2m
istio-galley-65cfbc6fd7-bpnqx              1/1       Running     0          2m
istio-ingressgateway-f8dd85989-6w6nj       1/1       Running     0          2m
istio-pilot-5fd885964b-l4df6               2/2       Running     0          2m
istio-policy-56f4f4cbbd-2z2bk              2/2       Running     0          2m
istio-sidecar-injector-646655c8cd-rwvsx    1/1       Running     0          2m
istio-statsd-prom-bridge-7fdbbf769-8k42l   1/1       Running     0          2m
istio-telemetry-8687d9d745-mwjbf           2/2       Running     0          2m
prometheus-55c7c698d6-f4drj                1/1       Running     0          2m
```

_3. **Opcional:** si desea utilizar Istio para todas las demás apps en el espacio de nombres **default**, añada la etiqueta **istio-injection=enabled** al espacio de nombres, si desea realizarlo el comado es el siguiente:_


```
kubectl label namespace default istio-injection=enabled
```
_4. Verifique que todos los pods del componente Serving de Knative están en el estado Running. Verifiquelo mediante el siguiente comando:_
