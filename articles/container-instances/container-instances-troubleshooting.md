---
title: Felsök vanliga problem
description: Lär dig hur du felsöker vanliga problem när du distribuerar, kör eller hanterar Azure Container Instances
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: ce7e3018e470df3840eb01127a7bf2ffa01b5cbc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771073"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Felsöka vanliga problem med Azure Container Instances

Den här artikeln visar hur du felsöker vanliga problem med att hantera eller distribuera containrar till Azure Container Instances. Se även [Vanliga frågor och svar.](container-instances-faq.md)

Om du behöver ytterligare support kan du se tillgängliga **alternativ för hjälp och support** i [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problem vid distribution av containergrupp
### <a name="naming-conventions"></a>Namngivningskonventioner

När du definierar containerspecifikationen måste vissa parametrar följa namngivningsbegränsningarna. Nedan visas en tabell med specifika krav för egenskaper för containergrupp. Mer information finns i [Namngivningskonventioner][azure-name-restrictions] i Azure Architecture Center regler [och namngivningsregler och begränsningar för Azure-resurser.][naming-rules]

| Omfång | Längd | Skiftläge | Giltiga tecken | Föreslaget mönster | Exempel |
| --- | --- | --- | --- | --- | --- |
| Containernamn<sup>1</sup> | 1–63 |Gemener | Alfanumeriskt och bindestreck var som helst förutom det första eller sista tecknet |`<name>-<role>-container<number>` |`web-batch-container1` |
| Containerportar | Mellan 1 och 65535 |Integer |Heltal mellan 1 och 65535 |`<port-number>` |`443` |
| DNS-namnetikett | 5-63 |Skiftlägesokänsligt |Alfanumeriskt och bindestreck var som helst förutom det första eller sista tecknet |`<name>` |`frontend-site1` |
| Miljövariabel | 1–63 |Skiftlägesokänsligt |Alfanumeriskt och understreck (_) var som helst utom det första eller sista tecknet |`<name>` |`MY_VARIABLE` |
| Volymnamn | 5-63 |Gemener |Alfanumeriskt och bindestreck var som helst förutom det första eller sista tecknet. Får inte innehålla två bindestreck i följd. |`<name>` |`batch-output-volume` |

<sup>1</sup> Begränsning för containergruppnamn när de inte anges oberoende av containerinstanser, till exempel med `az container create` kommandodistributioner.

### <a name="os-version-of-image-not-supported"></a>OS-versionen av avbildningen stöds inte

Om du anger en avbildning Azure Container Instances inte stöder `OsVersionNotSupported` returneras ett fel. Felet liknar följande, där är `{0}` namnet på den avbildning som du försökte distribuera:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Det här felet uppstår oftast när du distribuerar Windows-avbildningar som baseras på Semi-Annual Channel version 1709 eller 1803, som inte stöds. Information om Windows-avbildningar Azure Container Instances finns [i Vanliga frågor och svar.](container-instances-faq.md#what-windows-base-os-images-are-supported)

### <a name="unable-to-pull-image"></a>Det går inte att hämta avbildningen

Om Azure Container Instances inte kan hämta avbildningen försöker den på nytt under en viss tidsperiod. Om åtgärden för att hämta avbildningen fortsätter att misslyckas misslyckas distributionen av ACI och du kan se ett `Failed to pull image` fel.

Lös problemet genom att ta bort containerinstansen och försöka distribuera igen. Kontrollera att avbildningen finns i registret och att du har skrivit avbildningens namn korrekt.

Om avbildningen inte kan hämtas visas händelser som följande i utdata från [az container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type&quot;: &quot;Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type&quot;: &quot;Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type&quot;: &quot;Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Felet Resursen är inte tillgänglig

På grund av varierande regional resursbelastning i Azure kan du få följande fel när du försöker distribuera en containerinstans:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Det här felet indikerar att på grund av hög belastning i den region där du försöker distribuera, kan de resurser som anges för containern inte allokeras vid den tidpunkten. Använd ett eller flera av följande åtgärdssteg för att lösa problemet.

* Kontrollera att dina inställningar för containerdistribution ligger inom de parametrar som definierats [i Regionstillgänglighet för Azure Container Instances](container-instances-region-availability.md)
* Ange lägre processor- och minnesinställningar för containern
* Distribuera till en annan Azure-region
* Distribuera vid ett senare tillfälle

## <a name="issues-during-container-group-runtime"></a>Problem vid körning av containergrupp
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Containern avslutas och startar om kontinuerligt (ingen tidskrävande process)

Containergrupper har som standard [omstartsprincipen](container-instances-restart-policy.md) **Always**, så att containrar i containergruppen alltid startas om när de har körts klart. Du kan behöva ändra detta till **OnFailure eller** **Aldrig** om du tänker köra uppgiftsbaserade containrar. Om du anger **OnFailure** och fortfarande ser kontinuerliga omstarter kan det finnas ett problem med programmet eller skriptet som körs i containern.

När du kör containergrupper utan långvariga processer kan du se upprepade avslut och omstarter med avbildningar som Ubuntu eller Alpine. Det går inte att ansluta via [EXEC](container-instances-exec.md) eftersom containern inte har någon process som håller den vid liv. Lös problemet genom att inkludera ett startkommando som följande med distributionen av containergruppen så att containern körs.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

API:Container Instances och Azure Portal innehåller en `restartCount` egenskap. Om du vill kontrollera antalet omstarter för en container kan du använda [kommandot az container show][az-container-show] i Azure CLI. I följande exempelutdata (som har trunkerats av utrymmesutdata) kan `restartCount` du se egenskapen i slutet av utdata.

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
> De flesta containeravbildningar för Linux-distributioner anger ett gränssnitt, till exempel bash, som standardkommando. Eftersom ett gränssnitt på egen hand inte är en långvarig tjänst avslutas dessa containrar omedelbart och hamnar i en omstartsloop när de konfigureras med **standardprincipen Starta** alltid om.

### <a name="container-takes-a-long-time-to-start"></a>Det tar lång tid att starta containrar

De tre främsta faktorerna som bidrar till containerstarttiden i Azure Container Instances är:

* [Bildstorlek](#image-size)
* [Bildplats](#image-location)
* [Cachelagrade avbildningar](#cached-images)

Windows-avbildningar [har ytterligare överväganden.](#cached-images)

#### <a name="image-size"></a>Bildstorlek

Om det tar lång tid att starta containern, men så småningom lyckas, börjar du med att titta på storleken på containeravbildningen. Eftersom Azure Container Instances hämtar containeravbildningen på begäran är starttiden som visas direkt relaterad till dess storlek.

Du kan visa storleken på containeravbildningen med hjälp `docker images` av kommandot i Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

Nyckeln till att hålla avbildningsstorlekarna små är att se till att den slutliga avbildningen inte innehåller något som inte krävs vid körning. Ett sätt att göra detta är [med byggen i flera steg.][docker-multi-stage-builds] Byggen i flera steg gör det enkelt att se till att den slutliga avbildningen endast innehåller de artefakter som du behöver för ditt program och inte något extra innehåll som krävdes vid byggtiden.

#### <a name="image-location"></a>Bildplats

Ett annat sätt att minska effekten av att hämta avbildningen på containerns starttid är att vara värd för containeravbildningen [i Azure Container Registry](../container-registry/index.yml) i samma region där du planerar att distribuera containerinstanser. Detta förkortar den nätverkssökväg som containeravbildningen behöver färdas, vilket avsevärt förkortar nedladdningstiden.

#### <a name="cached-images"></a>Cachelagrade avbildningar

Azure Container Instances använder en cachelagringsmekanism för att påskynda containerstarttiden [](container-instances-faq.md#what-windows-base-os-images-are-supported)för avbildningar som bygger på vanliga Windows-basavbildningar, `nanoserver:1809` inklusive , och `servercore:ltsc2019` `servercore:1809` . Vanliga Linux-avbildningar som `ubuntu:1604` och `alpine:3.6` cachelagras också. Undvik att använda taggen för både Windows- och `latest` Linux-avbildningar. Mer Container Registry finns i [metodtipsen för](../container-registry/container-registry-image-tag-version.md) bildtaggar. Om du vill ha en uppdaterad lista över cachelagrade bilder och taggar använder du API:et [Lista cachelagrade avbildningar.][list-cached-images]

> [!NOTE]
> Användning av Windows Server 2019-baserade avbildningar i Azure Container Instances är i förhandsversion.

#### <a name="windows-containers-slow-network-readiness"></a>Långsam nätverksberedskap för Windows-containrar

När Windows-containrar skapas kan det hända att de inte har någon inkommande eller utgående anslutning på upp till 30 sekunder (eller längre, i sällsynta fall). Om containerprogrammet behöver en Internetanslutning lägger du till logik för fördröjning och omförsök för att tillåta 30 sekunder att upprätta Internetanslutning. Efter den första installationen bör containernätverket återupptas på lämpligt sätt.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Det går inte att ansluta till underliggande Docker-API eller köra privilegierade containrar

Azure Container Instances inte direkt åtkomst till den underliggande infrastruktur som är värd för containergrupper. Detta inkluderar åtkomst till Docker-API:et som körs på containerns värd och som kör privilegierade containrar. Om du behöver Docker-interaktion kan du läsa [REST-referensdokumentationen](/rest/api/container-instances/) för att se vad ACI-API:et stöder. Om något saknas skickar du en begäran i [ACI-feedbackforumen.](https://aka.ms/aci/feedback)

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>Det går inte att komma åt containergruppens IP-adress på grund av felmatchade portar

Azure Container Instances stöder ännu inte portmappning som med vanlig Docker-konfiguration. Om du upptäcker att en containergrupps IP-adress inte är tillgänglig när du tror att den borde vara det, måste du kontrollera att du har konfigurerat containeravbildningen så att den lyssnar på samma portar som du exponerar i containergruppen med `ports` egenskapen .

Om du vill bekräfta att Azure Container Instances kan lyssna på den port som du konfigurerade i containeravbildningen testar du en distribution av avbildningen som `aci-helloworld` exponerar porten. Kör även `aci-helloworld` appen så att den lyssnar på porten. `aci-helloworld` accepterar en valfri miljövariabel `PORT` för att åsidosätta standardporten 80 som den lyssnar på. Om du till exempel vill testa port 9000 anger du [miljövariabeln](container-instances-environment-variables.md) när du skapar containergruppen:

1. Konfigurera containergruppen att exponera port 9000 och skicka portnumret som värdet för miljövariabeln. Exemplet är formaterat för Bash-gränssnittet. Om du föredrar ett annat gränssnitt, till exempel PowerShell eller kommandotolken, måste du justera variabeltilldelningen därefter.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Hitta IP-adressen för containergruppen i kommandoutdata för `az container create` . Leta efter värdet för **ip**. 
1. När containern har etablerats bläddrar du till IP-adressen och porten för containerappen i webbläsaren, till exempel: `192.0.2.0:9000` . 

    Du bör se "Välkommen till Azure Container Instances!" som visas av webbappen.
1. När du är klar med containern tar du bort den med `az container delete` kommandot :

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hämtar containerloggar och händelser för](container-instances-get-logs.md) att felsöka dina containrar.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az_container_show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
