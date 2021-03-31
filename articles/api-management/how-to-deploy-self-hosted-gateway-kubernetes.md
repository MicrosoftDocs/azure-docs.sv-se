---
title: Distribuera en egen värd-Gateway till Kubernetes | Microsoft Docs
description: Lär dig hur du distribuerar en egen värdbaserad Gateway-komponent i Azure API Management till Kubernetes
services: api-management
author: vladvino
manager: gwallace
ms.service: api-management
ms.workload: mobile
ms.topic: article
ms.author: apimpm
ms.date: 04/23/2020
ms.openlocfilehash: 023c2c89b90d6ddc71abc95db325dcdeb7684a2d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89500138"
---
# <a name="deploy-a-self-hosted-gateway-to-kubernetes"></a>Distribuera en gateway med egen värd till Kubernetes

Den här artikeln beskriver stegen för att distribuera den lokala gateway-komponenten i Azure API Management till ett Kubernetes-kluster.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Skapa ett Kubernetes-kluster.
   > [!TIP]
   > [Kluster med en nod](https://kubernetes.io/docs/setup/#learning-environment) fungerar bra i utvecklings-och utvärderings syfte. Använd [Kubernetes Certified](https://kubernetes.io/partners/#conformance) multi-Node-kluster lokalt eller i molnet för produktions arbets belastningar.
- [Etablera en lokal gateway-resurs i din API Management-instans](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-to-kubernetes"></a>Distribuera till Kubernetes

1. Välj **gatewayer** under **distribution och infrastruktur**.
2. Välj den lokal gateway-resurs som du vill distribuera.
3. Välj **distribution**.
4. En åtkomsttoken i text rutan **token** genererades automatiskt åt dig, baserat på standardvärden för **förfallo datum** och **hemlig nyckel** . Om det behövs väljer du värden i någon av eller båda kontrollerna för att generera en ny token.
5. Välj fliken **Kubernetes** under **distributions skript**.
6. Välj länk för **\<gateway-name\> . yml** -filen och ladda ned yaml-filen.
7. Välj **kopierings** ikonen **i det nedre** högra hörnet i text rutan för att spara `kubectl` kommandona i Urklipp.
8. Klistra in kommandon i terminalfönstret (eller kommando fönstret). Det första kommandot skapar en Kubernetes-hemlighet som innehåller den åtkomsttoken som genererades i steg 4. Det andra kommandot tillämpar konfigurations filen som hämtades i steg 6 till Kubernetes-klustret och förväntar sig att filen är i den aktuella katalogen.
9. Kör kommandona för att skapa de nödvändiga Kubernetes-objekten i [standard namn området](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) och starta poddar för egen värd från [behållar avbildningen](https://aka.ms/apim/sputnik/dhub) som hämtats från Microsoft container Registry.
10. Kör följande kommando för att kontrol lera om distributionen har slutförts. Observera att det kan ta en stund innan alla objekt skapas och att poddar initieras.
    ```console
    kubectl get deployments
    NAME             READY   UP-TO-DATE   AVAILABLE   AGE
    <gateway-name>   1/1     1            1           18s
    ```
11. Kör följande kommando för att kontrol lera om tjänsten har skapats. Observera att tjänstens IP-adresser och portar är olika.
    ```console
    kubectl get services
    NAME             TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
    <gateway-name>   LoadBalancer   10.99.236.168   <pending>     80:31620/TCP,443:30456/TCP   9m1s
    ```
12. Gå tillbaka till Azure Portal och välj **Översikt**.
13. Bekräfta att **status** visar en grön bock markering, följt av ett antal noder som matchar antalet repliker som anges i yaml-filen. Den här statusen innebär att den distribuerade Gateway-poddar kommunicerar med API Management tjänsten och har ett vanligt "pulsslag".

    ![Gateway-status](media/how-to-deploy-self-hosted-gateway-kubernetes/status.png)

> [!TIP]
> Kör <code>kubectl logs deployment/<gateway-name></code> kommandot för att visa loggar från ett slumpmässigt valt Pod om det finns mer än ett.
> Kör <code>kubectl logs -h</code> en fullständig uppsättning kommando alternativ, till exempel hur du visar loggar för en speciell POD eller behållare.

## <a name="production-deployment-considerations"></a>Överväganden vid produktions distribution

### <a name="access-token"></a>Åtkomsttoken
Utan en giltig åtkomsttoken kan en lokal gateway inte komma åt och hämta konfigurations data från slut punkten för den associerade API Managements tjänsten. Åtkomsttoken kan vara giltig i högst 30 dagar. Den måste återskapas och klustret har kon figurer ATS med en ny token, antingen manuellt eller via automatisering innan det går ut.

När du automatiserar uppdatering av token ska du använda [denna hanterings-API-åtgärd](/rest/api/apimanagement/2019-12-01/gateway/generatetoken) för att generera en ny token. Information om hur du hanterar Kubernetes-hemligheter finns på [Kubernetes-webbplatsen](https://kubernetes.io/docs/concepts/configuration/secret).

### <a name="namespace"></a>Namnområde
Kubernetes- [namnområden](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) hjälper till att dela upp ett enskilt kluster mellan flera team, projekt eller program. Namn områden tillhandahåller ett omfång för resurser och namn. De kan associeras med en resurs kvot och principer för åtkomst kontroll.

Azure Portal innehåller kommandon för att skapa gateway-resurser med egen värd i **standard** namn området. Det här namn området skapas automatiskt, finns i alla kluster och kan inte tas bort.
Överväg att [skapa och distribuera](https://kubernetesbyexample.com/ns/) en lokal gateway i ett separat namn område i produktionen.

### <a name="number-of-replicas"></a>Antal repliker
Det minsta antalet repliker som lämpar sig för produktion är två.

Som standard distribueras en egen värd-Gateway med en **RollingUpdate** distributions [strategi](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#strategy). Granska standardvärdena och Överväg att uttryckligen ange fälten [maxUnavailable](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-unavailable) och [maxSurge](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#max-surge) , särskilt när du använder ett högt antal repliker.

### <a name="container-resources"></a>Container resurser
Som standard anger YAML-filen i Azure Portal inte behållar resurs förfrågningar.

Det är omöjligt att förutse och rekommendera mängden processor-och minnes resurser per behållare och antalet repliker som krävs för att stödja en viss arbets belastning. Många faktorer är i spel, till exempel:

- Speciell maskin vara som klustret körs på.
- Förekomst och typ av virtualisering.
- Antal och hastighet för samtidiga klient anslutningar.
- Begär ande frekvens.
- Typ av och antal konfigurerade principer.
- Nytto Last storlek och om nytto laster buffras eller strömmas.
- Svars tid för backend-tjänsten.

Vi rekommenderar att du anger resurs begär anden till två kärnor och 2 GiB som utgångs punkt. Utför ett belastnings test och skala upp/ned eller ned/i baserat på resultaten.

### <a name="container-image-tag"></a>Etikett för container avbildning
Den YAML-fil som anges i Azure Portal använder den **senaste** taggen. Den här taggen refererar alltid till den senaste versionen av den egna värdbaserade Gateway-behållar avbildningen.

Överväg att använda en speciell versions tagg i produktion för att undvika oavsiktlig uppgradering till en nyare version.

Du kan [Ladda ned en fullständig lista över tillgängliga Taggar](https://mcr.microsoft.com/v2/azure-api-management/gateway/tags/list).

### <a name="dns-policy"></a>DNS-princip
DNS-namnmatchning spelar en viktig roll i en lokal gateways möjlighet att ansluta till beroenden i Azure och skicka API-anrop till backend-tjänster.

YAML-filen som tillhandahölls i Azure Portal tillämpar standard principen för [ClusterFirst](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) . Den här principen gör att namn matchnings begär Anden som inte matchas av kluster-DNS kan vidarebefordras till den överordnade DNS-server som har ärvts från noden.

Mer information om namn matchning i Kubernetes finns på [Kubernetes-webbplatsen](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service). Överväg att anpassa [DNS-principen](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-policy) eller [DNS-konfigurationen](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/#pod-s-dns-config) efter behov.

### <a name="external-traffic-policy"></a>Extern trafik princip
YAML-filen som tillhandahölls i fältet Azure Portals uppsättningar `externalTrafficPolicy` i [serviceobjektet](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.19/#service-v1-core) till `Local` . Detta bevarar IP-adressen för anroparen (tillgänglig i [kontexten för begäran](api-management-policy-expressions.md#ContextVariables)) och inaktiverar belastnings utjämning över noder, vilket eliminerar nätverks hopp som orsakas av den. Tänk på att den här inställningen kan orsaka asymmetrisk distribution av trafik i distributioner med olika antal Gateway-poddar per nod.

### <a name="custom-domain-names-and-ssl-certificates"></a>Anpassade domän namn och SSL-certifikat

Om du använder anpassade domän namn för API Management slut punkter, särskilt om du använder ett anpassat domän namn för hanterings slut punkten, kan du behöva uppdatera värdet `config.service.endpoint` i **\<gateway-name\> . yaml** -filen för att ersätta standard domän namnet med det anpassade domän namnet. Se till att hanterings slut punkten kan nås från POD för den egen värdbaserade gatewayen i Kubernetes-klustret.

I det här scenariot, om SSL-certifikatet som används av hanterings slut punkten inte har signerats av ett välkänt CA-certifikat, måste du se till att CA-certifikatet är betrott av Pod för den egna värdbaserade gatewayen.

### <a name="configuration-backup"></a>Säkerhets kopiering av konfiguration
Om du vill veta mer om Gateway-beteende med egen värd i närvaro av ett tillfälligt Azure-anslutningsfel, se [Översikt över egen värd-Gateway](self-hosted-gateway-overview.md#connectivity-to-azure).

Konfigurera en lokal lagrings volym för den lokala gateway-behållaren, så att den kan behålla en säkerhets kopia av den senaste nedladdade konfigurationen. Om anslutningen inte är aktive rad kan lagrings volymen använda säkerhets kopian vid omstart. Volymens monterings Sök väg måste vara <code>/apim/config</code> . Se ett exempel på [GitHub](https://github.com/Azure/api-management-self-hosted-gateway/blob/master/examples/self-hosted-gateway-with-configuration-backup.yaml).
Mer information om lagring i Kubernetes finns på [Kubernetes-webbplatsen](https://kubernetes.io/docs/concepts/storage/volumes/).

### <a name="local-logs-and-metrics"></a>Lokala loggar och mått
Den egen värdbaserade gatewayen skickar telemetri till [Azure Monitor](api-management-howto-use-azure-monitor.md) och [Azure Application insikter](api-management-howto-app-insights.md) i enlighet med konfigurations inställningarna i den associerade API Managements tjänsten.
När [anslutningen till Azure](self-hosted-gateway-overview.md#connectivity-to-azure) är tillfälligt bruten avbryts flödet av telemetri till Azure och data går förlorade under tiden för avbrottet.
Överväg att [Konfigurera lokal övervakning](how-to-configure-local-metrics-logs.md) för att se till att det går att kontrol lera API-trafik och förhindra förlust av telemetri under Azures anslutnings avbrott.

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om den egen värdbaserade gatewayen, se [Översikt över egen värd-Gateway](self-hosted-gateway-overview.md).
