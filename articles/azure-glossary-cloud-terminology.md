---
title: Azure-ordlista – Azure-ordlista | Microsoft Docs
description: Använd Azures ord lista för att förstå moln terminologin på Azure-plattformen. Den här korta Azure-ordlistan innehåller definitioner för vanliga moln villkor för Azure.
keywords: Azure-ordlista, molnbaserad terminologi, Azure-ordlista, terminologi definitioner, moln villkor
services: na
documentationcenter: na
author: MonicaRush
manager: jhubbard
editor: ''
ms.assetid: d7ac12f7-24b5-4bcd-9e4d-3d76fbd8d297
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/16/2017
ms.author: monicar
ms.openlocfilehash: 785c87e8136430557af819cd8c7ab1db8f54c060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507729"
---
# <a name="microsoft-azure-glossary-a-dictionary-of-cloud-terminology-on-the-azure-platform"></a>Microsoft Azure ord lista: en ord lista med moln terminologi på Azure-plattformen

Ord listan Microsoft Azure är en kort ord lista med moln terminologi för Azure-plattformen. Se även:

* [Microsoft Azure och Amazon Web Services](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/) -definitioner av Azure-tjänster och deras AWS-motsvarigheter.<!-- I propose to link to https://azure.microsoft.com/services/ instead of this -->
* Användnings [villkor för molnbaserad data](https://azure.microsoft.com/overview/cloud-computing-dictionary/) behandling – allmänna bransch villkor för molnet.

## <a name="account"></a>konto
Ett konto som används för att få åtkomst till och hantera en Azure-prenumeration. Det kallas ofta ett Azure-konto även om ett konto kan vara något av följande: ett befintligt arbets-, skol-eller privat Microsoft-konto. Du kan också skapa ett konto för att hantera en Azure-prenumeration när du registrerar dig för den [kostnads fria utvärderings versionen](https://azure.microsoft.com).  
Se [Registrera dig för en Azure-prenumeration med ditt Microsoft 365 konto](cost-management-billing/manage/microsoft-365-account-for-azure-subscription.md) och [konton som du kan använda för att logga](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)in.

## <a name="api-app"></a>API-app
Ett annat namn för [App Service-appen](#app-service-app).

## <a name="app-service-app"></a>App Service-app
De beräknings resurser som [Azure App Service](app-service/overview.md) tillhandahåller för att vara värd för en webbplats eller ett webb program, webb-API eller Server del för [mobila appar](/previous-versions/azure/app-service-mobile/app-service-mobile-value-prop). App Service appar kallas även *app Services*, *webbappar*, *API-appar* och *mobilappar*.

## <a name="availability-set"></a>tillgänglighets uppsättning
En samling virtuella datorer som hanteras tillsammans för att ge programredundans och tillförlitlighet. Användningen av en tillgänglighets uppsättning garanterar att det under en planerad eller oplanerad underhålls händelse minst en virtuell dator är tillgänglig.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Hantera tillgängligheten för virtuella Linux-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="azure-classic-deployment-model"></a><a name="classic-model"></a>Klassisk Azure-distributions modell
En av två [distributions modeller](./azure-resource-manager/management/deployment-models.md) som används för att distribuera resurser i Azure (den nya modellen är Azure Resource Manager). Vissa Azure-tjänster stöder bara distributions modellen Resource Manager, vissa stöder bara den klassiska distributions modellen och vissa stöder båda. Dokumentationen för varje Azure-tjänst anger vilken eller vilka modeller de stöder.

## <a name="azure-command-line-interface-cli"></a><a name="cli"></a>Kommando rads gränssnitt för Azure (CLI)
Ett kommando rads gränssnitt som kan användas för att hantera Azure-tjänster från Windows, macOS och Linux.  Vissa tjänster eller tjänst funktioner kan endast hanteras via PowerShell eller CLI. Se [Azure CLI](/cli/azure)

## <a name="azure-powershell"></a><a name="powershell"></a>Azure PowerShell
Ett kommando rads gränssnitt för att hantera Azure-tjänster via en kommando rad från Windows-datorer. Vissa tjänster eller tjänst funktioner kan endast hanteras via PowerShell eller CLI.
Se [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/)

## <a name="azure-resource-manager-deployment-model"></a><a name="arm-model"></a>Azure Resource Manager distributions modell
En av två [distributions modeller](./azure-resource-manager/management/deployment-models.md) som används för att distribuera resurser i Microsoft Azure (den andra är den klassiska distributions modellen). Vissa Azure-tjänster stöder bara distributions modellen Resource Manager, vissa stöder bara den klassiska distributions modellen och vissa stöder båda. Dokumentationen för varje Azure-tjänst anger vilken eller vilka modeller de stöder.

## <a name="fault-domain"></a>feldomän
Samlingen av virtuella datorer i en tillgänglighets uppsättning som kan ha misslyckats på samma gång. Ett exempel är en grupp datorer i ett rack som delar en gemensam ström källa och nätverks växel. I Azure separeras de virtuella datorerna i en tillgänglighets uppsättning automatiskt över flera fel domäner.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [Hantera tillgängligheten för virtuella Linux-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)  

## <a name="geo"></a>geografiska
En definierad gräns för data placering som vanligt vis innehåller två eller flera regioner. Gränserna kan vara inom eller förbi nationella gränser och påverkas av skatte regler. Varje geo har minst en region. Exempel på geografiska områden är Asien och stillahavsområdet och Japan. Kallas även *geografi*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="geo-replication"></a>geo-replikering
Processen för att automatiskt replikera innehåll, till exempel blobbar, tabeller och köer inom ett regionalt par.  
Se [Active Geo-Replication för Azure SQL Database](./azure-sql/database/auto-failover-group-overview.md)
<!-- The meaning of "geo" in this term seems to be different than the meaning provided in the "geo" entry -->

## <a name="image"></a>image
En fil som innehåller operativ system och program konfiguration som kan användas för att skapa ett valfritt antal virtuella datorer. I Azure finns det två typer av avbildningar: VM-avbildning och OS-avbildning. En avbildning av en virtuell dator innehåller ett operativ system och alla diskar som är kopplade till en virtuell dator när avbildningen skapas. En operativ system avbildning innehåller bara ett generaliserat operativ system utan data disk konfiguration.  
Se [navigera och välja avbildningar för virtuella Windows-datorer i Azure med PowerShell eller CLI](virtual-machines/windows/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="limits"></a>sten
Antalet resurser som kan skapas eller prestanda måttet som kan uppnås. Gränser är vanligt vis kopplade till prenumerationer, tjänster och erbjudanden.  
Se [begränsningar, kvoter och begränsningar för Azure-prenumerationen och tjänsten](azure-resource-manager/management/azure-subscription-service-limits.md)

## <a name="load-balancer"></a>belastningsutjämnare
En resurs som distribuerar inkommande trafik mellan datorer i ett nätverk. I Azure distribuerar en belastningsutjämnare trafik till virtuella datorer som definierats i en belastnings Utjämnings uppsättning. En [belastningsutjämnare](load-balancer/load-balancer-overview.md) kan vara Internet-riktad, eller så kan den vara intern.  

## <a name="mobile-app"></a>mobilapp
Ett annat namn för [App Service-appen](#app-service-app).

## <a name="offer"></a>offer
Prissättning, krediter och relaterade villkor som gäller för en Azure-prenumeration.  
Se [sidan med information om Azure-erbjudanden](https://azure.microsoft.com/support/legal/offer-details/)

## <a name="portal"></a>portal
En säker webb portal som används för att distribuera och hantera Azure-tjänster.

## <a name="region"></a>region
Ett område inom en geo som inte korsar nationella gränser och innehåller ett eller flera data Center. Priser, regionala tjänster och erbjudande typer visas på regions nivå. En region är vanligt vis kopplad till en annan region, vilket kan vara upp till flera hundra mil. Regionala par kan användas som en mekanism för haveri beredskap och scenarier med hög tillgänglighet. Kallas även *plats*.  
Se [Azure-regioner](best-practices-availability-paired-regions.md)

## <a name="resource"></a>resource
Ett objekt som ingår i din Azure-lösning. Med varje Azure-tjänst kan du distribuera olika typer av resurser, till exempel databaser eller virtuella datorer.   
Se [Azure Resource Manager översikt](azure-resource-manager/management/overview.md)

## <a name="resource-group"></a>Resursgrupp
En behållare i Resource Manager som innehåller relaterade resurser för ett program. Resurs gruppen kan innehålla alla resurser för ett program, eller endast de resurser som är logiskt grupperade tillsammans. Du kan bestämma hur du vill allokera resurser till resursgrupper baserat på vad som är lämpligast för din organisation.  
Se [Azure Resource Manager översikt](azure-resource-manager/management/overview.md)

## <a name="resource-manager-template"></a><a name="arm-template"></a>Resource Manager-mall
En JSON-fil som definierar en eller flera Azure-resurser i taget och som definierar beroenden mellan de distribuerade resurserna. Mallen kan användas för att distribuera resurserna på ett konsekvent sätt och upprepade gånger.  
Se [redigera Azure Resource Manager mallar](./azure-resource-manager/templates/template-syntax.md)

## <a name="resource-provider"></a>resurs leverantör
En tjänst som tillhandahåller de resurser som du kan distribuera och hantera via Resource Manager. Varje resursprovider tillhandahåller åtgärder som hjälper dig att arbeta med de resurser du distribuerar. Resurs leverantörer kan nås via Azure Portal, Azure PowerShell och flera programmerings-SDK: er.  
Se [Azure Resource Manager översikt](azure-resource-manager/management/overview.md)

## <a name="role"></a>roll
Ett sätt att kontrol lera åtkomst som kan tilldelas till användare, grupper och tjänster. Roller kan utföra åtgärder som att skapa, hantera och läsa på Azure-resurser.  
Se [RBAC: inbyggda roller](role-based-access-control/built-in-roles.md)

## <a name="service-level-agreement-sla"></a><a name="sla"></a>service nivå avtal (SLA)
Avtalet som beskriver Microsofts åtaganden för drift tid och anslutning. Varje Azure-tjänst har ett speciellt service avtal.  
Se [service nivå avtal](https://azure.microsoft.com/support/legal/sla/)

## <a name="shared-access-signature-sas"></a><a name="sas"></a>signatur för delad åtkomst (SAS)
En signatur som gör att du kan ge begränsad åtkomst till en resurs utan att exponera din konto nyckel. [Azure Storage använder](./storage/common/storage-sas-overview.md) till exempel SAS för att bevilja klient åtkomst till objekt som blobbar. [IoT Hub använder SAS](iot-hub/iot-hub-devguide-security.md#security-tokens) för att ge enheter behörighet att skicka telemetri.

## <a name="storage-account"></a>storage account
Ett konto som ger dig åtkomst till Azure Blob-, Queue-, Table-och File-tjänsterna i Azure Storage. Lagrings kontots namn definierar det unika namn området för Azure Storage data objekt.  
Se [om Azure Storage-konton](./storage/common/storage-account-create.md)

## <a name="subscription"></a>prenumeration
Ett kund avtal med Microsoft som gör det möjligt för dem att skaffa Azure-tjänster. Prenumerations pris och relaterade villkor styrs av erbjudandet som valts för prenumerationen.
Se [prenumerationsavtal för Microsoft Online](https://azure.microsoft.com/support/legal/subscription-agreement/) och [hur Azure-prenumerationer är associerade med Azure Active Directory](active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

## <a name="tag"></a>tagg
En indexerings term som gör det möjligt att kategorisera resurser utifrån dina behov för hantering eller fakturering. När du har en komplex samling resurser kan du använda taggar för att visualisera dessa till gångar på det sätt som är mest begripligt. Du kan till exempel tagga resurser som har en liknande roll i organisationen eller som tillhör samma avdelning.  
Se [använda taggar för att organisera dina Azure-resurser](./azure-resource-manager/management/tag-resources.md)

## <a name="update-domain"></a>uppdatera domän
Samlingen av virtuella datorer i en tillgänglighets uppsättning som uppdateras på samma gång. Virtuella datorer i samma uppdaterings domän startas om tillsammans under planerat underhåll. Azure startar aldrig om mer än en uppdaterings domän i taget. Kallas även för en uppgraderings domän.  
Se [Hantera tillgängligheten för virtuella Windows-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) och [Hantera tillgängligheten för virtuella Linux-datorer](./virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-machine"></a><a name="vm"></a>virtuell dator
Program varu implementeringen av en fysisk dator som kör ett operativ system. Flera virtuella datorer kan köras samtidigt på samma maskin vara. I Azure är virtuella datorer tillgängliga i olika storlekar.  
Se [Virtual Machines-dokumentation](https://azure.microsoft.com/documentation/services/virtual-machines/)

## <a name="virtual-machine-extension"></a><a name="vm-extension"></a>tillägg för virtuell dator
En resurs som implementerar beteenden eller funktioner som antingen hjälper andra program att fungera eller ger möjlighet att interagera med en dator som körs. Du kan till exempel använda tillägget för VM-åtkomst för att återställa eller ändra värden för fjärråtkomst på en virtuell Azure-dator.
<!-- This definition seems obscure to me; maybe a list of examples would work better than a conceptual definition? -->
Se om tillägg och funktioner för [virtuella datorer (Windows)](./virtual-machines/extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) eller [om tillägg och funktioner för virtuella datorer (Linux)](./virtual-machines/extensions/features-linux.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="virtual-network"></a><a name="vnet"></a>virtuellt nätverk
Ett nätverk som ger anslutning mellan dina Azure-resurser som är isolerade från alla andra Azure-klienter. Med en [Azure-VPN gateway](vpn-gateway/vpn-gateway-about-vpngateways.md) kan du upprätta anslutningar mellan virtuella nätverk och mellan ett virtuellt nätverk och ett lokalt nätverk. Du kan helt kontrol lera IP-adressblock, DNS-inställningar, säkerhets principer och routningstabeller i ett virtuellt nätverk.  
Se [Virtual Network översikt](virtual-network/virtual-networks-overview.md)  

## <a name="web-app"></a>Webbapp
Ett annat namn för [App Service-appen](#app-service-app).

## <a name="see-also"></a>Se även

* [Kom igång med Azure](https://azure.microsoft.com/get-started/)
* [Moln resurs Center](https://azure.microsoft.com/resources/)  
* [Azure för ditt företags program](https://azure.microsoft.com/overview/business-apps-on-azure/)
* [Azure i ditt data Center](https://azure.microsoft.com/overview/business-apps-on-azure/)