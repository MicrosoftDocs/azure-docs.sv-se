---
title: Programarkitektur för Azure Migrate
description: Innehåller en översikt över Azure Migrates utrustning som används vid Server identifiering, utvärdering och migrering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: f3a94576ef58eabf9d747c6e6c3a6372569d4cf1
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785248"
---
# <a name="azure-migrate-appliance-architecture"></a>Programarkitektur för Azure Migrate

I den här artikeln beskrivs arkitekturen och processerna för Azure Migrate utrustning. Azure Migrate-installationen är en förenklad enhet som distribueras lokalt, för att identifiera servrar för migrering till Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Azure Migrates apparaten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för att**
--- | --- | ---
**Identifiering och utvärdering av servrar som körs i VMware-miljön** | Azure Migrate: identifiering och utvärdering | Identifiera servrar som körs i VMware-miljön<br/><br/> Utför identifiering av installerad program varu inventering, agent beroende analys och identifiering SQL Server instanser och databaser.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.
**Migrering utan agent för servrar som körs i VMware-miljön** | Azure Migrate: Server-migrering | Identifiera servrar som körs i VMware-miljön.<br/><br/> Replikera servrar utan att installera några agenter på dem.
**Identifiering och utvärdering av servrar som körs i Hyper-V-miljön** | Azure Migrate: identifiering och utvärdering | Identifiera servrar som körs i din Hyper-V-miljö.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.
**Identifiering och utvärdering av fysiska eller virtualiserade servrar lokalt** |  Azure Migrate: identifiering och utvärdering |  Identifiera fysiska eller virtualiserade servrar lokalt.<br/><br/> Samla in Server konfiguration och prestanda-metadata för utvärderingar.

## <a name="deployment-methods"></a>Distributions metoder

Installationen kan distribueras med ett par olika metoder:

- Installationen kan distribueras med hjälp av en mall för servrar som kör i VMware-eller Hyper-V-miljön ([ägg-mall för VMware](how-to-set-up-appliance-vmware.md) eller [VHD för Hyper-v](how-to-set-up-appliance-hyper-v.md)).
- Om du inte vill använda en mall kan du distribuera-installationen för VMware-eller Hyper-V-miljön med hjälp av ett [PowerShell-skript](deploy-appliance-script.md).
- I Azure Government bör du distribuera installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](deploy-appliance-script-government.md).
- För fysiska eller virtualiserade servrar lokalt eller i andra moln distribuerar du alltid installationen med hjälp av ett PowerShell-skript. Se distributions stegen [här](how-to-set-up-appliance-physical.md).
- Nedladdnings länkarna är tillgängliga i tabellerna nedan.

## <a name="appliance-services"></a>Apparat tjänster

Enheten har följande tjänster:

- Installation av **Configuration Manager**: det här är ett webb program som kan konfigureras med käll information för att starta identifieringen och utvärderingen av servrar. 
- **Identifierings agent**: agenten samlar in metadata för Server konfigurationen som kan användas för att skapa som lokala utvärderingar.
- **Bedömnings agent**: agenten samlar in metadata för Server prestanda som kan användas för att skapa prestandabaserade utvärderingar.
- **Tjänsten automatisk uppdatering**: tjänsten håller alla agenter som körs på enheten uppdaterade. Den körs automatiskt en gång var 24: e timme.
- **Dra-agent**: dirigerar Server replikering och koordinerar kommunikationen mellan replikerade servrar och Azure. Används endast när du replikerar servrar till Azure med hjälp av en agent lös migrering.
- **Gateway**: skickar replikerade data till Azure. Används endast när du replikerar servrar till Azure med hjälp av en agent lös migrering.
- **SQL-identifiering och utvärderings agent**: skickar metadata för konfiguration och prestanda för SQL Server instanser och databaser till Azure.

> [!Note]
> De senaste 3 tjänsterna är bara tillgängliga i den installation som används för identifiering och utvärdering av servrar som körs i VMware-miljön.<br/> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i för hands version. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

## <a name="discovery-and-collection-process"></a>Process för identifiering och insamling

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Utrustnings arkitektur":::

Enheten kommunicerar med identifierings källorna med hjälp av följande process.

**Process** | **VMware-apparat** | **Hyper-V-apparat** | **Fysisk installation**
---|---|---|---
**Starta identifiering** | Enheten kommunicerar med vCenter-servern på TCP-port 443 som standard. Om vCenter-servern lyssnar på en annan port kan du konfigurera den i Konfigurations hanteraren för installationen. | Enheten kommunicerar med Hyper-V-värdarna på WinRM-port 5985 (HTTP). | Enheten kommunicerar med Windows-servrar via WinRM-port 5985 (HTTP) med Linux-servrar via port 22 (TCP).
**Samla in konfigurations-och prestanda-metadata** | Installations programmet samlar in metadata för servrar som kör vCenter Server med vSphere-API: er genom att ansluta på port 443 (standard port) eller andra port vCenter Server lyssnar på. | Enheten samlar in metadata för servrar som körs på Hyper-V-värdar med hjälp av en Common Information Model-session (CIM) med värdar på port 5985.| Enheten samlar in metadata från Windows-servrar med hjälp av Common Information Model (CIM)-session med servrar på port 5985 och från Linux-servrar med SSH-anslutning på port 22.
**Skicka identifierings data** | Enheten skickar insamlade data till Azure Migrate: identifiering och utvärdering och Azure Migrate: Server migrering över SSL-port 443.<br/><br/> Enheten kan ansluta till Azure via Internet eller via ExpressRoute (kräver Microsoft-peering). | Enheten skickar insamlade data till Azure Migrate: identifiering och utvärdering över SSL-port 443.<br/><br/> Enheten kan ansluta till Azure via Internet eller via ExpressRoute (kräver Microsoft-peering).| Enheten skickar insamlade data till Azure Migrate: identifiering och utvärdering över SSL-port 443.<br/><br/> Enheten kan ansluta till Azure via Internet eller via ExpressRoute (kräver Microsoft-peering).
**Data insamlings frekvens** | Konfigurations-metadata samlas in och skickas var 30: e minut. <br/><br/> Prestanda för metadata samlas in var 20: e sekund och sammanställs för att skicka en data punkt till Azure var 10: e minut. <br/><br/> Program varu inventerings data skickas till Azure en gång var 12: e timme. <br/><br/> Data för agent utan agent samlas in var 5: e minut, som sammanställs på enheten och skickas till Azure var 6: e timme. <br/><br/> SQL Server konfigurations data uppdateras en gång var 24: e timme och prestanda data samlas in var 30: e sekund.| Konfigurations-metadata samlas in och skickas var 30: e minut. <br/><br/> Prestanda-metadata samlas in var 30: e sekund och sammanställs för att skicka en data punkt till Azure var 10: e minut.|  Konfigurations-metadata samlas in och skickas var 30: e minut. <br/><br/> Prestanda-metadata samlas in var 5: e minut och sammanställs för att skicka en data punkt till Azure var 10: e minut.
**Utvärdera och migrera** | Du kan skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate: verktyget för identifiering och bedömning.<br/><br/>Dessutom kan du också börja migrera servrar som körs i VMware-miljön med hjälp av Azure Migrate: Migreringsverktyg för Server för att dirigera agent lös Server replikering.| Du kan skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate: verktyget för identifiering och bedömning. | Du kan skapa utvärderingar från de metadata som samlas in av installations programmet med hjälp av Azure Migrate: verktyget för identifiering och bedömning.

## <a name="next-steps"></a>Nästa steg

[Granska](migrate-appliance.md) support matrisen för enheten.