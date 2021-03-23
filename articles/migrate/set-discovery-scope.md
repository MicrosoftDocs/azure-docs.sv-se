---
title: Ange omfång för identifiering av servrar i VMware med Azure Migrate
description: Beskriver hur du ställer in identifierings omfånget för servrar som finns i VMware-utvärdering och migrering med Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 03/13/2021
ms.openlocfilehash: 29ac42da6560a717f12cd256fdd71282e0bd313f
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104775365"
---
# <a name="set-discovery-scope-for-servers-in-vmware-environment"></a>Ange identifierings omfång för servrar i VMware-miljön

Den här artikeln beskriver hur du begränsar identifierings området för servrar i VMware-miljön när du är:

- Identifiera servrar med Azure Migrate- [enheten](migrate-appliance-architecture.md) när du använder verktyget Azure Migrate: identifiering och bedömning.
- Att identifiera servrar med [Azure Migrate-enheten](migrate-appliance-architecture.md) när du använder verktyget Azure Migrate: Migreringsverktyg för att migrera servrar från VMware-miljön till Azure.

När du konfigurerar enheten ansluter den till vCenter Server och påbörjar identifieringen. Innan du ansluter enheten till vCenter Server kan du begränsa identifieringen till vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda servrar. Om du vill ange ett omfång tilldelar du behörigheter för det konto som enheten använder för att få åtkomst till vCenter Server.

## <a name="before-you-start"></a>Innan du börjar

Om du inte har konfigurerat ett vCenter-användarkonto som Azure Migrate använder för identifiering, gör du det nu för [utvärdering](./tutorial-discover-vmware.md#prepare-vmware) eller utan [agent migrering](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Tilldela behörigheter och roller

Du kan tilldela behörigheter för VMware Inventory-objekt med en av två metoder:

- På det konto som används av enheten tilldelar du en roll med de behörigheter som krävs för de objekt som du vill använda som definitions område.
- Du kan också tilldela en roll till kontot på data center nivå och sprida till de underordnade objekten. Ge sedan kontot **Ingen åtkomst** roll för varje objekt som du inte vill ha i omfånget. Vi rekommenderar inte den här metoden eftersom den är besvärlig och kan exponera åtkomst kontroller, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade.

Du kan inte omfattning av inventerings identifiering på vCenter Server-mappens nivå. Om du behöver omfånget identifiera till servrar i en mapp skapar du en användare och beviljar åtkomst individuellt till varje server som krävs. Host-och cluster-mappar stöds.


### <a name="assign-a-role-for-assessment"></a>Tilldela en roll för utvärdering

1. På det dator-vCenter-konto som du använder för identifiering ska du använda den **skrivskyddade** rollen för alla överordnade objekt som är värd för de servrar som du vill identifiera och utvärdera (värd, kluster, värd mapp, mappen kluster, upp till data Center).
2. Sprid dessa behörigheter till underordnade objekt i hierarkin.

    ![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Tilldela en roll för migrering utan agent

1. På det Server-vCenter-konto som du använder för migrering använder du en användardefinierad roll som har de [behörigheter som krävs](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)för alla överordnade objekt som är värdar för de servrar som du vill identifiera och migrera.
2. Du kan ge rollen ett namn som är lättare att identifiera. Till exempel <em>Azure_Migrate</em>.

## <a name="work-around-for-server-folder-restriction"></a>Undvik begränsning av Server mappar

För närvarande kan Azure Migrate: identifierings-och utvärderings verktyget inte identifiera servrar om åtkomst beviljas på vCenter Server-mappens nivå. Om du vill begränsa identifieringen och utvärderingen av servermappar använder du den här lösningen.

1. Tilldela skrivskyddade behörigheter för alla servrar som finns i de mappar som du vill använda för identifiering och bedömning.
2. Bevilja skrivskyddad åtkomst till alla överordnade objekt som är värdar för servrar som värd, kluster, värd mapp, kluster, mapp, upp till data Center). Du behöver inte sprida behörigheterna till alla underordnade objekt.
3. Om du vill använda autentiseringsuppgifterna för identifiering väljer du data Center som **samlings omfång**.


Med den rollbaserad åtkomst kontroll inställningen ser du till att motsvarande vCenter-användarkonto har åtkomst till endast klient-/regionsspecifika servrar.


## <a name="next-steps"></a>Nästa steg

[Konfigurera installationen](how-to-set-up-appliance-vmware.md)