---
title: Azure Monitor loggar för tjänst leverantörer | Microsoft Docs
description: Azure Monitor loggar kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd leverantörer att hantera och övervaka servrar i kundens lokala eller molnbaserade infrastruktur.
ms.topic: conceptual
author: MeirMen
ms.author: meirm
ms.date: 02/03/2020
ms.openlocfilehash: 5f1421da10c4748dd78e4c6790568285fa646979
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047119"
---
# <a name="azure-monitor-logs-for-service-providers"></a>Azure Monitor loggar för tjänst leverantörer

Log Analytics arbets ytor i Azure Monitor kan hjälpa Managed Service Providers (MSP), stora företag, oberoende program varu leverantörer (ISV) och värd tjänster att hantera och övervaka servrar i kundens lokala eller molnbaserade infrastruktur.

Stora företag delar många likheter med tjänst leverantörer, särskilt när det finns ett centraliserat IT-team som ansvarar för att hantera den för många olika affär senheter. För enkelhetens skull använder det här dokumentet termen *Service Provider* men samma funktioner är också tillgängliga för företag och andra kunder.

För partner och tjänst leverantörer som ingår i ett [CSP-program (Cloud Solution Provider)](https://partner.microsoft.com/membership/cloud-solution-provider) Log Analytics i Azure Monitor en av de Azure-tjänster som är tillgängliga i Azure CSP-prenumerationer.

Log Analytics i Azure Monitor kan också användas av en tjänst leverantör som hanterar kund resurser via Azures delegerade resurs hanterings funktion i [Azure Lighthouse](../../lighthouse/overview.md).

## <a name="architectures-for-service-providers"></a>Arkitekturer för tjänst leverantörer

Log Analytics-arbetsytor är en metod som administratören kan använda för att styra flödet och isoleringen av [loggdata](../logs/data-platform-logs.md) och skapa en arkitektur som tillgodoser specifika affärs behov. [Den här artikeln](../logs/design-logs-deployment.md) beskriver design-, distributions-och migrerings överväganden för en arbets yta och i artikeln [Hantera åtkomst](../logs/manage-access.md) beskrivs hur du tillämpar och hanterar behörigheter för att logga data. Tjänste leverantörer har ytterligare överväganden.

Det finns tre möjliga arkitekturer för tjänst leverantörer angående Log Analytics arbets ytor:

### <a name="1-distributed---logs-are-stored-in-workspaces-located-in-the-customers-tenant"></a>1. distribuerade loggar lagras i arbets ytor som finns i kundens klient organisation

I den här arkitekturen distribueras en arbets yta i kundens klient organisation som används för alla loggar för kunden.

Det finns två sätt som administratörer kan använda för att få åtkomst till en Log Analytics-arbetsyta i en kund klient organisation:

- En kund kan lägga till enskilda användare från tjänst leverantören som [Azure Active Directory gäst användare (B2B)](../../active-directory/external-identities/what-is-b2b.md). Administratörer för tjänst leverantörer måste logga in på varje kunds katalog i Azure Portal för att kunna komma åt dessa arbets ytor. Detta kräver också att kunderna hanterar enskild åtkomst för varje tjänst leverantörs administratör.
- För bättre skalbarhet och flexibilitet kan tjänst leverantörer använda [Azures delegerade resurs hanterings](../../lighthouse/concepts/azure-delegated-resource-management.md) funktion i [Azure Lighthouse](../../lighthouse/overview.md) för att få åtkomst till kundens klient. Med den här metoden inkluderas tjänst leverantörs administratörer i en Azure AD-användargrupp i tjänst leverantörens klient organisation och gruppen beviljas åtkomst under onboarding-processen för varje kund. Administratörerna kan sedan komma åt varje kunds arbets ytor från sin egen tjänst leverantörs klient, i stället för att behöva logga in på varje kunds klient organisation individuellt. Genom att komma åt dina kunders Log Analytics arbets ytans resurser på det här sättet minskar du det arbete som krävs på kund sidan och kan göra det enklare att samla in och analysera data över flera kunder som hanteras av samma tjänst leverantör via verktyg som [Azure Monitor arbets böcker](../visualize/workbooks-overview.md). Mer information finns i [övervaka kund resurser i stor skala](../../lighthouse/how-to/monitor-at-scale.md).

Fördelarna med den distribuerade arkitekturen är:

* Kunden kan bekräfta specifika behörighets nivåer via [Azure-delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md)eller kan hantera åtkomst till loggarna med sin egen [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md).
* Loggar kan samlas in från alla typer av resurser, inte bara agentbaserade VM-data. Till exempel loggar i Azure granskning.
* Varje kund kan ha olika inställningar för arbets ytan, till exempel kvarhållning och data capping.
* Isolering mellan kunder för regler och kompatibilitet.
* Avgiften för varje arbets yta samlas in i kundens prenumeration.

Nack delarna med den distribuerade arkitekturen är:

* Att visualisera och analysera data centralt [mellan kund klienter](cross-workspace-query.md) med verktyg som Azure Monitor arbets böcker kan resultera i långsammare upplevelser, särskilt när du analyserar data över 50 + arbets ytor.
* Om kunderna inte har publicerats för Azure-delegerad resurs hantering måste tjänst leverantörs administratörerna vara etablerade i kund katalogen och det är svårare för tjänste leverantören att hantera ett stort antal kund klienter samtidigt.

### <a name="2-central---logs-are-stored-in-a-workspace-located-in-the-service-provider-tenant"></a>2. centrala loggar lagras på en arbets yta som finns i tjänste leverantörens klient organisation

I den här arkitekturen lagras loggarna inte i kundens klienter, utan bara på en central plats inom en av tjänst leverantörens prenumerationer. De agenter som är installerade på kundens virtuella datorer konfigureras för att skicka sina loggar till den här arbets ytan med hjälp av arbetsyte-ID och hemlig nyckel.

Fördelarna med den centraliserade arkitekturen är:

* Det är enkelt att hantera ett stort antal kunder och integrera dem i olika Server dels system.
* Tjänste leverantören har fullständig ägarskap över loggarna och de olika artefakterna, till exempel funktioner och sparade frågor.
* Tjänste leverantören kan utföra analyser för alla sina kunder.

Nack delarna med den centraliserade arkitekturen är:

* Den här arkitekturen kan bara användas för agentbaserade VM-data. den kommer inte att använda PaaS, SaaS och Azures infrastruktur data källor.
* Det kan vara svårt att separera data mellan kunderna när de slås samman till en enda arbets yta. Det enda bästa sättet att göra detta är att använda datorns fullständigt kvalificerade domän namn (FQDN) eller via Azures prenumerations-ID.
* Alla data från alla kunder kommer att lagras i samma region med en enda faktura och samma inställningar för kvarhållning och konfiguration.
* Azure Fabric-och PaaS-tjänster som Azure-diagnostik och Azures gransknings loggar kräver att arbets ytan finns i samma klient organisation som resursen, och kan därför inte skicka loggarna till den centrala arbets ytan.
* Alla VM-agenter från alla kunder kommer att autentiseras till den centrala arbets ytan med samma arbetsyte-ID och nyckel. Det finns ingen metod för att blockera loggar från en specifik kund utan att avbryta andra kunder.

### <a name="3-hybrid---logs-are-stored-in-workspace-located-in-the-customers-tenant-and-some-of-them-are-pulled-to-a-central-location"></a>3. Hybrid loggar lagras i arbets ytan som finns i kundens klient organisation och vissa av dem hämtas till en central plats.

Den tredje arkitektur kombinationen mellan de två alternativen. Den baseras på den första distribuerade arkitekturen där loggarna är lokala för varje kund, men använder en mekanism för att skapa en central logg databas. En del av loggarna hämtas till en central plats för rapportering och analys. Den här delen kan vara ett litet antal data typer eller en sammanfattning av aktiviteten, till exempel daglig statistik.

Det finns två alternativ för att implementera loggar på en central plats:

1. Central arbets yta: tjänste leverantören kan skapa en arbets yta i sin klient och använda ett skript som använder [fråge-API](https://dev.loganalytics.io/) [: t med data insamlings-API: et](../logs/data-collector-api.md) för att hämta data från de olika arbets ytorna till den här centrala platsen. Ett annat alternativ än ett skript är att använda [Azure Logic Apps](../../logic-apps/logic-apps-overview.md).

2. Power BI som en central plats: Power BI kan fungera som den centrala platsen när de olika arbets ytorna exporterar data till den med hjälp av integreringen mellan arbets ytan Log Analytics och [Power BI](../visualize/powerbi.md).

## <a name="next-steps"></a>Nästa steg

* Automatisera skapandet och konfigurationen av arbets ytor med [Resource Manager-mallar](../logs/resource-manager-workspace.md)

* Automatisera skapandet av arbets ytor med [PowerShell](../logs/powershell-workspace-configuration.md)

* Använd [aviseringar](../alerts/alerts-overview.md) för att integrera med befintliga system

* Generera sammanfattnings rapporter med [Power BI](../visualize/powerbi.md)

* Publicera kunder till [Azure delegerad resurs hantering](../../lighthouse/concepts/azure-delegated-resource-management.md).
