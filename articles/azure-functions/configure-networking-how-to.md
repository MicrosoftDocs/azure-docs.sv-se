---
title: Så här konfigurerar du Azure Functions med ett virtuellt nätverk
description: Artikel som visar hur du utför vissa virtuella nätverks aktiviteter för Azure Functions.
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: a28a59a0de40bba7914d1920b42034fbbc223ddc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609991"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>Så här konfigurerar du Azure Functions med ett virtuellt nätverk

Den här artikeln visar hur du utför uppgifter som rör hur du konfigurerar din Function-app för att ansluta till och köra i ett virtuellt nätverk. Mer information om Azure Functions och nätverk finns i [Azure Functions nätverks alternativ](functions-networking-options.md).

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>Begränsa ditt lagrings konto till ett virtuellt nätverk 

När du skapar en Function-app måste du skapa eller länka till ett allmänt Azure Storage konto som har stöd för BLOB-, Queue-och table-lagring. Du kan ersätta det här lagrings kontot med ett som skyddas av tjänst slut punkter eller privat slut punkt. 

> [!NOTE]  
> Den här funktionen fungerar för närvarande för alla Windows Virtual Network-stödda SKU: er i dedikerade (App Service) plan och för Premium planer. Förbruknings planen stöds inte. 

Så här konfigurerar du en funktion med ett lagrings konto som är begränsat till ett privat nätverk:

1. Skapa en funktion med ett lagrings konto där tjänstens slut punkter inte är aktiverade.

1. Konfigurera funktionen för att ansluta till ditt virtuella nätverk.

1. Skapa eller konfigurera ett annat lagrings konto.  Det här är lagrings kontot som vi skyddar med tjänstens slut punkter och ansluter vår funktion.

1. [Skapa en fil resurs](../storage/files/storage-how-to-create-file-share.md#create-file-share) på det skyddade lagrings kontot.

1. Aktivera tjänstens slut punkter eller privata slut punkter för lagrings kontot.  
    * Om du använder privata slut punkts anslutningar behöver lagrings kontot en privat slut punkt för- `file` och `blob` under resurserna.  Om du använder vissa funktioner som Durable Functions, behöver du också `queue` och `table` kan nås via en privat slut punkt anslutning.
    * Om du använder tjänst slut punkter aktiverar du det undernät som är dedikerat för dina funktions program för lagrings konton.

1. Kopiera filen och blob-innehållet från funktionen app Storage-kontot till det skyddade lagrings kontot och fil resursen.

1. Kopiera anslutnings strängen för det här lagrings kontot.

1. Uppdatera **program inställningarna** under **konfigurationen** för Function-appen till följande:

    | Inställningsnamn | Värde | Kommentar |
    |----|----|----|
    | `AzureWebJobsStorage`| Lagrings anslutnings sträng | Det här är anslutnings strängen för ett skyddat lagrings konto. |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  Lagrings anslutnings sträng | Det här är anslutnings strängen för ett skyddat lagrings konto. |
    | `WEBSITE_CONTENTSHARE` | Filresurs | Namnet på fil resursen som skapats i det skyddade lagrings kontot där Project-filerna finns. |
    | `WEBSITE_CONTENTOVERVNET` | 1 | Ny inställning |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | Tvingar all utgående trafik via det virtuella nätverket. Krävs när lagrings kontot använder privata slut punkts anslutningar. |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | Den DNS-server som används av appen. Krävs när lagrings kontot använder privata slut punkts anslutningar. |

1. Spara program inställningarna genom att välja **Spara** . Om du ändrar inställningar för appar startar appen om.  

När funktionen app har startats om är den nu ansluten till ett skyddat lagrings konto.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Nätverksalternativ för Azure Functions](functions-networking-options.md)

