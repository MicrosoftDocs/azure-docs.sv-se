---
title: Uppdatera hotinformationsdata
description: Datapaketet för hotinformation tillhandahålls med varje ny version av Defender for IoT eller, om det behövs, mellan olika versioner.
ms.date: 04/17/2021
ms.topic: how-to
ms.openlocfilehash: bb38d0a2486bda336d6881ec6f4c5d680906c973
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107750472"
---
# <a name="threat-intelligence-research-and-packages"></a>Forskning om hotinformation och paket #
## <a name="overview"></a>Översikt ##

Säkerhetsteam i Microsoft utför egenutvecklad ICS-hotinformation och sårbarhetsforskning. Dessa team omfattar MSTIC (Microsoft Threat Intelligence Center),GS (Microsoft Detection and Response Team), DCU (Digital Crimes Unit) och Avsnitt 52 (IoT/OT/ICS-domänexperter som spårar ICS-specifik nolldagars, omvänd teknik för skadlig kod, kampanjer och angripare)

Teamen tillhandahåller säkerhetsidentifiering, analys och svar på Microsofts:

- Molninfrastruktur och -tjänster.
- Traditionella produkter och enheter.
- Interna företagsresurser.

Säkerhetsteamen får fördelen av att:

- Skydd mot kända och relevanta hot.
- Insikter som hjälper dig att prioritera och prioritera.
- En förståelse för den fullständiga kontexten för hot innan de påverkas.
- Mer relevanta, korrekta och användbara data.

Den här intelligensen ger sammanhangsbaserad information för att utöka Microsofts plattformsanalys och stöder företagets hanterade tjänster för incident- och intrångsundersökning. Hotinformationspaket innehåller signaturer (inklusive signaturer för skadlig kod), CRE:er och annat säkerhetsinnehåll.

## <a name="when-are-packages-delivered"></a>När levereras paket ##

Hotinformationspaket tillhandahålls ungefär en gång i månaden, eller vid behov oftare. Meddelanden om nya paket är tillgängliga från: https://techcommunity.microsoft.com/t5/azure-defender-for-iot/bd-p/AzureDefenderIoT . 

## <a name="update-threat-intelligence-packages-to-your-sensors"></a>Uppdatera hotinformationspaket till dina sensorer ##

Det finns tre alternativ för att uppdatera hotinformationspaket till dina sensorer:

- Skicka automatiskt paket till sensorer när de levereras av Defender for IoT.
- Skicka paketet för hotinformation manuellt till sensorer efter behov.
- Ladda ned ett paket och ladda sedan upp det till en sensor eller flera sensorer.

Användare med behörigheten Defender for IoT Security Reader kan automatiskt och manuellt skicka paket till sensorer.

### <a name="automatically-push-threat-intelligence-updates-to-sensors"></a>Skicka automatiskt uppdateringar av hotinformation till sensorer ###

