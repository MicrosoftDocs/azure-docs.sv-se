---
title: Windows Virtual Desktop url-lista som krävs – Azure
description: En lista över URL:er som du måste avblockera för att Windows Virtual Desktop distributionen fungerar som avsett.
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 00ae761af44b9e6537149c96607c0ba00e6439c8
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514993"
---
# <a name="required-url-list"></a>Obligatorisk URL-lista

För att distribuera och använda Windows Virtual Desktop måste du avblockera vissa URL:er så att dina virtuella datorer (VM) kan komma åt dem när som helst. Den här artikeln innehåller de URL:er som du behöver avblockera för att Windows Virtual Desktop ska fungera korrekt. 

>[!IMPORTANT]
>Windows Virtual Desktop stöder inte distributioner som blockerar url:erna som anges i den här artikeln.

## <a name="required-url-check-tool"></a>Nödvändigt verktyg för URL-kontroll

Verktyget Nödvändig URL-kontroll validerar URL:er och visar om de URL:er som den virtuella datorn behöver för att fungera är tillgängliga. Annars visar verktyget de otillgängliga URL:erna så att du kan avblockera dem om det behövs.

Det är viktigt att ha följande i åtanke:

- Du kan bara använda verktyget Nödvändig URL-kontroll för distributioner i kommersiella moln.
- Verktyget Nödvändig URL-kontroll kan inte kontrollera URL:er med jokertecken, så se till att avblockera webbadresserna först.

### <a name="requirements"></a>Krav

Du behöver följande saker för att använda verktyget Nödvändig URL-kontroll:

- Den virtuella datorn måste ha ett .NET 4.6.2-ramverk
- RDAgent version 1.0.2944.400 eller senare
- Filen WVDAgentUrlTool.exe måste finnas i samma mapp som den WVDAgentUrlTool.config filen

### <a name="how-to-use-the-required-url-check-tool"></a>Så här använder du verktyget Nödvändig URL-kontroll

Så här använder du verktyget Nödvändig URL-kontroll:

1. Öppna en kommandotolk som administratör på den virtuella datorn.
2. Kör följande kommando för att ändra katalogen till samma mapp som bygga agenten:

    ```console
    cd C:\Program Files\Microsoft RDInfra\RDAgent_1.0.2944.1200
    ```

3. Kör följande kommando:

    ```console
    WVDAgentUrlTool.exe
    ```
 
4. När du har kört filen visas en lista över tillgängliga och otillgängliga URL:er.

    Följande skärmbild visar till exempel ett scenario där du behöver avblockera två url:er som inte är jokertecken:

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av utdata för icke-tillgängliga URL:er.](media/noaccess.png)
    
    Så här bör utdata se ut när du har avblockerade alla url:er som krävs för icke-jokertecken:

    > [!div class="mx-imgBorder"]
    > ![Skärmbild av utdata för tillgängliga URL:er.](media/access.png)

## <a name="virtual-machines"></a>Virtuella datorer

De virtuella Azure-datorer som du skapar Windows Virtual Desktop måste ha åtkomst till följande URL:er i det kommersiella Azure-molnet:

