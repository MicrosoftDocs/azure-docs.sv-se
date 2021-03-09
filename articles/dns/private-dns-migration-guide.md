---
title: Migrera äldre Azure DNS Private Zones till ny resurs modell
titleSuffix: Azure DNS
description: Den här guiden innehåller steg-för-steg-instruktioner om hur du migrerar äldre privata DNS-zoner till den senaste resurs modellen
services: dns
author: rohinkoul
ms.service: dns
ms.topic: how-to
ms.date: 06/18/2019
ms.author: rohink
ms.openlocfilehash: 72d046cde70d1224eb1fd47f527c9e49c6b002f6
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500469"
---
# <a name="migrating-legacy-azure-dns-private-zones-to-new-resource-model"></a>Migrera äldre Azure DNS privata zoner till ny resurs modell

Under den offentliga för hands versionen skapades privata DNS-zoner med "dnszones"-resurs med egenskapen "zoneType" inställd på "privat". Sådana zoner kommer inte att stödjas efter 31 december 2019 och måste migreras till GA-resurs modell som använder resurs typen "privateDnsZones" i stället för "dnszones". Migreringsprocessen är enkel och vi har angett ett PowerShell-skript för att automatisera processen. Den här guiden innehåller steg-för-steg-instruktioner om hur du migrerar Azure DNS privata zoner till den nya resurs modellen.

Ta reda på vilka dnszones-resurser som kräver migrering. Kör kommandot nedan i Azure CLI.
```azurecli
az account set --subscription <SubscriptionId>
az network dns zone list --query "[?zoneType=='Private']"
```

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har installerat den senaste versionen av Azure PowerShell. Mer information om Azure PowerShell (AZ) och hur du installerar det finns på https://docs.microsoft.com/powershell/azure/new-azureps-module-az

Kontrol lera att du har AZ. PrivateDns-modulen för den Azure PowerShell installerad. Du installerar den här modulen genom att öppna ett upphöjd PowerShell-fönster (administrations läge) och ange följande kommando

```powershell
Install-Module -Name Az.PrivateDns
```

>[!IMPORTANT]
>Migreringsprocessen är helt automatiserad och förväntas inte orsaka avbrott. Men om du använder Azure DNS privata zoner (för hands version) i en kritisk produktions miljö bör du köra följande migreringsprocessen under ett planerat underhålls tids fönster. Se till att du inte ändrar konfigurationen eller post uppsättningarna för en privat DNS-zon när du kör migrations skriptet.

## <a name="installing-the-script"></a>Installera skriptet

Öppna ett upphöjt PowerShell-fönster (administrativt läge) och kör följande kommando

```powershell
install-script PrivateDnsMigrationScript
```

Ange "A" när du uppmanas att installera skriptet

![Installera skriptet](./media/private-dns-migration-guide/install-migration-script.png)

Du kan också hämta den senaste versionen av PowerShell-skriptet manuellt på https://www.powershellgallery.com/packages/PrivateDnsMigrationScript

>[!IMPORTANT]
>Migrations skriptet får inte köras i Azure Cloud Shell och måste köras på en virtuell dator eller lokal dator som är ansluten till Internet.

## <a name="running-the-script"></a>Köra skriptet

Kör följande kommando för att köra skriptet

```powershell
PrivateDnsMigrationScript.ps1
```

![Köra skriptet](./media/private-dns-migration-guide/running-migration-script.png)

### <a name="enter-the-subscription-id-and-sign-in-to-azure"></a>Ange prenumerations-ID och inloggning till Azure

Du uppmanas att ange prenumerations-ID som innehåller de privata DNS-zoner som du vill migrera. Du uppmanas att logga in på ditt Azure-konto. Slutför inloggningen så att skriptet kan komma åt de privata DNS-zon resurserna i prenumerationen.

![Logga in till Azure](./media/private-dns-migration-guide/login-migration-script.png)

### <a name="select-the-dns-zones-you-want-to-migrate"></a>Välj de DNS-zoner som du vill migrera

Skriptet med hämta en lista över alla privata DNS-zoner i prenumerationen och du tillfrågas om vilka du vill migrera. Ange "A" om du vill migrera alla privata DNS-zoner. När du har kört det här steget skapar skriptet nya privata DNS-zoner med ny resurs modell och kopierar data till den nya DSN-zonen. Det här steget kommer inte att förändra dina befintliga privata DNS-zoner i alla fall.