Hotinformationspaket kan uppdateras automatiskt till *molnanslutna* sensorer när de släpps av Defender for IoT. Se till att paketuppdateringen uppdateras automatiskt genom att registrera din molnanslutna sensor med **alternativet Automatiska uppdateringar av hotinformation** aktiverat. Mer information finns i [Publicera en sensor](getting-started.md#onboard-a-sensor).

### <a name="manually-push-threat-intelligence-updates-to-sensors"></a>Skicka uppdateringar av hotinformation till sensorer manuellt ###

Dina *molnanslutna* sensorer kan uppdateras automatiskt med hotinformationspaket. Men om du vill använda en mer försiktig metod kan du push-skicka paket från Azure Defender for IoT-portalen till sensorer endast när du anser att det krävs.
Detta ger dig möjlighet att styra när ett paket installeras, utan att du behöver ladda ned och sedan ladda upp det till dina sensorer.

**Så här pushar du paket manuellt:**

1. Gå till sidan Azure Defender for IoT **platser och sensorer.**
1. Välj ellipsen (...) för en sensor och välj sedan **Push Threat Intelligence Update**. I **fältet Status för uppdatering av hotinformation** visas uppdateringsförloppet.

#### <a name="change-the-threat-intelligence-update-mode"></a>Ändra uppdateringsläget för hotinformation ####

Du kan ändra uppdateringsläget för sensorhotinformation efter den första onboarding-integreringen.

**Så här ändrar du uppdateringsläget:**

1. Välj ellipsen (...) för en sensor och välj sedan **Redigera**.
1. Aktivera eller inaktivera **växlingsknappen Automatiska uppdateringar av hotinformation.**

### <a name="download-packages-and-upload-to-sensors"></a>Ladda ned paket och ladda upp till sensorer ###

Paket kan laddas ned från Defender for IoT-portalen och laddas upp manuellt till enskilda sensorer. Om den lokala hanteringskonsolen hanterar dina sensorer kan du ladda ned hotinformationspaket till hanteringskonsolen och skicka dem till flera sensorer samtidigt.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Hämta uppdateringar via Azure Defender for IoT portalen.":::

Det här alternativet är tillgängligt för både *molnanslutna* och *lokalt hanterade* sensorer.

**Så här laddar du upp till en enda sensor:**

1. Gå till sidan Azure Defender for IoT **uppdateringar.**

2. Ladda ned och spara **threat intelligence-paketet.**

3. Logga in på sensorkonsolen.

4. Välj Systeminställningar på **sidomenyn.**

5. Välj **Hotinformationsdata** och välj sedan **Uppdatera**.

6. Ladda upp det nya paketet.

**Så här laddar du upp till flera sensorer samtidigt:**

1. Gå till sidan Azure Defender for IoT **uppdateringar.**

2. Ladda ned och spara **threat intelligence-paketet.**

3. Logga in på hanteringskonsolen.

4. Välj Systeminställningar på **sidomenyn.**

5. I avsnittet **Sensor Engine Configuration (Konfiguration av** sensormotor) väljer du de sensorer som ska ta emot de uppdaterade paketen.  

6. I avsnittet **Välj hotinformationsdata** väljer du plustecknet ( **+** ).

7. Ladda upp paketet.

## <a name="review-package-update-status-on-the-sensor"></a>Granska paketuppdateringsstatus på sensorn ##

Paketuppdateringsstatus och versionsinformation visas i avsnittet **Systeminställningar för** sensor, **Hotinformation.**  

## <a name="review-package-information-for-cloud-connected-sensors"></a>Granska paketinformation för molnanslutna sensorer ##

Granska följande information om hotinformationspaket för dina molnanslutna sensorer:

- Paketversion installerad
- Uppdateringsläge för hotinformation
- Uppdateringsstatus för hotinformation

Så här granskar du hotinformation:

1. Gå till sidan Azure Defender for IoT **platser och sensorer.**
1. Granska threat **intelligence-versionen som är** installerad på varje sensor. Versionsnamn baseras på den dag då paketet skapades av Defender for IoT.
1. Granska **hotinformationsläget** . *Automatiskt* anger att nyligen tillgängliga paket installeras automatiskt på sensorer när de släpps av Defender for IoT. *Manuell* anger att du kan skicka nyligen tillgängliga paket direkt till sensorer efter behov.
1. Granska **uppdateringsstatusen för Hotinformation.** Följande statusar kan visas:

- Misslyckad
- Pågår
- Tillgänglig uppdatering
- Okej

Om uppdateringar av molnansluten hotinformation misslyckas granskar du anslutningsinformationen i kolumnerna **Sensorstatus** och Senast ansluten **UTC** på **sidan Platser och** sensorer. 

## <a name="see-also"></a>Se även

[Registrera en sensor](getting-started.md#onboard-a-sensor)

[Hantera sensorer från hanteringskonsolen](how-to-manage-sensors-from-the-on-premises-management-console.md)