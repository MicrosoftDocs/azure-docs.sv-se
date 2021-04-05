---
title: Hantera program i Visual Studio
description: Använd Visual Studio för att skapa, utveckla, paketera, distribuera och felsöka Azure Service Fabric-program och-tjänster.
ms.topic: conceptual
ms.date: 03/26/2018
ms.openlocfilehash: 1fd9295a0dead180767febcc5339ef0a25cb1e48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574555"
---
# <a name="use-visual-studio-to-simplify-writing-and-managing-your-service-fabric-applications"></a>Använd Visual Studio för att förenkla skrivning och hantering av Service Fabric program
Du kan hantera dina Azure Service Fabric-program och-tjänster via Visual Studio. När du har [konfigurerat utvecklings miljön](service-fabric-get-started.md)kan du använda Visual Studio för att skapa Service Fabric program, lägga till tjänster eller paketera, registrera och distribuera program i ditt lokala utvecklings kluster.

## <a name="deploy-your-service-fabric-application"></a>Distribuera ditt Service Fabric program
Som standard kombinerar distribution av ett program följande steg till en enkel åtgärd:

1. Skapa programpaketet
2. Laddar upp programpaketet till avbildnings arkivet
3. Registrera program typen
4. Ta bort alla program instanser som körs
5. Skapa en program instans

Tryck på **F5** i Visual Studio för att distribuera ditt program och koppla fel sökaren till alla program instanser. Du kan använda **CTRL + F5** för att distribuera ett program utan fel sökning, eller så kan du publicera till ett lokalt kluster eller fjärrkluster med hjälp av publicerings profilen.

### <a name="application-debug-mode"></a>Program fel söknings läge
Visual Studio ger en egenskap som kallas **program fel söknings läge**, som styr hur du vill att Visual Studios ska hantera program distribution som en del av fel sökningen.

#### <a name="to-set-the-application-debug-mode-property"></a>Ange egenskapen för fel söknings läge för program
1. På snabb menyn Service Fabric program projektets (*. sfproj), väljer du **Egenskaper** (eller trycker på **F4** -tangenten).
2. I fönstret **Egenskaper** anger du egenskapen **program fel söknings läge** .

![Ange egenskapen för fel söknings läge för program][debugmodeproperty]

#### <a name="application-debug-modes"></a>Program fel söknings lägen

1. **Uppdatera program** Med det här läget kan du snabbt ändra och felsöka koden och stödja redigering av statiska webbfiler under fel sökning. Det här läget fungerar bara om det lokala utvecklings klustret är i 1-Node-läge. Detta är standard läget för fel sökning av program.
2. **Ta bort program** gör att programmet tas bort när Felsökningssessionen avslutas.
3. **Automatisk uppgradering** Programmet fortsätter att köras när Felsökningssessionen avslutas. Nästa felsökningssession kommer att behandla distributionen som en uppgradering. Uppgraderings processen bevarar alla data som du angav i en tidigare felsökningssession.
4. **Behåll program** Programmet fortsätter att köras i klustret när Felsökningssessionen avslutas. I början av nästa felsökningssession kommer programmet att tas bort.

För **automatisk uppgradering** av data bevaras genom att använda program uppgraderings funktionerna i Service Fabric. Mer information om hur du uppgraderar program och hur du kan utföra en uppgradering i en verklig miljö finns [Service Fabric program uppgradering](service-fabric-application-upgrade.md).

## <a name="add-a-service-to-your-service-fabric-application"></a>Lägga till en tjänst i Service Fabric programmet
Du kan lägga till nya tjänster i programmet för att utöka dess funktioner. För att säkerställa att tjänsten ingår i programpaketet lägger du till tjänsten via den **nya Fabric-tjänsten...** meny alternativet.

![Lägg till en ny Service Fabric-tjänst][newservice]

Välj en Service Fabric projekt typ som ska läggas till i programmet och ange ett namn för tjänsten.  Se [välja ett ramverk för tjänsten](service-fabric-choose-framework.md) för att hjälpa dig att bestämma vilken tjänst typ som ska användas.

![Välj en Service Fabric tjänst projekt typ som ska läggas till i programmet][addserviceproject]

Den nya tjänsten läggs till i lösningen och det befintliga programpaketet. Tjänst referenserna och en standard tjänst instans läggs till i program manifestet, vilket gör att tjänsten skapas och startas nästa gång du distribuerar programmet.

![Den nya tjänsten läggs till i program manifestet][newserviceapplicationmanifest]

## <a name="package-your-service-fabric-application"></a>Paketera ditt Service Fabric program
Om du vill distribuera programmet och dess tjänster till ett kluster måste du skapa ett programpaket.  Paketet ordnar applikations manifestet, tjänst manifesten och andra nödvändiga filer i en specifik layout.  Visual Studio konfigurerar och hanterar paketet i programprojektets mapp i "pkg"-katalogen.  Om du klickar på **paket** i **programmets** snabb meny skapas eller uppdateras programpaketet.

## <a name="remove-applications-and-application-types-using-cloud-explorer"></a>Ta bort program och program typer med hjälp av Cloud Explorer
Du kan utföra grundläggande kluster hanterings åtgärder i Visual Studio med hjälp av Cloud Explorer, som du kan starta från menyn **Visa** . Du kan till exempel ta bort program och avetablera program typer på lokala eller fjärranslutna kluster.

![Ta bort ett program][removeapplication]

> [!TIP]
> En omfattande kluster hanterings funktion finns i [visualisera klustret med Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
>
>

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Nästa steg
* [Service Fabric program modell](service-fabric-application-model.md)
* [Service Fabric program distribution](service-fabric-deploy-remove-applications.md)
* [Hantera program parametrar för flera miljöer](service-fabric-manage-multiple-environment-app-configuration.md)
* [Felsöka ditt Service Fabric program](service-fabric-debugging-your-application.md)
* [Visualisera klustret med hjälp av Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)

<!--Image references-->
[addserviceproject]:./media/service-fabric-manage-application-in-visual-studio/addserviceproject.png
[manageservicefabric]: ./media/service-fabric-manage-application-in-visual-studio/manageservicefabric.png
[newservice]:./media/service-fabric-manage-application-in-visual-studio/newservice.png
[newserviceapplicationmanifest]:./media/service-fabric-manage-application-in-visual-studio/newserviceapplicationmanifest.png
[debugmodeproperty]:./media/service-fabric-manage-application-in-visual-studio/debugmodeproperty.png
[removeapplication]:./media/service-fabric-manage-application-in-visual-studio/removeapplication.png
