---
title: "Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen | Microsoft Docs"
description: "Erfahren Sie, wie Sie automatischer Skalierungsvorgänge in Azure Monitor nutzen können, um Web-URLs aufzurufen oder E-Mail-Benachrichtigungen zu senden. "
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: eb9a4c98-0894-488c-8ee8-5df0065d094f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/03/2017
ms.author: ashwink
ms.translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: f0ab9d4bea3a3f7f1e1a0af2206e7b5641be1288
ms.contentlocale: de-de
ms.lasthandoff: 04/03/2017


---
# <a name="use-autoscale-actions-to-send-email-and-webhook-alert-notifications-in-azure-monitor"></a>Verwenden von automatischen Skalierungsvorgängen zum Senden von E-Mail- und Webhook-Warnbenachrichtigungen in Azure Monitor
In diesem Artikel wird beschrieben, wie Sie Trigger einrichten, damit Sie basierend auf Skalierungsvorgängen in Azure bestimmte Web-URLs aufrufen oder E-Mails senden können.  

## <a name="webhooks"></a>Webhooks
Mithilfe von Webhooks können Sie die Azure-Warnbenachrichtigungen für die Nachbearbeitung oder benutzerdefinierte Benachrichtigungen an andere Systeme weiterleiten. Beispielsweise können Warnungen an Dienste weitergeleitet werden, die eine eingehende Webanforderung zum Senden einer SMS, zum Protokollieren von Fehlern oder zum Benachrichtigen eines Teams über Chat- oder Messagingdienste usw. verarbeiten können. Der URI des Webhooks muss ein gültiger HTTP- oder HTTPS-Endpunkt sein.

## <a name="email"></a>E-Mail
E-Mails können an eine beliebige gültige E-Mail-Adresse gesendet werden. Administratoren und Co-Administratoren des Abonnements, in der die Regel ausgeführt wird, werden ebenfalls benachrichtigt.

## <a name="cloud-services-and-web-apps"></a>Clouddienste und Web-Apps
Sie können über das Azure-Portal für Cloud-Dienste und Serverfarmen (Web-Apps) teilnehmen.

* Wählen Sie die Metrik **skalieren nach** .

![skalieren nach](./media/insights-autoscale-to-webhook-email/insights-autoscale-notify.png)

## <a name="virtual-machine-scale-sets"></a>Skalierungsgruppen für virtuelle Computer
Bei neueren, mit Resource Manager erstellten virtuellen Computern (Skalierungsgruppen für virtuelle Computer) können Sie dies mit der REST-API, den Resource Manager-Vorlagen, PowerShell und der CLI konfigurieren. Eine Portalschnittstelle ist noch nicht verfügbar.
Wenn Sie die REST-API oder die Resource Manager-Vorlage verwenden, fügen Sie das Benachrichtigungselement mit den folgenden Optionen hinzu:

```
"notifications": [
      {
        "operation": "Scale",
        "email": {
          "sendToSubscriptionAdministrator": false,
          "sendToSubscriptionCoAdministrators": false,
          "customEmails": [
              "user1@mycompany.com",
              "user2@mycompany.com"
              ]
        },
        "webhooks": [
          {
            "serviceUri": "https://foo.webhook.example.com?token=abcd1234",
            "properties": {
              "optional_key1": "optional_value1",
              "optional_key2": "optional_value2"
            }
          }
        ]
      }
    ]
```
| Feld | Erforderlich? | Beschreibung |
| --- | --- | --- |
| operation |Ja |Als Wert muss „Scale“ angegeben werden. |
| sendToSubscriptionAdministrator |Ja |Als Wert muss „true“ oder „false“ festgelegt werden. |
| sendToSubscriptionCoAdministrators |Ja |Als Wert muss „true“ oder „false“ festgelegt werden. |
| customEmails |Ja |Der Wert kann NULL [] oder ein Zeichenfolgenarray aus E-Mail-Adressen sein. |
| Webhooks |Ja |Der Wert kann NULL oder ein gültiger URI sein. |
| serviceUri |Ja |Ein gültiger HTTPS-URI |
| Eigenschaften |Ja |Der Wert muss leer {} sein. Er kann auch Schlüssel-Wert-Paare enthalten. |

