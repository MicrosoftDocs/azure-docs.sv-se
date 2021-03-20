---
title: Ange omfattning för identifiering av VMware VM med Azure Migrate
description: Beskriver hur du ställer in identifierings omfånget för utvärdering av virtuella VMware-datorer och migrering med Azure Migrate.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: dc5bd178c837deea7a22fb3be5ba438085c0e748
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753560"
---
# <a name="set-discovery-scope-for-vmware-vms"></a>Ange identifierings omfång för virtuella VMware-datorer

Den här artikeln beskriver hur du begränsar omfattningen för virtuella VMware-datorer som du upptäcker när du är:

- Identifiera virtuella datorer med [Azure Migrate-enheten](migrate-appliance-architecture.md) när du använder verktyget Azure Migrate: Server bedömning.
- Identifiera virtuella datorer med [Azure Migrate-enheten](migrate-appliance-architecture.md) när du använder verktyget Azure Migrate: Migreringsverktyg för att migrera virtuella VMware-datorer till Azure.

När du konfigurerar enheten ansluter den till vCenter Server och påbörjar identifieringen. Innan du ansluter enheten till vCenter Server kan du begränsa identifieringen till vCenter Server Data Center, kluster, en mapp med kluster, värdar, en mapp med värdar eller enskilda virtuella datorer. Om du vill ange ett omfång tilldelar du behörigheter för det konto som enheten använder för att få åtkomst till vCenter Server.

## <a name="before-you-start"></a>Innan du börjar

Om du inte har konfigurerat ett vCenter-användarkonto som Azure Migrate använder för identifiering, gör du det nu för [utvärdering](./tutorial-discover-vmware.md#prepare-vmware) eller utan [agent migrering](./migrate-support-matrix-vmware-migration.md#agentless-migration).


## <a name="assign-permissions-and-roles"></a>Tilldela behörigheter och roller

Du kan tilldela behörigheter för VMware Inventory-objekt med en av två metoder:

- På det konto som används av enheten tilldelar du en roll med de behörigheter som krävs för de objekt som du vill använda som definitions område.
- Du kan också tilldela en roll till kontot på data center nivå och sprida till de underordnade objekten. Ge sedan kontot **Ingen åtkomst** roll för varje objekt som du inte vill ha i omfånget. Vi rekommenderar inte den här metoden eftersom den är besvärlig och kan exponera åtkomst kontroller, eftersom varje nytt underordnat objekt automatiskt beviljas åtkomst som ärvs från den överordnade.

Du kan inte omfattnings inventerings identifiering på den virtuella vCenter-mappens katalog nivå. Om du behöver omfattning för att identifiera virtuella datorer i en VM-mapp skapar du en användare och beviljar åtkomst individuellt till varje virtuell dator som krävs. Host-och cluster-mappar stöds.


### <a name="assign-a-role-for-assessment"></a>Tilldela en roll för utvärdering

1. På det dator-vCenter-konto som du använder för identifiering använder du den **skrivskyddade** rollen för alla överordnade objekt som är värdar för de virtuella datorer som du vill identifiera och utvärdera (värd, kluster, värd mapp, mappen kluster, upp till data Center).
2. Sprid dessa behörigheter till underordnade objekt i hierarkin.

    ![Tilldela behörigheter](./media/tutorial-assess-vmware/assign-perms.png)

### <a name="assign-a-role-for-agentless-migration"></a>Tilldela en roll för migrering utan agent

1. På det dator-vCenter-konto som du använder för migrering använder du en användardefinierad roll som har de [behörigheter som krävs](migrate-support-matrix-vmware-migration.md#vmware-requirements-agentless)för alla överordnade objekt som är värdar för de virtuella datorer som du vill identifiera och migrera.
2. Du kan ge rollen ett namn som är lättare att identifiera. Till exempel <em>Azure_Migrate</em>.

## <a name="work-around-vm-folder-restriction"></a>Lösning för begränsning av VM-mappar

För närvarande kan Server utvärderings verktyget inte identifiera virtuella datorer om åtkomst beviljas på den virtuella vCenter-mappens nivå. Använd den här lösningen om du vill begränsa identifieringen och utvärderingen av VM-mappar.

1. Tilldela skrivskyddade behörigheter för alla virtuella datorer som finns i de VM-mappar som du vill använda för identifiering och bedömning.
2. Bevilja skrivskyddad åtkomst till alla överordnade objekt som är värdar för den virtuella datorns värd, kluster, värd mapp, klustrar mapp, upp till data Center). Du behöver inte sprida behörigheterna till alla underordnade objekt.
3. Om du vill använda autentiseringsuppgifterna för identifiering väljer du data Center som **samlings omfång**.


Med den rollbaserad åtkomst kontroll inställningen ser du till att motsvarande vCenter-användarkonto har åtkomst till endast klient datorer som är användarspecifika.


## <a name="next-steps"></a>Nästa steg

[Konfigurera installationen](how-to-set-up-appliance-vmware.md)