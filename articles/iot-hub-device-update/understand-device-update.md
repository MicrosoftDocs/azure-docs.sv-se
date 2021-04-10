---
title: Introduktion till enhets uppdatering för Azure IoT Hub | Microsoft Docs
description: Enhets uppdatering för IoT Hub är en tjänst som gör det möjligt att distribuera över-Air-uppdateringar (OTA) för dina IoT-enheter.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: overview
ms.service: iot-hub-device-update
ms.openlocfilehash: 092078a79124682f7ee5c7824d4f7906c6e35475
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558507"
---
# <a name="device-update-for-iot-hub-preview-overview"></a>Enhets uppdatering för IoT Hub (för hands version) – Översikt

Enhets uppdatering för IoT Hub är en tjänst som gör det möjligt att distribuera över-Air-uppdateringar (OTA) för dina IoT-enheter.

När organisationer tittar på ytterligare möjlighet att öka produktiviteten och drift effektiviteten kan Sakernas Internet (IoT)-lösningar fortsätta att tillämpas vid ökande priser. Detta gör det viktigt att de enheter som utgör dessa lösningar bygger på en grund av tillförlitlighet och säkerhet och är enkla att ansluta och hantera i stor skala. Enhets uppdatering för IoT Hub är en slutpunkt-till-slutpunkt-plattform som kunder kan använda för att publicera, distribuera och hantera överfarts uppdateringar för allt från små sensorer till gateway-nivå enheter. 

För att kunna utnyttja de fulla fördelarna med IoT-aktiverad digital omvandling, behöver kunderna möjlighet att hantera, underhålla och uppdatera enheter i stor skala. Utforska fördelarna med att implementera enhets uppdatering för IoT Hub, vilket innefattar att snabbt kunna reagera på säkerhetshot och distribuera nya funktioner för att få affärs mål utan att dra nytta av de extra utvecklings-och underhållskostnaderna för att skapa dina egna uppdaterings plattformar.

## <a name="support-for-a-wide-range-of-iot-devices"></a>Stöd för ett brett utbud av IoT-enheter


