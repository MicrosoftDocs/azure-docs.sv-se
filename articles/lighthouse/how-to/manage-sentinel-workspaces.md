---
title: Hantera Azure Sentinel-arbetsytor i skala
description: Lär dig hur du effektivt hanterar Azure Sentinel på delegerade kund resurser.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 009edaefe021dedb5d9a40a8cc3bac2c2974ae10
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101702529"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Hantera Azure Sentinel-arbetsytor i skala

Som tjänst leverantör kan du ha registrerat flera kund klienter i [Azure-Lighthouse](../overview.md). Med Azure Lighthouse kan tjänst leverantörer utföra åtgärder i skala över flera Azure Active Directory (Azure AD)-klienter samtidigt, och göra hanterings uppgifter mer effektiva.

Azure Sentinel levererar säkerhets analys och hot information, som tillhandahåller en enda lösning för aviserings identifiering, Hot synlighet, proaktiv jakt och hot svar. Med Azure Lighthouse kan du hantera flera Azure Sentinel-arbetsytor mellan klienter i stor skala. Detta möjliggör scenarier som att köra frågor över flera arbets ytor eller skapa arbets böcker för att visualisera och övervaka data från dina anslutna data källor för att få insikter. IP-adresser, till exempel frågor och spel böcker finns kvar i din hanterings klient, men kan användas för att utföra säkerhets hantering i kundens klient organisation.

