---
title: Vanliga fel och kända problem vid migrering till Azure Cloud Services (utökad support)
description: Översikt över vanliga fel vid migrering från Cloud Services (klassisk) till moln tjänsten (utökad support)
ms.topic: how-to
ms.service: cloud-services-extended-support
ms.subservice: classic-to-arm-migration
author: tanmaygore
ms.author: tagore
ms.reviewer: mimckitt
ms.date: 2/08/2021
ms.custom: ''
ms.openlocfilehash: 58203730793202649c401d96182469fa1eac6ef1
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106287039"
---
# <a name="common-errors-and-known-issues-when-migration-to-azure-cloud-services-extended-support"></a>Vanliga fel och kända problem vid migrering till Azure Cloud Services (utökad support)

Den här artikeln beskriver kända problem och vanliga fel som kan uppstå när du migrerar från Cloud Services (klassisk) till Cloud Services (utökad support). 

## <a name="known-issues"></a>Kända problem
Följande problem är kända och behandlas.

| Kända problem | Åtgärd | 
|---|---|
| Roll instanser startar om UD med UD efter lyckad incheckning. | Restart-åtgärden sker på samma sätt som månatliga gäst operativ Systems distributioner. Genomför inte migrering av moln tjänster med en enda roll instans eller påverkas av omstart.| 
| Azure Portal det går inte att läsa tillståndsmigrering efter att webbläsaren uppdaterats. | Kör validera igen och Förbered åtgärden för att återgå till det ursprungliga migreringsprocessen. | 
| Certifikatet visas som en hemlig resurs i Key Vault. | Efter migreringen laddar du om certifikatet som en certifikat resurs för att förenkla uppdaterings åtgärden på Cloud Services (utökad support). | 
| Distributions etiketter sparas inte som taggar som en del av migreringen. | Skapa taggarna manuellt efter migreringen för att behålla denna information.
| Resurs gruppens namn är bara versaler. | Påverkas inte. Lösningen är inte tillgänglig ännu. |
| Namnet på låset för Cloud Services (utökad support) är felaktigt. | Påverkas inte. Lösningen är inte tillgänglig ännu. | 
| IP-adressens namn är felaktigt på Cloud Services (utökat stöd) Portal bladet. | Påverkas inte. Lösningen är inte tillgänglig ännu. | 
| Ogiltigt DNS-namn visas för virtuell IP-adress efter uppdaterings åtgärd på en migrerad moln tjänst. | Påverkas inte. Lösningen är inte tillgänglig ännu. | 
| Efter lyckad förberedelse är det inte tillåtet att länka en ny Cloud Services (utökad support) distribution som växlings bara. | Länka inte en ny moln tjänst som kan växlas till en för beredd moln tjänst. | 
| Fel meddelanden måste uppdateras. | Påverkas inte. | 

## <a name="common-migration-errors"></a>Vanliga migreringsfel
Vanliga fel och åtgärder för att undvika migrering. 

