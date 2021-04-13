---
title: Azure Automanage för virtuella datorer
description: Lär dig Azure Automanage för virtuella datorer.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/23/2021
ms.author: deanwe
ms.custom: references_regions
ms.openlocfilehash: 514f1af2a1b120254840986fc5ceb803dfc24345
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363384"
---
# <a name="azure-automanage-for-virtual-machines"></a>Azure Automanage för virtuella datorer

Den här artikeln innehåller information Azure Automanage för virtuella datorer, vilket har följande fördelar:

- Registrera virtuella datorer på ett intelligent sätt för att välja metodtips för Azure-tjänster
- Konfigurerar automatiskt varje tjänst enligt bästa praxis för Azure
- Övervakar drift och korrigerar för den när den identifieras
- Ger en enkel upplevelse (peka, klicka, ange, glöm)


## <a name="overview"></a>Översikt

Azure Automanage för virtuella datorer är en tjänst som eliminerar behovet av att identifiera, veta hur de ska publiceras och hur du konfigurerar vissa tjänster i Azure som skulle ha nytta av din virtuella dator. Dessa tjänster anses vara Azures metodtipstjänster och bidrar till att förbättra tillförlitlighet, säkerhet och hantering för virtuella datorer. Exempeltjänster är [Azure Uppdateringshantering](../automation/update-management/overview.md) och [Azure Backup](../backup/backup-overview.md).

När du har Azure Automanage virtuella datorer konfigureras varje bästa praxis-tjänst enligt de rekommenderade inställningarna. Metodtipsen är olika för var och en av tjänsterna. Ett exempel kan vara Azure Backup, där det bästa sättet kan vara att backa upp den virtuella datorn en gång om dagen och ha en kvarhållningsperiod på sex månader.

Azure Automanage övervakar också automatiskt föravdrift och korrigerar den när den identifieras. Det innebär att om den virtuella datorn har publiceras till Azure Automanage konfigurerar vi den inte bara enligt bästa praxis för Azure, utan vi övervakar även din dator för att säkerställa att den fortsätter att följa dessa metodtips under hela livscykeln. Om den virtuella datorn avviker eller avviker från dessa metoder (till exempel om en tjänst avboarderas) korrigerar vi den och hämtar tillbaka datorn till önskat tillstånd.

## <a name="prerequisites"></a>Förutsättningar

Det finns flera förutsättningar att tänka på innan du försöker Azure Automanage på dina virtuella datorer.

