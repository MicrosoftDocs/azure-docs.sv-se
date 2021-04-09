---
title: 'Snabb start: publicera i Azure Sentinel'
description: I den här snabb starten lär du dig att använda Azure Sentinel på kort genom att först aktivera det och sedan ansluta data källor.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 10/14/2020
ms.custom: references_regions
ms.openlocfilehash: a169b93b20df14e536d8764b5fb4c889d645a1fa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570419"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Snabb start: fordonsbaserad Azure Sentinel

I den här snabb starten lär du dig att använda Azure Sentinel på kort sikt. 

För att kunna använda Azure Sentinel måste du först aktivera Azure Sentinel och sedan ansluta dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga i real tid och ger integration i real tid, inklusive Microsoft 365 Defender-lösningar (tidigare Microsoft Threat Protection), Microsoft 365 källor (inklusive Office 365), Azure AD, Microsoft Defender för identitet (tidigare Azure ATP), Microsoft Cloud App Security, Azure Defender-varningar från Azure Security Center. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda common Event format (CEF), syslog eller REST-API för att ansluta dina data källor med Azure Sentinel. 

När du har anslutit dina data källor väljer du från ett galleri med arbets böcker som skapats av experten som är baserade på dina data. Dessa arbets böcker kan enkelt anpassas efter dina behov.

>[!IMPORTANT] 
> Information om de kostnader som uppstår när du använder Azure Sentinel finns i [priser för Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).

## <a name="global-prerequisites"></a>Globala krav

- Aktiv Azure-prenumeration, om du inte har en, kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Log Analytics arbets yta. Lär dig hur du [skapar en arbets yta för Log Analytics](../azure-monitor/logs/quick-create-workspace.md). Mer information om Log Analytics-arbetsytor finns i [utforma distributioner av Azure Monitors loggar](../azure-monitor/logs/design-logs-deployment.md).

- Om du vill aktivera Azure Sentinel måste du ha deltagar behörighet till den prenumeration där Azure Sentinel-arbetsytan finns. 
- Om du vill använda Azure Sentinel behöver du antingen deltagar-eller läsar behörighet för resurs gruppen som arbets ytan tillhör.
- Ytterligare behörigheter kan krävas för att ansluta till vissa data källor.
- Azure Sentinel är en betaltjänst. Pris information finns i [om Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).

### <a name="geographical-availability-and-data-residency"></a>Geografisk tillgänglighet och data placering

- Azure Sentinel kan köras på arbets ytor i de flesta [ga-regioner med Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) förutom regionerna Kina och Tyskland (suverän). Ibland kan det ta lite tid för nya Log Analytics regioner att publicera Azure Sentinel-tjänsten. 

- Data som genereras av Azure Sentinel, till exempel incidenter, bok märken och analys regler, kan innehålla viss kund information från kundens Log Analytics arbets ytor. Dessa Azure Sentinel-genererade data sparas i det geografiska området som anges i följande tabell, enligt det geografiska område där arbets ytan finns:

    | Geografi för arbets yta | Azure Sentinel-genererad data geografi |
    | --- | --- |
    | USA<br>Indien<br>Brasilien<br>Afrika<br>Korea<br>Förenade Arabemiraten | USA |
    | Europa<br>Frankrike<br>Schweiz | Europa |
    | Australien | Australien |
    | Storbritannien | Storbritannien |
    | Kanada | Kanada |
    | Japan | Japan |
    |

## <a name="enable-azure-sentinel"></a>Aktivera Azure Sentinel <a name="enable"></a>

1. Logga in på Azure-portalen. Kontrol lera att den prenumeration där Azure Sentinel har skapats har valts.

1. Sök efter och välj **Azure Sentinel**.

   ![Sökning efter tjänster](./media/quickstart-onboard/search-product.png)

1. Välj **Lägg till**.

1. Välj den arbets yta som du vill använda eller skapa en ny. Du kan köra Azure Sentinel på fler än en arbets yta, men data isoleras till en enda arbets yta.

   ![Välj en arbetsyta](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standard arbets ytor som skapats av Azure Security Center visas inte i listan. Du kan inte installera Azure Sentinel på dem.
   >

   >[!IMPORTANT]
   >
   > - När Azure Sentinel har distribuerats på en arbets yta **stöder den inte** flytten av arbets ytan till andra resurs grupper eller prenumerationer. 
   >
   >   Om du redan har flyttat arbets ytan inaktiverar du alla aktiva regler under **analyser** och aktiverar dem igen efter fem minuter. Detta bör vara effektivt i de flesta fall, men för att kunna upprepas, stöds den inte och görs på egen risk.

1. Välj **Lägg till Azure Sentinel**.

## <a name="connect-data-sources"></a>Ansluta till datakällor

Azure Sentinel matar in data från tjänster och appar genom att ansluta till tjänsten och vidarebefordra händelser och loggar till Azure Sentinel. För fysiska och virtuella datorer kan du installera Log Analytics-agenten som samlar in loggarna och vidarebefordrar dem till Azure Sentinel. För brand väggar och proxyservrar installerar Azure Sentinel Log Analytics-agenten på en Linux Syslog-server som agenten samlar in loggfilerna och vidarebefordrar dem till Azure Sentinel. 
 
1. Välj **data kopplingar** på huvud menyn. Då öppnas galleriet data anslutningar.

1. Galleriet är en lista över alla data källor som du kan ansluta. Välj en data källa och sedan knappen **Öppna kopplings sida** .

1. Sidan koppling visar instruktioner för hur du konfigurerar anslutningen och eventuella ytterligare instruktioner som kan vara nödvändiga.<br>
Om du till exempel väljer data källan **Azure Active Directory** som låter dig strömma loggar från Azure AD till Azure Sentinel, kan du välja vilken typ av loggar du använder WAN för att få inloggnings loggar och/eller gransknings loggar. <br> Följ installations anvisningarna eller [Läs den relevanta anslutnings guiden](connect-data-sources.md) för mer information. Information om data anslutningar finns i [ansluta Microsoft-tjänster](connect-data-sources.md).

1. På fliken **Nästa steg** på kopplings sidan visas relevanta inbyggda arbets böcker, exempel frågor och mallar för analys regler som medföljer data kopplingen. Du kan använda dem som de är eller ändra dem – oavsett hur du kan få intressanta insikter över dina data direkt. <br>

När dina data källor är anslutna börjar dina data strömma till Azure Sentinel och är redo att börja arbeta med. Du kan visa loggarna i de [inbyggda arbets böckerna](quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](tutorial-investigate-cases.md).

## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig om att publicera och ansluta data källor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga Event format-enheter](connect-common-event-format.md) till Azure Sentinel.