| Felmeddelande | Information | 
|---|---|
| Det gick inte att hitta resurs typen i namn rymden `Microsoft.Compute` för API-versionen 2020-10-01-Preview. | Registrera CloudServices-funktionen för [prenumerationen](in-place-migration-overview.md#setup-access-for-migration) för att få åtkomst till den offentliga för hands versionen. | 
| Ett internt fel inträffade på servern. Gör om begäran. | Försök igen, Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten. | 
| Ett oväntat fel uppstod i servern vid försök att allokera nätverks resurser för moln tjänsten. Gör om begäran. | Försök igen, Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten. | 
| Distributions distribution – namn i moln tjänst moln-tjänst namn måste vara inom ett virtuellt nätverk som ska migreras. | Distributionen finns inte i ett virtuellt nätverk. Se [det här](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentet för mer information. | 
| Migrering av distributions distribution – namn i moln tjänst moln-tjänst-namn stöds inte eftersom det finns i region region namn. Tillåtna regioner: [lista över tillgängliga regioner]. | Regionen stöds inte ännu för migrering. | 
| Distributions distributionen – namnet i moln tjänstens moln-service-Name kan inte migreras eftersom det inte finns några undernät som är associerade med rollens roll namn. Koppla alla roller till ett undernät och gör sedan om migreringen av moln tjänsten. | Uppdatera Cloud Service-distributionen (klassisk) genom att placera den i ett undernät innan migreringen. |  
| Distributions distributionen – namnet i moln tjänstens moln-service-Name kan inte migreras eftersom distributionen kräver minst en funktion som inte har registrerats för prenumerationen i Azure Resource Manager. Registrera alla nödvändiga funktioner för att migrera den här distributionen. Funktioner som saknas: [lista över saknade funktioner]. | Kontakta supporten för att hämta funktions flaggorna som registrerats. | 
| Det går inte att migrera distributionen eftersom distributionens moln tjänst har två upptagna platser. Migrering av moln tjänster stöds bara för distributioner som är den enda distributionen i moln tjänsten. Ta bort den andra distributionen i moln tjänsten för att fortsätta med migreringen av den här distributionen. | Mer information finns i listan med [scenarier som inte stöds](in-place-migration-overview.md#unsupported-configurations--migration-scenarios) . | 
| Distributions distribution – namn i HostedService Cloud-Service-Name är i mellanliggande tillstånd: State. Migrering är inte tillåten. | Distributionen skapas, tas bort eller uppdateras. Vänta tills åtgärden har slutförts och försök igen. | 
| Distributions distributionen – namnet i den värdbaserade tjänsten moln-service-Name har reserverade IP-adresser men inget reserverat IP-namn. Lös problemet genom att uppdatera det reserverade IP-namnet eller kontakta Microsoft Azure Service Desk. | Uppdatera moln tjänst distributionen. | 
| Distributions distributionen – namnet i den värdbaserade tjänsten moln-service-Name har reserverade IP-adresser, men ingen slut punkt på den reserverade IP-adressen. Lös problemet genom att lägga till minst en slut punkt till den reserverade IP-adressen. | Lägg till slut punkt för reserverad IP. | 
| Migreringen av distributionen {0} i HostedService håller {1} på att allokeras och kan inte ändras förrän den har slutförts.  | Vänta eller försök igen. | 
| Migreringen av distributionen {0} i HostedService håller {1} på att avbrytas och kan inte ändras förrän den har slutförts. | Vänta eller försök igen. |
| En uppdaterings åtgärd pågår för en eller flera virtuella datorer i distributionen {0} i HostedService {1} . Det går inte att migrera förrän den tidigare åtgärden har slutförts. Försök igen om en stund. | Vänta tills åtgärden har slutförts. | 
| Migrering stöds inte för distribution {0} i HostedService {1} eftersom den använder följande funktioner som ännu inte stöds för migrering: distribution utan VNet.| Distributionen finns inte i ett virtuellt nätverk. Se [det här](in-place-migration-technical-details.md#migration-of-deployments-not-in-a-virtual-network) dokumentet för mer information. | 
| Det virtuella nätverks namnet får inte vara null eller tomt. | Ange ett virtuellt nätverks namn i text för REST-begäran | 
| Under nätets namn får inte vara null eller tomt. | Ange under nätets namn i text för REST-begäran. | 
| DestinationVirtualNetwork måste vara inställt på något av följande värden: standard, ny eller befintlig. | Ange egenskapen DestinationVirtualNetwork i text för REST-begäran. | 
| Standard alternativet för VNet-mål är inte implementerat. | Värdet "default" stöds inte för egenskapen DestinationVirtualNetwork i REST Request-texten. | 
| Det {0} går inte att migrera distributionen eftersom CSPKG inte är tillgängligt. | Uppgradera distributionen och försök igen. | 
| Under nätet med ID: t {0} finns på en annan plats än distributionen {1} i den värdbaserade tjänsten {2} . Platsen för under nätet är {3} och platsen för den värdbaserade tjänsten är {4} .  Ange ett undernät på samma plats som distributionen. | Uppdatera moln tjänsten så att den har både ett undernät och en moln tjänst på samma plats innan migreringen. | 
| Migreringen av distributionen {0} i HostedService håller {1} på att avbrytas och kan inte ändras förrän den har slutförts. | Vänta tills Avbryt har slutförts eller försök att avbryta igen. Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten på annat sätt. | 
| Distributionen {0} i HostedService {1} har inte förberetts för migrering. | Kör förberedelse av moln tjänsten innan du kör inchecknings åtgärden. | 
| UnknownExceptionInEndExecute: kontrakt. assert misslyckades: rgName är null eller tomt: ett undantag togs emot i EndExecute som inte är en RdfeException. |   Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten. | 
| UnknownExceptionInEndExecute: en uppgift avbröts: undantag mottaget i EndExecute som inte är en RdfeException. | Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten. | 
| XrpVirtualNetworkMigrationError: det gick inte att migrera det virtuella nätverket. | Använd [Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-cloud-services-extended-support.html) eller kontakta supporten. | 
| Distribution {0} i HostedService {1}  tillhör Virtual Network {2} . Migrera Virtual Network {2} för att migrera den här HostedService {1} . | Se [Virtual Network migrering](in-place-migration-technical-details.md#virtual-network-migration). | 
| Den aktuella kvoten för resurs namnet i Azure Resource Manager är inte tillräcklig för att slutföra migreringen. Den aktuella kvoten är {0} , ytterligare krävs {1} . Skicka en support förfrågan för att öka kvoten och försök att migrera igen när kvoten har Aktiver ATS.    | Använd lämpliga kanaler för att begära kvot ökning: <br>[Kvot ökning för nätverks resurser](../azure-portal/supportability/networking-quota-requests.md) <br>[Kvot ökning för beräknings resurser](../azure-portal/supportability/per-vm-quota-requests.md) | 

## <a name="next-steps"></a>Nästa steg
Mer information om kraven för migrering finns i [teknisk information om migrering till Azure Cloud Services (utökad support)](in-place-migration-technical-details.md)
