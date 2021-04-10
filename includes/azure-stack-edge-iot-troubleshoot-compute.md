---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 03/23/2021
ms.openlocfilehash: 0d912d0ac3f0fcf4c52116e67909038a1973304b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105419"
---
Använd IoT Edge-agentens körnings svar för att felsöka Compute-relaterade fel. Här är en lista över möjliga svar:

* 200 – OK
* 400-distributions konfigurationen är felaktig eller ogiltig.
* 417 – enheten har ingen distributions konfigurations uppsättning.
* 412-schema versionen i distributions konfigurationen är ogiltig.
* 406 – den IoT Edge enheten är offline eller skickar inte status rapporter.
* 500 – ett fel uppstod i IoT Edge Runtime.

Mer information finns i [IoT Edge agent](../articles/iot-edge/iot-edge-runtime.md?preserve-view=true&view=iotedge-2018-06#iot-edge-agent).

Följande fel är relaterat till tjänsten IoT Edge på din Azure Stack Edge Pro-enhet.

### <a name="compute-modules-have-unknown-status-and-cant-be-used"></a>Compute-moduler har okänd status och kan inte användas

#### <a name="error-description"></a>Felbeskrivning

Alla moduler på enheten visar okänd status och kan inte användas. Den okända statusen finns kvar i en omstart.<!--Original Support ticket relates to trying to deploy a container app on a Hub. Based on the work item, I assume the error description should not be that specific, and that the error applies to Azure Stack Edge Devices, which is the focus of this troubleshooting.-->

#### <a name="suggested-solution"></a>Föreslagen lösning

Ta bort IoT Edge tjänsten och distribuera sedan om modulerna. Mer information finns i [ta bort IoT Edge-tjänsten](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#remove-iot-edge-service).


### <a name="modules-show-as-running-but-are-not-working"></a>Moduler visas som körs men fungerar inte

#### <a name="error-description"></a>Felbeskrivning

Körnings status för modulen visas som körs men förväntade resultat visas inte. 

Det här tillståndet kan bero på ett problem med en modul vägs konfiguration som inte fungerar eller `edgehub` inte dirigerar meddelanden som förväntat. Du kan kontrol lera `edgehub` loggarna. Om du ser att det finns fel som inte kan ansluta till den IoT Hub tjänsten, är den vanligaste orsaken till anslutnings problemen. Problem med anslutningen kan bero på att AMPQ-porten som används som standard port av IoT Hub tjänsten för kommunikation är blockerad eller att webbproxyservern blockerar dessa meddelanden.

#### <a name="suggested-solution"></a>Föreslagen lösning

Utför följande steg:
1. Lös felet genom att gå till IoT Hub resurs för enheten och sedan välja din Edge-enhet. 
1. Gå till **Ange moduler > körnings inställningar**. 
1. Lägg till `Upstream protocol` miljövariabeln och tilldela den värdet `AMQPWS` . De meddelanden som har kon figurer ATS i det här fallet skickas via WebSockets via port 443.

### <a name="modules-show-as-running-but-do-not-have-an-ip-assigned"></a>Moduler visas som körs men har ingen tilldelad IP

#### <a name="error-description"></a>Felbeskrivning

Körnings status för modulen visas som körs men appen för container har ingen tilldelad IP-adress. 

Det här tillståndet beror på att det IP-adressintervall som du har angett för Kubernetes external service IP-adresser inte räcker. Du måste utöka intervallet för att säkerställa att varje behållare eller virtuell dator som du har distribuerat omfattas.

#### <a name="suggested-solution"></a>Föreslagen lösning

I enhetens lokala webb gränssnitt gör du följande steg:
1. Gå till **beräknings** sidan. Välj den port som du har aktiverat beräknings nätverket för. 
1. Ange ett statiskt, sammanhängande utbud av IP **-adresser för Kubernetes external service IP-adresser**. Du behöver 1 IP för `edgehub` tjänst. Dessutom behöver du en IP-adress för varje IoT Edge modul och för varje virtuell dator som du distribuerar. 
1. Välj **Använd**. Det ändrade IP-intervallet bör börja gälla omedelbart.

Mer information finns i [ändra IP-adresser för externa tjänster för behållare](../articles/databox-online/azure-stack-edge-gpu-manage-compute.md#change-external-service-ips-for-containers).

### <a name="configure-static-ips-for-iot-edge-modules"></a>Konfigurera statiska IP-adresser för IoT Edge moduler

#### <a name="problem-description"></a>Problembeskrivning

Kubernetes tilldelar dynamiska IP-adresser till varje IoT Edge modul på din Azure Stack Edge Pro GPU-enhet. Det krävs en metod för att konfigurera statiska IP-adresser för modulerna.

#### <a name="suggested-solution"></a>Föreslagen lösning

Du kan ange fasta IP-adresser för dina IoT Edge-moduler via avsnittet K8s-experiment enligt beskrivningen nedan: 

```yaml
{
  "k8s-experimental": {
    "serviceOptions" : {
      "loadBalancerIP" : "100.23.201.78",
      "type" : "LoadBalancer"
    }
  }
}
```
### <a name="expose-kubernetes-service-as-cluster-ip-service-for-internal-communication"></a>Exponera Kubernetes-tjänsten som kluster-IP-tjänst för intern kommunikation

#### <a name="problem-description"></a>Problembeskrivning

Som standard är IoT-tjänstetypen av typ av belastningsutjämnare och tilldelad externt riktade IP-adresser. Du kanske inte vill ha en extern IP-adress för ditt program. Du kan behöva exponera poddar i KUbernetes-klustret för att få åtkomst som andra poddar och inte som en externt exponerad belastnings Utjämnings tjänst. 

#### <a name="suggested-solution"></a>Föreslagen lösning

Du kan använda alternativen för att skapa via avsnittet K8s-experimentell. Följande tjänst alternativ bör fungera med port bindningar.

```yaml
{
"k8s-experimental": {
  "serviceOptions" : {
    "type" : "ClusterIP"
    }
  }
}
```