---
title: 'Skapa din första Azure Service Fabric-app i Linux med C #'
description: Lär dig hur du skapar och distribuerar ett Service Fabric program med C# och .NET Core 2,0.
ms.topic: conceptual
ms.date: 04/11/2018
ms.openlocfilehash: 556d8dee4efd492fa98755f1ffd1cdc1c9887856
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "82193657"
---
# <a name="create-your-first-azure-service-fabric-application"></a>Skapa ditt första Azure Service Fabric-program
> [!div class="op_single_selector"]
> * [Java – Linux (förhandsversion)](service-fabric-create-your-first-linux-application-with-java.md)
> * [C# – Linux (förhandsversion)](service-fabric-create-your-first-linux-application-with-csharp.md)
>
>

Service Fabric innehåller SDK:er för att skapa tjänster i Linux i både .NET Core och Java. I den här självstudien visar vi hur man skapar ett program för Linux och en tjänst med C# i .NET Core 2.0.

## <a name="prerequisites"></a>Förutsättningar
Du måste [konfigurera Linux-utvecklingsmiljön](service-fabric-get-started-linux.md) innan du börjar. Om du använder Mac OS X kan du [konfigurera en Linux-miljö på en virtuell dator med hjälp av Vagrant](service-fabric-get-started-mac.md).

Du ska också installera [Service Fabric CLI](service-fabric-cli.md)

### <a name="install-and-set-up-the-generators-for-c"></a>Installera och konfigurera generatorerna för C#
Service Fabric tillhandahåller ramverktyg som hjälper dig att skapa Service Fabric-program från en terminal med en Yeoman-mallgenerator. Följ dessa steg för att konfigurera Service Fabric Yeoman-mallgeneratorer för C#:

1. Installera nodejs och NPM på datorn

   ```bash
   curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.0/install.sh | bash 
   nvm install node 
   ```
2. Installera [Yeoman](https://yeoman.io/) Template Generator på din dator från NPM

   ```bash
   npm install -g yo
   ```
3. Installera Service Fabric Yeoman C#-appgenerator från NPM

   ```bash
   npm install -g generator-azuresfcsharp
   ```

## <a name="create-the-application"></a>Skapa programmet
Ett Service Fabric-program kan innehålla en eller flera tjänster, som var och en ansvarar för att leverera programmets funktioner. Service Fabric [Yeoman](https://yeoman.io/)-generatorn för C#, som du installerade i förra steget, gör det enkelt att skapa din första tjänst och lägga till fler senare. Använd Yeoman för att skapa ett program med en enskild tjänst.

1. Skriv följande kommando i en terminal, för att börja bygga ställningarna: `yo azuresfcsharp`
2. Namnge ditt program.
3. Välj vilken typ din första tjänst ska ha och namnge den. För den här självstudien väljer vi en Reliable Actor-tjänst.

   ![Service Fabric Yeoman-generator för C#][sf-yeoman]

> [!NOTE]
> Mer information om alternativen finns i [Översikt över Service Fabric-programmeringsmodell](service-fabric-choose-framework.md).
>
>

## <a name="build-the-application"></a>Skapa programmet
Service Fabric Yeoman-mallarna inkluderar ett byggskript som du kan använda för att skapa programmet från terminalen (efter att du navigerat till programmappen).

  ```sh
 cd myapp
 ./build.sh
  ```

## <a name="deploy-the-application"></a>Distribuera programmet

När du har skapat programmet kan du distribuera det till det lokala klustret.

1. Anslut till det lokala Service Fabric-klustret.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Kör installationsskriptet som medföljer mallen för att kopiera programpaketet till klustrets avbildningsarkiv, registrera programtypen och skapa en instans av programmet.

    ```bash
    ./install.sh
    ```

Distributionen går till på samma sätt som för andra Service Fabric-program. Detaljerade instruktioner finns i dokumentationen om att [hantera ett Service Fabric-program med Service Fabric CLI](service-fabric-application-lifecycle-sfctl.md).

Du hittar parametrarna till de här kommandona i de genererade manifesten i programpaketet.

När programmet har distribuerats öppnar du en webbläsare och går till [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md) på `http://localhost:19080/Explorer`. Expandera sedan noden **Program** och observera att det nu finns en post för din programtyp och en post för den första instansen av den typen.

> [!IMPORTANT]
> Om du vill distribuera programmet till ett säkert Linux-kluster i Azure måste du konfigurera ett certifikat för att verifiera ditt program med Service Fabric Runtime. På så sätt kan dina Reliable Services-tjänster kommunicera med de underliggande Service Fabric runtime-API: erna. Mer information finns i [Konfigurera en Reliable Services app för att köra Linux-kluster](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters).  
>

## <a name="start-the-test-client-and-perform-a-failover"></a>Starta testklienten och utför en redundansväxling
Aktörsprojekt gör ingenting på egen hand. Det behövs en annan tjänst eller klient för att skicka meddelanden till dem. Aktörsmallen innehåller ett enkelt testskript som du kan använda för att interagera med aktörstjänsten.

1. Kör skriptet med övervakningsverktyget för att se resultatet av aktörstjänsten.

   Om du använder MAC OS X måste du kopiera mappen myactorsvcTestClient till samma plats i behållaren genom att köra följande ytterligare kommandon.
    
    ```bash
    docker cp  [first-four-digits-of-container-ID]:/home
    docker exec -it [first-four-digits-of-container-ID] /bin/bash
    cd /home
    ```
    
    ```bash
    cd myactorsvcTestClient
    watch -n 1 ./testclient.sh
    ```
2. I Service Fabric Explorer letar du reda på noden där den primära repliken för aktörstjänsten finns. På skärmbilden nedan är det nod 3.

    ![Hitta den primära repliken i Service Fabric Explorer][sfx-primary]
3. Klicka på noden du hittade i föregående steg och välj sedan **Deactivate (restart)** (Inaktivera (omstart)) på menyn Actions (Åtgärder). Den här åtgärden startar om en nod i ditt lokala kluster, vilket framtvingar en redundansväxling till en sekundär replik som körs på en annan nod. När du utför åtgärden, ska du vara uppmärksam på utdata från testklienten och notera att räknaren fortsätter att öka trots redundansen.

## <a name="adding-more-services-to-an-existing-application"></a>Lägga till fler tjänster till ett befintligt program

Om du vill lägga till en till tjänst till ett program som redan har skapats med hjälp av `yo` utför du följande steg:
1. Ändra katalogen till roten för det befintliga programmet.  Till exempel `cd ~/YeomanSamples/MyApplication` om `MyApplication` är programmet som skapats av Yeoman.
2. Kör `yo azuresfcsharp:AddService`

## <a name="next-steps"></a>Nästa steg

* [Interagera med Service Fabric-kluster med Service Fabric CLI](service-fabric-cli.md)
* Lär dig mer om [Service Fabric-supportalternativen](service-fabric-support.md)
* [Kom igång med Service Fabric CLI](service-fabric-cli.md)

<!-- Images -->
[sf-yeoman]: ./media/service-fabric-create-your-first-linux-application-with-csharp/yeoman-csharp.png
[sfx-primary]: ./media/service-fabric-create-your-first-linux-application-with-csharp/sfx-primary.png
