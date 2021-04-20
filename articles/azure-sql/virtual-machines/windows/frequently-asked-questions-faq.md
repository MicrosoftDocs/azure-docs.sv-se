---
title: SQL Server på Virtuella Windows-datorer Vanliga frågor och svar om Azure | Microsoft Docs
description: Den här artikeln innehåller svar på vanliga frågor om att köra SQL Server virtuella Azure-datorer.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.openlocfilehash: 02bb1f539369cf72a5d5b6503a3584069589b19e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727356"
---
# <a name="frequently-asked-questions-for-sql-server-on-azure-vms"></a>Vanliga frågor och svar om SQL Server virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](frequently-asked-questions-faq.md)
> * [Linux](../linux/frequently-asked-questions-faq.md)

Den här artikeln innehåller svar på några av de vanligaste frågorna [om att köra SQL Server på Windows Azure Virtual Machines (VM).](https://azure.microsoft.com/services/virtual-machines/sql-server/)

[!INCLUDE [support-disclaimer](../../../../includes/support-disclaimer.md)]

## <a name="images"></a><a id="images"></a> Bilder

1. **Vilka SQL Server galleriavbildningar för virtuella datorer är tillgängliga?** 

   Azure underhåller avbildningar av virtuella datorer för alla större versioner av SQL Server på alla utgåvor för både Windows och Linux. Mer information finns i den fullständiga listan över avbildningar av [virtuella Windows-datorer och](sql-server-on-azure-vm-iaas-what-is-overview.md#payasyougo) [Linux VM-avbildningar.](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md#create)

1. **Uppdateras befintliga SQL Server galleriavbildningar för virtuella datorer?**

   Varannan månad uppdateras SQL Server avbildningar i galleriet för virtuella datorer med de senaste uppdateringarna för Windows och Linux. För Windows-avbildningar omfattar detta alla uppdateringar som har markerats som viktiga i Windows Update, inklusive viktiga SQL Server säkerhetsuppdateringar och service pack. För Linux-avbildningar omfattar detta de senaste systemuppdateringarna. SQL Server kumulativa uppdateringar hanteras på olika sätt för Linux och Windows. För Linux SQL Server kumulativa uppdateringar också i uppdateringen. Men just nu uppdateras inte virtuella Windows-datorer med kumulativa SQL Server eller Windows Server.

1. **Kan SQL Server avbildningar av virtuella datorer tas bort från galleriet?**

   Ja. Azure har bara en avbildning per huvudversion och utgåva. När till exempel en ny SQL Server service pack lanseras lägger Azure till en ny avbildning i galleriet för den service pack. Den SQL Server avbildningen för föregående service pack tas omedelbart bort från Azure Portal. Den är dock fortfarande tillgänglig för etablering från PowerShell under de kommande tre månaderna. Efter tre månader är den tidigare service pack-avbildningen inte längre tillgänglig. Den här borttagningsprincipen gäller även om en SQL Server-version inte stöds när den når slutet av dess livscykel.


1. **Går det att distribuera en äldre avbildning SQL Server som inte visas i Azure Portal?**

   Ja, med hjälp av PowerShell. Mer information om hur du distribuerar SQL Server virtuella datorer med PowerShell finns i Så här [etablerar du SQL Server virtuella datorer med Azure PowerShell](create-sql-vm-powershell.md).
   
1. **Är det möjligt att skapa en generaliserad Azure Marketplace SQL Server av min SQL Server VM och använda den för att distribuera virtuella datorer?**

   Ja, men du måste sedan registrera varje SQL Server VM med [SQL IaaS Agent-tillägget](sql-agent-extension-manually-register-single-vm.md) för att hantera din SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatiska säkerhetskopieringar. När du registrerar med tillägget måste du också ange licenstypen för varje SQL Server VM.

1. **Hur gör jag för att du SQL Server virtuella Azure-datorer och använda den för att distribuera nya virtuella datorer?**

   Du kan distribuera en virtuell Windows Server-dator (utan SQL Server installerad) och använda [SQL-sysprep-processen](/sql/database-engine/install-windows/install-sql-server-using-sysprep) för att generalisera SQL Server på virtuella Azure-datorer (Windows) med SQL Server-installationsmediet. Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot%3aprimaryr3) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har någon Software Assurance använda installationsmediet från en Azure Marketplace SQL Server VM bild som har önskad utgåva.

   Du kan också använda en av avbildningarna SQL Server från Azure Marketplace för att generalisera SQL Server på virtuella Azure-datorer. Observera att du måste ta bort följande registernyckel i källavbildningen innan du skapar en egen avbildning. Om du inte gör det kan det leda till att SQL Server bootstrap-mappen och/eller SQL IaaS-tillägget är i felläge.

   Registernyckelsökväg:  
   `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Setup\SysPrepExternal\Specialize`

   > [!NOTE]
   > SQL Server på virtuella Azure-datorer, inklusive de som distribueras från anpassade generaliserade avbildningar, bör registreras med [SQL IaaS Agent-tillägget](./sql-agent-extension-manually-register-single-vm.md?tabs=azure-cli%252cbash) för att uppfylla efterlevnadskraven och för att använda valfria funktioner som automatisk uppdatering och automatiska säkerhetskopieringar. Med tillägget kan du också [ange licenstypen för](./licensing-model-azure-hybrid-benefit-ahb-change.md?tabs=azure-portal) varje SQL Server VM.

1. **Kan jag använda min egen virtuella hårddisk för att distribuera en SQL Server VM?**

   Ja, men du måste sedan registrera varje SQL Server VM med [SQL IaaS Agent-tillägget](sql-agent-extension-manually-register-single-vm.md) för att hantera din SQL Server VM i portalen samt använda funktioner som automatisk uppdatering och automatiska säkerhetskopieringar.

1. **Går det att konfigurera konfigurationer som inte visas i galleriet för virtuella datorer (till exempel Windows 2008 R2 + SQL Server 2012)?**

   Nej. För galleriavbildningar för virtuella datorer SQL Server måste du välja en av de tillhandahållna avbildningarna antingen via Azure Portal eller via [PowerShell](create-sql-vm-powershell.md). Du har dock möjlighet att distribuera en virtuell Windows-dator och självinstallera SQL Server till den. Du måste sedan registrera din SQL Server VM med [SQL IaaS Agent-tillägget](sql-agent-extension-manually-register-single-vm.md) för att hantera din SQL Server VM i Azure Portal samt använda funktioner som automatisk uppdatering och automatiska säkerhetskopieringar. 


## <a name="creation"></a>Skapa

1. **Hur gör jag för att skapa en virtuell Azure-dator med SQL Server?**

   Den enklaste metoden är att skapa en virtuell dator som innehåller SQL Server. En självstudiekurs om hur du registrerar dig för Azure och skapar en SQL Server VM från portalen finns i Etablera en SQL Server virtuell dator [i Azure Portal](create-sql-vm-portal.md). Du kan välja en avbildning av en virtuell dator som använder betala per sekund SQL Server licensiering, eller så kan du använda en avbildning som gör att du kan ta med din SQL Server licens. Du kan också välja att manuellt installera SQL Server på en virtuell dator med antingen en fritt licensierad version (Developer eller Express) eller genom att återanvända en lokal licens. Se till att registrera din SQL Server VM med [SQL IaaS Agent-tillägget](sql-agent-extension-manually-register-single-vm.md) för att hantera din SQL Server VM i portalen, samt använda funktioner som automatisk uppdatering och automatiska säkerhetskopieringar. Om du tar med din egen licens måste du ha [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/). Mer information finns i [Pricing guidance for SQL Server Azure VMs](pricing-guidance.md) (Prisvägledning för virtuella SQL Server Azure-datorer).

1. **Hur migrerar jag min lokala SQL Server till molnet?**

   Skapa först en virtuell Azure-dator med SQL Server instans. Migrera sedan dina lokala databaser till den instansen. Information om datamigreringsstrategier finns [i Migrera en SQL Server databas till SQL Server på en virtuell Azure-dator.](migrate-to-vm-from-sql-server.md)

## <a name="licensing"></a>Licensiering

1. **Hur kan jag installera min licensierade version av SQL Server på en virtuell Azure-dator?**

   Det finns tre sätt att göra detta på. Om du är ENTERPRISE-avtalskund (EA) kan du etablera en av de avbildningar av virtuella datorer som stöder licenser [,](sql-server-on-azure-vm-iaas-what-is-overview.md#BYOL)som även kallas BYOL (bring-your-own-license). Om du [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-default)kan du aktivera [Azure Hybrid-förmån](licensing-model-azure-hybrid-benefit-ahb-change.md) på en befintlig pay-as-you-go-avbildning (PAYG). Ett annat alternativ är att kopiera SQL Server-installationsmedia till en virtuell Windows Server-dator och installera SQL Server på den virtuella datorn. Se till att registrera SQL Server VM med tillägget [för](sql-agent-extension-manually-register-single-vm.md) funktioner som portalhantering, automatisk säkerhetskopiering och automatisk uppdatering. 


1. **Behöver en kund SQL Server klientåtkomstlicenser (CALs) för att ansluta till en SQL Server-avbildning med betala per användning som körs på Azure Virtual Machines?**

   Nej. Kunder behöver cals när de använder bring-your-own-license och flyttar sina SQL Server SA-server/CAL VM till virtuella Azure-datorer. 

1. **Kan jag ändra en virtuell till att använda min egen SQL Server-licens om den skapades från en av galleriavbildningarna med betala per användning?**

   Ja. Du kan enkelt byta en PAYG-galleriavbildning (betala per du-go) till BYOL (Bring Your Own License) genom att [aktivera Azure Hybrid-förmån](https://azure.microsoft.com/pricing/hybrid-benefit/faq/).  Mer information finns i [Så här ändrar du licensieringsmodellen för en SQL Server VM](licensing-model-azure-hybrid-benefit-ahb-change.md). För närvarande är den här anläggningen endast tillgänglig för offentliga och Azure Government molnkunder.


1. **Kräver byte av licensieringsmodeller någon avbrottstid för SQL Server?**

   Nej. [Om du ändrar licensieringsmodellen](licensing-model-azure-hybrid-benefit-ahb-change.md) krävs ingen avbrottstid för SQL Server eftersom ändringen börjar gälla omedelbart och inte kräver någon omstart av den virtuella datorn. Men för att registrera din SQL Server VM med SQL IaaS Agent-tillägget är [SQL IaaS-tillägget](sql-server-iaas-agent-extension-automate-management.md) ett krav  och installationen av SQL IaaS-tillägget i fullständigt läge startar om SQL Server tjänsten. Om SQL IaaS-tillägget måste installeras kan du  antingen installera det i förenklat läge för begränsade funktioner eller installera det i _fullständigt_ läge under en underhållsfönstret. SQL IaaS-tillägget  som är installerat i  förenklat läge kan uppgraderas till fullständigt läge när som helst, men kräver en omstart av SQL Server tjänsten. 
   
1. **Är det möjligt att byta licensieringsmodeller på en SQL Server VM distribueras med hjälp av den klassiska modellen?**

   Nej. Det går inte att ändra licensieringsmodeller på en klassisk virtuell dator. Du kan migrera den virtuella datorn till Azure Resource Manager och registrera dig med SQL IaaS Agent-tillägget. När den virtuella datorn har registrerats med SQL IaaS Agent-tillägget blir licensmodelländringar tillgängliga på den virtuella datorn.

1. **Kan jag använda Azure Portal för att hantera flera instanser på samma virtuella dator?**

   Nej. Portalhantering är en funktion som tillhandahålls av SQL IaaS Agent-tillägget, som förlitar sig på SQL Server IaaS Agent-tillägget. Därför gäller samma begränsningar för tillägget som för tillägget. Portalen kan antingen bara hantera en standardinstans, eller en namngiven instans, så länge den har konfigurerats korrekt. Mer information om dessa begränsningar finns i SQL Server [IaaS-agenttillägget](sql-server-iaas-agent-extension-automate-management.md). 

1. **Kan prenumerationer hos molnlösningsleverantörer aktivera Azure Hybrid-förmånen?**

   Ja, Azure Hybrid-förmånen är tillgänglig för prenumerationer hos molnlösningsleverantörer. CSP-kunder bör först distribuera en betala per användning-avbildning och sedan ändra licensieringsmodellen till bring-your-own-license. [](licensing-model-azure-hybrid-benefit-ahb-change.md)
   
 
1. **Måste jag betala för att licensiera SQL Server på en virtuell Azure-dator om den endast används för vänteläge/redundans?**

   Om du vill ha en kostnadsfri passiv licens för en sekundär tillgänglighetsgrupp i vänteläge eller redundansklusterinstans måste du uppfylla alla följande kriterier enligt beskrivningen i [produktlicensvillkoren:](https://www.microsoft.com/licensing/product-licensing/products)

   1. Du har [licensmobilitet](https://www.microsoft.com/licensing/licensing-programs/software-assurance-license-mobility?activetab=software-assurance-license-mobility-pivot:primaryr2) via [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3). 
   1. Den passiva SQL Server-instansen hanterar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Den används endast för att synkronisera med den primära servern och på annat sätt upprätthålla den passiva databasen i ett varmt vänteläge. Om den betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server-arbetsbelastningar eller utför något annat arbete än vad som anges i produktvillkoren, måste det vara en betald licensierad SQL Server instans. Följande aktivitet tillåts på den sekundära instansen: konsekvenskontroll av databas eller CheckDB, fullständiga säkerhetskopior, säkerhetskopior av transaktionslogg och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande haveriberedskapsinstansen samtidigt under korta perioder av haveriberedskap var 90:e dag. 
   1. Den aktiva SQL Server-licensen omfattas av Software Assurance och tillåter  endast en passiv sekundär SQL Server-instans med upp till samma mängd beräkning som den licensierade aktiva servern. 
   1. Den sekundära SQL Server VM använder [haveriberedskapslicensen](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) i Azure Portal.
   
1. **Vad betraktas som en passiv instans?**

   Den passiva SQL Server-instansen hanterar inte SQL Server-data till klienter eller kör aktiva SQL Server-arbetsbelastningar. Den används endast för att synkronisera med den primära servern och på annat sätt upprätthålla den passiva databasen i ett varmt vänteläge. Om den betjänar data, till exempel rapporter till klienter som kör aktiva SQL Server-arbetsbelastningar, eller utför något annat arbete än vad som anges i produktvillkoren, måste det vara en betald licensierad SQL Server-instans. Följande aktivitet tillåts på den sekundära instansen: konsekvenskontroll av databas eller CheckDB, fullständiga säkerhetskopior, säkerhetskopior av transaktionslogg och övervakning av resursanvändningsdata. Du kan också köra den primära och motsvarande haveriberedskapsinstansen samtidigt under korta perioder av haveriberedskap var 90:e dag.
   

1. **Vilka scenarier kan utnyttja haveriberedskapsförmånen (DR)?**

   [Licensieringsguiden](https://aka.ms/sql2019licenseguide) innehåller scenarier där haveriberedskapsförmånen kan användas. Mer information hittar du i produktvillkoren och du kan även prata med dina licenskontakter eller kontoansvarig.

1. **Vilka prenumerationer har stöd för haveriberedskapsförmånen (DR)?**

   Omfattande program som erbjuder Software Assurance-likvärdiga prenumerationsrättigheter som en fast förmån har stöd för DR-förmånen. Det här omfattar, men är inte begränsat till, Open Value (OV), Open Value Subscription (OVS), Enterprise Agreement (EA), Enterprise Agreement Subscription (EAS) och Server and Cloud Enrollment (SCE). Se produktvillkoren [och prata](https://www.microsoft.com/licensing/product-licensing/products) med dina licenskontakter eller kontoansvarig för mer information. 

   
 ## <a name="extension"></a>Anknytning

1. **Medför registreringen av min virtuella dator med det nya SQL IaaS Agent-tillägget ytterligare kostnader?**

   Nej. SQL IaaS Agent-tillägget möjliggör bara ytterligare hanterbarhet för SQL Server på virtuella Azure-datorer utan extra avgifter. 

1. **Är SQL IaaS Agent-tillägget tillgängligt för alla kunder?**
 
   Ja, så länge SQL Server VM distribuerades i det offentliga molnet med hjälp av Resource Manager och inte den klassiska modellen. Alla andra kunder kan registrera sig med det nya SQL IaaS Agent-tillägget. Dock kan endast kunder med Software Assurance-förmånen använda sin egen licens genom [att aktivera Azure Hybrid-förmån (AHB)](https://azure.microsoft.com/pricing/hybrid-benefit/) på en SQL Server VM. [](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?activetab=software-assurance-default-pivot%3aprimaryr3) 

1. **Vad händer med tillägget _(Microsoft.SqlVirtualMachine)_ om VM-resursen flyttas eller tas bort?** 

   När resursen Microsoft.Compute/VirtualMachine tas bort eller flyttas meddelas den associerade Microsoft.SqlVirtualMachine-resursen om att replikera åtgärden asynkront.

1. **Vad händer med den virtuella datorn om tillägget (_Microsoft.SqlVirtualMachine_) tas bort?**

    Resursen Microsoft.Compute/VirtualMachine påverkas inte när resursen Microsoft.SqlVirtualMachine tas bort. Licensändringarna kommer dock som standard tillbaka till den ursprungliga avbildningskällan. 

1. **Är det möjligt att registrera självdi distribuerade SQL Server virtuella datorer med SQL IaaS Agent-tillägget?**

    Ja. Om du har distribuerat SQL Server från dina egna media och installerat SQL IaaS-tillägget kan du registrera din SQL Server VM med tillägget för att få hanterbarhetsfördelarna som tillhandahålls av SQL IaaS-tillägget.    


## <a name="administration"></a>Administration

1. **Kan jag installera en andra instans av SQL Server på samma virtuella dator? Kan jag ändra installerade funktioner i standardinstansen?**

   Ja. Installationsmediet SQL Server finns i en mapp på **C-enheten.** Kör **Setup.exe** plats för att lägga till nya SQL Server instanser eller ändra andra installerade funktioner i SQL Server på datorn. Observera att vissa funktioner, till exempel automatisk säkerhetskopiering, automatisk uppdatering och Azure Key Vault-integrering, endast fungerar mot standardinstansen eller en namngiven instans som har konfigurerats korrekt (se fråga 3). Kunder som [använder Software Assurance](licensing-model-azure-hybrid-benefit-ahb-change.md) via Azure Hybrid-förmån eller  licensieringsmodellen med betala per användning kan installera flera instanser av SQL Server på den virtuella datorn utan att medföra extra licenskostnader. Ytterligare SQL Server instanser kan belasta systemresurser om de inte konfigureras korrekt. 

1. **Vad är det maximala antalet instanser på en virtuell dator?**
   SQL Server 2012 till SQL Server 2019 har [stöd för 50](/sql/sql-server/editions-and-components-of-sql-server-version-15#RDBMSSP) instanser på en fristående server. Det här är samma gräns oavsett i Azure lokalt. Se [metodtips för](performance-guidelines-best-practices.md#multiple-instances) att lära dig hur du förbereder din miljö på ett bättre sätt. 

1. **Kan jag avinstallera standardinstansen av SQL Server?**

   Ja, men det finns vissa överväganden. Först SQL Server faktureringen fortsätta att ske beroende på licensmodellen för den virtuella datorn. För det andra, som anges i föregående svar, finns det funktioner som förlitar sig [på SQL Server IaaS-agenttillägget](sql-server-iaas-agent-extension-automate-management.md). Om du avinstallerar standardinstansen utan att även ta bort IaaS-tillägget fortsätter tillägget att leta efter standardinstansen och kan generera fel i händelseloggen. De här felen kommer från följande två **källor: Microsoft SQL Server hantering** av autentiseringsuppgifter **och Microsoft SQL Server IaaS-agenten**. Ett av felen kan vara något av liknar följande:

      Ett nätverksrelaterat eller instansspecifikt fel uppstod när en anslutning upprättades till SQL Server. Servern hittades inte eller var inte tillgänglig.

   Om du väljer att avinstallera standardinstansen avinstallerar du även [SQL Server IaaS-agenttillägget.](sql-server-iaas-agent-extension-automate-management.md) 

1. **Kan jag använda en namngiven instans av SQL Server med IaaS-tillägget?**
   
   Ja, om den namngivna instansen är den enda instansen på SQL Server och om den ursprungliga standardinstansen [avinstallerades korrekt.](sql-server-iaas-agent-extension-automate-management.md#named-instance-support) Om det inte finns någon standardinstans och det finns flera namngivna instanser på en enda SQL Server VM, kommer SQL Server IaaS-agenttillägget inte att installeras.  

1. **Kan jag ta SQL Server och associerad licensfakturering från en SQL Server VM?**

   Ja, men du måste vidta ytterligare åtgärder för att undvika att debiteras för din SQL Server instans enligt beskrivningen i [prisvägledningen](pricing-guidance.md). Om du vill ta bort SQL Server-instansen helt kan du migrera till en annan virtuell Azure-dator utan SQL Server förinstallerad på den virtuella datorn och ta bort den aktuella SQL Server VM. Om du vill behålla den virtuella datorn men SQL Server faktureringen följer du dessa steg: 

   1. Backa upp alla dina data, inklusive systemdatabaser, om det behövs. 
   1. Avinstallera SQL Server, inklusive SQL IaaS-tillägget (om det finns).
   1. Installera den [kostnadsfria SQL Express-utgåvan](https://www.microsoft.com/sql-server/sql-server-downloads).
   1. Registrera med SQL IaaS Agent-tillägget i [förenklat läge.](sql-agent-extension-manually-register-single-vm.md)
   1. [Ändra versionen av SQL Server](change-sql-server-edition.md#change-edition-in-portal) i [Azure Portal](https://portal.azure.com) Express för att stoppa faktureringen.  
   1. (valfritt) Inaktivera Express SQL Server tjänsten genom att inaktivera start av tjänsten. 

1. **Kan jag använda Azure Portal för att hantera flera instanser på samma virtuella dator?**

   Nej. Portalhantering tillhandahålls av SQL IaaS Agent-tillägget, som förlitar sig på SQL Server IaaS Agent-tillägget. Därför gäller samma begränsningar för tillägget som tillägget. Portalen kan antingen bara hantera en standardinstans eller en namngiven instans så länge den är korrekt konfigurerad. Mer information finns i SQL Server [IaaS Agent-tillägg](sql-server-iaas-agent-extension-automate-management.md) 


## <a name="updating-and-patching"></a>Uppdatera och korrigera

1. **Hur gör jag för att till en annan version/utgåva av en SQL Server på en virtuell Azure-dator?**

   Kunder kan ändra sin version/utgåva av SQL Server med hjälp av installationsmediet som innehåller den önskade versionen eller utgåvan av SQL Server. När utgåvan har ändrats använder du Azure-portalen för att ändra versionsegenskapen för den virtuella datorn så att faktureringen för den virtuella datorn återspeglas korrekt. Mer information finns i ändra [utgåva av en SQL Server VM](change-sql-server-edition.md). Det finns inte någon faktureringsskillnad för olika versioner av SQL Server, så ingen ytterligare åtgärd krävs när versionen av SQL Server har ändrats.

1. **Var kan jag få installationsmedia för att ändra utgåva eller version av SQL Server?**

   Kunder som har [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) kan hämta installationsmedia från [Volume Licensing Center](https://www.microsoft.com/Licensing/servicecenter/default.aspx). Kunder som inte har Software Assurance kan använda installationsmediet från en Azure Marketplace SQL Server VM-avbildning som har önskad utgåva.
   
1. **Hur tillämpas uppdateringar och servicepack på en SQL Server VM?**

   Virtuella datorer ger dig kontroll över värddatorn, inklusive när och hur du tillämpar uppdateringar. Du kan manuellt installera Windows-uppdateringar för operativsystemet eller aktivera en schemaläggningstjänst som kallas [automatisk uppdatering](automated-patching.md). Automatisk uppdatering installerar alla uppdateringar som markeras som viktiga, inklusive SQL Server-uppdateringar i den kategorin. Andra valfria uppdateringar till SQL Server måste installeras manuellt.

1. **Kan jag uppgradera min SQL Server 2008/2008 R2-instans när jag har registrerat den med SQL IaaS Agent-tillägget?**

   Om operativsystemet är Windows Server 2008 R2 eller senare, ja. Du kan använda alla installationsmedier för att uppgradera versionen och utgåvan av SQL Server, och sedan kan du uppgradera [SQL IaaS-tilläggsläget](sql-server-iaas-agent-extension-automate-management.md#management-modes)) från ingen _agent_ till _fullständig_. Om du gör det får du tillgång till alla fördelar med SQL IaaS-tillägget, till exempel portalhanterbarhet, automatiserade säkerhetskopieringar och automatisk uppdatering. Om os-versionen är Windows Server 2008 stöds endast NoAgent-läge. 

1. **Hur kan jag få kostnadsfria utökade säkerhetsuppdateringar för mina SQL Server 2008- och SQL Server 2008 R2-instanser vars support har nått slutet?**

   Du kan få [kostnadsfria utökade säkerhetsuppdateringar](sql-server-2008-extend-end-of-support.md) genom att flytta SQL Server till en virtuell Azure-dator. Mer information finns i avsnittet om [alternativ när supporten upphör](/sql/sql-server/end-of-support/sql-server-end-of-life-overview). 
  
   

## <a name="general"></a>Allmänt

1. **Stöds SQL Server redundansklusterinstanser (FCI) på virtuella Azure-datorer?**

   Ja. Du kan installera en instans av ett redundanskluster med [antingen Premium-filresurser (PFS)](failover-cluster-instance-premium-file-share-manually-configure.md) eller [lagringsutrymmen direct (S2D)](failover-cluster-instance-storage-spaces-direct-manually-configure.md) för lagringsundersystemet. Premium-filresurser tillhandahåller IOPS- och dataflödeskapaciteter som uppfyller behoven för många arbetsbelastningar. För I//S-intensiva arbetsbelastningar bör du överväga att använda lagringsplatser direkt baserat på en premium- eller ultradisk. Du kan också använda klustrings- eller lagringslösningar från tredje part enligt beskrivningen i Hög tillgänglighet och haveriberedskap för SQL Server [på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md#azure-only-high-availability-solutions).

   > [!IMPORTANT]
   > För närvarande stöds inte _det fullständiga_ [SQL Server IaaS-agenttillägget](sql-server-iaas-agent-extension-automate-management.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar _det fullständiga_ tillägget från virtuella datorer som deltar i FCI och installerar tillägget i _förenklat_ läge i stället. Det här tillägget stöder funktioner, till exempel automatisk säkerhetskopiering och uppdatering och vissa portalfunktioner för SQL Server. De här funktionerna fungerar inte för SQL Server virtuella datorer när den _fullständiga_ agenten har avinstallerats.

1. **Vad är skillnaden mellan de SQL Server virtuella datorerna och SQL Database tjänsten?**

   Konceptuellt skiljer sig SQL Server på en virtuell Azure-dator inte så mycket från att köra SQL Server i ett fjärranslutet datacenter. Däremot erbjuder [Azure SQL Database](../../database/sql-database-paas-overview.md) databas som en tjänst. Med SQL Database har du inte åtkomst till de datorer som är värdar för dina databaser. En fullständig jämförelse finns i [Choose a cloud SQL Server option: Azure SQL (PaaS) Database or SQL Server on Azure VMs (IaaS) (Välja ett molntjänstalternativ: Azure SQL (PaaS)-databas eller SQL Server på virtuella Azure-datorer (IaaS).](../../azure-sql-iaas-vs-paas-what-is-overview.md)

1. **Hur gör jag för att installera SQL Data-verktyg på min virtuella Azure-dator?**

    Ladda ned och installera SQL [Data-verktygen från Microsoft SQL Server Data Tools – Business Intelligence för Visual Studio 2013.](https://www.microsoft.com/download/details.aspx?id=42313)

1. **Stöds distribuerade transaktioner med MSDTC på virtuella SQL Server datorer?**
   
    Ja. Lokal DTC stöds för SQL Server 2016 SP2 och högre. Program måste dock testas när always On-tillgänglighetsgrupper används, eftersom transaktioner som körs under en redundans misslyckas och måste göras på nytt. Klustrad DTC är tillgänglig från och med Windows Server 2019. 
    
1. **Flyttar Azure SQL virtuella datorn eller lagrar kunddata från regionen?**

   Nej. I själva Azure SQL virtuella datorn och SQL IaaS-agenttillägget inte några kunddata.

## <a name="sql-server-iaas-agent-extension"></a>SQL Server IaaS Agent-tillägg

1. **Ska jag registrera min SQL Server VM från en SQL Server-avbildning i Azure Marketplace?**

   Nej. Microsoft registrerar automatiskt virtuella datorer som etablerats från SQL Server avbildningar i Azure Marketplace. Registrering med tillägget krävs endast om  den virtuella datorn inte har etablerats från SQL Server i Azure Marketplace och SQL Server självinstallerades.

1. **Är SQL IaaS Agent-tillägget tillgängligt för alla kunder?** 

   Ja. Kunder bör registrera sina virtuella SQL Server-datorer med tillägget om de inte använder en SQL Server-avbildning från Azure Marketplace och i stället självinstallerade SQL Server, eller om de har tagit med sin anpassade virtuella hårddisk. Virtuella datorer som ägs av alla typer av prenumerationer (Direkt, Enterprise-avtal och Molnlösningsleverantör) kan registreras med SQL IaaS Agent-tillägget.

1. **Vilket är standardhanteringsläget när du registrerar med SQL IaaS Agent-tillägget?**

   Standardhanteringsläget när du registrerar med SQL IaaS Agent-tillägget är *enkelt.* Om SQL Server för hantering inte har angetts när du registrerar med tillägget, anges läget som förenklat och SQL Server tjänsten startar inte om. Vi rekommenderar att du först registrerar dig med SQL IaaS Agent-tillägget i förenklat läge och sedan uppgraderar till full under en underhållsfönstret. På samma sätt är standardhanteringen också enkel när du använder [funktionen för automatisk registrering.](sql-agent-extension-automatic-registration-all-vms.md)

1. **Vilka är kraven för att registrera med SQL IaaS Agent-tillägget?**

   Det finns inga krav för att registrera med SQL IaaS Agent-tillägget förutom att SQL Server installerat på den virtuella datorn. Observera att om SQL IaaS-agenttillägget installeras i fullständigt läge startas SQL Server-tjänsten om, så detta rekommenderas under en underhållsfönstret.

1. **Kommer registrering med SQL IaaS Agent-tillägget att installera en agent på min virtuella dator?**

   Ja, registrering med SQL IaaS Agent-tillägget i fullständigt hanterbarhetsläge installerar en agent på den virtuella datorn. Registrering i förenklat läge eller NoAgent-läge gör det inte. 

   Registrering med SQL IaaS Agent-tillägget i förenklat läge kopierar  endast binärfilerna för SQL IaaS Agent-tillägget till den virtuella datorn. Agenten installeras inte. Dessa binärfiler används sedan för att installera agenten när hanteringsläget uppgraderas till fullt.


1. **Kommer registreringen med SQL IaaS Agent-tillägget att starta om SQL Server på min virtuella dator?**

   Det beror på det läge som anges under registreringen. Om lightweight- eller NoAgent-läge har angetts startar SQL Server tjänsten inte om. Men om hanteringsläget anges som fullständigt kommer SQL Server tjänsten att startas om. Funktionen för automatisk registrering registrerar dina virtuella SQL Server-datorer i förenklat läge, såvida inte Windows Server-versionen är 2008, i vilket fall SQL Server VM registreras i NoAgent-läge. 

1. **Vad är skillnaden mellan lätta och NoAgent-hanteringslägen vid registrering med SQL IaaS Agent-tillägget?** 

   NoAgent-hanteringsläget är det enda tillgängliga hanteringsläget för SQL Server 2008 och SQL Server 2008 R2 på Windows Server 2008. För alla senare versioner av Windows Server är de två tillgängliga hanterbarhetslägena lätta och fullständiga. 

   NoAgent-läge kräver SQL Server för version och utgåva som anges av kunden. Förenklat läge frågar den virtuella datorn efter versionen och utgåvan av SQL Server instansen.

1. **Kan jag registrera mig med SQL IaaS Agent-tillägget utan att ange SQL Server licenstypen?**

   Nej. Den SQL Server licenstypen är inte en valfri egenskap när du registrerar med SQL IaaS Agent-tillägget. Du måste ange SQL Server-licenstypen som betalas enligt din registrering eller Azure Hybrid-förmån när du registrerar dig med SQL IaaS Agent-tillägget i alla hanterbarhetslägen (NoAgent, lightweight och full). Om du har någon av de kostnadsfria versionerna av SQL Server, till exempel Developer eller Evaluation Edition, måste du registrera dig med betala per användning-licensiering. Azure Hybrid-förmån är endast tillgängligt för betalversioner av SQL Server till exempel Enterprise- och Standard-versioner.

1. **Kan jag uppgradera SQL Server IaaS-tillägget från NoAgent-läge till fullständigt läge?**

   Nej. Uppgradering av hanterbarhetsläget till full eller förenklad är inte tillgängligt för NoAgent-läge. Detta är en teknisk begränsning i Windows Server 2008. Du måste först uppgradera operativsystemet till Windows Server 2008 R2 eller högre och sedan kan du uppgradera till fullständigt hanteringsläge. 

1. **Kan jag uppgradera SQL Server IaaS-tillägget från förenklat läge till fullständigt läge?**

   Ja. Uppgradering av hanterbarhetsläget från förenklat till fullt stöds via Azure PowerShell eller Azure Portal. Detta utlöser en omstart av SQL Server tjänsten.

1. **Kan jag nedgradera SQL Server IaaS-tillägget från fullständigt läge till NoAgent eller enkelt hanteringsläge?**

   Nej. Nedgradering av SQL Server IaaS-tilläggets hanterbarhetsläge stöds inte. Hanterbarhetsläget kan inte nedgraderas från fullständigt läge till enkelt läge eller NoAgent-läge och kan inte nedgraderas från förenklat läge till NoAgent-läge. 

   Om du vill ändra hanterbarhetsläget från fullständig hanterbarhet avregistrerar du SQL Server VM från SQL  IaaS Agent-tillägget genom att ta bort den virtuella SQL-datorresursen och registrera om SQL Server VM med SQL IaaS Agent-tillägget igen i ett annat hanteringsläge. [](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)

1. **Kan jag registrera mig med SQL IaaS Agent-tillägget från Azure Portal?**

   Nej. Registrering med SQL IaaS Agent-tillägget är inte tillgängligt i Azure Portal. Registrering med SQL IaaS Agent-tillägget stöds endast med Azure CLI eller Azure PowerShell. 

1. **Kan jag registrera en virtuell dator med SQL IaaS Agent-tillägget innan SQL Server installeras?**

   Nej. En virtuell dator måste ha minst en SQL Server (Database Engine)-instans för att kunna registreras med SQL IaaS Agent-tillägget. Om det inte finns SQL Server instans på den virtuella datorn är den nya Microsoft.SqlVirtualMachine-resursen i ett misslyckat tillstånd.

1. **Kan jag registrera en virtuell dator med SQL IaaS Agent-tillägget om det finns SQL Server instanser?**

   Ja, förutsatt att det finns en standardinstans på den virtuella datorn. SQL IaaS Agent-tillägget registrerar bara en SQL Server (Databasmotor) instans. SQL IaaS Agent-tillägget registrerar standardinstansen SQL Server för flera instanser.

1. **Kan jag registrera en SQL Server redundansklusterinstans med SQL IaaS Agent-tillägget?**

   Ja. SQL Server redundansklusterinstanser på en virtuell Azure-dator kan registreras med SQL IaaS Agent-tillägget i förenklat läge. Men SQL Server redundansklusterinstanser kan inte uppgraderas till fullständigt hanterbarhetsläge.

1. **Kan jag registrera min virtuella dator med SQL IaaS Agent-tillägget om en Always On-tillgänglighetsgrupp har konfigurerats?**

   Ja. Det finns inga begränsningar för att registrera en SQL Server-instans på en virtuell Azure-dator med SQL IaaS Agent-tillägget om du deltar i en Always On-tillgänglighetsgruppkonfiguration.

1. **Vad kostar det att registrera med SQL IaaS Agent-tillägget eller uppgradera till fullständigt hanterbarhetsläge?**

   Inga. Det finns ingen avgift för registrering med SQL IaaS Agent-tillägget eller med någon av de tre hanterbarhetslägena. Att hantera SQL Server VM med tillägget är helt kostnadsfritt. 

1. **Hur påverkas prestandan av att använda de olika hanterbarhetslägena?**

   Det påverkar inte när du använder *lägena NoAgent* *och förenklad* hanterbarhet. Det finns minimal påverkan när du använder *fullständigt* hanterbarhetsläge från två tjänster som är installerade på operativsystemet. Dessa kan övervakas via Aktivitetshanteraren och visas i den inbyggda Windows Services-konsolen. 

   De två tjänstnamnen är:
   - `SqlIaaSExtensionQuery` (Visningsnamn - `Microsoft SQL Server IaaS Query Service` )
   - `SQLIaaSExtension` (Visningsnamn - `Microsoft SQL Server IaaS Agent` )

1. **Hur gör jag för att du ta bort tillägget?**

   Ta bort tillägget genom [att avregistrera](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension) SQL Server VM från SQL IaaS Agent-tillägget. 

## <a name="resources"></a>Resurser

**Virtuella Windows-datorer:**

* [Översikt över SQL Server på en virtuell Windows-dator](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Etablera SQL Server på en virtuell Windows-dator](create-sql-vm-portal.md)
* [Migrera en databas till en SQL Server virtuell Azure-dator](migrate-to-vm-from-sql-server.md)
* [Hög tillgänglighet och haveriberedskap för SQL Server på Azure Virtual Machines](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Metodtips för prestanda för SQL Server på Azure Virtual Machines](performance-guidelines-best-practices.md)
* [Programmönster och utvecklingsstrategier för SQL Server på Azure Virtual Machines](application-patterns-development-strategies.md)

**Virtuella Linux-datorer:**

* [Översikt över SQL Server på en virtuell Linux-dator](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Etablera SQL Server en virtuell Linux-dator](../linux/sql-vm-create-portal-quickstart.md)
* [Vanliga frågor och svar (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server på Linux dokumentation](/sql/linux/sql-server-linux-overview)