Det här avsnittet innehåller en översikt över hur du använder [Azure Sentinel](../../sentinel/overview.md) på ett skalbart sätt för synlighet och hanterade säkerhets tjänster mellan klienter.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet gäller den här vägledningen även för [företag som använder Azure-Lighthouse för att hantera flera klienter](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Arkitektur överväganden

För en hanterad säkerhets tjänst leverantör (MSSP) som vill bygga ett erbjudande för säkerhet som en tjänst med hjälp av Azure Sentinel, kan ett enda säkerhets åtgärds Center (SOC) behövas för att övervaka, hantera och konfigurera flera Azure Sentinel-arbetsytor som distribueras i enskilda kund klienter. På samma sätt kan företag med flera Azure AD-klienter behöva hantera flera Azure Sentinel-arbetsytor som distribueras över sina klienter centralt.

Den här centraliserade distributions modellen har följande fördelar:

- Ägarskapet av data finns kvar med varje hanterad klient.
- Stöder krav för att lagra data inom geografiska gränser.
- Säkerställer data isolering eftersom data för flera kunder inte lagras i samma arbets yta.
- Förhindrar data exfiltrering från hanterade klienter, vilket hjälper till att säkerställa att data efterlevs.
- Relaterade kostnader debiteras för varje hanterad klient i stället för att hantera klient organisationen.
- Data från alla data källor och data anslutningar som är integrerade med Azure Sentinel (till exempel Azure AD-aktivitets loggar, Office 365-loggar eller Microsoft Threat Protection-aviseringar) förblir inom varje kund klient.
- Minskar nätverks fördröjningen.
- Lätt att lägga till eller ta bort nya dotter bolag eller kunder.

> [!NOTE]
> Du kan hantera delegerade resurser som finns i olika [regioner](../../availability-zones/az-overview.md#regions). Delegering av prenumerationer i ett [nationellt moln](../../active-directory/develop/authentication-national-cloud.md) och det offentliga Azure-molnet, eller över två separata nationella moln, stöds dock inte.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Detaljerad Azure rollbaserad åtkomst kontroll (Azure RBAC)

Varje kund prenumeration som en MSSP ska hantera måste registreras [på Azure Lighthouse](onboard-customer.md). Detta gör det möjligt för utvalda användare i hanterings klienten att komma åt och utföra hanterings åtgärder på Azure Sentinel-arbetsytor som distribueras i kund klienter.

När du skapar dina auktoriseringar kan du tilldela de inbyggda rollerna för Azure Sentinel till användare, grupper eller tjänstens huvud namn i hanterings klienten:

- [Azure Sentinel-läsare](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-svarare](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel-deltagare](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

Du kanske också vill tilldela ytterligare inbyggda roller för att utföra ytterligare funktioner. Information om vilka roller som kan användas med Azure Sentinel finns i [behörigheter i Azure Sentinel](../../sentinel/roles.md).

När du har publicerat dina kunder kan användarna logga in på din hanterings klient och [direkt komma åt kundens Azure Sentinel-arbetsyta](../../sentinel/multiple-tenants-service-providers.md) med de roller som har tilldelats.

## <a name="view-and-manage-incidents-across-workspaces"></a>Visa och hantera incidenter över arbets ytor

Om du hanterar Azure Sentinel-resurser för flera kunder kan du Visa och hantera incidenter i flera arbets ytor över flera klienter samtidigt. Mer information finns i [arbeta med incidenter i många arbets ytor samtidigt](../../sentinel/multiple-workspace-view.md) och [utöka Azure Sentinel över arbets ytor och klienter](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Se till att användarna i hanterings klienten har tilldelats Läs-och Skriv behörighet för alla arbets ytor som hanteras. Om en användare bara har Läs behörighet på vissa arbets ytor kan varnings meddelanden visas när du väljer incidenter på dessa arbets ytor, och användaren kan inte ändra dessa incidenter eller andra som du har valt med dem (även om du har behörighet för de andra).

## <a name="configure-playbooks-for-mitigation"></a>Konfigurera spel böcker för minskning

[Spel böcker](../../sentinel/tutorial-respond-threats-playbook.md) kan användas för automatisk minskning när en avisering utlöses. Dessa spel böcker kan köras manuellt, eller så kan de köras automatiskt när vissa aviseringar utlöses. Spel böcker kan distribueras antingen i hanterings klienten eller kund klienten, med de svars procedurer som kon figurer ATS baserat på vilka klient användare som måste vidta åtgärder som svar på ett säkerhetshot.

## <a name="create-cross-tenant-workbooks"></a>Skapa arbets böcker för flera klienter

[Azure Monitor arbets böcker i Azure Sentinel](../../sentinel/overview.md#workbooks) hjälper dig att visualisera och övervaka data från dina anslutna data källor för att få insikter. Du kan använda de inbyggda mallarna för arbets böcker i Azure Sentinel eller skapa anpassade arbets böcker för dina scenarier.

Du kan distribuera arbets böcker i hanterings klienten och skapa instrument paneler på en skala för att övervaka och fråga efter data mellan kund klienter. Mer information finns i [övervakning av flera arbets ytor](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). 

Du kan också distribuera arbets böcker direkt i en enskild klient som du hanterar för scenarier som är specifika för kunden.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Kör Log Analytics-och jakt frågor i Azure Sentinel-arbetsytor

Skapa och spara Log Analytics frågor för att identifiera hot centralt i hanterings klienten, inklusive [jakt frågor](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting). Dessa frågor kan sedan köras i alla dina kunders Azure Sentinel-arbetsytor med hjälp av operatorn union och arbetsyte (). Mer information finns i [frågor om flera arbets ytor](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Använd Automation för hantering över arbets ytor

Du kan använda Automation för att hantera flera Azure Sentinel-arbetsytor och konfigurera [jakt frågor](../../sentinel/hunting.md), spel böcker och arbets böcker. Mer information finns i [hantering av flera arbets ytor med hjälp av Automation](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Övervaka säkerheten i Office 365-miljöer

Använd Azure-Lighthouse tillsammans med Azure Sentinel för att övervaka säkerheten för Office 365-miljöer mellan klienter. Först måste du vara inloggad [i box 365-data kopplingarna i den hanterade klienten](../../sentinel/connect-office-365.md) så att information om användar-och administratörs aktiviteter i Exchange och SharePoint (inklusive OneDrive) kan matas in till en Azure Sentinel-arbetsyta i den hanterade klienten. Detta omfattar information om åtgärder som fil hämtningar, åtkomst förfrågningar som skickats, ändringar av grupp händelser och post lådor, tillsammans med information om de användare som utförde åtgärderna. [Office 365 DLP-aviseringar](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) stöds också som en del av den inbyggda Office 365-anslutningen.

Du kan aktivera [Microsoft Cloud App Security-anslutningen (MCAS)](../../sentinel/connect-cloud-app-security.md) för att strömma aviseringar och Cloud Discovery loggar i Azure Sentinel. På så sätt kan du få insyn i molnappar, få sofistikerade analyser för att identifiera och bekämpa cyberhot, samt styra hur data överförs. Aktivitets loggar för MCAS kan [förbrukas med hjälp av CEF (common Event format)](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849).

När du har konfigurerat Office 365 data Connectors kan du använda funktioner för Azure-kontroll över flera klienter, till exempel Visa och analysera data i arbets böcker, använda frågor för att skapa anpassade aviseringar och konfigurera spel böcker för att svara på hot.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [Azure Sentinel](../../sentinel/overview.md).
- Gå igenom [prissättnings sidan för Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).