Enhets uppdatering för IoT Hub är utformad för att erbjuda optimerad uppdaterings distribution och effektiviserade åtgärder genom integrering med [Azure IoT Hub](https://azure.microsoft.com/en-us/services/iot-hub/). Den här integrationen gör det enkelt att införa enhets uppdateringar på alla befintliga lösningar. Den tillhandahåller en moln värd lösning för att ansluta praktiskt taget vilken enhet som helst. Enhets uppdatering har stöd för ett brett utbud av IoT-operativsystem – inklusive Linux och [Azure återställnings tider](https://azure.microsoft.com/en-us/services/rtos/) (real tids operativ system) – och är utöknings bar via öppen källkod. Vi samarbetar med att utveckla enhets uppdateringar för IoT Hub erbjudanden med våra halvledar partners, inklusive STMicroelectronics, NXP, Renesas och mikrochip. Se [exempel](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) på utvärderings kort för nyckel halvledare som innehåller guider för att komma igång och lär dig hur du konfigurerar, bygger och distribuerar OTA-uppdateringar (över-Air) till MCU-enhets enheter. 

Både en enhets uppdatering av en binär och Raspberry Pi-referens yocto-avbildningar tillhandahålls.
Enhets uppdatering för IoT Hub stöder även uppdatering av Azure IoT Edge enheter. En enhets uppdaterings agent tillhandahålls för Ubuntu Server 18,04 amd64-plattform. Enhets uppdatering för IoT Hub innehåller även kod med öppen källkod om du inte kör någon av plattformarna ovan. Du kan Porta agenten på den distribution du kör.

Enhets uppdatering fungerar med IoT Plug and Play (PnP) och kan hantera alla enheter som stöder de PnP-gränssnitt som krävs. Mer information finns i [enhets uppdatering för IoT Hub-och IoT-plug and Play](device-update-plug-and-play.md).

## <a name="support-for-a-wide-range-of-update-artifacts"></a>Stöd för ett brett utbud av uppdaterings artefakter

Enhets uppdatering för IoT Hub stöder två typer av uppdateringar – bildbaserad och paket-baserad.

Paketbaserade uppdateringar är riktade uppdateringar som bara ändrar en specifik komponent eller ett program på enheten. Detta leder till lägre konsumtion av bandbredd och hjälper till att minska tiden för att ladda ned och installera uppdateringen. Paket uppdateringar tillåter normalt mindre stillestånd av enheter när en uppdatering tillämpas och du slipper att skapa avbildningar.

Avbildnings uppdateringar ger en högre säkerhets nivå i enhetens slut tillstånd. Det är vanligt vis enklare att replikera resultaten av en avbildnings uppdatering mellan en för produktions miljö och en produktions miljö, eftersom den inte innebär samma utmaningar som paket och deras beroenden.
På grund av deras atomiska natur kan en enkel-/B-redundansrelation också användas.

Det finns inget rätt svar och du kan välja olika beroende på dina speciella användnings fall. Enhets uppdatering för IoT Hub har stöd för både image-och paket form av uppdatering, så att du kan välja rätt uppdaterings modell för enhets miljön.

## <a name="flexible-features-for-updating-devices"></a>Flexibla funktioner för att uppdatera enheter

Enhets uppdatering för IoT Hub funktioner ger en kraftfull och flexibel upplevelse, inklusive:

* Uppdaterings hantering UX integrerat med Azure IoT Hub
* Gradvis uppdaterings distribution genom enhets gruppering och uppdatering av schema kontroller
* Programmerings-API: er för att aktivera automatisering och anpassade Portal upplevelser
* En snabb uppdatering av efterlevnads-och status visningar i heterogena enhets flottor
* Stöd för elastiska enhets uppdateringar (A/B) för att leverera sömlös återställning
* Prenumerations-och rollbaserade åtkomst kontroller som är tillgängliga via Azure.com-portalen
* Lokalt innehålls-cache och stöd för kapslad Edge för att aktivera uppdatering av frånkopplade moln enheter
* Detaljerade verktyg för uppdaterings hantering och rapportering 

Med enhets uppdatering för IoT Hub hanterings-och distributions kontroller kan användare maximera produktiviteten och spara värdefull tid. Enhets uppdatering för IoT Hub omfattar möjligheten att gruppera enheter och ange vilka enheter en uppdatering ska distribueras till. Användarna kan också visa status för uppdaterings distributioner och kontrol lera att varje enhet har tillämpat uppdateringar.

När ett uppdaterings fel inträffar tillåter enhets uppdatering för IoT Hub även att användare kan identifiera de enheter som inte kunde tillämpa uppdateringen och se information om relaterade fel. Möjligheten att identifiera vilka enheter som inte kunde uppdateras innebär att oräkneliga manuella timmar som sparats försöker hitta källan.

### <a name="best-in-class-security-at-global-scale"></a>Förstklassig säkerhet i global skala

Microsoft Azure stöder fler än en miljard IoT-enheter runtom i världen – ett tal som växer snabbt med dagen. Enhets uppdatering för IoT Hub bygger på den här upplevelsen och den beprövade tillförlitlighet som demonstreras av Windows Update plattform, så att enheterna kan uppdateras sömlöst i global skala.

Enhets uppdatering för IoT Hub använder omfattande moln-till-gräns-säkerhet som har utvecklats för Microsoft Azure, så kunderna behöver inte ägna tid åt att ta reda på hur man skapar dem själva från grunden.


## <a name="device-update-workflows"></a>Arbets flöden för enhets uppdatering

Enhets uppdaterings funktionen kan delas upp i tre områden: Agent integrering, import och hantering.

### <a name="device-update-agent"></a>Enhets uppdaterings agent

När ett uppdaterings kommando tas emot på en enhet körs den begärda fasen av uppdatering (Ladda ned, installera och tillämpa). Under varje fas returneras statusen till enhets uppdatering via IoT Hub så att du kan visa den aktuella statusen för en distribution. Om inga uppdateringar pågår returneras statusen "inaktiv". En distribution kan när som helst avbrytas.

:::image type="content" source="media/understand-device-update/client-agent-workflow.png" alt-text="Diagram över arbets flödet för enhets uppdaterings agenten." lightbox="media/understand-device-update/client-agent-workflow.png":::

[Läs mer](device-update-agent-overview.md) om enhets uppdaterings agenten. 

### <a name="importing"></a>Import

Att importera är hur dina uppdateringar matas in i enhets uppdateringen så att de kan distribueras till enheter. Enhets uppdatering stöder distribution av en enskild uppdatering per enhet. Detta gör det idealiskt för fullständiga uppdateringar som uppdaterar en hel OS-partition på en gång, eller ett apt-manifest som beskriver alla paket som du vill uppdatera på enheten. Om du vill importera uppdateringar till enhets uppdatering skapar du först ett import manifest som beskriver uppdateringen och laddar sedan ned uppdaterings filen (-erna) och import manifestet till en plats som kan nås via Internet. Sedan kan du använda Azure Portal eller [enhets uppdateringens import REST API](https://github.com/Azure/iot-hub-device-update/tree/main/docs/publish-api-reference) för att initiera den asynkrona processen för uppdaterings import. Enhets uppdatering överför filerna, bearbetar dem och gör dem tillgängliga för distribution till IoT-enheter.

För känsligt innehåll skyddar du hämtningen med en signatur för delad åtkomst (SAS), till exempel ad hoc SAS för Azure Blob Storage. [Läs mer om SAS](../storage/common/storage-sas-overview.md)

:::image type="content" source="media/understand-device-update/import-update.png" alt-text="Diagram över enhets uppdatering för IoT Hub import av arbets flöde." lightbox="media/understand-device-update/import-update.png":::

[Läs mer](import-concepts.md) om att importera uppdateringar. 

### <a name="grouping-and-deployment"></a>Gruppering och distribution

När du har importerat en uppdatering kan du Visa kompatibla uppdateringar för dina enheter och enhets klasser.

Enhets uppdatering stöder konceptet **grupper** via taggar i IoT Hub. Att distribuera en uppdatering till en test grupp först är ett bra sätt att minska risken för problem under en produktions lansering.

I enhets uppdatering är distributioner ett sätt att ansluta rätt innehåll till en speciell uppsättning kompatibla enheter. Enhets uppdatering dirigerar processen för att skicka kommandon till varje enhet, och instruera dem att hämta och installera uppdateringarna och få tillbaka status.

:::image type="content" source="media/understand-device-update/manage-deploy-updates.png" alt-text="Diagram över enhets uppdatering för IoT Hub gruppering och distributions arbets flöde." lightbox="media/understand-device-update/manage-deploy-updates.png":::

[Lär dig mer](device-update-compliance.md) om distributions koncept

[Läs mer](device-update-groups.md) om enhets uppdaterings grupper


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa konto och instans för enhets uppdatering](create-device-update-account.md)