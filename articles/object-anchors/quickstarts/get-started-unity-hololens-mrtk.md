---
title: 'Snabb start: skapa en HoloLens-app med Unity och MRTK'
description: I den här snabb starten får du lära dig hur du skapar en HoloLens Unit-app med hjälp av MRTK och objekt ankare.
author: craigktreasure
manager: virivera
services: azure-object-anchors
ms.author: crtreasu
ms.date: 03/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 1fd42f7b2da82da17dc19f2a57ea9b64f78f3fe0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102049753"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-unity-with-mrtk"></a>Snabb start: skapa en HoloLens-app med Azure Object-ankare, i union med MRTK

I den här snabb starten skapar du en enhet för enhets-HoloLens som använder [Azures objekt ankare](../overview.md). Objekt ankare i Azure är en hanterad moln tjänst som konverterar 3D-tillgångar till AI-modeller som möjliggör objekt medveten, mixad verklighets upplevelser för HoloLens. När du är klar har du en HoloLens-app byggd med Unity som kan identifiera objekt i den fysiska världen.

Du lär dig följande:

> [!div class="checklist"]
> * Förbered Unity build-inställningar.
> * Exportera HoloLens Visual Studio-projektet.
> * Distribuera appen och kör den på en HoloLens 2-enhet.

[!INCLUDE [Unity quickstart prerequisites](../../../includes/object-anchors-quickstart-unity-prerequisites.md)]

[!INCLUDE [Unity device setup](../../../includes/object-anchors-quickstart-unity-device-setup.md)]

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

[!INCLUDE [Download Unity Package](../../../includes/object-anchors-quickstart-unity-download-package.md)]

Öppna projektet i Unity `quickstarts/apps/unity/mrtk` .

[!INCLUDE [Import Unity Package](../../../includes/object-anchors-quickstart-unity-import-package.md)]

[!INCLUDE [Unity build sample scene 1](../../../includes/object-anchors-quickstart-unity-build-sample-scene-1.md)]

När en "TMP-import" anger att du vill importera TextMesh Pro-resurser väljer du "Importera TMP Essentials".
:::image type="content" source="./media/textmesh-pro-importer-dialog.png" alt-text="Importera TextMesh Pro-resurser":::

[!INCLUDE [Unity build sample scene 2](../../../includes/object-anchors-quickstart-unity-build-sample-scene-2.md)]

[!INCLUDE [Unity build and deploy](../../../includes/object-anchors-quickstart-unity-build-deploy.md)]

### <a name="run-the-sample-app"></a>Kör exempelappen

Sätt på enheten, Välj **alla appar** och leta upp och starta appen. När välkomst skärmen är upprättad bör du se en vit avgränsnings ruta. Du kan använda din hand för att flytta, skala eller rotera avgränsnings rutan. Placera rutan för att se det objekt som du vill identifiera.

Öppna <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menyn handen</a> och välj **Lås SearchArea** för att förhindra ytterligare förflyttning av markerings rutan. Välj **Starta sökning** för att starta objekt identifiering. När objektet identifieras återges ett nät på objektet. Information om en identifierad instans visas på skärmen, till exempel uppdaterad tids stämpling och täcknings grad. Välj **stoppa sökning** för att stoppa spårningen och alla identifierade instanser tas bort.

#### <a name="the-app-menus"></a>Appens menyer

Du kan också utföra andra åtgärder med hjälp av <a href="https://microsoft.github.io/MixedRealityToolkit-Unity/Documentation/README_HandMenu.html" target="_blank">menyn handen</a>.

##### <a name="primary-menu"></a>Primär meny

* **Starta sökning/stoppa sökning** – startar eller stoppar processen för objekt identifiering.
* **Växla spatial mappning** – Visa/Dölj åter givning av spatial mappning. Det här alternativet kan användas för att felsöka om genomsökningen är klar eller inte.
* **Spårnings inställningar** – aktiverar aktivering av menyn spår inställningar.
* **Inställningar för sökområde** – aktiverar aktivering av menyn Sök områdes inställningar.
* **Starta spårning** – samla in diagnostikdata och spara den på enheten. Se mer information i avsnittet **fel sökning av identifierings problem och att avbilda diagnostik**.
* **Överför spårning** – Ladda upp diagnostikdata till objekt Ankares tjänsten. En användare måste ange sitt prenumerations konto i `subscription.json` och överföra det till `LocalState` mappen. Du hittar en exempel `subscription.json` fil nedan.

    :::image type="content" source="./media/mrtk-hand-menu-primary.png" alt-text="Enhets primär behållning-meny":::

##### <a name="tracker-settings-menu"></a>Menyn Spårare inställningar

* **Hög noggrannhet** – en experimentell funktion som används för att få en mer exakt attityd. Att aktivera det här alternativet kräver mer system resurser under objekt identifiering. Objektets nät kommer att renderas i rosa läge i det här läget. Välj den här knappen igen för att växla tillbaka till normalt spårnings läge.
* **Avslappnad lodrät justering** – när aktive rad kan ett objekt identifieras i en icke-lodrät vinkel. Användbart för att identifiera objekt på ramper.
* **Tillåt skalnings ändring** – tillåter spåraren att ändra storleken på det identifierade objektet baserat på miljö information.
* **Skjutreglage för täcknings grad** – justerar den andel av Surface-punkter som måste matchas för spåraren för att identifiera ett objekt.  Med lägre värden kan spåraren bättre identifiera objekt som är svåra för HoloLens-sensorer att upptäcka, till exempel mörka objekt eller mycket reflekterande objekt. Högre värden minskar frekvensen av falska identifieringar.

    :::image type="content" source="./media/mrtk-hand-menu-tracker.png" alt-text="Hand meny för enhets Spårare":::

##### <a name="search-area-settings-menu"></a>Menyn Sök områdes inställningar

* **Lås sökområde** – avgränsnings ruta för lås område för att förhindra oavsiktlig förflyttning av händerna.
* **Automatisk justering av Sök område** – gör att Sök fältet kan flytta sig själv under objekt identifieringen.
* **Krets nät** – växlar genom visualisering av de inlästa maskorna inuti Sök-ytan.  Det här alternativet kan hjälpa användare att justera sökrutan för svårt att identifiera objekt.

    :::image type="content" source="./media/mrtk-hand-menu-search-area.png" alt-text="List rutan för Unity search Area":::

Exempel `subscription.json` :

```json
{
  "AccountId": "<your account id>",
  "AccountKey": "<your account key>",
  "AccountDomain": "<your account domain>"
}
```

[!INCLUDE [Unity setup Windows Device Portal](../../../includes/object-anchors-quickstart-unity-setup-device-portal.md)]

[!INCLUDE [Unity upload your model](../../../includes/object-anchors-quickstart-unity-upload-model.md)]

[!INCLUDE [Unity troubleshooting](../../../includes/object-anchors-quickstart-unity-troubleshooting.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Begrepp: SDK-översikt](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar](../faq.md)
