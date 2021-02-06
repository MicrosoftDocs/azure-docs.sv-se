---
title: Felsöka en Azure Service Fabric nät-webbapp som körs lokalt
description: I den här självstudien får du felsöka ett Azure Service Fabric Mesh-program som körs i ett lokalt kluster.
author: georgewallace
ms.topic: tutorial
ms.date: 10/31/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 9e3dc16481340c0266cd398d0970e2147648e17f
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625456"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Självstudie: felsök ett Service Fabric Mesh-program som körs i ditt lokala utvecklingskluster

> [!IMPORTANT]
> Förhands granskningen av Azure Service Fabric-nätet har dragits tillbaka. Nya distributioner kommer inte längre att tillåtas via Service Fabric nät-API. Stöd för befintliga distributioner fortsätter till 28 april 2021.
> 
> Mer information finns i [förhands granskning av Azure Service Fabric nät](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Den här självstudien är del två i en serie och visar hur du skapar och felsöker ett Azure Service Fabric Mesh-program på ditt lokala utvecklingskluster.

I den här självstudien lär du dig:

> [!div class="checklist"]
> * Vad händer när du skapar ett Azure Service Fabric Mesh-program
> * Så ställer du in en brytpunkt för att observera ett tjänst-till-tjänst-samtal

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Skapa en Service Fabric Mesh-app i Visual Studio](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Felsök en Service Fabric Mesh-app som körs i ditt lokala utvecklingskluster
> * [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Uppgradera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-upgrade.md)
> * [Rensa Service Fabric Mesh-resurser](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Se till att du har [skapat utvecklingsmiljön](service-fabric-mesh-howto-setup-developer-environment-sdk.md) vilket omfattar att installera Service Fabric-runtime, SDK, Docker, och Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Hämta att göra-exempelprogrammet

Om du inte skapade att göra-exempelprogrammet i [del ett av självstudieserien](service-fabric-mesh-tutorial-create-dotnetcore.md) kan du ladda ned det. Kör följande kommando i ett kommandofönster för att klona databasen för exempelappen till den lokala datorn.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Programmet ligger under katalogen `src\todolistapp`.

## <a name="build-and-debug-on-your-local-cluster"></a>Skapa och felsöka i lokalt kluster

En Docker-avbildning skapas och distribuerats automatiskt till det lokala klustret så fort projektet läses in. Den här processen kan ta en stund. För att övervaka förloppet i Visual Studio-rutan **Output** (Utdata) ställer du in Output-fönstret **Show output from:** listrutan (Visa utdata från) på **Service Fabric Tools** (Service Fabric-verktyg).

Tryck på **F5** för att kompilera och köra tjänsten lokalt. När projektet körs och felsöks lokalt händer följande i Visual Studio:

* Se till att Docker för Windows körs och är inställt på att använda Windows som operativsystem för containern.
* Hämta alla eventuella saknade Docker-grundavbildningar. Det här kan ta lite tid
* Skapa (eller återskapa) den Docker-avbildning som användes under kodprojektet.
* Distribuera och kör containern i det lokala Service Fabric-utvecklingsklustret.
* Kör dina tjänster och nå alla brytpunkter som du har angett.

När den lokala distributionen är klar och Visual Studio kör din app öppnas ett webbläsarfönster med en standardexempelwebbsida.

## <a name="debugging-tips"></a>Felsökningstips

Gör din första felsökningskörning (F5) mycket snabbare genom att följa instruktionerna i [Optimera Visual Studio-prestanda](service-fabric-mesh-howto-optimize-vs.md).

Det har uppstått ett problem som gör att anropet till `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` inte ansluter till tjänsten. Detta kan inträffa när värdens IP-adress ändras. Lösning:

1. Ta bort appen från det lokala klustret (i Visual Studio, **Bygg**  >  **ren lösning**).
2. Från Service Fabric Local Cluster Manager väljer du **Stop Local CLuster** (Stoppa lokalt kluster) och sedan **Start Local Cluster** (Starta lokalt kluster).
3. Distribuera om appen (i Visual Studio **F5**).

Om felet **No Service Fabric local cluster is running** (Inget lokalt Service Fabric-kluster körs) returneras kontrollerar du att LCM (Service Fabric Local Custer Manager) körs. Högerklicka på LCM-ikonen i uppgiftsfältet och klicka på **Starta lokalt kluster**. När starten är klar återgår du till Visual Studio och trycker på **F5**.

Om ett fel av typen **404** returneras när appen startar innebär det förmodligen att miljövariablerna i **service.yaml** är felaktiga. Se till att `ApiHostPort` och `ToDoServiceName` är rätt inställda enligt instruktionerna i dokumentationen om att [skapa miljövariabler](./service-fabric-mesh-tutorial-create-dotnetcore.md#create-environment-variables).

Om det uppstår kompileringsfel i **service.yaml** kontrollerar du att blanksteg används för att dra in linjerna. Det går inte att använda tabbar. För närvarande måste du skapa appen med engelska som nationella inställningar.

### <a name="debug-in-visual-studio"></a>Felsökning i Visual Studio

När du felsöker ett Service Fabric Mesh-program i Visual Studio använder du ett lokalt utvecklingskluster för Service Fabric. Om du vill se hur att göra-objekt hämtas från serversidetjänsten felsöker du i OnGet()-metoden.
1. I projektet **WebFrontEnd** öppnar du **Pages** (sidor)  > **Index.cshtml** > **Index.cshtml.cs** och ställer in en brytpunkt i metoden **OnGet** (rad 17).
2. I projektet **ToDoService** öppnar du **TodoController.cs** och ställer in en brytpunkt i metoden **Get** (rad 15).
3. Gå tillbaka till webbläsaren och uppdatera sidan. Du kommer till brytpunkten i webbklientdelens `OnGet()`-metod. Du kan granska variabeln `backendUrl` för att se hur miljövariablerna som du definierade i **service.yaml**-filen kombineras i den webbadress som användes för att kontakta serversidetjänsten.
4. Stega över (F10) `client.GetAsync(backendUrl).GetAwaiter().GetResult())`-anropet. Du kommer till kontrollantens `Get()`-brytpunkt. I den här metoden kan du se hur listan över att göra-objekt hämtas från listan i minnet.
5. När du är klar kan du stoppa felsökningen av projektet i Visual Studio genom att trycka på **Skift + F5**.

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig:

> [!div class="checklist"]
> * Vad händer när du skapar ett Azure Service Fabric Mesh-program
> * Så ställer du in en brytpunkt för att observera ett tjänst-till-tjänst-samtal

Gå vidare till nästa kurs:
> [!div class="nextstepaction"]
> [Distribuera en Service Fabric Mesh-app](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
