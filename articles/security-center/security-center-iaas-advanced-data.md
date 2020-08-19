---
title: Azure Security Center avancerad data säkerhet för SQL-datorer (för hands version)
description: Lär dig hur du aktiverar avancerad data säkerhet för SQL-datorer i Azure Security Center
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: ba46c460-6ba7-48b2-a6a7-ec802dd4eec2
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: e0085ef5213853a1577ec039d5e360114aa7c64e
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88566275"
---
# <a name="advanced-data-security-for-sql-machines-preview"></a>Avancerad data säkerhet för SQL-datorer (för hands version)

Azure Security Center avancerad data säkerhet för SQL-datorer skyddar SQL-servrar som finns i Azure, i andra moln miljöer och till och med lokala datorer. Detta utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullt stödja hybrid miljöer.

Den här förhands gransknings funktionen innehåller funktioner för att identifiera och åtgärda potentiella databas sårbarheter och identifiera avvikande aktiviteter som kan tyda på hot mot databasen: 

* **Sårbarhets bedömning** – genomsöknings tjänsten för att identifiera, spåra och hjälpa dig att åtgärda potentiella databas sårbarheter. Utvärderings genomsökningar ger en översikt över säkerhets läget för SQL-datorer och information om eventuella säkerhets brister.

* [Avancerat skydd](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection-overview) – identifierings tjänsten som kontinuerligt övervakar dina SQL-servrar för hot som SQL-inmatning, brute-force-attacker och behörighets missbruk. Den här tjänsten innehåller åtgärds lösa säkerhets aviseringar i Azure Security Center med information om den misstänkta aktiviteten, vägledning om hur du minimerar hoten och alternativ för att fortsätta med din undersökning med Azure Sentinel.

>[!TIP]
> Avancerad data säkerhet för SQL-datorer är ett tillägg till Azure Security Center [avancerade data säkerhets paket](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security), tillgängliga för Azure SQL Database, Azure-SYNAPSE och SQL-hanterad instans.


## <a name="set-up-advanced-data-security-for-sql-machines"></a>Konfigurera avancerad data säkerhet för SQL-datorer 

Att ställa in Azure Security Center avancerade data säkerhet för SQL-datorer innefattar två steg:

* Etablera Log Analytics-agenten på SQL Server-värden. Detta ger anslutningen till Azure.

* Aktivera det valfria paketet på Security Center pris-och inställnings sida.

Båda beskrivs nedan.

### <a name="step-1-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Steg 1. Etablera Log Analytics-agenten på SQL Server-värden:

- **SQL Server på Azure VM** – om din SQL-dator finns på en virtuell Azure-dator kan du [etablera Log Analytics-agenten automatiskt](security-center-enable-data-collection.md#workspace-configuration). Alternativt kan du följa den manuella proceduren för [att lägga till en virtuell Azure-dator](quick-onboard-azure-stack.md#add-the-virtual-machine-extension-to-your-existing-azure-stack-virtual-machines).

- **SQL Server på Azure-bågen** – om SQL Server finns på en [Azure Arc](https://docs.microsoft.com/azure/azure-arc/) -dator kan du distribuera Log Analytics agenten med hjälp av Security Center rekommendationen Log Analytics Agent installeras på dina Windows-baserade Azure Arc-datorer (för hands version) ". Du kan också följa den manuella proceduren i [Azure Arc-dokumentationen](https://docs.microsoft.com/azure/azure-arc/servers/manage-vm-extensions#enable-extensions-from-the-portal).

- **SQL Server lokal** – om din SQL Server finns på en lokal Windows-dator utan Azure ARC har du två alternativ för att ansluta den till Azure:
    
    - **Distribuera Azure-båge** – du kan ansluta en Windows-dator till Security Center. Azure-bågen ger dock djupare integrering i *hela* Azure-miljön. Om du konfigurerar Azure Arc visas sidan **SQL Server – Azure-båge** i portalen. dina säkerhets aviseringar visas på en särskild **säkerhets** flik på sidan. Det första och rekommenderade alternativet är att [Konfigurera Azure-bågen på värden](https://docs.microsoft.com/azure/azure-arc/servers/onboard-portal#install-and-validate-the-agent-on-windows) och följa anvisningarna för **SQL Server på Azure-bågen**ovan.
        
    - **Anslut Windows-datorn utan Azure-båge** – om du väljer att ansluta en SQL Server som körs på en Windows-dator utan att använda Azure-bågen följer du anvisningarna i [Anslut Windows-datorer till Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/agent-windows).


### <a name="step-2-enable-the-optional-bundle-in-security-centers-pricing-and-settings-page"></a>Steg 2. Aktivera det valfria paketet på Security Center pris-och inställnings sida:

1. Från Security Centerens marginal List öppnar du sidan **Inställningar för pris &** .

    - Om du använder **Azure Security Center standard arbets ytan** (med namnet "defaultworkspace-[ditt prenumerations-ID]-[region]") väljer du den relevanta **prenumerationen**.

    - Om du använder **en arbets yta som inte är standard**väljer du relevant **arbets yta** (ange arbets ytans namn i filtret om det behövs):

        ![rubrik](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)


1. Växla alternativet för **SQL-servrar på datorer (för hands version)** till aktive rad. 

    [![Sidan Security Center prissättning med valfria paket](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png)](media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-large.png#lightbox)

    Avancerad data säkerhet för SQL-servrar på datorer kommer att aktive ras på alla SQL-servrar som är anslutna till den valda arbets ytan. Skyddet aktive ras fullständigt efter den första omstarten av SQL Server-instansen.

    >[!TIP] 
    > Skapa en ny arbets yta genom att följa anvisningarna i [skapa en Log Analytics arbets yta](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace).


1. Du kan också konfigurera e-postavisering om säkerhets aviseringar. 
    Du kan ange en lista över mottagare som ska få ett e-postmeddelande när Security Center aviseringar genereras. E-postmeddelandet innehåller en direkt länk till aviseringen i Azure Security Center med all relevant information. Mer information finns i [Konfigurera e-postaviseringar för säkerhets aviseringar](https://docs.microsoft.com/azure/security-center/security-center-provide-security-contact-details).



## <a name="explore-vulnerability-assessment-reports"></a>Utforska rapporter om sårbarhets bedömning

Tjänsten sårbarhets bedömning söker igenom dina databaser en gång i veckan. Genomsökningarna körs samma dag i veckan då du aktiverade tjänsten.

Instrument panelen för sårbarhets bedömning ger en översikt över dina utvärderings resultat över alla dina databaser, tillsammans med en sammanfattning av felfria och felaktiga databaser, och en översikt över misslyckade kontroller efter risk distribution.

Du kan visa resultaten för sårbarhets bedömning direkt från Security Center.

1. Från Security Centerens marginal List öppnar du sidan **rekommendationer** och väljer **säkerhets risker på dina SQL-servrar på datorer som ska åtgärdas (för hands version)**. Mer information finns i [Security Center rekommendationer](security-center-recommendations.md). 


    [![* * Säkerhets risker på dina SQL-servrar på datorer bör åtgärdas (för hands version) * * rekommendation](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png)](media/security-center-advanced-iaas-data/data-and-storage-sqldb-vulns-on-vm.png#lightbox)

    Den detaljerade vyn för den här rekommendationen visas.

    [![Detaljerad vy för * *-sårbarheter på dina SQL-servrar på datorer bör åtgärdas (för hands version) * * rekommendation](media/security-center-advanced-iaas-data/all-servers-view.png)](media/security-center-advanced-iaas-data/all-servers-view.png#lightbox)

1. Mer information finns i öka detalj nivån:

    * Om du vill ha en översikt över scannade resurser (databaser) och en lista över säkerhets kontroller som har testats väljer du Server av intresse.

    * En översikt över sårbarheter grupperade efter en speciell SQL-databas får du genom att välja databasens intresse.

    I varje vy sorteras säkerhets kontrollerna efter **allvarlighets grad**. Klicka på en säkerhets kontroll för att visa ett informations fönster med en **Beskrivning**, hur du **åtgärdar** den och annan relaterad information som **påverkan** eller **benchmark**.

## <a name="advanced-threat-protection-for-sql-servers-on-machines-alerts"></a>Avancerade hot skydd för SQL-servrar på dator aviseringar
Aviseringar genereras av ovanliga och potentiellt skadliga försök att komma åt eller utnyttja SQL-datorer. Dessa händelser kan utlösa aviseringar som visas i [avsnittet aviseringar för SQL Database och SQL Data Warehouse på referens sidan aviseringar](alerts-reference.md#alerts-sql-db-and-warehouse).



## <a name="explore-and-investigate-security-alerts"></a>Utforska och undersök säkerhets aviseringar

Säkerhets aviseringar är tillgängliga på Security Center sidan aviseringar, på resursens säkerhets flik eller via den direkta länken i e-postaviseringarna.

1. Om du vill visa aviseringar väljer du **säkerhets aviseringar** från Security Centers marginal list och väljer en avisering.

1. Aviseringar är utformade för att vara fristående, med detaljerade åtgärds steg och information om undersökningar i var och en. Du kan undersöka ytterligare med hjälp av andra Azure Security Center-och Azure Sentinel-funktioner för en bredare vy:

    * Aktivera SQL Server gransknings funktion för ytterligare undersökningar. Om du är en Azure Sentinel-användare kan du överföra SQL-gransknings loggarna från Windows säkerhets logg händelser till kontroll och få en omfattande gransknings upplevelse. [Läs mer om SQL Server granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?view=sql-server-ver15).
    * Använd Security Center rekommendationer för värddatorn som anges i varje avisering för att förbättra din säkerhets position. Detta minskar risken för framtida attacker. 

    [Lär dig mer om att hantera och svara på aviseringar](https://docs.microsoft.com/azure/security-center/security-center-managing-and-responding-alerts).


## <a name="next-steps"></a>Nästa steg

Information om relaterade material finns i följande artikel:

- [Säkerhets aviseringar för SQL Database och SQL Data Warehouse](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Konfigurera e-postaviseringar för säkerhets aviseringar](security-center-provide-security-contact-details.md)
- [Läs mer om Azure Sentinel](https://docs.microsoft.com/azure/sentinel/)
- [Azure Security Centers avancerade data säkerhets paket](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)