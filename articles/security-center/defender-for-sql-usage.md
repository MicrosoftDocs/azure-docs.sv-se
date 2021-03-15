---
title: Så här konfigurerar du Azure Defender för SQL
description: Lär dig hur du aktiverar Azure Security Center s valfria Azure Defender för SQL-plan
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2021
ms.author: memildin
ms.openlocfilehash: a91329d3bd0247932614233ef5b1ec71bf4d2a6b
ms.sourcegitcommit: 33ac5cd254c33659f668a76a2e295fddcd5d194d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103465471"
---
# <a name="enable-azure-defender-for-sql-servers-on-machines"></a>Aktivera Azure Defender för SQL-servrar på datorer 

Den här Azure Defender-planen identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja databaser.

Du ser aviseringar när det finns misstänkta databas aktiviteter, potentiella sårbarheter eller SQL-injektering, samt avvikande databas åtkomst och fråga mönster.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|**Azure Defender för SQL-servrar på datorer** faktureras på det sätt som visas på [Security Center priser](https://azure.microsoft.com/pricing/details/security-center/)|
|Skyddade SQL-versioner:|Azure SQL Server (alla versioner som omfattas av Microsofts support)|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) US Gov<br>![Inga](./media/icons/no-icon.png) Kina gov, andra gov|
|||

## <a name="set-up-azure-defender-for-sql-servers-on-machines"></a>Konfigurera Azure Defender för SQL-servrar på datorer

Så här aktiverar du den här planen:

[Steg 1. Etablera Log Analytics-agenten på SQL Server-värden:](#step-1-provision-the-log-analytics-agent-on-your-sql-servers-host)

[Steg 2. Aktivera den valfria planen på Security Center pris-och inställnings sida:](#step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page)


### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Steg 1. Etablera Log Analytics-agenten på SQL Server-värden:

- **SQL Server på Azure VM** – om din SQL-dator finns på en virtuell Azure-dator kan du [Aktivera automatisk etablering av Log Analytics agenten <a name="auto-provision-mma"></a>](security-center-enable-data-collection.md#auto-provision-mma). Alternativt kan du följa den manuella proceduren för att [publicera dina virtuella Azure Stack hubb-datorer](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms).
- **SQL Server på Azure-bågen** – om SQL Server hanteras av [Azure Arc](../azure-arc/index.yml) -aktiverade servrar kan du distribuera Log Analytics agenten med hjälp av Security Center rekommendationen Log Analytics Agent installeras på dina Windows-baserade Azure Arc-datorer (för hands version) ". Du kan också följa installations metoderna som beskrivs i [Azure Arc-dokumentationen](../azure-arc/servers/manage-vm-extensions.md).

- **SQL Server lokal** – om din SQL Server finns på en lokal Windows-dator utan Azure ARC har du två alternativ för att ansluta den till Azure:
    
    - **Distribuera Azure-båge** – du kan ansluta en Windows-dator till Security Center. Azure-bågen ger dock djupare integrering i *hela* Azure-miljön. Om du konfigurerar Azure Arc visas sidan **SQL Server – Azure-båge** i portalen. dina säkerhets aviseringar visas på en särskild **säkerhets** flik på sidan. Det första och rekommenderade alternativet är att [Konfigurera Azure-bågen på värden](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) och följa anvisningarna för **SQL Server på Azure-bågen** ovan.
        
    - **Anslut Windows-datorn utan Azure-båge** – om du väljer att ansluta en SQL Server som körs på en Windows-dator utan att använda Azure-bågen följer du anvisningarna i [Anslut Windows-datorer till Azure Monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-2-enable-the-optional-plan-in-security-centers-pricing-and-settings-page"></a>Steg 2. Aktivera den valfria planen på Security Center pris-och inställnings sida:

1. På Security Center menyn öppnar du sidan **Inställningar för pris &** .

    - Om du använder **Azure Security Center standard arbets ytan** (med namnet "defaultworkspace-[ditt PRENUMERATIONS-ID]-[region]") väljer du den relevanta **prenumerationen**.

    - Om du använder **en arbets yta som inte är standard** väljer du relevant **arbets yta** (ange arbets ytans namn i filtret om det behövs):

        ![Hitta din arbets yta som inte är standard enligt title](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Ställ in alternativet för **Azure Defender för SQL-servrar på datorer** som planerar **på**. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Sidan Security Center prissättning med valfria planer":::

    Planen kommer att aktive ras på alla SQL-servrar som är anslutna till den valda arbets ytan. Skyddet aktive ras fullständigt efter den första omstarten av SQL Server-instansen.

    >[!TIP] 
    > Skapa en ny arbets yta genom att följa anvisningarna i [skapa en Log Analytics arbets yta](../azure-monitor/logs/quick-create-workspace.md).


1. Du kan också konfigurera e-postavisering om säkerhets aviseringar. 
    Du kan ange en lista över mottagare som ska få ett e-postmeddelande när Security Center aviseringar genereras. E-postmeddelandet innehåller en direkt länk till aviseringen i Azure Security Center med all relevant information. Mer information finns i [Konfigurera e-postaviseringar för säkerhets aviseringar](security-center-provide-security-contact-details.md).


## <a name="azure-defender-for-sql-alerts"></a>Azure Defender för SQL-aviseringar
Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja SQL-datorer. Dessa händelser kan utlösa aviseringar som visas på [referens sidan aviseringar](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersök säkerhets aviseringar

Azure Defender för SQL-aviseringar finns på sidan aviseringar på Security Center, på fliken säkerhet i [Azure Defender-instrumentpanelen](azure-defender-dashboard.md)eller via den direkta länken i e-postaviseringarna.

1. Om du vill visa aviseringar väljer du **säkerhets aviseringar** på Security Center menyn och väljer en avisering.

1. Aviseringar är utformade för att vara fristående, med detaljerade åtgärds steg och information om undersökningar i var och en. Du kan undersöka ytterligare med hjälp av andra Azure Security Center-och Azure Sentinel-funktioner för en bredare vy:

    * Aktivera SQL Server gransknings funktion för ytterligare undersökningar. Om du är en Azure Sentinel-användare kan du överföra SQL-gransknings loggarna från Windows säkerhets logg händelser till kontroll och få en omfattande gransknings upplevelse. [Läs mer om SQL Server granskning](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Använd Security Center rekommendationer för värddatorn som anges i varje avisering för att förbättra din säkerhets position. Detta minskar risken för framtida attacker. 

    [Lär dig mer om att hantera och svara på aviseringar](security-center-managing-and-responding-alerts.md).


## <a name="next-steps"></a>Nästa steg

Information om relaterade material finns i följande artikel:

- [Säkerhets aviseringar för SQL Database och Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Konfigurera e-postavisering för säkerhetsmeddelanden](security-center-provide-security-contact-details.md)
- [Läs mer om Azure Sentinel](../sentinel/index.yml)