## <a name="authentication-in-webhooks"></a>Authentifizierung in Webhooks
Die Authentifizierung des Webhooks kann mithilfe der Token-basierten Authentifizierung erfolgen, wobei der Webhook-URI mit einer Token-ID (z.B. einem Abfrageparameter) gespeichert wird. Beispiel: https://beispielwarnung/web-rückruf?tokenid=token-id&parameter=wert

## <a name="autoscale-notification-webhook-payload-schema"></a>Benachrichtigung über automatische Skalierung mit dem Webhook-Nutzlastschema
Beim Generieren der Benachrichtigung über automatische Skalierung werden die folgenden Metadaten in die Webhook-Nutzlast aufgenommen:

```
{
        "version": "1.0",
        "status": "Activated",
        "operation": "Scale In",
        "context": {
                "timestamp": "2016-03-11T07:31:04.5834118Z",
                "id": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/autoscalesettings/myautoscaleSetting",
                "name": "myautoscaleSetting",
                "details": "Autoscale successfully started scale operation for resource 'MyCSRole' from capacity '3' to capacity '2'",
                "subscriptionId": "s1",
                "resourceGroupName": "rg1",
                "resourceName": "MyCSRole",
                "resourceType": "microsoft.classiccompute/domainnames/slots/roles",
                "resourceId": "/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService/slots/Production/roles/MyCSRole",
                "portalLink": "https://portal.azure.com/#resource/subscriptions/s1/resourceGroups/rg1/providers/microsoft.classicCompute/domainNames/myCloudService",
                "oldCapacity": "3",
                "newCapacity": "2"
        },
        "properties": {
                "key1": "value1",
                "key2": "value2"
        }
}
```


| Feld | Erforderlich? | Beschreibung |
| --- | --- | --- |
| status |Ja |Status, der angibt, dass ein automatischer Skalierungsvorgang generiert wurde |
| operation |Ja |Der Vorgang zum Erhöhen der Anzahl der Instanzen ist „Horizontal hochskalieren“ und „Horizontal herunterskalieren“ zum Verringern der Anzahl der Instanzen. |
| context |Ja |Der Kontext des automatischen Skalierungsvorgangs |
| timestamp |Ja |Zeitstempel der Auslösung des automatischen Skalierungsvorgangs |
| id |Ja |Resource Manager-ID der Einstellung für die automatische Skalierung |
| Name |Ja |Name der Einstellung für die automatische Skalierung |
| Details |Ja |Erläuterung der Aktion, die der Dienst für die automatische Skalierung ausgeführt hat und der Änderung der Instanzenanzahl |
| subscriptionId |Ja |Abonnement-ID der Zielressource, die skaliert wird |
| ResourceGroupName |Ja |Ressourcengruppenname der Zielressource, die skaliert wird |
| resourceName |Ja |Name der Zielressource, die skaliert wird |
| resourceType |Ja |Die drei unterstützten Werte: „microsoft.classiccompute/domainnames/slots/roles“ – Clouddienstrollen, „microsoft.compute/virtualmachinescalesets“ – Skalierungsgruppen für virtuelle Computer und „Microsoft.Web/serverfarms“ – Web-App |
| resourceId |Ja |Resource Manager-ID der Zielressource, die skaliert wird |
| portalLink |Ja |Link vom Azure-Portal zur Zusammenfassungsseite der Zielressource |
| oldCapacity |Ja |Die aktuelle (alte) Anzahl von Instanzen, wenn die automatische Skalierung eine Skalierungsaktion durchgeführt hat |
| newCapacity |Ja |Die neue Anzahl der Instanzen, auf die die automatische Skalierung die Ressource skaliert hat |
| Eigenschaften |Nein |Optional. Eine Reihe von Schlüssel-Wert-Paaren (Beispiel: Wörterbuch <Zeichenfolge, Zeichenfolge>). Das Feld "properties" ist optional. In einer angepassten Benutzeroberfläche oder einem auf Logik-Apps basierenden Workflow können Sie Schlüssel und Werte eingeben, die mithilfe der Nutzlast übergeben werden können. Alternativ können benutzerdefinierte Eigenschaften über den Webhook-URI selbst (als Abfrageparameter) an den ausgehenden Webhook-Aufruf zurückgegeben werden. |

