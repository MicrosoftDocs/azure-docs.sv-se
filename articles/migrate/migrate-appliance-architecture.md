---
title: Programarkitektur för Azure Migrate
description: Innehåller en översikt över Azure Migrate som används vid serveridentifiering, utvärdering och migrering.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: 4fc71f3242cc5607acebc68b62c5c0565b8f8e56
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715018"
---
# <a name="azure-migrate-appliance-architecture"></a>Programarkitektur för Azure Migrate

I den här artikeln beskrivs Azure Migrate arkitektur och processer för installationen. Den Azure Migrate är en enkel installation som distribueras lokalt för att identifiera servrar för migrering till Azure.

## <a name="deployment-scenarios"></a>Distributionsscenarier

Den Azure Migrate enheten används i följande scenarier.

**Scenario** | **Verktyg** | **Används för att**
--- | --- | ---
**Identifiering och utvärdering av servrar som körs i VMware-miljön** | Azure Migrate: Identifiering och utvärdering | Identifiera servrar som körs i din VMware-miljö<br/><br/> Utför identifiering av installerad programvaruinventering, agentlös beroendeanalys och identifiera SQL Server instanser och databaser.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.
**Agentlös migrering av servrar som körs i VMware-miljö** | Azure Migrate:Server Migration | Identifiera servrar som körs i din VMware-miljö.<br/><br/> Replikera servrar utan att installera några agenter på dem.
**Identifiering och utvärdering av servrar som körs i Hyper-V-miljö** | Azure Migrate: Identifiering och utvärdering | Identifiera servrar som körs i Hyper-V-miljön.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.
**Identifiering och utvärdering av fysiska eller virtualiserade servrar lokalt** |  Azure Migrate: Identifiering och utvärdering |  Identifiera fysiska eller virtualiserade servrar lokalt.<br/><br/> Samla in serverkonfiguration och prestandametadata för utvärderingar.

## <a name="deployment-methods"></a>Distributionsmetoder

Installationen kan distribueras på ett par olika sätt:

- Installationen kan distribueras med hjälp av en mall för servrar som körs i VMware eller Hyper-V-miljö[(OVA-mall för VMware](how-to-set-up-appliance-vmware.md) eller [VHD för Hyper-V).](how-to-set-up-appliance-hyper-v.md)
- Om du inte vill använda en mall kan du distribuera installationen för VMware- eller Hyper-V-miljön med hjälp av ett [PowerShell-installationsskript.](deploy-appliance-script.md)
- I Azure Government bör du distribuera installationen med hjälp av ett skript för Installationsprogram för PowerShell. Se distributionsstegen [här.](deploy-appliance-script-government.md)
- För fysiska eller virtualiserade servrar lokalt eller i andra moln distribuerar du alltid installationen med hjälp av ett PowerShell-installationsskript. Se distributionsstegen [här.](how-to-set-up-appliance-physical.md)
- Nedladdningslänkar finns i tabellerna nedan.

## <a name="appliance-services"></a>Installationstjänster

Installationen har följande tjänster:

- **Konfigurationshanteraren** för installationen: Det här är en webbapp som kan konfigureras med källinformation för att starta identifieringen och utvärderingen av servrar. 
- **Identifieringsagent:** Agenten samlar in metadata för serverkonfiguration som kan användas för att skapa som lokala utvärderingar.
- **Utvärderingsagent:** Agenten samlar in metadata om serverprestanda som kan användas för att skapa prestandabaserade utvärderingar.
- **Tjänst för automatisk** uppdatering: Tjänsten håller alla agenter som körs på installationen uppdaterade. Den körs automatiskt en gång var 24:e timme.
- **DRA-agent:** Samordnar serverreplikering och kommunikationen mellan replikerade servrar och Azure. Används endast vid replikering av servrar till Azure med agentlös migrering.
- **Gateway:** Skickar replikerade data till Azure. Används endast vid replikering av servrar till Azure med agentlös migrering.
- **SQL-identifierings- och** utvärderingsagent: skickar konfigurations- och prestandametadata för SQL Server instanser och databaser till Azure.

