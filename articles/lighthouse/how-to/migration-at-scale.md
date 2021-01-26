---
title: Hantera migreringsjobb i skala med Azure Migrate
description: Lär dig hur du effektivt använder Azure Migrate på delegerade kund resurser.
ms.date: 12/4/2020
ms.topic: how-to
ms.openlocfilehash: 53f7c390d9f16dcbccbb1d09f46e63fec13eee2d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788951"
---
# <a name="manage-migration-projects-at-scale-with-azure-migrate"></a>Hantera migreringsjobb i skala med Azure Migrate

Som tjänst leverantör kan du ha registrerat flera kund klienter i [Azure-Lighthouse](../overview.md). Med Azure Lighthouse kan tjänst leverantörer utföra åtgärder i skala över flera Azure Active Directory (Azure AD)-klienter samtidigt, och göra hanterings uppgifter mer effektiva.

[Azure Migrate](../../migrate/migrate-services-overview.md) tillhandahåller en central hubb för att utvärdera och migrera till lokala Azure-servrar, infrastruktur, program och data. Vanligt vis måste partner som utför utvärderingar och migrering i skala för flera kunder komma åt varje kund prenumeration individuellt med hjälp av [prenumerations modellen för CSP (Cloud Solution Provider)](/partner-center/customers-revoke-admin-privileges) eller genom att [skapa en gäst användare i kund klienten](../../active-directory/external-identities/what-is-b2b.md).

Azure Lighthouse-integrering med Azure Migrate låter tjänst leverantörer upptäcka, utvärdera och migrera arbets belastningar för olika kunder i stor skala, samtidigt som kunderna får fullständig insyn och kontroll över sina miljöer. Via Azures delegerad resurs hantering har tjänst leverantörer en enda vy över alla Azure Migrate-projekt som de hanterar över flera kund klienter.

> [!NOTE]
> Via Azure Lighthouse kan partners utföra identifiering, utvärdering och migrering för lokala virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och AWS/GCP-instanser. Det finns två alternativ för [migrering av virtuella VMware-datorer](../../migrate/server-migrate-overview.md). För närvarande kan endast den agentbaserade metoden för migrering användas när du arbetar med ett migreringsjobb i en delegerad kund prenumeration. migrering med hjälp av en agent lös replikering stöds för närvarande inte via delegerad åtkomst till kundens omfång.

Det här avsnittet innehåller en översikt över hur du använder [Azure Migrate](../../migrate/migrate-services-overview.md) på ett skalbart sätt.

> [!TIP]
> Även om vi refererar till tjänst leverantörer och kunder i det här avsnittet gäller den här vägledningen även för [företag som använder Azure-Lighthouse för att hantera flera klienter](../concepts/enterprise.md).

Beroende på ditt scenario kanske du vill skapa Azure Migrate i kund innehavaren eller i hanterings klienten. Läs igenom övervägandena nedan och ta reda på vilken modell som passar din kunds migrations behov bäst.

## <a name="create-an-azure-migrate-project-in-the-customer-tenant"></a>Skapa ett Azure Migrate-projekt i kund klienten

Ett alternativ när du använder Azure Lighthouse är att skapa ett Azure Migrate-projekt i kund klienten. Användare i hanterings klienten kan sedan välja kund prenumerationen när de skapar ett migreringsjobb. Från hanterings klienten kan tjänste leverantören utföra de åtgärder som krävs för migreringen. Detta kan omfatta distribution av Azure Migrates utrustning för att identifiera arbets belastningarna, utvärdera arbets belastningar genom att gruppera virtuella datorer och beräkna molnbaserade kostnader, granska VM-beredskap och utföra migreringen.

I det här scenariot skapas och lagras inga resurser i hanterings klienten, även om identifierings-och utvärderings stegen kan initieras och köras från den klienten. Alla resurser, till exempel migreringsjobb, utvärderings rapporter för lokal arbets belastningar och migrerade resurser på mål destinationen, kommer att distribueras i kund prenumerationen. Tjänste leverantören kan dock komma åt alla kund projekt från sina egna klient-och Portal upplevelser.

Den här metoden minimerar kontext byten för tjänst leverantörer som arbetar över flera kunder, samtidigt som kunderna behåller sina resurser på sina egna klienter.

Arbets flödet för den här modellen ser ut ungefär så här:

1. Kunden registreras [i Azure-Lighthouse](onboard-customer.md). Den inbyggda rollen deltagare krävs för den identitet som ska användas med Azure Migrate. Se exempel mal len [delegerad-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) för ett exempel som använder den här rollen.
1. Den angivna användaren loggar in på hanterings klienten i Azure Portal och går sedan till Azure Migrate. Den här användaren [skapar ett Azure Migrate-projekt](../../migrate/create-manage-projects.md)och väljer lämplig delegerad kund prenumeration.
1. Användaren utför sedan [steg för identifiering och utvärdering](../../migrate/tutorial-discover-vmware.md).

   Innan du konfigurerar installations programmet för virtuella VMware-datorer kan du begränsa identifieringen till vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer. Om du vill ange omfånget tilldelar du behörighet för det konto som enheten använder för att få åtkomst till vCenter Server. Detta är användbart om flera kunders virtuella datorer finns på hypervisorn. Du kan inte begränsa identifierings omfånget för Hyper-V.

    > [!NOTE]
    > Du kan identifiera och utvärdera virtuella VMware-datorer för migrering med hjälp Azure Migrate via den delegerade åtkomsten som tillhandahålls av Azure dataLighthouses. För migrering av virtuella VMware-datorer stöds för närvarande endast den agentbaserade metoden när du arbetar med ett migreringsjobb i en delegerad kund prenumeration.

1. När mål kund prenumerationen är klar fortsätter du med migreringen genom åtkomsten som beviljats av Azure-Lighthouse. Det migreringsjobb som innehåller utvärderings resultat och migrerade resurser kommer att skapas i kund innehavaren under mål prenumerationen.

> [!TIP]
> Innan migreringen måste du distribuera en landnings zon för att etablera bas infrastruktur resurserna och förbereda den prenumeration som de virtuella datorerna ska migreras till. För att få åtkomst till eller skapa vissa resurser i den här landnings zonen kan den inbyggda rollen ägare krävas, vilket inte stöds för närvarande i Azure-Lighthouse. För sådana scenarier kan kunden behöva tillhandahålla gäst åtkomst roll eller delegera administratörs åtkomst via prenumerations modellen för CSP. En metod för att skapa landnings zoner för flera innehavare finns i [demo lösningen för flera innehavare-landnings zoner](https://github.com/Azure/Multi-tenant-Landing-Zones) på GitHub.

## <a name="create-an-azure-migrate-project-in-the-managing-tenant"></a>Skapa ett Azure Migrate-projekt i hanterings klienten

I det här scenariot utförs migreringsåtgärder som identifiering och utvärdering fortfarande av användare i hanterings klienten. Migrerings projektet och alla relevanta resurser kommer dock att finnas i partner innehavaren och kunden kommer inte att ha direkt insyn i projektet (men utvärderingarna kan delas med kunderna om det behövs). Målet för migreringen för varje kund är kundens prenumeration.

Den här metoden gör det möjligt för tjänste leverantörer att starta identifiering av migrering och utvärderings projekt snabbt, vilket innebär att de första stegen från kund prenumerationer och klient organisationer kan skapas.

Arbets flödet för den här modellen ser ut ungefär så här:

1. Kunden registreras [i Azure-Lighthouse](onboard-customer.md). Den inbyggda rollen deltagare krävs för den identitet som ska användas med Azure Migrate. Se exempel mal len [delegerad-Resource-Management-azmigrate](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate) för ett exempel som använder den här rollen.
1. Den angivna användaren loggar in på hanterings klienten i Azure Portal och går sedan till Azure Migrate. Den här användaren [skapar ett Azure Migrate-projekt](../../migrate/create-manage-projects.md) i en prenumeration som tillhör hanterings klienten.
1. Användaren utför sedan [steg för identifiering och utvärdering](../../migrate/tutorial-discover-vmware.md). Lokala virtuella datorer identifieras och utvärderas i det migreringsjobb som skapats i hanterings klienten, sedan migreras därifrån.

   Om du hanterar flera kunder på samma Hyper-V-värd kan du identifiera alla arbets belastningar samtidigt. Kundspecifika virtuella datorer kan väljas i samma grupp, en utvärdering kan skapas och migrering kan utföras genom att välja rätt kund prenumeration som mål destination. Du behöver inte begränsa identifierings omfånget och du kan ha en fullständig översikt över alla kund arbets belastningar i ett enda migreringsjobb.

1. När du är klar fortsätter du med migreringen genom att välja den delegerade kund prenumerationen som mål destination för replikering och migrering av arbets belastningarna. De nyligen skapade resurserna kommer att finnas i kund prenumerationen, medan utvärderings data och resurser som hör till migreringsjobbet finns kvar i hanterings klienten.

Obs: du måste ändra parameter filen för att avspegla din miljö innan du distribuerar https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management-azmigrate

## <a name="partner-recognition-for-customer-migrations"></a>Partner igenkänning för kundmigreringar

Som medlem i [Microsoft Partner Network](https://partner.microsoft.com)kan du länka ditt partner-ID med de autentiseringsuppgifter som används för att hantera delegerade kund resurser. Med hjälp av partner admin-länken (PAL) kan Microsoft påverka och utnyttja intäkter från Azure till din organisation baserat på de uppgifter du utför för kunder, inklusive migreringsjobb.

Mer information finns i [Länka ditt partner-ID för att se hur du påverkar delegerade resurser](partner-earned-credit.md).

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Azure Migrate](../../migrate/migrate-services-overview.md).
- Lär dig mer om [hanterings upplevelser mellan flera innehavare](../concepts/cross-tenant-management-experience.md).