---
title: Rendera en modell med Unity
description: Snabb start som vägleder användaren genom stegen för att återge en modell
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: b5865f2fd76c1159f7f72633362a96335af8a059
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509373"
---
# <a name="quickstart-render-a-model-with-unity"></a>Snabb start: rendera en modell med Unity

Den här snabb starten beskriver hur du kör ett Unity-exempel som återger en inbyggd modell via fjärr anslutning med hjälp av tjänsten Azure Remote rendering (ARR).

Vi kommer inte att gå in i detalj om ARR-API: et eller hur du konfigurerar ett nytt Unity-projekt. De här avsnitten beskrivs i [själv studie kursen: Visa fjärranslutna modeller](../tutorials/unity/view-remote-models/view-remote-models.md).

I den här snabb starten får du lära dig att:
> [!div class="checklist"]
>
>* Konfigurera din lokala utvecklings miljö
>* Hämta och skapa exempel appen ARR starter för enhets enhet
>* Rendera en modell i exempel appen ARR starter

## <a name="prerequisites"></a>Krav

För att få åtkomst till tjänsten Azure Remote rendering måste du först [skapa ett konto](../how-tos/create-an-account.md).

Följande program vara måste vara installerad:

* Windows SDK 10.0.18362.0 [(Hämta)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* Den senaste versionen av Visual Studio 2019 [(Hämta)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Visual Studio Tools för Mixad verklighet](https://docs.microsoft.com/windows/mixed-reality/install-the-tools). Mer specifikt är följande *arbets belastnings* installationer obligatoriska:
  * **Skriv bords utveckling med C++**
  * **Universell Windows-plattform (UWP) utveckling**
* GIT [(nedladdning)](https://git-scm.com/downloads)
* Unity 2019.3.1 [(Hämta)](https://unity3d.com/get-unity/download)
  * Installera de här modulerna i Unity:
    * **UWP** -stöd för Build-universell Windows-plattform
    * **IL2CPP** – stöd för Windows-build (IL2CPP)

## <a name="clone-the-sample-app"></a>Klona exempelappen

Öppna en kommando tolk (Skriv `cmd` in Start-menyn i Windows) och ändra till en katalog där du vill lagra exempel projektet arr.

Kör följande kommandon:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
```

Det sista kommandot skapar en under katalog i katalogen ARR som innehåller de olika exempel projekten för Azure Remote rendering.

Exempel appen snabb start för enhet finns i under katalogen *Unit/snabb start*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Återge en modell med exempel projektet Unity

Öppna Unity Hub och Lägg till exempelprojektet, som är mappen *ARR\azure-Remote-rendering\Unity\Quickstart* .
Öppna projektet. Om det behövs kan du göra det möjligt för Unity att uppgradera projektet till den installerade versionen.

Standard modellen som vi återger är en [inbyggd exempel modell](../samples/sample-model.md). Vi kommer att visa hur du konverterar en anpassad modell med hjälp av tjänsten ARR Conversion i [Nästa snabb start](convert-model.md).

### <a name="enter-your-account-info"></a>Ange din konto information

1. I Unity Asset Browser navigerar du till mappen *scener* och öppnar **snabb starts** scenen.
1. Välj Game-objektet **RemoteRendering** i *hierarkin*.
1. I *kontrollanten*anger du dina [autentiseringsuppgifter för kontot](../how-tos/create-an-account.md). [Skapa ett](../how-tos/create-an-account.md)konto om du inte redan har det.

![Information om ARR-konto](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Azure Portal visar endast ditt kontos domän som *mixedreality.Azure.com*. Detta är inte tillräckligt för att kunna ansluta.
> Ange **AccountDomain** till `<region>.mixedreality.azure.com` , där `<region>` är [en av de tillgängliga regionerna nära dig](../reference/regions.md).

Senare vill vi distribuera det här projektet till en HoloLens och ansluta till tjänsten Remote rendering från den enheten. Eftersom vi inte har något enkelt sätt att ange autentiseringsuppgifterna på enheten sparar snabb starts exemplet **autentiseringsuppgifterna i Unity-scenen**.

> [!WARNING]
> Se till att du inte kontrollerar projektet med dina sparade autentiseringsuppgifter i en lagrings plats där det skulle avslöja hemlig inloggnings information!

### <a name="create-a-session-and-view-the-default-model"></a>Skapa en session och Visa standard modellen

Starta sessionen genom att trycka på unions knappen **Play** . Du bör se ett överlägg med status text, längst ned i visnings området på *spel* panelen. Sessionen kommer att genomgå en serie tillstånds över gångar. I Start läge är servern i sin **publikation** , vilket tar flera minuter. Vid lyckad överföring övergår den till **klart** läge. Nu går sessionen in i **anslutnings** läget, där den försöker gå över till åter givnings körningen på den servern. När det är klart kan exemplet gå över till **anslutet** tillstånd. Nu börjar den att ladda ned modellen för åter givning. Nedladdningen kan ta några minuter på grund av modellens storlek. Sedan visas den fjärranslutna modellen.

![Utdata från exemplet](media/arr-sample-output.png)

Grattis! Nu visar du en fjärrrenderad modell!

## <a name="inspecting-the-scene"></a>Inspekterar scenen

När fjärran sluten anslutning har körts uppdateras panelen granska med ytterligare statusinformation:

![Enhets exempel spel](./media/arr-sample-configure-session-running.png)

Nu kan du utforska scen diagrammet genom att välja den nya noden och klicka på **Visa underordnade** i kontrollanten.

![Unity-hierarki](./media/unity-hierarchy.png)

Det finns ett [klipp Plans](../overview/features/cut-planes.md) objekt i scenen. Prova att aktivera den i dess egenskaper och flytta den runt:

![Ändra det klippta planet](media/arr-sample-unity-cutplane.png)

Om du vill synkronisera transformeringar klickar du antingen på **Synkronisera nu** eller markerar alternativet **synkronisera varje ram** . För komponent egenskaper räcker det bara att ändra dem.

## <a name="next-steps"></a>Nästa steg

I nästa snabb start kommer vi att distribuera exemplet till en HoloLens för att visa den fjärranslutna modellen i ursprunglig storlek.

> [!div class="nextstepaction"]
> [Snabb start: Distribuera Unity-exempel till HoloLens](deploy-to-hololens.md)

Alternativt kan exemplet också distribueras till en stationär dator.

> [!div class="nextstepaction"]
> [Snabb start: Distribuera Unity-exempel till Skriv bordet](deploy-to-desktop.md)
