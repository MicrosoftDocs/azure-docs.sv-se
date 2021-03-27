---
title: Azure Migrate support mat ris
description: Innehåller en översikt över support inställningar och begränsningar för tjänsten Azure Migrate.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 07/23/2020
ms.openlocfilehash: af0b8a4d3dfbce32e412f5294fb19ade61fd7661
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628177"
---
# <a name="azure-migrate-support-matrix"></a>Azure Migrate support mat ris

Du kan använda [tjänsten Azure Migrate](./migrate-services-overview.md) för att utvärdera och migrera servrar till Microsoft Azure molnet. I den här artikeln sammanfattas allmänna support inställningar och begränsningar för Azure Migrate scenarier och distributioner.

## <a name="supported-assessmentmigration-scenarios"></a>Scenarier för bedömning/migrering som stöds

Tabellen sammanfattar scenarier för identifiering, utvärdering och migrering som stöds.

**Distribution** | **Information** 
--- | --- 
**Identifiering** | Du kan identifiera serverns metadata och dynamiska prestanda data.
**App-Discovery** | Du kan identifiera appar, roller och funktioner som körs på virtuella VMware-datorer. För närvarande är den här funktionen begränsad till enbart identifiering. Utvärderingen är för närvarande på server nivå. Vi erbjuder ännu inte app-, roll-eller funktionsbaserade utvärderingar. 
**Utvärdering** | Utvärdera lokala arbets belastningar och data som körs på virtuella VMware-datorer, virtuella Hyper-V-datorer och fysiska servrar. Utvärdera med Azure Migrate: Server utvärdering, Microsoft Data Migration Assistant (DMA), samt andra verktyg och ISV-erbjudanden.
**Migrering** | Migrera arbets belastningar och data som körs på fysiska servrar, virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och molnbaserade virtuella datorer till Azure. Migrera med Azure Migrate: Server utvärdering och Azure Database Migration Service (DMS) och även andra verktyg och ISV-erbjudanden.

> [!NOTE]
> ISV-verktyg kan för närvarande inte skicka data till Azure Migrate i Azure Government. Du kan använda integrerade Microsoft-verktyg eller använda partner verktyg oberoende av varandra.

## <a name="supported-tools"></a>Verktyg som stöds


Stöd för särskilt verktyg sammanfattas i tabellen.

