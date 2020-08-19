---
title: Planera för migrering från klassisk till Azure Resource Manager
description: Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager
author: tanmaygore
manager: vashan
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: dc8279f8227c437a09a61fad2b84c5b061257eb5
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510223"
---
# <a name="planning-for-migration-of-iaas-resources-from-classic-to-azure-resource-manager-in-windows"></a>Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager i Windows

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](../classic-vm-deprecation.md#how-does-this-affect-me).

Även om Azure Resource Manager erbjuder många fantastiska funktioner, är det viktigt att planera migreringen för att se till att saker går smidigt. När du planerar planeringen ser du till att du inte stöter på problem när du kör migreringsåtgärder.

Det finns fyra allmänna faser för migrerings resan:<br>

![Faser för migrering](../media/virtual-machines-windows-migration-classic-resource-manager/plan-labtest-migrate-beyond.png)

## <a name="plan"></a>Planera

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Beroende på din tekniska krav storlek, geografiska områden och operativa metoder kanske du vill överväga följande:

1. Varför är Azure Resource Manager önskas för din organisation?  Vilka är affärs orsakerna till en migrering?
2. Vilka är de tekniska orsakerna till Azure Resource Manager?  Vad (om det finns några) ytterligare Azure-tjänster vill du använda?
3. Vilka program (eller uppsättningar av virtuella datorer) ingår i migreringen?
4. Vilka scenarier stöds med migrations-API: et?  Granska de [funktioner och konfigurationer som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations).
5. Kommer dina drift team nu att ha stöd för program/virtuella datorer i både klassiska och Azure Resource Manager?
6. Hur (om hela) Azure Resource Manager ändra distributions-, hanterings-, övervaknings-och rapporterings processer för virtuella datorer?  Behöver dina distributions skript uppdateras?
7. Vad är kommunikations planen för att meddela intressenter (slutanvändare, program ägare och infrastruktur ägare)?
8. Bör det finnas en underhålls period där programmet inte är tillgängligt för slutanvändare och till program ägare, beroende på miljöns komplexitet?  I så fall, för hur lång tid?
9. Vad är utbildnings planen för att säkerställa att intressenter är kunskaps bara och kunskaper i Azure Resource Manager?
10. Vad är program hanterings-eller projekt hanterings planen för migreringen?
11. Vilka är tids linjerna för migreringen av Azure Resource Manager och andra relaterade teknik vägar Maps?  Är de optimalt justerade?

### <a name="patterns-of-success"></a>Slutförda mönster

Kunder som lyckades har detaljerade planer där föregående frågor diskuteras, dokumenteras och regleras.  Se till att migrerings planerna kommunicerar brett till sponsorer och intressenter.  Utrusta dig med kunskaper om dina migrerings alternativ. Vi rekommenderar starkt att du läser igenom den här gruppen för migrering nedan.

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

### <a name="pitfalls-to-avoid"></a>Fall GRO par för att undvika

- Det gick inte att planera.  Teknik stegen för den här migreringen är beprövade och resultatet är förutsägbart.
- Förutsätter att plattformar som stöds av migrerings-API: n kommer att användas för alla scenarier. Läs de [funktioner och konfigurationer som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) för att förstå vilka scenarier som stöds.
- Planera inte potentiella program avbrott för slutanvändare.  Planera tillräckligt med buffert för att varna slutanvändarna om potentiellt otillgänglig program tid.


## <a name="lab-test"></a>Labb test

**Replikera din miljö och gör en testmigrering**
  > [!NOTE]
  > Den exakta replikeringen av din befintliga miljö utförs med hjälp av ett community-verktyg som inte stöds av Microsoft Support. Därför är det ett **valfritt** steg, men det är det bästa sättet att ta reda på problem utan att behöva röra produktions miljöer. Om du inte använder ett community-verktyg som inte är ett alternativ läser du rekommendationen validera/Förbered/Avbryt torr körning nedan.
  >

  Att utföra ett labb test av ditt exakta scenario (beräkning, nätverk och lagring) är det bästa sättet att säkerställa en smidig migrering. På så sätt kan du se till att:

- Ett helt separat labb eller en befintlig icke-produktions miljö att testa. Vi rekommenderar ett helt separat labb som kan migreras upprepade gånger och kan ändras destruktivt.  Skript för att samla in/torka metadata från de verkliga prenumerationerna visas nedan.
- Det är en bra idé att skapa labbet i en separat prenumeration. Anledningen är att labbet går bort upprepade gånger och att en separat, isolerad prenumeration minskar risken för att ett verkligt objekt tas bort av misstag.

  Detta kan åstadkommas med hjälp av AsmMetadataParser-verktyget. [Läs mer om det här verktyget här](https://github.com/Azure/classic-iaas-resourcemanager-migration/tree/master/AsmToArmMigrationApiToolset)

### <a name="patterns-of-success"></a>Slutförda mönster

Följande problem upptäcktes i många av de större migreringarna. Detta är inte en fullständig lista och du bör se de [funktioner och konfigurationer som inte stöds](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#unsupported-features-and-configurations) för mer information.  Du kanske inte kan stöta på de här tekniska problemen, men om du löser dessa innan du försöker migreringen ser du till att det blir en smidigare upplevelse.

- **Gör en verifiering/Förbered/Avbryt torr körning** – detta är kanske det viktigaste steget för att säkerställa att klassisk Azure Resource Manager migreringen lyckades. Migrerings-API: et har tre huvudsakliga steg: validera, Förbered och genomför. Validate läser den klassiska miljöns tillstånd och returnerar resultatet av alla problem. Men eftersom vissa problem kan finnas i Azure Resource Managers stacken kommer verifieringen inte att fånga allt. Nästa steg i migreringsprocessen hjälper förberedelsen att exponera dessa problem. PREPARE flyttar metadata från klassisk till Azure Resource Manager, men kommer inte att bekräfta och kommer inte att ta bort eller ändra något på den klassiska sidan. Den torra körningen innebär att förbereda migreringen och sedan avbryta (**genomför inte**) migreringen Förbered. Målet med att validera/förbereda/avbryta torr körning är att se alla metadata i Azure Resource Manager-stacken, granska den (*program mässigt eller i portalen*) och kontrol lera att allting migreras korrekt och att det fungerar genom tekniska problem.  Du får också en uppfattning om varaktigheten för migreringen så att du kan planera för stillestånds tid.  Att verifiera/förbereda/avbryta orsakar inga avbrott i användaren. Därför är det inte störningar på program användningen.
  - Objekten nedan måste lösas före den torra körningen, men ett torrt körnings test kommer också att på ett säkert sätt tömma dessa förberedelse steg om de saknas. Under företagets migrering har vi påträffat den torra körningen så att den är ett säkert och värdefullt sätt att säkerställa migreringens beredskap.
  - När förberedelsen är igång låses kontroll planet (Azure-hanterings åtgärder) för hela det virtuella nätverket, så inga ändringar kan göras i VM-metadata under verifiering/Förbered/Avbryt.  Men annars påverkas inte alla program funktioner (RD, VM-användning osv.).  Användare av de virtuella datorerna vet inte att den torra körningen körs.

- **ExpressRoute-kretsar och VPN**. För närvarande går det inte att migrera gatewayer med behörighets länkar utan drift avbrott. För att lösa problemet kan du läsa mer i [migrera ExpressRoute-kretsar och associerade virtuella nätverk från den klassiska distributions modellen till Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md).

- **VM-tillägg** – tillägg för virtuella datorer är potentiellt ett av de största hindrenen för att migrera virtuella datorer som körs. Reparationen av VM-tillägg kan ta en gräns på 1-2 dagar, så planera detta.  En fungerande Azure-agent krävs för att rapportera om status för VM-tillägg som körs på virtuella datorer. Om statusen kommer tillbaka som felaktig för en virtuell dator som körs stoppar migreringen. Själva agenten behöver inte vara i arbets ordning för att aktivera migrering, men om tillägg finns på den virtuella datorn, kommer både en fungerande agent och utgående Internet anslutning (med DNS) att behövas för att migreringen ska gå framåt.
  - Om anslutningen till en DNS-server går förlorad under migreringen går alla VM-tillägg utom BGInfo version 1. \* måste först tas bort från varje virtuell dator innan migreringen förbereds och sedan läggas tillbaka till den virtuella datorn igen efter Azure Resource Manager migreringen.  **Detta gäller endast för virtuella datorer som kör.**  Om de virtuella datorerna är avallokerade behöver inte VM-tillägg tas bort.

  > [!NOTE]
  > Många tillägg som Azure Diagnostics och Security Center-övervakning kommer att återinstallera sig själva efter migreringen, så det är inte ett problem med att ta bort dem.

  - Se också till att nätverks säkerhets grupper inte begränsar utgående Internet åtkomst. Detta kan hända med vissa inställningar för nätverks säkerhets grupper. Utgående Internet åtkomst (och DNS) krävs för att VM-tillägg ska migreras till Azure Resource Manager.
  - Det finns två versioner av BGInfo-tillägget och kallas version 1 och 2.  

      - Om den virtuella datorn använder tillägget BGInfo version 1 kan du lämna det här tillägget som det är. Migrerings-API: n hoppar över tillägget. BGInfo-tillägget kan läggas till efter migreringen.
      - Om den virtuella datorn använder det JSON-baserade BGInfo version 2-tillägget skapades den virtuella datorn med hjälp av Azure Portal. Migrations-API: et inkluderar tillägget i migreringen till Azure Resource Manager, förutsatt att agenten fungerar och har utgående Internet åtkomst (och DNS).

  - **Reparations alternativ 1**. Om du vet att dina virtuella datorer inte har utgående Internet åtkomst, en fungerande DNS-tjänst och arbetar med Azure-agenter på de virtuella datorerna avinstallerar du alla VM-tillägg som en del av migreringen innan du förbereder och installerar sedan om VM-tilläggen efter migreringen.
  - **Reparations alternativ 2**. Om de virtuella dator tilläggen är för stora av en Hurdle är ett annat alternativ att stänga av/frigöra alla virtuella datorer före migrering. Migrera de friallokerade virtuella datorerna och starta sedan om dem på Azure Resource Managers sidan. Fördelen här är att VM-tilläggen migreras. Nack delen är att alla offentliga virtuella IP-adresser går förlorade (detta kan vara ett icke-startprogram) och självklart att de virtuella datorerna stängs av, vilket ger en mycket större inverkan på arbets programmen.

    > [!NOTE]
    > Om en Azure Security Center-princip har kon figurer ATS mot de virtuella datorer som migreras måste säkerhets principen stoppas innan tillägg tas bort, annars installeras säkerhets övervaknings tillägget om automatiskt på den virtuella datorn efter att den tagits bort.

- **Tillgänglighets uppsättningar** – för att ett virtuellt nätverk (vNet) ska migreras till Azure Resource Manager måste den klassiska distributions tjänsten (t. ex. moln tjänst) som innehåller virtuella datorer finnas i en tillgänglighets uppsättning, eller så måste de virtuella datorerna inte finnas i någon tillgänglighets uppsättning. Att ha mer än en tillgänglighets uppsättning i moln tjänsten är inte kompatibel med Azure Resource Manager och stoppar migreringen.  Dessutom kan det inte finnas några virtuella datorer i en tillgänglighets uppsättning och vissa virtuella datorer inte är i en tillgänglighets uppsättning. För att lösa detta måste du åtgärda eller blanda din moln tjänst.  Planera därför att det kan ta lång tid.

- **Distributioner av webb** -och arbets roller – Cloud Services som innehåller webb-och arbets roller kan inte migreras till Azure Resource Manager. Om du vill migrera innehållet i dina webb-och arbets roller måste du migrera koden till nyare PaaS App Services (den här diskussionen ligger utanför det här dokumentets omfattning). Om du vill lämna rollerna webb/arbetare som de är men migrera klassiska virtuella datorer till Resource Manager-distributions modellen, måste webb-och arbets rollerna först tas bort från det virtuella nätverket innan migreringen kan starta.  En typisk lösning är att bara flytta webb-och arbets Rolls instanser till ett separat klassiskt virtuellt nätverk som också är länkat till en ExpressRoute-krets. I det tidigare omdistributions fallet skapar du ett nytt klassiskt virtuellt nätverk, flyttar/distribuerar om webb-och arbets rollerna till det nya virtuella nätverket och tar sedan bort distributionerna från det virtuella nätverk som flyttas. Inga kod ändringar krävs. Den nya [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md) -funktionen kan användas för att koppla samman det klassiska virtuella nätverket som innehåller webb-och arbets roller och andra virtuella nätverk i samma Azure-region, till exempel det virtuella nätverk som migreras (efter att det**virtuella nätverket har migrerats som peer-kopplade virtuella nätverk kan inte migreras**), och därför ger samma funktioner utan prestanda förlust och ingen fördröjning/bandbredds avgifter. Förutom att [Virtual Network peering](../../virtual-network/virtual-network-peering-overview.md)kan distributioner av webb-och arbets roller nu enkelt begränsas och inte hindra migreringen till Azure Resource Manager.

- **Azure Resource Manager kvoter** – Azure-regioner har separata kvoter/gränser för både klassisk och Azure Resource Manager. Även om den nya maskin varan för migrering inte används *(vi håller på att byta ut befintliga virtuella datorer från klassisk till Azure Resource Manager)*, måste Azure Resource Manager kvoter vara på plats med tillräckligt med kapacitet innan migreringen kan starta. Nedan visas de viktigaste gränserna som vi har sett orsaker till problem.  Öppna ett kvot support ärende om du vill höja gränsen.

    > [!NOTE]
    > Dessa gränser måste höjas i samma region som din aktuella miljö som ska migreras.
    >

  - Nätverksgränssnitt
  - Lastbalanserare
  - Offentliga IP-adresser
  - Statiska offentliga IP-adresser
  - Kärnor
  - Nätverkssäkerhetsgrupper
  - Routningstabeller

    Du kan kontrol lera dina nuvarande Azure Resource Manager kvoter med hjälp av följande kommandon med den senaste versionen av Azure PowerShell.



    **Compute** *(kärnor, tillgänglighets uppsättningar)*

    ```powershell
    Get-AzVMUsage -Location <azure-region>
    ```

    **Nätverk** *(virtuella nätverk, statiska offentliga IP-adresser, offentliga IP-adresser, nätverks säkerhets grupper, nätverks gränssnitt, belastnings utjämning, väg tabeller)*

    ```powershell
    Get-AzUsage /subscriptions/<subscription-id>/providers/Microsoft.Network/locations/<azure-region> -ApiVersion 2016-03-30 | Format-Table
    ```

    **Lagring** *(lagrings konto)*

    ```powershell
    Get-AzStorageUsage
    ```

- **Begränsningar för Azure Resource Manager-API-begränsning** – om du har en stor tillräckligt stor miljö (t. ex. > 400 virtuella datorer i ett VNET) kan du trycka på standard begränsningarna för API-begränsning för skrivningar (för närvarande `1200 writes/hour` ) i Azure Resource Manager. Innan du påbörjar migreringen bör du anskaffa ett support ärende för att öka den här gränsen för din prenumeration.


- **Tids gränsen har nåtts för etablering av VM-status** – om en virtuell dator har statusen `provisioning timed out` måste detta lösas före migrering. Det enda sättet att göra detta är med nedtid genom att ta bort/etablera den virtuella datorn (ta bort den, Behåll disken och återskapa den virtuella datorn).

- **Status för ROLESTATEUNKNOWN VM** – om migreringen stoppas på grund av ett `role state unknown` fel meddelande, kontrol lera den virtuella datorn med portalen och se till att den körs. Det här felet uppstår vanligt vis på egen hand (ingen åtgärd krävs) efter några minuter och är ofta en tillfällig typ som ofta visas under en virtuell dator `start` , `stop` och `restart` åtgärder. **Rekommenderad praxis:** försök att migrera igen om några minuter.

- **Fabric-klustret finns inte** – i vissa fall går det inte att migrera vissa virtuella datorer för olika udda orsaker. En av dessa kända fall är om den virtuella datorn nyligen har skapats (under den senaste veckan eller så) och hände med att landa ett Azure-kluster som ännu inte är utrustat för Azure Resource Manager arbets belastningar.  Du får ett fel meddelande som säger att `fabric cluster does not exist` den virtuella datorn inte kan migreras. Att vänta några dagar kommer vanligt vis att lösa detta problem eftersom klustret snart kommer att få Azure Resource Manager aktiverat. En omedelbar lösning är dock till `stop-deallocate` den virtuella datorn och fortsätter sedan med migreringen och startar den virtuella datorn i Azure Resource Manager efter migreringen.

### <a name="pitfalls-to-avoid"></a>Fall GRO par för att undvika

- Ta inte med genvägar och utelämna migreringen verifiera/Förbered/Avbryt torr körning.
- De flesta, om inte alla, de potentiella problemen kommer att finnas på ytan under validering/preping/abort-stegen.

## <a name="migration"></a>Migrering

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Nu är du redo eftersom du har arbetat igenom de kända problemen med din miljö.

För de verkliga migreringarna kanske du vill överväga följande:

1. Planera och Schemalägg det virtuella nätverket (minsta enhet för migrering) med ökande prioritet.  Gör enkla virtuella nätverk först och fortskrider med de mer komplicerade virtuella nätverken.
2. De flesta kunder har icke-produktions-och produktions miljöer.  Schemalägg produktion senast.
3. **(Valfritt)** Schemalägg en stillestånds tid för underhåll med massor av buffert om oväntade problem uppstår.
4. Kommunicera med och anpassa till support teamen om problem uppstår.

### <a name="patterns-of-success"></a>Slutförda mönster

Den tekniska vägledningen från _labb test_ avsnittet bör övervägas och minimeras före en riktig migrering.  Vid lämplig testning är migreringen i själva verket en icke-händelse.  För produktions miljöer kan det vara bra att ha ytterligare support, till exempel en betrodd Microsoft-partner eller Microsoft Premier-tjänster.

### <a name="pitfalls-to-avoid"></a>Fall GRO par för att undvika

Att inte helt testa kan orsaka problem och fördröjning i migreringen.  

## <a name="beyond-migration"></a>Bortom migreringen

### <a name="technical-considerations-and-tradeoffs"></a>Tekniska överväganden och kompromisser

Nu när du är i Azure Resource Manager maximerar du plattformen.  Läs [översikten över Azure Resource Manager](../../azure-resource-manager/management/overview.md) för att få mer information om ytterligare förmåner.

Saker att tänka på:

- Sammankoppla migreringen till andra aktiviteter.  De flesta kunder väljer ett program underhålls fönster.  I så fall kanske du vill använda den här stillestånds tiden för att aktivera andra Azure Resource Manager funktioner som kryptering och migrering till Managed Disks.
- Gå tillbaka till tekniska och affärsmässiga grunder för Azure Resource Manager. aktivera ytterligare tjänster som bara är tillgängliga på Azure Resource Manager som gäller för din miljö.
- Modernisera din miljö med PaaS-tjänster.

### <a name="patterns-of-success"></a>Slutförda mönster

Var ändamålsenlig på vilka tjänster du nu vill aktivera i Azure Resource Manager.  Många kunder hittar de nedan övertygande i sina Azure-miljöer:

- [Rollbaserad åtkomst kontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md).
- [Azure Resource Manager mallar för enklare och mer kontrollerad distribution](../../azure-resource-manager/templates/overview.md).
- [Taggar](../../azure-resource-manager/management/tag-resources.md).
- [Aktivitets kontroll](../../azure-resource-manager/management/view-activity-logs.md)
- [Azure-principer](../../governance/policy/overview.md)

### <a name="pitfalls-to-avoid"></a>Fall GRO par för att undvika

Kom ihåg varför du startade den här klassiska för att Azure Resource Manager migreringen.  Vad var de ursprungliga affärs orsakerna? Har du uppnått affärs orsaken?


## <a name="next-steps"></a>Nästa steg

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager](../migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway klassisk till Resource Manager-migrering](../../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrera ExpressRoute-kretsar och tillhör ande virtuella nätverk från den klassiska distributions modellen till Resource Manager](../../expressroute/expressroute-migration-classic-resource-manager.md)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](../migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