- [Windows Server-versioner och](automanage-windows-server.md#supported-windows-server-versions) [Linux-distributioner som stöds](automanage-linux.md#supported-linux-distributions-and-versions)
- Virtuella datorer måste finnas i en region som stöds (se nedan)
- Användaren måste ha rätt behörigheter (se nedan)
- Automanage stöder inte Sandbox-prenumerationer just nu

### <a name="supported-regions"></a>Regioner som stöds
Automanage stöder endast virtuella datorer som finns i följande regioner:
* Europa, västra
* Europa, norra
* Central US
* East US
* USA, östra 2
* USA, västra
* USA, västra 2
* Kanada, centrala
* USA, västra centrala
* USA, södra centrala
* Japan, östra
* Storbritannien, södra
* Australien, östra
* Australien, sydöstra
* Sydostasien

### <a name="required-rbac-permissions"></a>RBAC-behörigheter som krävs
Ditt konto kräver lite olika RBAC-roller beroende på om du aktiverar Automanage med ett nytt automanagekonto.

Om du aktiverar Automanage med ett nytt konto för automanage:
* **Ägarroll** för prenumerationerna som innehåller dina virtuella _**datorer, eller**_
* **Rollerna** **Deltagare och Administratör för** användaråtkomst i prenumerationerna som innehåller dina virtuella datorer

Om du aktiverar Automanage med ett befintligt automanagekonto:
* **Deltagarrollen** i resursgruppen som innehåller dina virtuella datorer

Kontot Automanage beviljas deltagar- **och** **resursprincipdeltagares** behörighet att utföra åtgärder på automatisktmanageerade datorer.

> [!NOTE]
> Om du vill använda Automanage på en virtuell dator som är ansluten till en arbetsyta i en annan prenumeration måste du ha de behörigheter som beskrivs ovan för varje prenumeration.

## <a name="participating-services"></a>Deltagande tjänster

:::image type="content" source="media\automanage-virtual-machines\intelligently-onboard-services-1.png" alt-text="Publicera tjänster på ett intelligent sätt.":::

En fullständig lista över deltagande Azure-tjänster och deras miljö som stöds finns i följande:
- [Automanage för Linux](automanage-linux.md)
- [Automatisk installation för Windows Server](automanage-windows-server.md)

 Vi kommer automatiskt att registrera dig för dessa deltagande tjänster. De är viktiga för våra metodtips white paper, som du hittar i [våra Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

För alla dessa tjänster kommer vi att automatiskt registrera, konfigurera automatiskt, övervaka föravdrift och medla om avdrift upptäcks.


## <a name="enabling-automanage-for-vms-in-azure-portal"></a>Aktivera automanage för virtuella datorer i Azure Portal

I Azure Portal kan du aktivera Automanage på en befintlig virtuell dator eller när du skapar en ny virtuell dator. För koncisa steg i den här processen kan du ta [en titt på snabbstarten Automanage for virtual machines (Automanage for virtual machines).](quick-create-virtual-machines-portal.md)

Om det är första gången du aktiverar automatisk hantering för den virtuella datorn kan du söka i Azure Portal efter bästa praxis för automatisk hantering **– virtuell Azure-dator.** Klicka **på Aktivera på befintlig virtuell** dator, välj de virtuella datorer som du vill publicera, klicka på **Välj** och klicka **på** Aktivera så är du klar.

Den enda gången du kan behöva interagera med den här virtuella datorn för att hantera dessa tjänster är om vi försökte åtgärda den virtuella datorn, men inte gjorde det. Om vi åtgärdar den virtuella datorn på rätt sätt kommer vi att se till att den följer kraven utan att ens meddela dig. Mer information finns i [Status för virtuella datorer.](#status-of-vms)

## <a name="enabling-automanage-for-vms-using-azure-policy"></a>Aktivera automanage för virtuella datorer med hjälp av Azure Policy
Du kan också aktivera automanage på virtuella datorer i stor skala med hjälp av den inbyggda Azure Policy. Principen har en DeployIfNotExists-effekt, vilket innebär att alla berättigade virtuella datorer som finns inom principens omfång automatiskt publiceras till bästa praxis för automatisk användning av virtuella datorer.

En direktlänk till principen finns [här.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F270610db-8c04-438a-a739-e8e6745b22d3)

### <a name="how-to-apply-the-policy"></a>Så här tillämpar du principen
1. Klicka på **knappen** Tilldela när du visar principdefinitionen
1. Välj det omfång där du vill tillämpa principen (kan vara hanteringsgrupp, prenumeration eller resursgrupp)
1. Under **Parametrar** anger du parametrar för kontot Automanage, Konfigurationsprofil och Effekt (effekten bör vanligtvis vara DeployIfNotExists)
    1. Om du inte har ett automanage-konto måste du [skapa ett](./automanage-account.md).
1. Under **Reparation markerar** du kryssrutan "Klicka på en reparationsaktivitet". Detta utför onboarding till Automanage.
1. Klicka **på Granska + skapa** och se till att alla inställningar ser bra ut.
1. Klicka på **Skapa**.

## <a name="environment-configuration"></a>Miljökonfiguration

När du aktiverar Automanage för den virtuella datorn krävs en miljö. Miljöer utgör grunden för den här tjänsten. De definierar vilka tjänster som vi ska registrera dina datorer till och i viss utsträckning vad konfigurationen av dessa tjänster skulle vara.

### <a name="default-environments"></a>Standardmiljöer

Det finns för närvarande två tillgängliga miljöer.

- **Utvecklings-/testmiljön** är utformad för Dev/Test-datorer.
- **Produktionsmiljön** är till för produktion.

Orsaken till den här differentiatorn är att vissa tjänster rekommenderas baserat på den arbetsbelastning som körs. I en produktionsdator kommer vi till exempel automatiskt att registrera dig för Azure Backup. För en Dev/Test-dator skulle dock en säkerhetskopieringstjänst vara en onödig kostnad, eftersom Dev/Test-datorer vanligtvis har lägre påverkan på verksamheten.

### <a name="customizing-an-environment-using-preferences"></a>Anpassa en miljö med hjälp av inställningar

Förutom de standardtjänster som vi konfigurerar med dig kan du konfigurera en viss delmängd av inställningarna. Dessa inställningar tillåts inom ett antal konfigurationsalternativ. När det gäller Azure Backup kan du till exempel definiera säkerhetskopieringsfrekvensen och vilken veckodag den inträffar på.

> [!NOTE]
> I Dev/Test-miljön kommer vi inte att a upp den virtuella datorn alls.

Du kan justera inställningarna för en standardmiljö via inställningar. Lär dig hur du skapar en inställning [här.](virtual-machines-custom-preferences.md)

> [!NOTE]
> Du kan inte ändra konfigurationen för -funktionen på den virtuella datorn när Automanage är aktiverat. Du måste inaktivera Automanage för den virtuella datorn och sedan återaktivera Automanage med önskad miljö och inställningar.

En fullständig lista över deltagande Azure-tjänster och om de stöder inställningar finns här:
- [Automanage för Linux](automanage-windows-server.md)
- [Automanage för Windows Server](automanage-windows-server.md)


## <a name="automanage-account"></a>Automanage-konto

Automanage-kontot är säkerhetskontexten eller identiteten som de automatiserade åtgärderna utförs under. Normalt är alternativet För automatisk hantering inte nödvändigt för dig att välja, men om det fanns ett delegeringsscenario där du ville dela upp den automatiserade hanteringen av dina resurser (kanske mellan två systemadministratörer) kan du med alternativet Automanage Account i aktiverande flödet definiera en Azure-identitet för var och en av dessa administratörer.

Mer information om automanage-kontot och hur du skapar ett finns i dokumentet [Automanage Account (Automanage-konto).](./automanage-account.md)

## <a name="status-of-vms"></a>Status för virtuella datorer

I den Azure Portal går du till sidan Metodtips för automatisk hantering **– Virtuell Azure-dator** med en lista över alla dina automatiskt hanterade virtuella datorer. Här visas den övergripande statusen för varje virtuell dator.

:::image type="content" source="media\automanage-virtual-machines\configured-status.png" alt-text="Lista över konfigurerade virtuella datorer.":::

För varje listad virtuell dator visas följande information: Namn, Miljö, Konfigurationsinställningar, Status, Operativsystem, Konto, Prenumeration och Resursgrupp.

Kolumnen **Status** kan visa följande tillstånd:
- *Pågår – den* virtuella datorn har precis aktiverats och håller på att konfigureras
- *Konfigurerad* – den virtuella datorn är konfigurerad och ingen avdrift identifieras
- *Misslyckades* – den virtuella datorn har avvikit och vi kunde inte åtgärda det
- *Väntar –* den virtuella datorn körs inte för närvarande och automanage försöker registrera eller åtgärda den virtuella datorn nästa gång den körs

Om du ser **Status som** *Misslyckad* kan du felsöka distributionen via den resursgrupp som den virtuella datorn finns i. Gå till **Resursgrupper,** välj din resursgrupp, klicka på **Distributioner** och se *statusen* Misslyckades där tillsammans med felinformation.


## <a name="disabling-automanage-for-vms"></a>Inaktivera automanage för virtuella datorer

Du kan välja att en dag ska inaktivera automanage på vissa virtuella datorer. Till exempel kör datorn en mycket känslig säker arbetsbelastning och du måste låsa den ytterligare än vad Azure skulle ha gjort på ett naturligt sätt, så du måste konfigurera datorn utanför Bästa praxis för Azure.

Om du vill göra det Azure Portal du sidan Med metodtips för Automatisk hantering – Virtuell **Azure-dator** med en lista över alla dina automatiskt hanterade virtuella datorer. Markera kryssrutan bredvid den virtuella dator som du vill inaktivera från Automanage (Hantera automatiskt) och klicka sedan på **knappen Disable automanagment (Inaktivera automanagment).**

:::image type="content" source="media\automanage-virtual-machines\disable-step-1.png" alt-text="Inaktivera automanage på en virtuell dator.":::

Läs igenom meddelandena noggrant i popup-fönster som visas innan du godkänner **Inaktivera**.

> [!NOTE]
> Om du inaktiverar automanagement i en virtuell dator resulterar det i följande beteende:
>
> - Konfigurationen av den virtuella datorn och de tjänster som den är inbyggd i ändras inte.
> - Eventuella avgifter som tillkommer för dessa tjänster förblir fakturerbara och fortsätter att debiteras.
> - Automanage drift monitoring immediately stops ( Automanage drift monitoring immediately stops.


Först och främst kommer vi inte att ta bort den virtuella datorn från någon av de tjänster som vi har registrera den till och konfigurerat. Det gör att eventuella avgifter som tillkommer för dessa tjänster fortsätter att vara fakturerbara. Du måste avkorta om det behövs. Eventuella beteenden för automanage avbryts omedelbart. Vi kommer till exempel inte längre att övervaka den virtuella datorn för drift.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig att automanage för virtuella datorer är ett sätt att eliminera behovet av att känna till, registrera till och konfigurera bästa praxis för Azure-tjänster. Om en dator som du har registrerat för automanage för virtuella datorer drivs från miljökonfigurationen kommer vi dessutom automatiskt att se till att den blir efterlevnad igen.

Prova att aktivera Automanage för virtuella datorer i Azure Portal.

> [!div class="nextstepaction"]
> [Aktivera automanage för virtuella datorer i Azure Portal](quick-create-virtual-machines-portal.md)