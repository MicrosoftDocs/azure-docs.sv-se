---
title: Konfigurera en IP-brandvägg för Azure Kognitiv sökning-tjänsten
titleSuffix: Azure Cognitive Search
description: Konfigurera principer för IP-kontroll för att begränsa åtkomsten till Azure Kognitiv sökning-tjänsten.
manager: nitinme
author: mrcarter8
ms.author: mcarter
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: 98a516e76d5e4f5c225873b934949759d7fcba8d
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535339"
---
# <a name="configure-ip-firewall-for-azure-cognitive-search"></a>Konfigurera IP-brandvägg för Azure Kognitiv sökning

Azure Kognitiv sökning stöder IP-regler för inkommande brand Väggs stöd. Den här modellen ger ett extra säkerhets lager för din Sök tjänst som liknar de IP-regler som du hittar i en virtuell Azure-nätverks säkerhets grupp. Med dessa IP-regler kan du konfigurera Sök tjänsten så att den bara kan nås från en godkänd uppsättning datorer och/eller moln tjänster. Åtkomst till data som lagras i Sök tjänsten från dessa godkända uppsättningar av datorer och tjänster kräver fortfarande att anroparen visar en giltig autentiseringstoken.

Du kan ange IP-regler i Azure Portal, enligt beskrivningen i den här artikeln. Du kan också använda [hanterings REST API version 2020-03-13](/rest/api/searchmanagement/), [Azure POWERSHELL](/powershell/module/az.search)eller [Azure CLI](/cli/azure/search).

## <a name="configure-an-ip-firewall-using-the-azure-portal"></a><a id="configure-ip-policy"></a> Konfigurera en IP-brandvägg med hjälp av Azure Portal

Om du vill ange princip för IP-åtkomstkontroll i Azure Portal går du till Azure Kognitiv sökning service-sidan och väljer **nätverk** på navigerings menyn. Slut punktens nätverks anslutning måste vara **offentlig**. Om din anslutning är inställd på **privat** kan du bara komma åt Sök tjänsten via en privat slut punkt.

![Skärm bild som visar hur du konfigurerar IP-brandväggen i Azure Portal](./media/service-configure-firewall/azure-portal-firewall.png)

Azure Portal ger möjlighet att ange IP-adresser och IP-adressintervall i CIDR-format. Ett exempel på CIDR-notering är 8.8.8.0/24 som representerar de IP-adresser som sträcker sig från 8.8.8.0 till 8.8.8.255.

> [!NOTE]
> När du har aktiverat principen för IP-åtkomstkontroll för din Azure Kognitiv sökning-tjänst avvisas alla förfrågningar till data planet från datorer utanför listan över tillåtna IP-adressintervall. När IP-regler har kon figurer ATS är vissa funktioner i Azure Portal inaktiverade. Du kan visa och hantera information om service nivå, men Portal åtkomst till index data och de olika komponenterna i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl. Som ett alternativ till portalen kan du använda [vs Code-tillägget](https://aka.ms/vscode-search) för att interagera med de olika komponenterna i tjänsten.

### <a name="requests-from-your-current-ip"></a>Begär Anden från din aktuella IP

För att förenkla utvecklingen hjälper Azure Portal att identifiera och lägga till IP-adressen för klient datorn i listan över tillåtna. Appar som körs på datorn kan sedan komma åt Azure Kognitiv sökning-tjänsten.

Portalen identifierar automatiskt klientens IP-adress. Det kan vara klient-IP-adressen för din dator eller nätverksgateway. Se till att ta bort den här IP-adressen innan du tar din arbets belastning till produktion.

Om du vill lägga till din aktuella IP-adress i listan över IP-adresser markerar du **Lägg till klientens IP-adress**. Välj sedan **Spara**.

![Skärm bild som visar hur du konfigurerar inställningar för IP-brandvägg för att tillåta den aktuella IP-adressen](./media/service-configure-firewall/enable-current-ip.png)

## <a name="troubleshoot-issues-with-an-ip-access-control-policy"></a><a id="troubleshoot-ip-firewall"></a>Felsöka problem med en princip för åtkomst kontroll för IP

Du kan felsöka problem med en kontroll princip för IP-åtkomst med hjälp av följande alternativ:

### <a name="azure-portal"></a>Azure Portal

Om du aktiverar en princip för IP-åtkomstkontroll för din Azure Kognitiv sökning-tjänst blockeras alla begär Anden från datorer utanför listan över tillåtna IP-adressintervall, inklusive Azure Portal.  Du kan visa och hantera information om service nivå, men Portal åtkomst till index data och de olika komponenterna i tjänsten, till exempel index, indexerare och färdigheter definitioner, är begränsad av säkerhets skäl. 

### <a name="sdks"></a>SDK:er

När du kommer åt Azure Kognitiv sökning-tjänsten med hjälp av SDK från datorer som inte finns i listan över tillåtna, returneras ett allmänt **403 förbjudet** svar utan ytterligare information. Kontrol lera att det finns en lista över tillåtna IP-adresser för ditt konto och se till att rätt konfiguration har uppdaterats för Sök tjänsten.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kommer åt din Sök tjänst via privat länk finns i följande artikel:

* [Skapa en privat slut punkt för en säker anslutning till Azure Kognitiv sökning](service-create-private-endpoint.md)