![Välj DNS-zoner](./media/private-dns-migration-guide/migratezone-migration-script.png)

### <a name="switching-dns-resolution-to-the-new-dns-zones"></a>Växla DNS-matchning till de nya DNS-zonerna

När zonerna och posterna har kopierats till den nya resurs modellen uppmanas du att växla DNS-matchningen till nya DNS-zoner i skriptet. Det här steget tar bort associationen mellan äldre privata DNS-zoner och dina virtuella nätverk. När den äldre zonen är olänkad från de virtuella nätverken tar de nya DNS-zonerna som skapats i ovanstående steg automatiskt över DNS-matchningen för de virtuella nätverken.

Välj "A" om du vill ändra DNS-matchningen för alla virtuella nätverk.

![Byter namn matchning](./media/private-dns-migration-guide/switchresolution-migration-script.png)

### <a name="verify-the-dns-resolution"></a>Verifiera DNS-matchningen

Innan du fortsätter bör du kontrol lera att DNS-matchningen på dina DNS-zoner fungerar som förväntat. Du kan logga in på dina virtuella Azure-datorer och utfärda nslookup-fråga mot migrerade zoner för att kontrol lera att DNS-matchning fungerar.

![Verifiera namn matchning](./media/private-dns-migration-guide/verifyresolution-migration-script.png)

Om du upptäcker att DNS-frågor inte kan lösas väntar du några minuter och försöker köra frågorna igen. Om DNS-frågor fungerar som förväntat anger du ' Y ' När skriptet frågar dig om du vill ta bort det virtuella nätverket från den privata DNS-zonen.

![Bekräfta namn matchning](./media/private-dns-migration-guide/confirmresolution-migration-script.png)

>[!IMPORTANT]
>Om DNS-matchning av DNS-matchning mot migrerade zoner inte fungerar som förväntat, anger du "N" i ovanstående steg och skriptet växlar DNS-matchningen tillbaka till äldre zoner. Skapa ett support ärende och vi kan hjälpa dig med migreringen av dina DNS-zoner.

## <a name="cleanup"></a>Rensa

Det här steget tar bort de äldre DNS-zonerna och ska endast köras när du har kontrollerat att DNS-matchningen fungerar som förväntat. Du uppmanas att ta bort varje privat DNS-zon. Ange "Y" vid varje prompt när du har verifierat att DNS-matchningen för zonerna fungerar som den ska.

![Rensa](./media/private-dns-migration-guide/cleanup-migration-script.png)

## <a name="update-your-automation"></a>Uppdatera din automatisering

Om du använder Automation, inklusive mallar, PowerShell-skript eller anpassad kod som har utvecklats med SDK, måste du uppdatera automatiseringen så att den använder den nya resurs modellen för privata DNS-zoner. Nedan visas länkar till en ny privat DNS CLI/PS/SDK-dokumentation.
* [Azure DNS privata zoner REST API](/rest/api/dns/privatedns/privatezones)
* [Azure DNS privata zoner CLI](/cli/azure/ext/privatedns/network/private-dns)
* [Azure DNS privata zoner PowerShell](/powershell/module/az.privatedns/)
* [Azure DNS privata zoner SDK](/dotnet/api/overview/azure/privatedns/management?view=azure-dotnet-preview)

## <a name="need-further-help"></a>Behöver ytterligare hjälp

Skapa ett support ärende om du behöver ytterligare hjälp med migreringsprocessen eller på grund av eventuella skäl som anges ovan. Inkludera avskrifts filen som genererats av PowerShell-skriptet med ditt support ärende.

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du skapar en privat zon i Azure DNS med [Azure PowerShell](./private-dns-getstarted-powershell.md) eller [Azure CLI](./private-dns-getstarted-cli.md).

* Läs om några vanliga [scenarier för privata](./private-dns-scenarios.md) zoner som kan realiseras med privata zoner i Azure DNS.

* Vanliga frågor och svar om privata zoner i Azure DNS, inklusive specifika funktioner som kan förväntas för vissa typer av åtgärder, finns i [privat DNS vanliga frågor](./dns-faq-private.md)och svar.

* Lär dig mer om DNS-zoner och poster genom att gå till [Översikt över DNS-zoner och-poster](dns-zones-records.md).

* Lär dig mer om de andra viktiga [nätverksfunktionerna](../networking/networking-overview.md) i Azure.