|Adress|Utgående TCP-port|Syfte|Tjänsttagg|
|---|---|---|---|
|*.wvd.microsoft.com|443|Tjänsttrafik|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Agenttrafik|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Agenttrafik|AzureCloud|
|*xt.blob.core.windows.net|443|Agenttrafik|AzureCloud|
|*eh.servicebus.windows.net|443|Agenttrafik|AzureCloud|
|*xt.table.core.windows.net|443|Agenttrafik|AzureCloud|
|*xt.queue.core.windows.net|443|Agenttrafik|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-aktivering|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Uppdateringar av agent- och SXS-stacken|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal support|AzureCloud|
| 169.254.169.254 | 80 | [Tjänstslutpunkt för Azure Instance Metadata](../virtual-machines/windows/instance-metadata-service.md) | Ej tillämpligt |
| 168.63.129.16 | 80 | [Hälsoövervakning av sessionsvärdar](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Ej tillämpligt |

>[!IMPORTANT]
>Windows Virtual Desktop stöder nu taggen FQDN. Mer information finns i Använda [Azure Firewall för att skydda Windows Virtual Desktop-distributioner.](../firewall/protect-windows-virtual-desktop.md)
>
>Vi rekommenderar att du använder FQDN-taggar eller tjänsttaggar i stället för URL:er för att förhindra tjänstproblem. De angivna URL:erna och taggarna motsvarar endast Windows Virtual Desktop webbplatser och resurser. De innehåller inte URL:er för andra tjänster som Azure Active Directory.

De virtuella Azure-datorer som du skapar Windows Virtual Desktop måste ha åtkomst till följande URL:er i Azure Government molnet:

|Adress|Utgående TCP-port|Syfte|Tjänsttagg|
|---|---|---|---|
|*.wvd.microsoft.us|443|Tjänsttrafik|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|*xt.blob.core.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|*.servicebus.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|*xt.table.core.usgovcloudapi.net|443|Agenttrafik|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows-aktivering|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Uppdateringar av agent- och SXS-stacken|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal support|AzureCloud|
| 169.254.169.254 | 80 | [Tjänstslutpunkt för Azure Instance Metadata](../virtual-machines/windows/instance-metadata-service.md) | Ej tillämpligt |
| 168.63.129.16 | 80 | [Hälsoövervakning av sessionsvärdar](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Ej tillämpligt |

I följande tabell visas valfria URL:er som dina virtuella Azure-datorer kan ha åtkomst till:

|Adress|Utgående TCP-port|Syfte|Azure Gov|
|---|---|---|---|
|*.microsoftonline.com|443|Autentisering till Microsoft Online Services|login.microsoftonline.us|
|*.events.data.microsoft.com|443|Telemetry Service|Inget|
|www.msftconnecttest.com|443|Identifierar om operativsystemet är anslutet till Internet|Inget|
|*.prod.do.dsp.mp.microsoft.com|443|Windows Update|Inget|
|login.windows.net|443|Logga in på Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*.sfx.ms|443|Uppdateringar för OneDrive-klientprogramvara|oneclient.sfx.ms|
|*.digicert.com|443|Kontroll av certifikatåterkallelse|Inget|
|*.azure-dns.com|443|Azure DNS lösning|Inget|
|*.azure-dns.net|443|Azure DNS lösning|Inget|

>[!NOTE]
>Windows Virtual Desktop för närvarande inte någon lista över IP-adressintervall som du kan avblockera för att tillåta nätverkstrafik. Vi stöder endast avblockering av specifika URL:er just nu.
>
>Om du använder en nästa generations brandvägg (NGFW) måste du använda en dynamisk lista som är särskilt utformad för Azure IP-adresser för att se till att du kan ansluta.
>
>En lista över säkra Office-relaterade URL:er, inklusive Azure Active Directory url:er, finns i URL:er och IP-adressintervall för [Office 365.](/office365/enterprise/urls-and-ip-address-ranges)
>
>Du måste använda jokertecknet (*) för URL:er som rör tjänsttrafik. Om du föredrar att inte använda * för agentrelaterad trafik gör du så här för att hitta URL:er utan jokertecken:
>
>1. Registrera dina virtuella datorer till Windows Virtual Desktop värdpoolen.
>2. Öppna **Loggboken** och gå sedan till **Windows-loggar**  >  **Program**  >  **WVD-Agent** och leta efter Händelse-ID 3701.
>3. Avblockera url:erna som du hittar under Händelse-ID 3701. URL:erna under Händelse-ID 3701 är regionsspecifika. Du måste upprepa avblockeringsprocessen med relevanta URL:er för varje region som du vill distribuera dina virtuella datorer i.

## <a name="remote-desktop-clients"></a>Fjärrskrivbordsklienter

Alla fjärrskrivbordsklienter som du använder måste ha åtkomst till följande URL:er:

|Adress|Utgående TCP-port|Syfte|Klienter|Azure Gov|
|---|---|---|---|---|
|*.wvd.microsoft.com|443|Tjänsttrafik|Alla|*.wvd.microsoft.us|
|*.servicebus.windows.net|443|Felsöka data|Alla|*.servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft FWLinks|Alla|Inget|
|aka.ms|443|Microsoft URL-förkortning|Alla|Inget|
|docs.microsoft.com|443|Dokumentation|Alla|Inget|
|privacy.microsoft.com|443|Sekretesspolicy|Alla|Inget|
|query.prod.cms.rt.microsoft.com|443|Klientuppdateringar|Windows-skrivbordet|Inget|

>[!IMPORTANT]
>Det är viktigt att du öppnar dessa URL:er för en tillförlitlig klientupplevelse. Blockering av åtkomst till dessa URL:er stöds inte och påverkar tjänstens funktioner.
>
>Dessa URL:er motsvarar endast klientplatser och resurser. Den här listan innehåller inte URL:er för andra tjänster som Azure Active Directory. Azure Active Directory URL:er finns under ID 56 på URL:er och IP-adressintervall [för Office 365.](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online)
