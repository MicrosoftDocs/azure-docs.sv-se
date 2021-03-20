---
title: Företags referens arkitektur för Azure DevTest Labs
description: Den här artikeln innehåller rikt linjer för referens arkitektur för Azure DevTest Labs i ett företag.
ms.topic: article
ms.date: 06/26/2020
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 29f739c2fb9dd1cc58bf6c400eeee1bebb6243c2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92328852"
---
# <a name="azure-devtest-labs-reference-architecture-for-enterprises"></a>Azure DevTest Labs referens arkitektur för företag
Den här artikeln innehåller en referens arkitektur som hjälper dig att distribuera en lösning som baseras på Azure DevTest Labs i ett företag. Den innehåller följande:
- Lokal anslutning via Azure ExpressRoute
- En Fjärrskrivbordsgateway för att logga in på virtuella datorer via fjärr anslutning
- Anslutning till en artefakt lagrings plats för privata artefakter
- Andra PaaS-tjänster som används i labbet

![Referens arkitektur diagram](./media/devtest-lab-reference-architecture/reference-architecture.png)

## <a name="architecture"></a>Arkitektur
Dessa är viktiga element i referens arkitekturen:

- **Azure Active Directory (Azure AD)**: DevTest Labs använder [Azure AD-tjänsten för identitets hantering](../active-directory/fundamentals/active-directory-whatis.md). Tänk på följande två viktiga aspekter när du ger användarna åtkomst till en miljö baserat på DevTest Labs:
    - **Resurs hantering**: den ger till gång till Azure Portal för att hantera resurser (skapa virtuella datorer, skapa miljöer, starta, stoppa, starta om, ta bort och tillämpa artefakter osv.). Resurs hantering görs med hjälp av rollbaserad åtkomst kontroll i Azure (Azure RBAC). Du tilldelar roller till användare och anger behörigheter för resurs-och åtkomst nivå.
    - **Virtuella datorer (nätverks nivå)**: i standard konfigurationen använder virtuella datorer ett lokalt administratörs konto. Om det finns en tillgänglig domän ([Azure AD Domain Services](../active-directory-domain-services/overview.md), en lokal domän eller en molnbaserad domän) kan datorer anslutas till domänen. Användarna kan sedan använda sina domänbaserade identiteter för att ansluta till de virtuella datorerna.
- **Lokal anslutning**: i vårt arkitektur diagram används [ExpressRoute](../expressroute/expressroute-introduction.md) . Men du kan också använda en [VPN för plats-till-plats](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md). Även om ExpressRoute inte krävs för DevTest Labs, används det ofta i företag. ExpressRoute krävs endast om du behöver åtkomst till företags resurser. Vanliga scenarier är:
    - Du har lokala data som inte kan flyttas till molnet.
    - Du föredrar att ansluta Labbets virtuella datorer till den lokala domänen.
    - Du vill tvinga all nätverks trafik in och ut ur moln miljön via en lokal brand vägg för säkerhet/efterlevnad.
- **Nätverks säkerhets grupper**: ett vanligt sätt att begränsa trafiken till moln miljön (eller inom moln miljön) baserat på käll-och mål-IP-adresser är att använda en [nätverks säkerhets grupp](../virtual-network/network-security-groups-overview.md). Till exempel vill du bara tillåta trafik som kommer från företags nätverket till labb nätverk.
- **Fjärrskrivbordsgateway**: företag blockerar vanligt vis utgående fjärr skrivbords anslutningar i företags brand väggen. Det finns flera alternativ för att aktivera anslutning till den molnbaserade miljön i DevTest Labs, inklusive:
  - Använd en [Fjärrskrivbordsgateway](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)och Tillåt den statiska IP-adressen för gateway-belastningsutjämnaren.
  - [Dirigera all inkommande RDP-trafik](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) via ExpressRoute/plats-till-plats-VPN-anslutningen. Den här funktionen är ett vanligt övervägande när företag planerar en DevTest Labs-distribution.
