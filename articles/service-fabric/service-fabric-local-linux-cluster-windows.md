---
title: Konfigurera Azure Service Fabric Linux-kluster i Windows
description: Den här artikeln beskriver hur du konfigurerar Service Fabric Linux-kluster som körs på Windows Development-datorer. Den här metoden är användbar för plattforms oberoende utveckling.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087085"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurera ett Linux Service Fabric-kluster på Windows Developer-datorn

Det här dokumentet beskriver hur du konfigurerar ett lokalt Linux Service Fabric-kluster på en Windows-utvecklings dator. Det är praktiskt att konfigurera ett lokalt Linux-kluster för att snabbt testa program som är riktade mot Linux-kluster, men som utvecklas på en Windows-dator.

## <a name="prerequisites"></a>Förutsättningar
Linux-baserade Service Fabric kluster kan inte köras i Windows, men för att aktivera flera plattformar har vi angett en kluster Docker-behållare för Linux Service Fabric som kan distribueras via Docker för Windows.

Innan du börjar behöver du:

* Minst 4 GB RAM-minne
* Senaste versionen av [Docker för Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Docker måste köras i Linux containers-läge

>[!TIP]
> Följ anvisningarna i [Docker-dokumentationen](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions)om du vill installera Docker på din Windows-dator. [Verifiera installationen](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine) när du är färdig.
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Skapa en lokal container och konfigurera Service Fabric
Kör följande steg för att konfigurera en lokal Docker-behållare och ha ett Service Fabric kluster som kör:


1. Uppdatera konfigurationen av Docker-daemon på värden enligt följande och starta om Docker-daemon: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Vi rekommenderar att du uppdaterar genom att gå till: 

    * Docker-ikonen > inställningar > Docker-motorn
    * Lägg till de nya fälten ovan
    * Använd & starta om – starta om Docker daemon för att ändringarna ska börja gälla.

2. Starta klustret via PowerShell.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. För särskilda revideringar, se sidan [Service Fabric Onebox behållaravbildningen](https://hub.docker.com/_/microsoft-service-fabric-onebox) på Docker Hub.



3. Valfritt: Bygg din utökade Service Fabric-avbildning.

    I en ny katalog skapar du en fil `Dockerfile` som heter för att skapa en anpassad avbildning:

    >[!NOTE]
    >Du kan anpassa avbildningen ovan med en Dockerfile för att lägga till ytterligare program eller beroenden i din behållare.
    >Om du t.ex. lägger till `RUN apt-get install nodejs -y` tillåter du stöd för `nodejs`-program som körbara gästfiler.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Som standard hämtas avbildningen med den senaste Service Fabric-versionen. Läs mer om vissa revideringar på sidan [Docker-hubb](https://hub.docker.com/r/microsoft/service-fabric-onebox/).

    Om du vill skapa en åter användnings bild från `Dockerfile` öppnar du en Terminal och `cd` till den direkt innehavaren och `Dockerfile` kör sedan:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Den här åtgärden kan ta lite tid, men det behöver bara göras en gång.

    Nu kan du snabbt starta en lokal kopia av Service Fabric när du behöver den genom att köra:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Ange ett namn för din containerinstans så att du kan hantera den enklare. 
    >
    >Om programmet lyssnar på vissa portar måste du ange portarna med hjälp av ytterligare `-p`-taggar. Om programmet till exempel lyssnar på port 8080 lägger du till följande `-p`-tagg:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Det tar en liten stund för klustret att starta. Du kan se loggar med följande kommando eller gå till instrumentpanelen och visa klusterhälsotillståndet `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. När klustret har distribuerats enligt vad som observerats i steg 4 kan du gå till ``http://localhost:19080`` från din Windows-dator för att hitta Service Fabric Explorer instrument panelen. I det här läget kan du ansluta till det här klustret med hjälp av verktyg från Windows Developer-datorn och distribuera program som är riktade till Linux Service Fabric-kluster. 

    > [!NOTE]
    > Eclipse-pluginprogrammet stöds för närvarande inte av Windows. 

6. När du är klar, stoppa och rensa behållaren med det här kommandot:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Kända begränsningar 
 
 Följande begränsningar är kända begränsningar i lokala kluster som körs i en container för Mac: 
 
 * DNS-tjänsten körs inte och stöds för närvarande inte i behållaren. [Problem #132](https://github.com/Microsoft/service-fabric/issues/132)
 * Att köra containerbaserade appar kräver att du kör SF på en Linux-värd. Kapslade behållar program stöds inte för närvarande.

## <a name="next-steps"></a>Nästa steg
* [Skapa och distribuera ditt första Service Fabric-program med Java i Linux med hjälp av Yeoman](service-fabric-create-your-first-linux-application-with-java.md)
* Kom igång med [Sol förmörkelse](./service-fabric-get-started-eclipse.md)
* Kolla ut andra [Java-exempel](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
