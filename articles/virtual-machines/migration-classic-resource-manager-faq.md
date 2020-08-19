---
title: Vanliga frågor och svar om migrering från klassiskt läge till Azure Resource Manager
description: Vanliga frågor och svar om migrering från klassiskt läge till Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 3d8c5d408ef2b958ca7d3454f23ce2c52fb1d1be
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513215"
---
# <a name="frequently-asked-questions-about-classic-to-azure-resource-manager-migration"></a>Vanliga frågor och svar om migrering från klassiskt läge till Azure Resource Manager

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](./classic-vm-deprecation.md#how-does-this-affect-me).

## <a name="what-is-azure-service-manager-and-what-does-it-mean-by-classic"></a>Vad är Azure Service Manager och vad innebär det?

Ordet "klassisk" i IaaS VM (klassisk) refererar till virtuella datorer som hanteras av Azure Service Manager (ASM). Azure Service Manager (ASM) är det gamla kontroll planet för Azure som ansvarar för att skapa, hantera och ta bort virtuella datorer och utföra andra kontroll Plans åtgärder. 

## <a name="what-is-azure-resource-manager"></a>Vad är Azure Resource Manager?

[Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) är det senaste kontroll planet för Azure som ansvarar för att skapa, hantera och ta bort virtuella datorer och utföra andra kontroll Plans åtgärder. 

## <a name="what-is-the-time-required-for-migration"></a>Vad är den tid som krävs för migrering?

Planering och utförande av migreringen beror avsevärt på arkitekturens komplexitet och det kan ta några månader.  

## <a name="what-is-the-definition-of-a-new-customer-on-iaas-vms-classic"></a>Vad är definitionen av en ny kund på virtuella IaaS-datorer (klassisk)?

Kunder som inte har IaaS virtuella datorer (klassisk) i prenumerationerna av månaden i februari 2020 (en månad innan utgångs punkt startade) betraktas som nya kunder. 

## <a name="what-is-the-definition-of-an-existing-customer-on-iaas-virtual-machines-classic"></a>Vad är definitionen av en befintlig kund på IaaS Virtual Machines (klassisk)?

Kunder som haft aktiva eller stoppade men allokerade IaaS-VM: ar (klassisk) i sina prenumerationer under månaden februari 2020 betraktas som en befintlig kund. Endast de här kunderna får fram till den 1 mars 2023 att migrera sina virtuella datorer från Azure Service Manager till Azure Resource Manager. 

## <a name="why-am-i-getting-an-error-stating-newclassicvmcreationnotallowedforsubscription"></a>Varför får jag ett fel meddelande om att "NewClassicVMCreationNotAllowedForSubscription"?

IaaS VM (klassisk) är inte längre tillgängligt för nya kunder som en del av indragnings processen. Vi har identifierat dig som nya kunder och därför var din åtgärd inte auktoriserad. Vi rekommenderar starkt att du använder [Azure Virtual Machines med arm](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell). Om du inte kan använda virtuella Azure-datorer med ARM kontaktar du support för att lägga till din prenumeration i listan över tillåtna.

## <a name="does-this-migration-plan-affect-any-of-my-existing-services-or-applications-that-run-on-azure-virtual-machines"></a>Påverkar den här migreringsplanen några befintliga tjänster eller program som körs på virtuella Azure-datorer? 

Inte förrän den 1 mars 2023 för virtuella IaaS-datorer (klassisk). De virtuella IaaS-datorerna (klassiska) är fullt tillgängliga tjänster som är allmänt tillgängliga. Du kan fortsätta att använda de här resurserna för att utöka storleken på Microsoft Azure. Den 1 mars 2023 kommer de virtuella datorerna att dras tillbaka och alla aktiva eller allokerade virtuella datorer stoppas & frigörs. Andra klassiska resurser påverkas inte, t. ex. Cloud Services (klassisk), lagrings konton (klassisk) osv.   

## <a name="what-happens-to-my-vms-if-i-dont-plan-on-migrating-in-the-near-future"></a>Vad händer med mina virtuella datorer om jag inte planerar för migrering inom den närmaste framtiden? 

Den 1 mars 2023 kommer virtuella IaaS-datorer (klassisk) att dras tillbaka och alla aktiva eller allokerade virtuella datorer stoppas & frigörs. För att förhindra påverkan på verksamheten, kommenterar vi hög kommentarer för att börja planera migreringen idag och slutföra den före den 1 mars 2023. Vi är inte föråldrade de befintliga klassiska API: erna, Cloud Services och resurs modellen. Vi vill att migreringen ska gå lätt och smidigt med tanke på de avancerade funktionerna i Resource Manager-distributionsmodellen. Vi rekommenderar att du börjar planera för att migrera de här resurserna till Azure Resource Manager. 

## <a name="what-does-this-migration-plan-mean-for-my-existing-tooling"></a>Vad innebär den här migreringsplanen för mina befintliga verktyg? 

Det är mycket viktigt att du uppdaterar dina verktyg till Resource Manager-distributionsmodellen.

## <a name="how-long-will-the-management-plane-downtime-be"></a>Hur länge kommer hanteringsplanet vara avstängt? 

Det beror på antalet resurser som ska migreras. För mindre distributioner (ett tiotal virtuella datorer) bör hela migreringen ta högst en timme. Vid stora distributioner (hundratals virtuella datorer) kan migreringen ta några timmar.

## <a name="can-i-roll-back-after-my-migrating-resources-are-committed-in-resource-manager"></a>Kan jag återställa när mina migreringsresurser har checkats in till Resource Manager? 

Du kan avbryta migreringen så länge resurserna är i förberedelsefasen. Återställning stöds inte när resurserna har migrerats via incheckningsåtgärden.

## <a name="can-i-roll-back-my-migration-if-the-commit-operation-fails"></a>Kan jag återställa migreringen om incheckningen misslyckas? 

Du kan inte avbryta migreringen om incheckningen misslyckas. Alla migreringsåtgärder, inklusive incheckning, är idempotenta. Vi rekommenderar därför att du gör om åtgärden om en stund. Om du fortfarande stöter på ett fel kan du skapa ett support ärende.

## <a name="do-i-have-to-buy-another-express-route-circuit-if-i-have-to-use-iaas-under-resource-manager"></a>Måste jag köpa en annan ExpressRoute-krets om jag måste använda IaaS med Resource Manager? 

Nej. Vi har nyligen gjort det möjligt att [flytta ExpressRoute-kretsar från klassiskt läge till Resource Manager-distributionsmodellen](~/articles/expressroute/expressroute-move.md). Du behöver inte köpa någon ny ExpressRoute-krets om du redan har en.

## <a name="what-if-i-had-configured-role-based-access-control-policies-for-my-classic-iaas-resources"></a>Vad händer om jag har konfigurerat principer för rollbaserad åtkomstkontroll för mina klassiska IaaS-resurser? 

Under migreringen omformas resurserna från klassiskt läge till Resource Manager-läge. Vi rekommenderar att du planerar de RBAC-principuppdateringar som behövs efter migreringen.

## <a name="i-backed-up-my-classic-vms-in-a-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>Jag har säkerhetskopierat mina klassiska virtuella datorer i ett valv. Kan jag migrera mina virtuella datorer från klassiskt läge till Resource Manager-läge och skydda dem i ett Recovery Services-valv?

När du flyttar en virtuell dator från klassiskt läge till Resource Manager-läge migreras inte säkerhets kopior som togs innan migreringen till den nyligen migrerade Resource Manager-datorn. Men om du vill behålla dina säkerhets kopior av klassiska virtuella datorer följer du de här stegen innan du migrerar. 

1. I Recovery Services-valvet går du till fliken **skyddade objekt** och väljer den virtuella datorn. 
2. Klicka på Stoppa skydd. Lämna alternativet *Ta bort associerade säkerhetskopieringsdata***avmarkerat**.

> [!NOTE]
> Du debiteras reserv instans kostnaden för att spara data. Säkerhets kopior kommer att rensas enligt kvarhållningsintervall. Den senaste säkerhets kopian sparas dock alltid tills du uttryckligen tar bort säkerhetskopierade data. Vi rekommenderar att du kontrollerar kvarhållningsintervallet för den virtuella datorn och utlöser "ta bort säkerhets kopierings data" på det skyddade objektet i valvet när kvarhållningsintervallet är över. 
>
>

För att migrera den virtuella datorn till Resource Manager-läge 

1. Ta bort tillägget för säkerhetskopiering/ögonblicksbild från den virtuella datorn.
2. Migrera den virtuella datorn från klassiskt läge till Resource Manager-läge. Kontrollera att lagrings- och nätverksinformationen för den virtuella datorn också migreras till Resource Manager-läge.

Om du vill säkerhetskopiera den migrerade virtuella datorn går du vidare till bladet hantering av virtuell dator för att [Aktivera säkerhets kopiering](~/articles/backup/quick-backup-vm-portal.md#enable-backup-on-a-vm).

## <a name="can-i-validate-my-subscription-or-resources-to-see-if-theyre-capable-of-migration"></a>Kan jag verifiera prenumeration eller resurser för att se om de kan migreras? 

Ja. I alternativet för migrering som stöds av plattformen är det första steget vid migreringsförberedelse att verifiera om resurserna kan migreras. Om resurserna inte godkänns för migrering får du meddelanden om orsaken till detta.

## <a name="what-happens-if-i-run-into-a-quota-error-while-preparing-the-iaas-resources-for-migration"></a>Vad händer om jag får ett kvotfel när IaaS-resurserna förbereds för migrering? 

Vi rekommenderar att du avbryter migreringen och loggar en supportbegäran om att öka kvoten i den region där du migrerar de virtuella datorerna. När din begäran om ökad kvot har godkänts kan du fortsätta migreringen igen.

## <a name="how-do-i-report-an-issue"></a>Hur gör jag för att rapportera ett problem? 

Publicera dina problem och frågor om migrering till vår [Microsoft Q&en fråge sida för virtuell dator](https://docs.microsoft.com/answers/topics/azure-virtual-machines.html)med nyckelordet ClassicIaaSMigration. Vi rekommenderar att du lägger upp alla dina frågor på det här forumet. Om du har ett supportavtal är du även välkommen att logga ett supportärende.

## <a name="what-if-i-dont-like-the-names-of-the-resources-that-the-platform-chose-during-migration"></a>Vad gör jag om jag inte gillar namnen på resurserna som plattformen väljer under migreringen? 

Alla resursnamn som du uttryckligen angett i den klassiska distributionsmodellen behålls under migreringen. I vissa fall skapas nya resurser. Exempelvis skapas ett nätverksgränssnitt för varje virtuell dator. För närvarande har vi inte möjlighet att styra över namnen på de nya resurser som skapas under migreringen. Logga dina röster för den här funktionen i [Azures feedbackforum](https://feedback.azure.com).

## <a name="can-i-migrate-expressroute-circuits-used-across-subscriptions-with-authorization-links"></a>Kan jag migrera ExpressRoute-kretsar som används över prenumerationer med auktoriseringslänkar? 

ExpressRoute-kretsar som använder auktoriseringslänkar över flera prenumerationer migreras inte automatiskt utan driftavbrott. Det finns information om hur de kan migreras manuellt. Anvisningar och mer information finns i [Migrate ExpressRoute circuits and associated virtual networks from the classic to the Resource Manager deployment model](~/articles/expressroute/expressroute-migration-classic-resource-manager.md) (Migrera ExpressRoute-kretsar och tillhörande virtuella nätverk från klassisk till Resource Manager-distributionsmodell).

## <a name="i-got-the-message-vm-is-reporting-the-overall-agent-status-as-not-ready-hence-the-vm-cannot-be-migrated-ensure-that-the-vm-agent-is-reporting-overall-agent-status-as-ready-or-vm-contains-extension-whose-status-is-not-being-reported-from-the-vm-hence-this-vm-cannot-be-migrated"></a>Jag fick meddelandet *"den virtuella datorn rapporterar den övergripande agent statusen som inte klar. Den virtuella datorn kan därför inte migreras. Se till att den virtuella dator agenten rapporterar övergripande agent status som färdig "* eller *" VM innehåller tillägg vars status inte rapporteras från den virtuella datorn. Den här virtuella datorn kan därför inte migreras. "*

Det här meddelandet visas när den virtuella datorn saknar utgående anslutning till Internet. Den virtuella datoragenten använder utgående anslutning till Azure Storage-konto för att uppdatera agentstatusen var femte minut.


## <a name="next-steps"></a>Nästa steg

För Linux:

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](./linux/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

För Windows:

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](./migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](./windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)