**Verktyg** | **Utvärdera** | **Migrera** 
--- | --- | ---
Azure Migrate: Server utvärdering | Utvärdera virtuella [VMware-datorer](./tutorial-discover-vmware.md), [virtuella Hyper-V-datorer](./tutorial-discover-hyper-v.md)och [fysiska servrar](./tutorial-discover-physical.md). |  Inte tillgängligt (ej tillämpligt)
Azure Migrate: Server-migrering | Ej tillämpligt | Migrera [virtuella VMware-datorer](tutorial-migrate-vmware.md), [virtuella Hyper-V-datorer](tutorial-migrate-hyper-v.md)och [fysiska servrar](tutorial-migrate-physical-virtual-machines.md).
[Carbonite](https://www.carbonite.com/data-protection-resources/resource/Datasheet/carbonite-migrate-for-microsoft-azure) | Ej tillämpligt | Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar. 
[Cloudamize](https://www.cloudamize.com/platform#tab-0)| Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar. | Ej tillämpligt
[Corent Technology](https://go.microsoft.com/fwlink/?linkid=2084928) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska server sand andra moln arbets belastningar. |  Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar, offentliga moln arbets belastningar.
[Enhet 42](https://go.microsoft.com/fwlink/?linkid=2097158) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar.| Ej tillämpligt
[DMA](/sql/dma/dma-overview) | Utvärdera SQL Server-databaser. | Ej tillämpligt
[DMS](../dms/dms-overview.md) | Ej tillämpligt | Migrera SQL Server, Oracle, MySQL, PostgreSQL, MongoDB. 
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | Utvärdera VDI (Virtual Desktop Infrastructure) | Ej tillämpligt
[Movere](https://www.movere.io/) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, virtuella Xen-datorer, fysiska servrar, arbets stationer (inklusive VDI) och andra moln arbets belastningar. | Ej tillämpligt
[Rack](https://go.microsoft.com/fwlink/?linkid=2102735) | Ej tillämpligt | Migrera virtuella VMWare-datorer, virtuella Hyper-V-datorer, virtuella Xen-datorer, KVM-VM, fysiska servrar och andra moln arbets belastningar 
[Turbonomic](https://go.microsoft.com/fwlink/?linkid=2094295)  | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar. | Ej tillämpligt
[UnifyCloud](https://go.microsoft.com/fwlink/?linkid=2097195) | Utvärdera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar och SQL Server databaser. | Ej tillämpligt
[Webapp-Migration Assistant](https://appmigration.microsoft.com/) | Utvärdera webbappar | Migrera webbappar.
[Zerto](https://go.microsoft.com/fwlink/?linkid=2157322) | Ej tillämpligt |  Migrera virtuella VMware-datorer, virtuella Hyper-V-datorer, fysiska servrar och andra moln arbets belastningar.


## <a name="project"></a>Project

**Support** | **Information**
--- | ---
Prenumeration | Kan ha flera projekt i en prenumeration.
Azure-behörigheter | Användaren behöver deltagar-eller ägar behörigheter i prenumerationen för att skapa ett projekt.
VMwares virtuella datorer  | Utvärdera upp till 35 000 virtuella VMware-datorer i ett enda projekt.
Hyper-V:s virtuella datorer    | Utvärdera upp till 35 000 virtuella Hyper-V-datorer i ett enda projekt.

Ett projekt kan innehålla både virtuella VMware-datorer och virtuella Hyper-V-datorer, upp till utvärderings gränserna.

## <a name="azure-permissions"></a>Azure-behörigheter

För att Azure Migrate ska fungera med Azure behöver du dessa behörigheter innan du börjar utvärdera och migrera servrar.

**Uppgift** | **Behörigheter** | **Information**
--- | --- | ---
Skapa ett projekt | Ditt Azure-konto måste ha behörighet att skapa ett projekt. | Konfigurera för [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)eller [fysiska servrar](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Registrera Azure Migrates apparaten| Azure Migrate använder en förenklad [Azure Migrate](migrate-appliance.md) -enhet för att utvärdera servrar med Azure Migrate: Server utvärdering och för att köra en [agent lös migrering](server-migrate-overview.md) av virtuella VMware-datorer med Azure Migrate: Server-migrering. Den här enheten identifierar servrar och skickar metadata och prestanda data till Azure Migrate.<br/><br/> Under registreringen registreras leverantörer (Microsoft. OffAzure, Microsoft. Migrate och Microsoft. nyckel valv) med den prenumeration som valts i installationen, så att prenumerationen fungerar med resurs leverantören. För att registrera dig måste du ha deltagar-eller ägar åtkomst till prenumerationen.<br/><br/> **VMware**– under onboarding skapar Azure Migrate två Azure Active Directory-appar (Azure AD). Den första appen kommunicerar mellan installations agenter och tjänsten Azure Migrate. Appen har inte behörighet att göra Azure Resource Management-anrop eller ha Azure RBAC-åtkomst för resurser. Den andra appen får åtkomst till en Azure Key Vault som skapats i användar prenumerationen endast för en agent utan enbart VMware-migrering. Vid en icke-migrering skapar Azure Migrate en Key Vault för att hantera åtkomst nycklar till lagrings kontot för replikering i din prenumeration. Den har Azure RBAC-åtkomst på Azure Key Vault (i kund klienten) när identifieringen initieras från enheten.<br/><br/> **Hyper-V**– under onboarding. Azure Migrate skapar en Azure AD-App. Appen kommunicerar mellan installations agenter och tjänsten Azure Migrate. Appen har inte behörighet att göra Azure Resource Management-anrop eller ha Azure RBAC-åtkomst för resurser. | Konfigurera för [VMware](./tutorial-discover-vmware.md#prepare-an-azure-user-account), [Hyper-V](./tutorial-discover-hyper-v.md#prepare-an-azure-user-account)eller [fysiska servrar](./tutorial-discover-physical.md#prepare-an-azure-user-account).
Skapa ett nyckel valv för migrering av VMware-agent | För att migrera virtuella VMware-datorer med agent lös Azure Migrate: Server migrering skapar Azure Migrate en Key Vault för att hantera åtkomst nycklar till replikeringens lagrings konto i din prenumeration. Om du vill skapa valvet ställer du in behörigheter (ägare eller deltagare och administratör för användar åtkomst) på resurs gruppen där projektet finns. | [Konfigurera](./tutorial-discover-vmware.md#prepare-an-azure-user-account) behörigheter.

## <a name="supported-geographies-public-cloud"></a>Geografiska områden som stöds (offentligt moln)

Du kan skapa ett projekt i ett antal geografiska områden i det offentliga molnet.

- Även om du bara kan skapa projekt i dessa geografiska områden kan du utvärdera eller migrera servrar för andra mål platser.
- Projektets geografi används bara för att lagra identifierade metadata.
- När du skapar ett projekt väljer du ett geografiskt område. Projektet och relaterade resurser skapas i en av regionerna i geografin. Regionen tilldelas av Azure Migrates tjänsten.

**Geografi** | **Lagrings plats för metadata**
--- | ---
Asien och stillahavsområdet | Asien, östra eller Sydostasien
Australien | Östra Australien eller Australien, sydöstra
Brasilien | Brasilien, södra
Kanada | Kanada, centrala eller Kanada, öst
Europa | Europa, norra eller Europa, västra
Frankrike | Frankrike, centrala
Indien | Centrala Indien eller södra Indien
Japan |  Japan, östra eller Japan, väst
Korea | Korea, centrala eller Korea, södra
Schweiz | Schweiz, norra
Storbritannien | Storbritannien, södra eller Storbritannien, västra
USA | USA, centrala eller västra USA 2

> [!NOTE]
> För Schweiz geografi är Schweiz, västra endast tillgängligt för REST API användare och behöver en godkänd prenumeration.

## <a name="supported-geographies-azure-government"></a>Geografiska områden som stöds (Azure Government)

**Uppgift** | **Geografi** | **Information**
--- | --- | ---
Skapa projekt | USA | Metadata lagras i US Gov, Arizona US Gov, Virginia
Mål utvärdering | USA | Mål regioner: US Gov, Arizona, US Gov, Virginia, US Gov, Texas
Rikta replikering | USA | Mål regioner: US DoD, centrala, US DoD, östra, US Gov, Arizona, US Gov, Iowa, US Gov, Texas, US Gov, Virginia


## <a name="vmware-assessment-and-migration"></a>VMware-bedömning och migrering

[Granska](migrate-support-matrix-vmware.md) Azure Migrate: Server utvärdering och Azure Migrate: stöd för Server migration för virtuella VMware-datorer.

## <a name="hyper-v-assessment-and-migration"></a>Utvärdering och migrering av Hyper-V

[Granska](migrate-support-matrix-hyper-v.md) Azure Migrate: Server utvärdering och Azure Migrate: stöd för Server migration för virtuella Hyper-V-datorer.



## <a name="azure-migrate-versions"></a>Azure Migrate versioner

Det finns två versioner av tjänsten Azure Migrate:

- **Aktuell version**: om du använder den här versionen kan du skapa nya projekt, identifiera lokala utvärderingar och dirigera utvärderingar och migreringar. [Läs mer](whats-new.md).
- **Tidigare version**: för kund som använder den tidigare versionen av Azure Migrate (endast utvärdering av lokala virtuella VMware-datorer stöddes) bör du nu använda den aktuella versionen. I den tidigare versionen kan du inte skapa nya projekt eller utföra nya identifieringar.

## <a name="next-steps"></a>Nästa steg

- [Utvärdera virtuella VMware-datorer](./tutorial-assess-vmware-azure-vm.md) för migrering.
- [Utvärdera virtuella Hyper-V-datorer](tutorial-assess-hyper-v.md) för migrering.
