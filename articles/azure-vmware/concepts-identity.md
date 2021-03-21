---
title: Koncept – identitet och åtkomst
description: Lär dig mer om identitets-och åtkomst koncepten i Azure VMware-lösningen
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 07a7ac8093524ef4240b8f7607d649520b9439e1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586258"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Identitets koncept för Azure VMware-lösning

Privata moln i Azure VMware-lösningen är etablerade med vCenter Server och NSX-T Manager. Du använder vCenter för att hantera arbets belastningar för virtuella datorer (VM). Du kan använda NSX-T-hanteraren för att hantera och utöka det privata moln nätverket.

VCenter-åtkomst-och identitets hanteringen använder grupp privilegierna build-CloudAdmin. NSX-T-hanteraren använder begränsade administratörs behörigheter. Detta är av den hanterade tjänstens beskaffenhet och säkerställer att din privata moln plattform uppgraderas med de senaste funktionerna och korrigeringarna som ska förväntas.  Mer information finns i [artikeln om uppgraderingar av privata moln][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>vCenter-åtkomst och identitet

VCenter CloudAdmin-gruppen definierar och ger behörighet i vCenter. Ett annat alternativ är att ge åtkomst och identitet genom integrering av enkel inloggning med vCenter LDAP med Azure Active Directory. Du aktiverar den integrationen när du har distribuerat ditt privata moln. 

Tabellen visar **CloudAdmin** -och **CloudGlobalAdmin** -privilegier.

|  Privilegie rad uppsättning           | CloudAdmin | CloudGlobalAdmin | Kommentar |
| :---                     |    :---:   |       :---:      |   :--:  |
|  Larm                  | En CloudAdmin-användare har alla larm privilegier för larm i Compute-ResourcePool och virtuella datorer.     |          --        |  -- |
|  Automatisk distribution             |  --  |        --        |  Microsoft hanterar värd hantering.  |
|  Certifikat            |  --  |        --       |  Microsoft certifikat hantering.  |
|  Innehållsbibliotek         | En CloudAdmin-användare har behörighet att skapa och använda filer i ett innehålls bibliotek.    |         Aktive rad med SSO.         |  Microsoft distribuerar filer i innehålls biblioteket till ESXi-värdar.  |
|  Datacenter              |  --  |        --          |  Microsoft utför alla data Center åtgärder.  |
|  Datalager               | Data lager. AllocateSpace, data lager. browse, Datastore.Config, data lager. DeleteFile, data lager. FileManagement, data lager. UpdateVirtualMachineMetadata     |    --    |   -- |
|  ESX Agent Manager       |  --  |         --       |  Microsoft utför alla åtgärder.  |
|  Mapp                  |  En CloudAdmin-användare har alla mappbehörigheter.     |  --  |  --  |
|  Global                  |  Global. CancelTask, global. GlobalTag, global. Health, global. LogEvent, global. ManageCustomFields, global. ServiceManagers, global. SetCustomField, Global.SystemTag         |                  |    |
|  Värd                    |  Host. HBR. HbrManagement      |        --          |  Microsoft utför alla andra värd åtgärder.  |
|  InventoryService        |  InventoryService. taggning      |        --          |  --  |
|  Nätverk                 |  Nätverk. tilldela    |                  |  Microsoft utför alla andra nätverks åtgärder.  |
|  Behörigheter             |  --  |        --       |  Microsoft gör alla behörigheter-åtgärder.  |
|  Profil driven lagring  |  --  |        --       |  Microsoft utför alla profil åtgärder.  |
|  Resurs                |  En CloudAdmin-användare har alla resurs behörigheter.        |      --       | --   |
|  Schemalagd aktivitet          |  En CloudAdmin-användare har alla ScheduleTask-behörigheter.   |   --   | -- |
|  Sessioner                |  Sessions. GlobalMessage, sessions. ValidateSession      |   --   |  Microsoft gör alla andra åtgärder i sessioner.  |
|  Lagringspooler           |  StorageViews. View   |        --          |  Microsoft gör alla andra lagrings visnings åtgärder (konfigurera tjänsten).  |
|  Aktiviteter                   |  --  |  --   |  Microsoft hanterar tillägg som hanterar aktiviteter.  |
|  vApp                    |  En CloudAdmin-användare har alla vApp-behörigheter.  |  --  |  --  |
|  Virtuell dator         |  En CloudAdmin-användare har alla VirtualMachine-behörigheter.  |  --  |  --  |
|  vService                |  En CloudAdmin-användare har alla vService-behörigheter.  |  --  |  --  |

## <a name="nsx-t-manager-access-and-identity"></a>NSX-T-hanterarens åtkomst och identitet

Använd *Administratörs* kontot för att komma åt NSX-T-hanteraren. Den har fullständig behörighet och låter dig skapa och hantera nivåer-1 (T1) Gateway, segment (logiska växlar) och alla tjänster. Det här kontot ger även åtkomst till NSX-T-nivå-0-gatewayen (t0). Var mindfull på att göra sådana ändringar, eftersom det kan leda till försämrade nätverks prestanda eller ingen åtkomst till privata moln. Öppna en supportbegäran i Azure Portal för att begära ändringar i din NSX-T t0-Gateway.
  
## <a name="next-steps"></a>Nästa steg

Nu när du har täckt Azure VMware-lösningens åtkomst och identitets koncept kan du vilja lära dig mer om:

- [Koncept för uppgradering av privata moln](concepts-upgrades.md).
- [vSphere-rollbaserad åtkomst kontroll för Azure VMware-lösning](concepts-role-based-access-control.md).
- [Så här aktiverar du Azure VMware-lösnings resurser](enable-azure-vmware-solution.md).

<!-- LINKS - external -->

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
