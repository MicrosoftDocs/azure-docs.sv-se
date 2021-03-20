---
title: Självstudie för Service Fabric program uppgradering
description: Den här artikeln beskriver hur du distribuerar ett Service Fabric program, ändrar koden och installerar en uppgradering med hjälp av Visual Studio.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: acde2f4e51bee29d2eefb0d5fbb54fbe421a41f1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996244"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Själv studie kurs om Service Fabric program uppgradering med Visual Studio
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Azure Service Fabric fören klar processen med att uppgradera moln program genom att se till att endast ändrade tjänster uppgraderas och att program hälsan övervakas under uppgraderings processen. Det återställer också programmet automatiskt till den tidigare versionen när problem påträffas. Service Fabric program uppgraderingar är *noll avbrotts* tid, eftersom programmet kan uppgraderas utan drift avbrott. Den här självstudien beskriver hur du slutför en rullande uppgradering från Visual Studio.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>Steg 1: Bygg och publicera exempel på visuella objekt
Börja med att ladda ned [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) -programmet från GitHub. Sedan kan du bygga och publicera programmet genom att högerklicka på programprojektet, **VisualObjects** och välja kommandot **publicera** i meny alternativet Service Fabric.

![Snabb meny för ett Service Fabric program][image1]

Om du väljer **publicera** visas en popup-meny och du kan ställa in **mål profilen** på **PublishProfiles\Local.xml**. Fönstret bör se ut ungefär så här innan du klickar på **publicera**.

![Publicera ett Service Fabric program][image2]

Nu kan du klicka på **publicera** i dialog rutan. Du kan använda [Service Fabric Explorer för att Visa klustret och programmet](service-fabric-visualizing-your-cluster.md). Programmet för visuella objekt har en webb tjänst som du kan gå till genom att skriva `http://localhost:8081/visualobjects/` i adress fältet i webbläsaren.  Du bör se 10 flytande visuella objekt som flyttas runt på skärmen.

**Obs:** Om du distribuerar till `Cloud.xml` profil (Azure Service Fabric) bör programmet vara tillgängligt på **http://{ServiceFabricName}. { Region}. cloudapp. Azure. com: 8081/visualobjects/**. Kontrol lera att du har `8081/TCP` konfigurerat i Load Balancer (hitta Load Balancer i samma resurs grupp som Service Fabric-instansen).

## <a name="step-2-update-the-visual-objects-sample"></a>Steg 2: uppdatera det visuella objekt exemplet
Du kanske märker att de visuella objekten inte roterar med den version som distribuerades i steg 1. Vi ska uppgradera det här programmet till ett ställe där de visuella objekten också roterar.

Välj VisualObjects. ActorService-projektet i VisualObjects-lösningen och öppna filen **VisualObjectActor. cs** . I den filen går du till metoden `MoveObject` , kommentera ut och ta bort `visualObject.Move(false)` kommentaren `visualObject.Move(true)` . Den här kod ändringen roterar objekten när tjänsten har uppgraderats.  **Nu kan du bygga (inte återskapa) lösningen**, som bygger de ändrade projekten. Om du väljer *återskapa alla* måste du uppdatera versionerna för alla projekt.

Vi behöver också version av programmet. Om du vill göra versionen ändringar när du högerklickar på **VisualObjects** -projektet kan du använda alternativet Visual Studio **Edit manifest versions** . Om du väljer det här alternativet visas dialog rutan för versions versioner på följande sätt:

![Dialog rutan versions hantering][image3]

Uppdatera versionerna för de ändrade projekten och deras kod paket, tillsammans med programmet till version 2.0.0. När ändringarna har gjorts bör manifestet se ut ungefär så här (feta delar visar ändringarna):

![Uppdaterar versioner][image4]

Med Visual Studio-verktygen kan du automatiskt samla in versioner när du väljer **Uppdatera program-och tjänst versioner automatiskt**. Om du använder [SemVer](http://www.semver.org)måste du uppdatera koden och/eller konfigurations paket versionen separat om alternativet är markerat.

Spara ändringarna och kontrol lera sedan rutan **uppgradera program** .

## <a name="step-3--upgrade-your-application"></a>Steg 3: uppgradera programmet
Bekanta dig med parametrarna för [program uppgradering](service-fabric-application-upgrade-parameters.md) och [uppgraderings processen](service-fabric-application-upgrade.md) för att få en god förståelse för de olika uppgraderings parametrarna, tids gränser och hälso kriterier som kan tillämpas. I den här genom gången anges kriteriet för utvärdering av service hälsa till standardvärdet (oövervakat läge). Du kan konfigurera de här inställningarna genom att välja **Konfigurera uppgraderings inställningar** och sedan ändra parametrarna efter behov.

Nu är allt klart för att starta program uppgraderingen genom att välja **publicera**. Med det här alternativet uppgraderas ditt program till version 2.0.0, där objekten roterar. Service Fabric uppgraderar en uppdaterings domän i taget (vissa objekt uppdateras först, följt av andra) och tjänsten är fortfarande tillgänglig under uppgraderingen. Åtkomst till tjänsten kan kontrol leras via klienten (webbläsare).  

När program uppgraderingen fortsätter kan du nu övervaka den med Service Fabric Explorer genom att använda fliken **uppgraderingar på** fliken under programmen.

Om några minuter bör alla uppdaterings domäner uppgraderas (slutföras) och fönstret Visual Studio output måste också ange att uppgraderingen är klar. Och du bör se att *alla* visuella objekt i webbläsarfönstret nu roteras!

Du kanske vill prova att ändra versionerna och flytta från version 2.0.0 till version 3.0.0 som en övning, eller till och med från version 2.0.0 tillbaka till version 1.0.0. Spela med timeout-och hälso principer för att ge dig bekanta med dem. När du distribuerar till ett Azure-kluster i stället för ett lokalt kluster kan de parametrar som används skilja sig åt. Vi rekommenderar att du ställer in tids gränsen försiktigt.

## <a name="next-steps"></a>Nästa steg
Genom [att uppgradera ditt program med hjälp av PowerShell](service-fabric-application-upgrade-tutorial-powershell.md) får du en program uppgradering med PowerShell.

Styr hur programmet uppgraderas med hjälp av [uppgraderings parametrar](service-fabric-application-upgrade-parameters.md).

Gör dina program uppgraderingar kompatibla genom att lära dig hur du använder [Dataserialisering](service-fabric-application-upgrade-data-serialization.md).

Lär dig hur du använder avancerade funktioner när du uppgraderar ditt program genom att titta på [avancerade ämnen](service-fabric-application-upgrade-advanced.md).

Åtgärda vanliga problem i program uppgraderingar genom att följa stegen i [Felsöka program uppgraderingar](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