- **Nätverks tjänster (virtuella nätverk, undernät)**: [Azures nätverks](../networking/networking-overview.md) sto pol Ogin är ett annat nyckel element i DevTest Labs-arkitekturen. Den styr om resurser från labbet kan kommunicera och ha åtkomst till lokalt och Internet. Vårt arkitektur diagram innehåller de vanligaste sätt som kunderna använder DevTest Labs: alla labb ansluter via [virtuella nätverks-peering](../virtual-network/virtual-network-peering-overview.md) med hjälp av en [nav-eker-modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) till ExpressRoute/plats-till-plats-VPN-anslutningen till en lokal plats. Men DevTest Labs använder Azure Virtual Network direkt, så det finns inga begränsningar för hur du konfigurerar nätverks infrastrukturen.
- **DevTest Labs**: DevTest Labs är en viktig del av den övergripande arkitekturen. Mer information om tjänsten finns i [About DevTest Labs](devtest-lab-overview.md).
- **Virtuella datorer och andra resurser (SaaS, PaaS, IaaS)**: virtuella datorer är en viktig arbets belastning som DevTest Labs stöder tillsammans med andra Azure-resurser. DevTest Labs gör det enkelt och snabbt för ett företag att ge åtkomst till Azure-resurser (inklusive virtuella datorer och andra Azure-resurser). Läs mer om åtkomst till Azure för [utvecklare](devtest-lab-developer-lab.md) och [testare](devtest-lab-test-env.md).

## <a name="scalability-considerations"></a>Skalbarhetsöverväganden
Även om DevTest Labs inte har några inbyggda kvoter eller begränsningar, har andra Azure-resurser som används i en typisk funktion i ett labb [prenumerations nivå kvoter](../azure-resource-manager/management/azure-subscription-service-limits.md). I en typisk företags distribution behöver du alltså flera Azure-prenumerationer för att få en stor distribution av DevTest Labs. Kvoterna som företag oftast når är:

- **Resurs grupper**: i standard konfigurationen skapar DevTest Labs en resurs grupp för varje ny virtuell dator, eller så skapar användaren en miljö med hjälp av tjänsten. Prenumerationer kan innehålla [upp till 980 resurs grupper](../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits). Det är därför gränsen för virtuella datorer och miljöer i en prenumeration. Det finns två andra konfigurationer som du bör tänka på:
    - **[Alla virtuella datorer går till samma resurs grupp](resource-group-control.md)**: även om den här inställningen hjälper dig att uppfylla gränsen för resurs gruppen påverkar gränsen resurs typ per resurs grupp.
    - **Använda delade offentliga IP-adresser**: alla virtuella datorer med samma storlek och region hamnar i samma resurs grupp. Den här konfigurationen är en "mellanliggande" mellan resurs grupps kvoter och resurs typ per resurs grupp kvoter om de virtuella datorerna får ha offentliga IP-adresser.
- **Resurser per resurs grupp per resurs typ**: standard gränsen för [resurser per resurs grupp per resurs typ är 800](../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits).  När du använder *alla virtuella datorer går du till samma resurs grupps* konfiguration, så träffar användare den här prenumerations gränsen mycket tidigare, särskilt om de virtuella datorerna har många extra diskar.
- **Lagrings konton**: ett labb i DevTest Labs levereras med ett lagrings konto. Azure-kvoten för [antalet lagrings konton per region per prenumeration är 250](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits). Det maximala antalet DevTest Labs i samma region är också 250.
- **Roll tilldelningar**: en roll tilldelning är hur du ger en användare eller huvud åtkomst till en resurs (ägare, resurs, behörighets nivå). I Azure finns det en [gräns på 2 000 roll tilldelningar per prenumeration](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-role-based-access-control-limits). Som standard skapar DevTest Labs-tjänsten en resurs grupp för varje virtuell dator. Ägaren beviljas *ägar* behörighet för DEVTEST Labs VM och *läsar* behörighet till resurs gruppen. På så sätt använder varje ny virtuell dator som du skapar två roll tilldelningar utöver de tilldelningar som används när du ger användare behörighet till labbet.
- **API-läsningar/skrivningar**: det finns olika sätt att automatisera Azure-och DevTest Labs, inklusive REST-API: er, PowerShell, Azure CLI och Azure SDK. Genom automatisering kan du träffa en annan gräns för API-begäranden: varje prenumeration tillåter upp till [12 000 Läs begär Anden och 1 200 Skriv förfrågningar per timme](../azure-resource-manager/management/request-limits-and-throttling.md). Tänk på den här gränsen när du automatiserar DevTest Labs.

