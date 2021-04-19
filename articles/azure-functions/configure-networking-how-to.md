---
title: Så här konfigurerar Azure Functions med ett virtuellt nätverk
description: Artikel som visar hur du utför vissa virtuella nätverksuppgifter för Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: c123b20e163731f9a872a969f2f1564479b6e308
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718439"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Så här konfigurerar Azure Functions med ett virtuellt nätverk

Den här artikeln visar hur du utför uppgifter som rör konfigurering av funktionsappen för att ansluta till och köra i ett virtuellt nätverk. Mer information om Azure Functions och nätverk finns i [Azure Functions nätverksalternativ](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagringskonto till ett virtuellt nätverk 

När du skapar en funktionsapp måste du skapa eller länka till ett allmänt Azure Storage som stöder Blob-, Queue- och Table Storage. Du kan ersätta det här lagringskontot med ett som skyddas med tjänstslutpunkter eller privata slutpunkter. 

> [!NOTE]  
> Den här funktionen fungerar för närvarande för alla SKU:er som stöds av windows-nätverk i dedikerade (App Service) och för Premium-planer. Förbrukningsplanen stöds inte. 

Konfigurera en funktion med ett lagringskonto som är begränsat till ett privat nätverk:

1. Skapa en funktion med ett lagringskonto som inte har tjänstslutpunkter aktiverade.

1. Konfigurera funktionen för att ansluta till ditt virtuella nätverk.

1. Skapa eller konfigurera ett annat lagringskonto.  Det här är det lagringskonto som vi skyddar med tjänstslutpunkter och ansluter vår funktion.

1. [Skapa en filresurs i](../storage/files/storage-how-to-create-file-share.md#create-a-file-share) det skyddade lagringskontot.

1. Aktivera tjänstslutpunkter eller privat slutpunkt för lagringskontot.  
    * Om du använder privata slutpunktsanslutningar behöver lagringskontot en privat slutpunkt för `file` `blob` underresurserna och .  Om du använder vissa funktioner som Durable Functions behöver du också och `queue` är tillgänglig via en privat `table` slutpunktsanslutning.
    * Om du använder tjänstslutpunkter aktiverar du det undernät som är dedikerat till dina funktionsappar för lagringskonton.

1. Kopiera fil- och blobinnehållet från funktionsappens lagringskonto till det skyddade lagringskontot och filresursen.

1. Kopiera anslutningssträngen för det här lagringskontot.

1. Uppdatera **programinställningarna** under **Konfiguration** för funktionsappen till följande:

    | Inställningsnamn | Värde | Kommentar |
    |----|----|----|
    | `AzureWebJobsStorage`| Lagringsanslutningssträng | Det här är anslutningssträngen för ett skyddat lagringskonto. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Lagringsanslutningssträng | Det här är anslutningssträngen för ett skyddat lagringskonto. |
    | `WEBSITE_CONTENTSHARE` | Filresurs | Namnet på filresursen som skapats i det skyddade lagringskontot där projektdistributionsfilerna finns. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Ny inställning |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Tvingar all utgående trafik genom det virtuella nätverket. Krävs när lagringskontot använder privata slutpunktsanslutningar. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | DNS-servern som används av appen. Krävs när lagringskontot använder privata slutpunktsanslutningar. |

1. Välj **Spara** för att spara programinställningarna. Om du ändrar appinställningarna startas appen om.  

När funktionsappen har startats om är den nu ansluten till ett skyddat lagringskonto.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nätverksalternativ för Azure Functions](functions-networking-options.md)