> [!Note]
> De senaste tre tjänsterna är bara tillgängliga i installationen som används för identifiering och utvärdering av servrar som körs i din VMware-miljö.<br/> Identifiering och utvärdering av SQL Server instanser och databaser som körs i din VMware-miljö är nu i förhandsversion. Om du vill testa funktionen använder du [**den här länken**](https://aka.ms/AzureMigrate/SQL) till att skapa ett projekt i regionen **Australien, östra**. Om du redan har ett projekt i Australien, östra och vill prova den här funktionen måste du se till att du har slutfört dessa [**krav**](how-to-discover-sql-existing-project.md) på portalen.

## <a name="discovery-and-collection-process"></a>Identifierings- och insamlingsprocess

:::image type="content" source="./media/migrate-appliance-architecture/architecture1.png" alt-text="Arkitektur för installation":::

Installationen kommunicerar med identifieringskällorna med hjälp av följande process.

**Process** | **VMware-installation** | **Hyper-V-installation** | **Fysisk installation**
---|---|---|---
**Starta identifiering** | Installationen kommunicerar med vCenter-servern på TCP-port 443 som standard. Om vCenter-servern lyssnar på en annan port kan du konfigurera den i konfigurationshanteraren för installationen. | Installationen kommunicerar med Hyper-V-värdarna på WinRM-port 5985 (HTTP). | Installationen kommunicerar med Windows-servrar via WinRM-port 5985 (HTTP) med Linux-servrar via port 22 (TCP).
**Samla in konfigurations- och prestandametadata** | Installationen samlar in metadata för servrar som körs på vCenter Server med vSphere-API:er genom att ansluta på port 443 (standardport) eller någon annan port vCenter Server lyssnar på. | Installationen samlar in metadata för servrar som körs på Hyper-V-värdar med hjälp av en CIM-session (Common Information Model) med värdar på port 5985.| Enheten samlar in metadata från Windows-servrar med hjälp av Common Information Model-session (CIM) med servrar på port 5985 och från Linux-servrar med hjälp av SSH-anslutning på port 22.
**Skicka identifieringsdata** | Installationen skickar insamlade data till Azure Migrate: Identifiering och utvärdering och Azure Migrate: Servermigrering via SSL-port 443.<br/><br/>  Installationen kan ansluta till Azure via Internet eller via privat ExpressRoute-peering eller Microsofts peeringkretsar. | Installationen skickar insamlade data till Azure Migrate: Identifiering och utvärdering via SSL-port 443.<br/><br/> Installationen kan ansluta till Azure via Internet eller via privat ExpressRoute-peering eller Microsofts peeringkretsar. | Installationen skickar insamlade data till Azure Migrate: Identifiering och utvärdering via SSL-port 443.<br/><br/> Installationen kan ansluta till Azure via Internet eller via privat ExpressRoute-peering eller Microsofts peeringkretsar. 
**Datainsamlingsfrekvens** | Konfigurationsmetadata samlas in och skickas var 30:e minut. <br/><br/> Prestandametadata samlas in var 20:e sekund och aggregeras för att skicka en datapunkt till Azure var 10:e minut. <br/><br/> Programvaruinventeringsdata skickas till Azure var 12:e timme. <br/><br/> Agentlösa beroendedata samlas in var 5:e minut, aggregeras för installationen och skickas till Azure var 6:e timme. <br/><br/> De SQL Server konfigurationsdata uppdateras en gång var 24:e timme och prestandadata samlas in var 30:e sekund.| Konfigurationsmetadata samlas in och skickas var 30:e minut. <br/><br/> Prestandametadata samlas in var 30:e sekund och aggregeras för att skicka en datapunkt till Azure var 10:e minut.|  Konfigurationsmetadata samlas in och skickas var 30:e minut. <br/><br/> Prestandametadata samlas in var femte minut och aggregeras för att skicka en datapunkt till Azure var 10:e minut.
**Utvärdera och migrera** | Du kan skapa utvärderingar från de metadata som samlas in av enheten med hjälp Azure Migrate: Identifierings- och utvärderingsverktyg.<br/><br/>Dessutom kan du börja migrera servrar som körs i din VMware-miljö med hjälp av Azure Migrate: Server Migration för att samordna agentlös serverreplikering.| Du kan skapa utvärderingar från de metadata som samlas in av enheten med hjälp Azure Migrate: Identifierings- och utvärderingsverktyg. | Du kan skapa utvärderingar från de metadata som samlas in av enheten med hjälp Azure Migrate: Identifierings- och utvärderingsverktyg.

## <a name="next-steps"></a>Nästa steg

[Granska](migrate-appliance.md) supportmatrisen för installationen.