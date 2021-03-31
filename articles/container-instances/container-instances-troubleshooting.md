---
title: Felsök vanliga problem
description: Lär dig hur du felsöker vanliga problem när du distribuerar, kör eller hanterar Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: d8e7fb85e369f5f278436370944eafeb1fb6a50e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96779523"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Felsöka vanliga problem med Azure Container Instances

Den här artikeln visar hur du felsöker vanliga problem med att hantera eller distribuera behållare till Azure Container Instances. Se även [vanliga frågor och svar](container-instances-faq.md).

Om du behöver ytterligare support, se tillgängliga **Hjälp + Support** alternativ i [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problem under distribution av container grupp
### <a name="naming-conventions"></a>Namngivningskonventioner

När du definierar din behållar specifikation kräver vissa parametrar att namngivnings begränsningar uppkommer. Nedan visas en tabell med särskilda krav för egenskaper för behållar grupp. Mer information finns i [namn konventioner][azure-name-restrictions] i Azure Architecture Center och [namngivnings regler och begränsningar för Azure-resurser][naming-rules].

| Omfång | Längd | Skiftläge | Giltiga tecken | Föreslaget mönster | Exempel |
| --- | --- | --- | --- | --- | --- |
| Behållar namn<sup>1</sup> | 1–63 |Gemener | Alfanumeriskt och bindestreck var som helst förutom det första eller sista tecken |`<name>-<role>-container<number>` |`web-batch-container1` |
| Container portar | Mellan 1 och 65535 |Integer |Heltal mellan 1 och 65535 |`<port-number>` |`443` |
| DNS-namnetikett | 5-63 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst förutom det första eller sista tecken |`<name>` |`frontend-site1` |
| Miljövariabel | 1–63 |Skiftlägesokänsligt |Alfanumeriskt och under streck (_) var som helst förutom det första eller sista tecknet |`<name>` |`MY_VARIABLE` |
| Volymnamn | 5-63 |Gemener |Alfanumeriskt och bindestreck var som helst utom det första eller sista. Får inte innehålla två bindestreck i följd. |`<name>` |`batch-output-volume` |

<sup>1</sup> Begränsning för behållar grupp namn när de inte anges oberoende av behållar instanser, till exempel med `az container create` kommando distributioner.

### <a name="os-version-of-image-not-supported"></a>OS-versionen av avbildningen stöds inte

Om du anger en bild som Azure Container Instances inte stöder returneras ett `OsVersionNotSupported` fel. Felet liknar följande, där `{0}` är namnet på den avbildning som du försökte distribuera:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Det här felet uppstår oftast när du distribuerar Windows-avbildningar som baseras på Semi-Annual Channel release 1709 eller 1803, som inte stöds. Information om vilka Windows-avbildningar som stöds i Azure Container Instances finns i [vanliga frågor och svar](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Det gick inte att hämta bilden

Om Azure Container Instances inlednings vis inte kan hämta din avbildning försöker den igen under en viss tids period. Om image-pull-åtgärden fortsätter att Miss lyckas kan ACI slutligen Miss lyckas med distributionen och du kan se ett `Failed to pull image` fel meddelande.

Lös problemet genom att ta bort behållar instansen och försök att distribuera igen. Se till att avbildningen finns i registret och att du har angett avbildnings namnet korrekt.

Om det inte går att hämta bilden visas händelser som följande i utmatningen av [AZ container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Resurs inte tillgängligt-fel

På grund av varierande regionala resurs belastning i Azure kan du få följande fel vid försök att distribuera en behållar instans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av en kraftig belastning i den region där du försöker distribuera, kan de resurser som anges för din behållare inte tilldelas vid denna tidpunkt. Använd en eller flera av följande åtgärder för att lösa problemet.

* Kontrol lera att inställningarna för behållar distribution ligger inom de parametrar som definierats i [regionens tillgänglighet för Azure Container instances](container-instances-region-availability.md)
* Ange lägre processor-och minnes inställningar för behållaren
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

## <a name="issues-during-container-group-runtime"></a>Problem under container Group runtime
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Containern avslutas och startar om kontinuerligt (ingen tidskrävande process)

Behållar grupper som standard till en [omstarts princip](container-instances-restart-policy.md) av **Always**, så behållare i behållar gruppen startar alltid om när de körts till slutförd. Du kan behöva ändra detta till **onFailure** eller **aldrig** om du tänker köra uppgiftsbaserade behållare. Om du anger **onFailure** och fortfarande ser kontinuerliga omstarter kan det bero på ett problem med att programmet eller skriptet körs i din behållare.

När du kör behållar grupper utan tids krävande processer kan du se upprepade avslutningar och starta om med bilder som Ubuntu eller Alpine. Att ansluta via [exec](container-instances-exec.md) fungerar inte eftersom behållaren inte har någon process som håller den igång. Lös problemet genom att inkludera ett start kommando som följer med distribution av behållare grupp för att hålla behållaren igång.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

Container Instances API och Azure Portal innehåller en `restartCount` egenskap. Om du vill kontrol lera antalet omstarter för en behållare kan du använda kommandot [AZ container show][az-container-show] i Azure CLI. I följande exempel på utdata (som har trunkerats för det kortfattat) kan du se `restartCount` egenskapen i slutet av utdata.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> De flesta behållar avbildningar för Linux-distributioner anger ett gränssnitt, till exempel bash, som standard kommando. Eftersom ett gränssnitt på egen hand inte är en tids krävande tjänst, avslutar dessa behållare omedelbart och hamnar i en omstart-slinga när de kon figurer ATS med standard principen för att starta om **alltid** .

### <a name="container-takes-a-long-time-to-start"></a>Det tar lång tid att starta containrar

De tre primära faktorer som bidrar till behållar start tiden i Azure Container Instances är:

* [Bild storlek](#image-size)
* [Bild plats](#image-location)
* [Cachelagrade avbildningar](#cached-images)

Windows-avbildningar har [ytterligare överväganden](#cached-images).

#### <a name="image-size"></a>Bild storlek

Om din behållare tar lång tid att starta, men kommer att lyckas, börjar du med att titta på storleken på behållar avbildningen. Eftersom Azure Container Instances hämtar behållar avbildningen på begäran är start tiden som visas direkt relaterad till dess storlek.

Du kan visa storleken på behållar avbildningen med hjälp av `docker images` kommandot i Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Nyckeln för att hålla bild storlekarna liten säkerställer att den slutliga bilden inte innehåller något som inte krävs vid körning. Ett sätt att göra detta är med [flera stegs versioner][docker-multi-stage-builds]. Med hjälp av flera steg blir det enkelt att se till att den slutliga avbildningen bara innehåller de artefakter som du behöver för ditt program, och inte något annat extra innehåll som krävdes vid tidpunkten för bygget.

#### <a name="image-location"></a>Bild plats

Ett annat sätt att minska effekten av image-hämtningen på din behållares start tid är att vara värd för behållar avbildningen i [Azure Container Registry](../container-registry/index.yml) i samma region där du tänker distribuera behållar instanser. Detta förkortar nätverks Sök vägen som behållar avbildningen behöver för att kunna färdas, vilket avsevärt kortare nedladdnings tiden.

#### <a name="cached-images"></a>Cachelagrade avbildningar

Azure Container Instances använder en mekanism för cachelagring som hjälper till att påskynda behållarens start tid för avbildningar som bygger på vanliga [Windows bas avbildningar](container-instances-faq.md#what-windows-base-os-images-are-supported), inklusive `nanoserver:1809` , `servercore:ltsc2019` och `servercore:1809` . Vanliga Linux-avbildningar som `ubuntu:1604` och `alpine:3.6` är också cachelagrade. Undvik att använda taggen för både Windows-och Linux-avbildningar `latest` . Läs Container Registry [metod tips för Image-Taggar](../container-registry/container-registry-image-tag-version.md) för vägledning. Om du vill ha en uppdaterad lista över cachelagrade avbildningar och taggar kan du använda API: n [lista med cachelagrade avbildningar][list-cached-images] .

> [!NOTE]
> Användning av Windows Server 2019-baserade avbildningar i Azure Container Instances är en för hands version.

#### <a name="windows-containers-slow-network-readiness"></a>Windows-behållare långsam nätverks beredskap

Vid första skapandet kan Windows-behållare ha ingen inkommande eller utgående anslutning i upp till 30 sekunder (eller längre, i sällsynta fall). Om behållar programmet behöver en Internet anslutning lägger du till fördröjning och omprövnings logik så att du kan upprätta en Internet anslutning på 30 sekunder. Efter den första installationen bör behållar nätverket återupptas korrekt.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Det går inte att ansluta till en underliggande Docker-API eller köra privilegierade behållare

Azure Container Instances visar inte direkt åtkomst till den underliggande infrastrukturen som är värd för behållar grupper. Detta inkluderar åtkomst till Docker-API som körs på behållarens värd och kör privilegierade behållare. Om du behöver Docker-interaktion, kontrollerar du [dokumentationen om rest-referensen](/rest/api/container-instances/) för att se vad ACI-API: et stöder. Om något saknas kan du skicka en begäran i [ACI feedback-forumet](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Det går inte att komma åt containergruppens IP-adress på grund av felmatchade portar

Azure Container Instances har ännu inte stöd för port mappning som med vanlig Docker-konfiguration. Om du hittar en behållar grupps IP-adress inte är tillgänglig när du tror att den bör vara, se till att du har konfigurerat behållar avbildningen så att den lyssnar på samma portar som du exponerar i behållar gruppen med `ports` egenskapen.

Om du vill bekräfta att Azure Container Instances kan lyssna på den port som du konfigurerade i behållar avbildningen testar du en distribution av `aci-helloworld` avbildningen som exponerar porten. Kör även `aci-helloworld` appen så att den lyssnar på porten. `aci-helloworld` accepterar en valfri miljö variabel `PORT` som åsidosätter standard porten 80 den lyssnar på. Om du till exempel vill testa port 9000 ställer du in [miljövariabeln](container-instances-environment-variables.md) när du skapar behållar gruppen:

1. Konfigurera behållar gruppen för att exponera port 9000 och skicka port numret som värde för miljövariabeln. Exemplet är formaterat för bash-gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommando tolken, måste du justera variabel tilldelningen enligt detta.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Hitta IP-adressen för behållar gruppen i kommandots utdata från `az container create` . Leta efter **IP-** värdet. 
1. När behållaren har skapats kan du bläddra till IP-adressen och porten för behållar appen i webbläsaren, till exempel: `192.0.2.0:9000` . 

    Du bör se "Välkommen till Azure Container Instances!" meddelande som visas av webbappen.
1. När du är klar med behållaren tar du bort den med `az container delete` kommandot:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hämtar behållar loggar och händelser](container-instances-get-logs.md) för att felsöka dina behållare.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
