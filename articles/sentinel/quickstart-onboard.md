---
title: 'Snabb start: publicera i Azure Sentinel'
description: I den här snabb starten lär du dig att använda Azure Sentinel på kort genom att först aktivera Sentinel och sedan ansluta data källor.
services: sentinel
author: yelevin
ms.author: yelevin
ms.assetid: d5750b3e-bfbd-4fa0-b888-ebfab7d9c9ae
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: quickstart
ms.date: 04/27/2020
ms.openlocfilehash: 3efd0303b447242c0229bc71720e55c8aa3a2f2d
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566258"
---
# <a name="quickstart-on-board-azure-sentinel"></a>Snabb start: fordonsbaserad Azure Sentinel

I den här snabb starten lär du dig att använda Azure Sentinel på kort sikt. 

För att kunna använda Azure Sentinel måste du först aktivera Azure Sentinel och sedan ansluta dina data källor. Azure Sentinel levereras med ett antal anslutningar för Microsoft-lösningar, som är tillgängliga direkt och ger real tids integrering, inklusive Microsoft Threat Protection-lösningar, Microsoft 365 källor (inklusive Office 365), Azure AD, Azure ATP, Microsoft Cloud App Security med mera. Dessutom finns det inbyggda anslutnings program till det bredare säkerhets eko systemet för lösningar som inte kommer från Microsoft. Du kan också använda common Event format (CEF), syslog eller REST-API för att ansluta dina data källor med Azure Sentinel.  

När du har anslutit dina data källor väljer du från ett galleri med arbets böcker som skapats av experten som är baserade på dina data. Dessa arbets böcker kan enkelt anpassas efter dina behov.

>[!IMPORTANT] 
> Information om de kostnader som uppstår när du använder Azure Sentinel finns i [priser för Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
  

## <a name="global-prerequisites"></a>Globala krav

- Aktiv Azure-prenumeration, om du inte har en, kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Log Analytics arbets yta. Lär dig hur du [skapar en arbets yta för Log Analytics](../log-analytics/log-analytics-quick-create-workspace.md). Mer information om Log Analytics-arbetsytor finns i [utforma distributioner av Azure Monitors loggar](../azure-monitor/platform/design-logs-deployment.md).

- Om du vill aktivera Azure Sentinel måste du ha deltagar behörighet till den prenumeration där Azure Sentinel-arbetsytan finns. 
- Om du vill använda Azure Sentinel behöver du antingen deltagar-eller läsar behörighet för resurs gruppen som arbets ytan tillhör.
- Ytterligare behörigheter kan krävas för att ansluta till vissa data källor.
- Azure Sentinel är en betald tjänst. Pris information finns i [om Azure Sentinel](https://go.microsoft.com/fwlink/?linkid=2104058).
 
## <a name="enable-azure-sentinel"></a>Aktivera Azure Sentinel <a name="enable"></a>

1. Logga in på Azure-portalen. Kontrol lera att den prenumeration där Azure Sentinel har skapats har valts.

1. Sök efter och välj **Azure Sentinel**.

   ![sök](./media/quickstart-onboard/search-product.png)

1. Välj **Lägg till**.

1. Välj den arbets yta som du vill använda eller skapa en ny. Du kan köra Azure Sentinel på fler än en arbets yta, men data isoleras till en enda arbets yta.

   ![sök](./media/quickstart-onboard/choose-workspace.png)

   >[!NOTE] 
   > - Standard arbets ytor som skapats av Azure Security Center visas inte i listan. Du kan inte installera Azure Sentinel på dem.
   > - Azure Sentinel kan köras på arbets ytor i alla [ga-regioner med Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor) förutom regionerna Kina och Tyskland (suverän). Data som genereras av Azure Sentinel (t. ex. incidenter, bok märken och varnings regler som kan innehålla vissa kund uppgifter från dessa arbets ytor) sparas antingen i Europa (för arbets ytor som finns i Europa) i Australien (för arbets ytor i Australien) eller i östra USA (för arbets ytor som finns i en annan region).

   >[!IMPORTANT]
   >
   > - När Azure Sentinel har distribuerats på en arbets yta **stöder den inte** flytten av arbets ytan till andra resurs grupper eller prenumerationer. 
   >
   >   Om du redan har flyttat arbets ytan inaktiverar du alla aktiva regler under **analyser** och aktiverar dem igen efter fem minuter. Detta bör vara effektivt i de flesta fall, men för att kunna upprepas, stöds den inte och görs på egen risk.

1. Välj **Lägg till Azure Sentinel**.
  

## <a name="connect-data-sources"></a>Ansluta till datakällor

Azure Sentinel skapar anslutningen till tjänster och appar genom att ansluta till tjänsten och vidarebefordra händelser och loggar till Azure Sentinel. För datorer och virtuella datorer kan du installera Azure Sentinel-agenten som samlar in loggarna och vidarebefordrar dem till Azure Sentinel. För brand väggar och proxyservrar använder Azure Sentinel en Linux Syslog-server. Agenten installeras på den och där agenten samlar in loggfilerna och vidarebefordrar dem till Azure Sentinel. 
 
1. Klicka på **data kopplingar**.
1. Det finns en panel för varje data källa som du kan ansluta.<br>
Klicka till exempel på **Azure Active Directory**. Om du ansluter den här data källan strömmar du alla loggar från Azure AD till Azure Sentinel. Du kan välja vilken typ av loggar du vill använda för att få inloggnings loggar och/eller gransknings loggar. <br>
I det nedre avsnittet ger Azure Sentinel rekommendationer för vilka arbets böcker du bör installera för varje koppling så att du snabbt kan få intressanta insikter över dina data. <br> Följ installations anvisningarna eller [Läs den relevanta anslutnings guiden](connect-data-sources.md) för mer information. Information om data anslutningar finns i [ansluta Microsoft-tjänster](connect-data-sources.md).

När dina data källor är anslutna börjar dina data strömma till Azure Sentinel och är redo att börja arbeta med. Du kan visa loggarna på de [inbyggda instrument panelerna](quickstart-get-visibility.md) och börja skapa frågor i Log Analytics för att [undersöka data](tutorial-investigate-cases.md).



## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter data källor till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).
- Strömma data från [vanliga Event format-enheter](connect-common-event-format.md) till Azure Sentinel.
