---
title: 'Snabb start: skapa en HoloLens-app med DirectX'
description: I den här snabb starten får du lära dig hur du skapar en HoloLens-app med hjälp av objekt ankare.
author: craigktreasure
manager: virivera
ms.author: crtreasu
ms.date: 02/02/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: b5db9f3766bdd7d754f49403665a371f9d10afd7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105047617"
---
# <a name="quickstart-create-a-hololens-app-with-azure-object-anchors-in-cwinrt-and-directx"></a>Snabb start: skapa en HoloLens-app med Azure Object-ankare, i C++/WinRT och DirectX

Den här snabb starten beskriver hur du skapar en HoloLens-app med hjälp av [Azure Object ankare](../overview.md) i C++/WinRT och DirectX. Objekt ankare i Azure är en hanterad moln tjänst som konverterar 3D-tillgångar till AI-modeller som möjliggör objekt medveten, mixad verklighets upplevelser för HoloLens. När du är klar har du en HoloLens-app som kan identifiera ett objekt och dess attityd i ett Holographic DirectX 11-program (Universal Windows).

Du lär dig följande:

> [!div class="checklist"]
> * Skapa och Läs in ett HoloLens-program på sidan
> * Identifiera ett objekt och visualisera modellen

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande så att du kan utföra den här snabbstarten:

* Ett fysiskt objekt i din miljö och dess 3D-modell (antingen CAD eller scannat).
* En Windows-dator med följande installerat:
  * <a href="https://git-scm.com" target="_blank">Git för Windows</a>
  * <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a> med arbets belastningen **universell Windows-plattform utveckling** och **Windows 10 SDK-komponenten (10.0.18362.0 eller senare)**
* En HoloLens 2-enhet som är aktuell och har [utvecklarläge](/windows/mixed-reality/using-visual-studio#enabling-developer-mode) aktiverat.
  * Om du vill uppdatera till den senaste versionen av HoloLens öppnar du appen **Inställningar** , går till **Uppdatera & säkerhet** och väljer sedan **Sök efter uppdateringar**.

## <a name="open-the-sample-project"></a>Öppna exempelprojektet

[!INCLUDE [Clone Sample Repo](../../../includes/object-anchors-clone-sample-repository.md)]

Öppna `quickstarts/apps/directx/DirectXAoaSampleApp.sln` i Visual Studio.

Ändra **lösningens konfiguration** till **version**, ändra **lösnings plattform** till **arm64**, Välj **enhet** från alternativ för distributions mål. Bygg sedan **AoaSampleApp** -projektet genom att högerklicka på projektet och välja **build**.

:::image type="content" source="./media/vs-deploy-to-device.png" alt-text="Konfigurera Visual Studio-projekt för distribution":::

## <a name="deploy-the-app-to-hololens"></a>Distribuera appen till HoloLens

När du har kompilerat exempel projektet kan du distribuera appen till HoloLens.

Slå på HoloLens-enheten, logga in och anslut den till datorn via en USB-kabel. Kontrol lera att **enheten** är det valda distributions målet (se ovan).

Högerklicka på **AoaSampleApp** -projekt och klicka sedan på **distribuera** på popup-menyn för att installera appen. Om inget fel visas i Visual Studios **fönstret utmatning** installeras appen på HoloLens.

:::image type="content" source="./media/vs-deploy-app.png" alt-text="Distribuera App till HoloLens":::

Innan du startar appen måste du ladda upp en objekt modell. Följ anvisningarna i inmatnings **objekt modellen och identifiera dess instans** avsnitt nedan.

Starta och Felsök appen genom att välja **felsök > starta fel sökning**. Om du vill stoppa appen väljer du antingen **stoppa fel sökning** eller tryck på **Shift + F5**.

## <a name="ingest-object-model-and-detect-its-instance"></a>Mata in objekt modell och identifiera dess instans

Du måste skapa en objekt modell för att köra exempel appen. Anta att du redan har antingen en CAD-eller skannad 3D-modell för ett objekt på ditt space. Se [snabb start: mata in en 3D-modell](./get-started-model-conversion.md) för att skapa en modell.

Hämta modellen, **stol. ou** i vårt fall till din dator. På HoloLens-enhetens Portal väljer du sedan **System > utforskaren > LocalAppData > AoaSampleApp > LocalState** och väljer **Bläddra.**... Välj sedan din modell fil, **stol. ou** till exempel och välj **Ladda upp**. Du bör sedan se modell filen i det lokala cacheminnet.

:::image type="content" source="../../../includes/media/object-anchors-quickstarts/portal-upload-model.png" alt-text="Modell för uppladdning av portalen":::

Starta **AoaSampleApp** -appen från HoloLens (om den redan är öppen, Stäng den och öppna den igen). Gå nära (inom 2 mätar avstånd) till målobjektet (stol) och skanna det genom att titta på det från flera olika perspektiv. Du bör se en rosa avgränsnings ruta runt objektet med några gula punkter som återges nära objektets yta, vilket indikerar att det har identifierats.

:::image type="content" source="./media/chair-detection.png" alt-text="Stols identifiering":::

Bild: en identifierad stol som återges med dess markerings ruta (rosa), punkt moln (gul) och ett sökområde (stor gul ruta).

Du kan definiera ett sökutrymme för objektet i appen genom finger avtryck genom att klicka i luften med antingen höger eller vänster hand. Sök utrymmet växlar mellan en sfär med radien på 2 meter, en 4 m ^ 3-avgränsnings ram och en vy-Frustum. För större objekt, till exempel bilar, är det bästa valet vanligt vis att använda vyn Frustum när du står inför ett hörn i objektet vid ett avstånd på 2 meter.
Varje gång Sök område ändras tar appen bort instanser som spåras och försöker sedan hitta dem igen i det nya Sök fältet.

Den här appen kan spåra flera objekt på en och samma tidpunkt. Det gör du genom att ladda upp flera modeller till mappen **LocalState** och ange ett sökområde som täcker alla mål objekt. Det kan ta längre tid att identifiera och spåra flera objekt.

Appen justerar en 3D-modell till dess fysiska motsvarighet. En användare kan knacka med vänster hand för att aktivera hög precisions spårnings läget, vilket beräknar en mer exakt attityd. Detta är fortfarande en experimentell funktion som förbrukar fler system resurser och kan leda till högre Darr i den uppskattade attityden. Air trycker igen med vänster om du vill växla tillbaka till normalt spårnings läge.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Snabb start: mata in en 3D-modell](./get-started-model-conversion.md)

> [!div class="nextstepaction"]
> [Begrepp: SDK-översikt](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [Vanliga frågor och svar](../faq.md)