## <a name="manageability-considerations"></a>Överväganden för hantering
DevTest Labs har ett bra administrativt användar gränssnitt för att arbeta med ett enda labb. Men i ett företag har du troligen flera Azure-prenumerationer och många labb. Att göra ändringar konsekvent i alla labb kräver skript/automatisering. Här följer några exempel och bästa hanterings praxis för en DevTest Labs-distribution:

- **Ändringar i labb inställningarna**: ett vanligt scenario är att uppdatera en speciell labb inställning för alla labb i distributionen. Till exempel är en ny storlek på virtuell dator instans tillgänglig och alla labb måste uppdateras för att tillåta det. Det är bäst att automatisera dessa ändringar med hjälp av PowerShell-skript, CLI-eller REST-API: er.  
- **Token för personlig åtkomst-token för artefakt lagring**: vanligt vis går det att använda personliga åtkomsttoken för en git-lagringsplats på 90 dagar, ett år eller två år. För att säkerställa kontinuitet är det viktigt att utöka den personliga åtkomsttoken eller skapa en ny. Använd sedan Automation för att använda den nya personliga åtkomsttoken för alla labb.
- **Begränsa ändringar av en labb inställning**: ofta måste en viss inställning vara begränsad (till exempel tillåta användning av Marketplace-avbildningar). Du kan använda Azure Policy för att förhindra ändringar av en resurs typ. Du kan också skapa en anpassad roll och tilldela användare rollen i stället för rollen *ägare* för labbet. Du kan göra detta för de flesta inställningar i labbet (intern support, labb annonsering, tillåtna VM-storlekar och så vidare).
- **Kräv att virtuella datorer följer en namngivnings konvention**: hanterare vill ofta enkelt identifiera virtuella datorer som är en del av en molnbaserad utvecklings-och testnings miljö. Du kan göra detta med hjälp av [Azure policy](https://github.com/Azure/azure-policy/tree/master/samples/TextPatterns/allow-multiple-name-patterns).

Det är viktigt att Observera att DevTest Labs använder underliggande Azure-resurser som hanteras på samma sätt: nätverk, diskar, beräkning och så vidare. Azure Policy gäller till exempel för virtuella datorer som skapas i ett labb. Azure Security Center kan rapportera om VM-kompatibiliteten. Och Azure Backup tjänsten kan tillhandahålla regelbundna säkerhets kopieringar för de virtuella datorerna i labbet.

## <a name="security-considerations"></a>Säkerhetsöverväganden
Azure DevTest Labs använder befintliga resurser i Azure (beräkning, nätverk och så vidare). Det ger den automatiskt nytta av säkerhetsfunktionerna som är inbyggda i plattformen. Om du till exempel vill kräva att inkommande fjärr skrivbords anslutningar endast kommer från företags nätverket, lägger du bara till en nätverks säkerhets grupp i det virtuella nätverket på fjärr skrivbords-gatewayen. Den enda ytterligare säkerhets aspekten är den behörighets nivå som du beviljar de grupp medlemmar som använder labben i dag till dags basis. De vanligaste behörigheterna är [ *ägare* och *användare*](devtest-lab-add-devtest-user.md). Mer information om dessa roller finns i [Lägg till ägare och användare i Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="next-steps"></a>Nästa steg
Se nästa artikel i serien: [skala upp Azure DevTest Labs-infrastrukturen](devtest-lab-guidance-scale.md).