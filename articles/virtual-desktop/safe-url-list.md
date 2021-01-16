---
title: URL-lista som krävs för Windows Virtual Desktop – Azure
description: 'En lista med URL: er som du måste avblockera för att säkerställa att distributionen av Windows virtuella datorer fungerar som avsett.'
author: Heidilohr
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 872a67d8d9f41c0c809df54304352b2a5f58e011
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251768"
---
# <a name="required-url-list"></a>Obligatorisk URL-lista

För att kunna distribuera och använda det virtuella Windows-skrivbordet måste du avblockera vissa URL: er så att dina virtuella datorer (VM) kan komma åt dem när som helst. Den här artikeln innehåller de URL: er som krävs för att avblockera för att Windows Virtual Desktop ska fungera korrekt. 

>[!IMPORTANT]
>Virtuella Windows-datorer stöder inte distributioner som blockerar URL: erna som anges i den här artikeln.

## <a name="virtual-machines"></a>Virtuella datorer

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste ha åtkomst till följande URL: er i det kommersiella Azure-molnet:

|Adress|Utgående TCP-port|Syfte|Service tag|
|---|---|---|---|
|*. wvd.microsoft.com|443|Tjänst trafik|WindowsVirtualDesktop|
|gcs.prod.monitoring.core.windows.net|443|Agent trafik|AzureCloud|
|production.diagnostics.monitoring.core.windows.net|443|Agent trafik|AzureCloud|
|* xt.blob.core.windows.net|443|Agent trafik|AzureCloud|
|* eh.servicebus.windows.net|443|Agent trafik|AzureCloud|
|* xt.table.core.windows.net|443|Agent trafik|AzureCloud|
|* xt.queue.core.windows.net|443|Agent trafik|AzureCloud|
|catalogartifact.azureedge.net|443|Azure Marketplace|AzureCloud|
|kms.core.windows.net|1688|Windows-aktivering|Internet|
|mrsglobalsteus2prod.blob.core.windows.net|443|Uppdateringar av agent-och SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.windows.net|443|Azure Portal support|AzureCloud|
| 169.254.169.254 | 80 | [Azure instance metadata service-slutpunkt](../virtual-machines/windows/instance-metadata-service.md) | Ej tillämpligt |
| 168.63.129.16 | 80 | [Hälso övervakning av sessions värd](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Ej tillämpligt |

>[!IMPORTANT]
>Windows Virtual Desktop stöder nu FQDN-taggen. Mer information finns i [använda Azure Firewall för att skydda fönster distributioner av virtuella skriv bord](../firewall/protect-windows-virtual-desktop.md).
>
>Vi rekommenderar att du använder FQDN-taggar eller tjänst Taggar i stället för URL: er för att förhindra tjänst problem. URL: er och taggar i listan motsvarar endast Windows virtuella Skriv bords webbplatser och resurser. De omfattar inte URL: er för andra tjänster som Azure Active Directory.

De virtuella Azure-datorer som du skapar för virtuella Windows-datorer måste ha åtkomst till följande URL: er i Azure Government molnet:

|Adress|Utgående TCP-port|Syfte|Service tag|
|---|---|---|---|
|*. wvd.microsoft.us|443|Tjänst trafik|WindowsVirtualDesktop|
|gcs.monitoring.core.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|monitoring.core.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|fairfax.warmpath.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|* xt.blob.core.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|*. servicebus.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|* xt.table.core.usgovcloudapi.net|443|Agent trafik|AzureCloud|
|Kms.core.usgovcloudapi.net|1688|Windows-aktivering|Internet|
|mrsglobalstugviffx.blob.core.usgovcloudapi.net|443|Uppdateringar av agent-och SXS-stack|AzureCloud|
|wvdportalstorageblob.blob.core.usgovcloudapi.net|443|Azure Portal support|AzureCloud|
| 169.254.169.254 | 80 | [Azure instance metadata service-slutpunkt](../virtual-machines/windows/instance-metadata-service.md) | Ej tillämpligt |
| 168.63.129.16 | 80 | [Hälso övervakning av sessions värd](../virtual-network/network-security-groups-overview.md#azure-platform-considerations) | Ej tillämpligt |

I följande tabell visas valfria URL: er som dina virtuella Azure-datorer kan ha åtkomst till:

|Adress|Utgående TCP-port|Syfte|Azure-gov|
|---|---|---|---|
|*.microsoftonline.com|443|Autentisering till Microsoft Online Services|login.microsoftonline.us|
|*. events.data.microsoft.com|443|Telemetry Service|Inget|
|www.msftconnecttest.com|443|Identifierar om operativ systemet är anslutet till Internet|Inget|
|*. prod.do.dsp.mp.microsoft.com|443|Windows Update|Inget|
|login.windows.net|443|Logga in på Microsoft Online Services, Microsoft 365|login.microsoftonline.us|
|*. sfx.ms|443|Uppdateringar för OneDrive-klientprogramvara|oneclient.sfx.ms|
|*. digicert.com|443|Kontroll av certifikatåterkallelse|Inget|
|*. azure-dns.com|443|Azure DNS lösning|Inget|
|*. azure-dns.net|443|Azure DNS lösning|Inget|

>[!NOTE]
>Det finns för närvarande ingen lista över IP-adressintervall som kan avblockeras för att tillåta nätverks trafik i det virtuella Windows-skrivbordet. Vi har bara stöd för att avblockera vissa URL: er just nu.
>
>Om du använder en nästa generations brand vägg (NGFW) måste du använda en dynamisk lista som är särskilt gjord för Azure IP-adresser för att se till att du kan ansluta.
>
>En lista över säkra Office-relaterade URL: er, inklusive nödvändiga Azure Active Directory-relaterade URL: er, finns i [Office 365-URL: er och IP-adressintervall](/office365/enterprise/urls-and-ip-address-ranges).
>
>Du måste använda jokertecknet (*) för URL: er som involverar tjänst trafiken. Om du inte vill använda * för agent-relaterad trafik så här hittar du URL: erna utan jokertecken:
>
>1. Registrera dina virtuella datorer på Windows-poolen för virtuella skriv bord.
>2. Öppna **logg boken** och gå sedan till **Windows loggar**  >  **Application**  >  **WVD-agent** och leta efter händelse-ID 3701.
>3. Avblockera de URL: er som du hittar under händelse-ID 3701. URL: erna under händelse-ID 3701 är landsspecifika. Du måste upprepa den avblockerande processen med relevanta URL: er för varje region som du vill distribuera dina virtuella datorer i.

## <a name="remote-desktop-clients"></a>Fjärrskrivbordsklienter

Alla fjärr skrivbords klienter som du använder måste ha åtkomst till följande URL: er:

|Adress|Utgående TCP-port|Syfte|Klient (er)|Azure-gov|
|---|---|---|---|---|
|*. wvd.microsoft.com|443|Tjänst trafik|Alla|*. wvd.microsoft.us|
|*.servicebus.windows.net|443|Felsöka data|Alla|*. servicebus.usgovcloudapi.net|
|go.microsoft.com|443|Microsoft-FWLinks|Alla|Inget|
|aka.ms|443|Microsoft URL-kortare|Alla|Inget|
|docs.microsoft.com|443|Dokumentation|Alla|Inget|
|privacy.microsoft.com|443|Sekretesspolicy|Alla|Inget|
|query.prod.cms.rt.microsoft.com|443|Klient uppdateringar|Windows-skrivbordet|Inget|

>[!IMPORTANT]
>Att öppna dessa URL: er är viktigt för en tillförlitlig klient upplevelse. Det finns inte stöd för att blockera åtkomst till dessa URL: er och det påverkar service funktionerna.
>
>Dessa URL: er motsvarar bara klient platser och resurser. Den här listan innehåller inte URL: er för andra tjänster som Azure Active Directory. Azure Active Directory-URL: er finns under ID 56 på [Office 365-URL: er och IP-adressintervall](/office365/enterprise/urls-and-ip-address-ranges#microsoft-365-common-and-office-online).