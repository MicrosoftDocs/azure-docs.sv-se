---
title: Klassisk teknik för Azure Resource Manager migrering av tekniska djup
description: Teknisk djupgående om plattforms oberoende migrering av resurser från den klassiska distributions modellen till Azure Resource Manager.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 6f633a585e4fa6ebd12e8d12408847b5ee758855
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88513205"
---
# <a name="technical-deep-dive-on-platform-supported-migration-from-classic-to-azure-resource-manager"></a>En teknisk djupdykning i plattformsstödd migrering från klassisk distribution till Azure Resource Manager

> [!IMPORTANT]
> Idag, cirka 90% av virtuella IaaS-datorer använder [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Från och med den 28 februari 2020 har klassiska virtuella datorer föråldrats och kommer att dras tillbaka den 1 mars 2023. [Läs mer]( https://aka.ms/classicvmretirement) om den här utfasningen och [hur den påverkar dig](./classic-vm-deprecation.md#how-does-this-affect-me).

Låt oss ta en titt på migreringen från den klassiska distributions modellen i Azure till Azure Resource Manager distributions modell. Vi tittar på resurser på en resurs-och funktions nivå för att hjälpa dig att förstå hur Azure-plattformen migrerar resurser mellan de två distributions modellerna. Mer information finns i artikeln om service meddelande:

* För Linux: [plattform som stöds migrering av IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* För Windows:  [plattform som stöds migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="migrate-iaas-resources-from-the-classic-deployment-model-to-azure-resource-manager"></a>Migrera IaaS-resurser från den klassiska distributions modellen till Azure Resource Manager
Först är det viktigt att förstå skillnaden mellan data planet och hanterings Plans åtgärder på infrastruktur som en tjänst (IaaS) resurser.

* *Hantering/kontroll plan* beskriver de anrop som ingår i hanterings-och kontroll planet eller API: et för att ändra resurser. Åtgärder som exempelvis skapa en virtuell dator, starta om en virtuell dator och uppdatera ett virtuellt nätverk med ett nytt undernät, hanterar de resurser som körs. De påverkar inte att ansluta till de virtuella datorerna direkt.
* *Data planet* (program) beskriver körningen av själva programmet och involverar interaktion med instanser som inte går via Azure-API: et. Till exempel, att komma åt din webbplats eller hämta data från en SQL Server instans eller en MongoDB-Server, är data planet eller program interaktioner. Andra exempel är att kopiera en BLOB från ett lagrings konto och komma åt en offentlig IP-adress för att använda Remote Desktop Protocol (RDP) eller Secure Shell (SSH) i den virtuella datorn. Med de här åtgärderna kan programmet köras i beräknings-, nätverks- och lagringsresurser.

Data planet är detsamma mellan den klassiska distributions modellen och Resource Manager-stackarna. Skillnaden är att under migreringsprocessen översätter Microsoft sin representation av resurser från den klassiska distributions modellen till den i Resource Manager-stacken. Därför måste du använda nya verktyg, API: er och SDK: er för att hantera dina resurser i Resource Manager-stacken.

![Diagram som visar skillnaden mellan hanterings-och kontroll planet och data planet](~/articles/virtual-machines/media/virtual-machines-windows-migration-classic-resource-manager/data-control-plane.png)


> [!NOTE]
> I vissa migreringsfall stoppas, frigörs och startas dina virtuella datorer om av Azure-plattformen. Detta leder till ett kort avbrott i data planet.
>

## <a name="the-migration-experience"></a>Migreringen
Innan du påbörjar migreringen:

* Se till att inga funktioner eller konfigurationer som inte stöds används av de resurser du vill migrera. Vanligtvis upptäcks de här problemen och plattformen genererar ett fel.
* Om du har virtuella datorer som inte finns i ett virtuellt nätverk, stoppas de och frigörs som en del av förberedelse åtgärden. Om du inte vill förlora den offentliga IP-adressen bör du överväga att reservera IP-adressen innan du utlöser förberedelse åtgärden. Om de virtuella datorerna finns i ett virtuellt nätverk stoppas de inte och frigörs.
* Lägg migreringen utanför kontorstid för att lättare kunna hantera eventuella fel som kan uppstå.
* Ladda ned den aktuella konfigurationen av dina virtuella datorer med hjälp av PowerShell, CLI-kommandon (kommandoradsgränssnitt) eller REST-API:er för att underlätta verifieringen när förberedelsesteget är klart.
* Uppdatera Automation-och driftsättning-skripten för att hantera distributions modellen för Resource Manager innan du påbörjar migreringen. Du kan också utföra GET-åtgärder när resurserna är i förberedelsefasen.
* Utvärdera de rollbaserade principerna för Access Control (RBAC) som är konfigurerade på IaaS-resurserna i den klassiska distributions modellen och planera för när migreringen är klar.

Arbets flödet för migrering ser ut så här:

![Diagram som visar arbets flödet för migrering](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-workflow.png)

> [!NOTE]
> De åtgärder som beskrivs i följande avsnitt är alla idempotenta. Om du har problem med en funktion som inte stöds eller ett konfigurations fel kan du försöka förbereda igen, avbryta eller bekräfta. Azure försöker utföra åtgärden igen.
>
>

### <a name="validate"></a>Verifiera
Verifiering är det första steget under migreringen. Målet med det här steget är att analysera statusen för de resurser som du vill migrera i den klassiska distributions modellen. Åtgärden utvärderar om resurserna kan migreras (lyckades eller Miss lyckas).

Du väljer det virtuella nätverket eller en moln tjänst (om det inte finns i ett virtuellt nätverk) som du vill validera för migrering. Om resursen inte kan migreras, visar Azure orsaken till varför.

#### <a name="checks-not-done-in-the-validate-operation"></a>Kontrollerna har inte utförts under verifieringen

I validate-åtgärden analyseras endast statusen för resurserna i den klassiska distributions modellen. Den kan kontrol lera om det finns några problem och scenarier som inte stöds på grund av olika konfigurationer i den klassiska distributions modellen. Det går inte att söka efter alla problem som Azure Resource Manager stacken kan ha på resurserna under migreringen. De här problemen kontrol leras bara när resurserna genomgår omvandling i nästa steg i migreringen (förberedelse åtgärden). I följande tabell visas alla problem som inte har checkats in i validerings åtgärden:


|Nätverks kontroller som inte är i verifierings åtgärden|
|-|
|Ett virtuellt nätverk som har både ER-och VPN-gatewayer.|
|En anslutning till en virtuell nätverksgateway i frånkopplat läge.|
|Alla ER-kretsar förmigreras i förväg till Azure Resource Manager stack.|
|Azure Resource Manager kvot kontroller för nätverks resurser. Till exempel: statisk offentlig IP, dynamiska offentliga IP-adresser, belastningsutjämnare, nätverks säkerhets grupper, väg tabeller och nätverks gränssnitt. |
| Alla belastnings Utjämnings regler är giltiga mellan distribution och det virtuella nätverket. |
| Motstridiga privata IP-adresser mellan stoppade och avallokerade virtuella datorer i samma virtuella nätverk. |

### <a name="prepare"></a>Förbereda
Förberedelse är det andra steget under migreringen. Målet med det här steget är att simulera omvandlingen av IaaS-resurser från den klassiska distributions modellen till Resource Manager-resurser. Vidare presenterar förberedelse åtgärden det här sida vid sida för att visualisera.

> [!NOTE] 
> Dina resurser i den klassiska distributions modellen ändras inte under det här steget. Det är ett säkert steg att köra om du försöker utföra migreringen. 

Du väljer det virtuella nätverket eller moln tjänsten (om det inte är ett virtuellt nätverk) som du vill förbereda för migrering.

* Om resursen inte kan migreras stoppar Azure migreringen och listar varför förberedelse åtgärden misslyckades.
* Om resursen kan migreras låser Azure ned hanterings planens åtgärder för resurserna under migreringen. Du kan till exempel inte lägga till en datadisk i en virtuell dator under migreringen.

Azure startar sedan migreringen av metadata från den klassiska distributions modellen till Resource Manager för migrering av resurser.

När förberedelse åtgärden har slutförts har du möjlighet att visualisera resurserna både i den klassiska distributions modellen och Resource Manager. För varje molntjänst i den klassiska distributionsmodellen skapar Azure-plattformen ett resursgruppnamn som har mönstret `cloud-service-name>-Migrated`.

> [!NOTE]
> Det går inte att välja namnet på en resurs grupp som har skapats för migrerade resurser (dvs. "-migrerad"). När migreringen är klar kan du dock använda flytt funktionen i Azure Resource Manager för att flytta resurser till valfri resurs grupp som du vill ha. Mer information finns i [Flytta resurser till en ny resursgrupp eller prenumeration](~/articles/resource-group-move-resources.md).

Följande två skärm bilder visar resultatet efter en lyckad förberedelse åtgärd. Den första en visar en resurs grupp som innehåller den ursprungliga moln tjänsten. Den andra visar den nya resurs gruppen "-migrerad" som innehåller motsvarande Azure Resource Manager-resurser.

![Skärm bild som visar den ursprungliga moln tjänsten](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-classic.png)

![Skärm bild som visar Azure Resource Manager resurser i förberedelse åtgärden](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/portal-arm.png)

Här är en titt bakom dina resurser när förberedelse fasen har slutförts. Observera att resursen i data planet är densamma. Den visas i både hanterings planet (klassisk distributions modell) och kontroll planet (Resource Manager).

![Diagram över förberedelse fasen](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-prepare.png)

> [!NOTE]
> Virtuella datorer som inte ingår i ett virtuellt nätverk i den klassiska distributions modellen stoppas och frigörs i den här fasen av migreringen.
>

### <a name="check-manual-or-scripted"></a>Kontrollera (manuellt eller via skript)
I det här steget har du möjlighet att använda den konfiguration som du laddade ned tidigare för att kontrol lera att migreringen ser korrekt ut. Alternativt kan du logga in på portalen och kontrol lera egenskaperna och resurserna för att verifiera att migreringen av metadata ser bra ut.

Om du migrerar ett virtuellt nätverk startas inte de flesta konfigurationer av virtuella datorer om. För program på dessa virtuella datorer kan du kontrol lera att programmet fortfarande körs.

Du kan testa dina övervaknings-och drift skript för att se om de virtuella datorerna fungerar som förväntat, och om dina uppdaterade skript fungerar som de ska. Det finns bara stöd för GET-åtgärder när resurserna är i förberedelsefasen.

Det finns ingen angiven tids period innan du måste utföra migreringen. Du kan ta den tid du vill i det här läget. Dock låses hanteringsplanet för de här resurserna tills du antingen avbryter eller checkar in.

Om problem skulle uppstå kan du alltid avbryta migreringen och gå tillbaka till den klassiska distributionsmodellen. När du har gått tillbaka öppnar Azure hanterings Plans åtgärderna för resurserna så att du kan återuppta normala åtgärder på de virtuella datorerna i den klassiska distributions modellen.

### <a name="abort"></a>Avbryta
Detta är ett valfritt steg om du vill återställa ändringarna till den klassiska distributions modellen och avbryta migreringen. Den här åtgärden tar bort Resource Manager-metadata (som skapats i förberedelse steget) för dina resurser. 

![Diagram över abort-steg](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-abort.png)


> [!NOTE]
> Det går inte att utföra den här åtgärden när commit-åtgärden har Aktiver ATS.     
>

### <a name="commit"></a>Checka in
När verifieringen är klar kan du checka in migreringen. Resurser visas inte längre i den klassiska distributions modellen och är bara tillgängliga i distributions modellen för Resource Manager. De migrerade resurserna kan bara hanteras i den nya portalen.

> [!NOTE]
> Det här är en idempotent åtgärd. Försök igen om det Miss lyckas. Om det fortfarande inte fungerar kan du skapa ett support ärende eller skapa ett forum på [Microsoft Q&a](https://docs.microsoft.com/answers/index.html)
>
>

![Diagram över genomförande steg](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/behind-the-scenes-commit.png)

## <a name="migration-flowchart"></a>Flödes schema för migrering

Här är ett flödes schema som visar hur du fortsätter med migreringen:

![Skärmbild som visar migreringsstegen](~/articles/virtual-machines/windows/media/migration-classic-resource-manager/migration-flow.png)

## <a name="translation-of-the-classic-deployment-model-to-resource-manager-resources"></a>Översättning av den klassiska distributions modellen till Resource Manager-resurser
Du hittar den klassiska distributions modellen och resurs hanterarens representationer av resurserna i följande tabell. Andra funktioner och resurser stöds för närvarande inte.

| Klassiskt läge | Resource Manager-läge | Anteckningar |
| --- | --- | --- |
| Molntjänstens namn |DNS-namn |Under migreringen skapas en ny resursgrupp för varje molntjänst med namngivningsmönstret `<cloudservicename>-migrated`. Den här resursgruppen innehåller alla dina resurser. Namnet på molntjänsten blir ett DNS-namn som kopplas till den offentliga ip-adressen. |
| Virtuell dator |Virtuell dator |Egenskaper för virtuella datorer migreras oförändrade. Vissa osProfile-uppgifter, t. ex. dator namn, lagras inte i den klassiska distributions modellen och förblir tomma efter migreringen. |
| Diskresurser anslutna till den virtuella datorn |Implicita diskar anslutna till den virtuella datorn |Diskarna utformas inte som toppresurser i Resource Manager-distributionsmodellen. De migreras som implicita diskar för den virtuella datorn. För närvarande finns bara stöd för diskar som är anslutna till en virtuell dator. Virtuella Resource Manager-datorer kan nu använda lagrings konton i den klassiska distributions modellen, vilket gör att diskarna lätt kan migreras utan några uppdateringar. |
| VM-tillägg |VM-tillägg |Alla resurstillägg utom XML-tillägg migreras från den klassiska distributionsmodellen. |
| Certifikat för virtuell dator |Certifikat i Azure Key Vault |Om en moln tjänst innehåller tjänst certifikat skapar migreringen ett nytt Azure Key Vault per moln tjänst och flyttar certifikaten till nyckel valvet. De virtuella datorerna uppdateras för att hänvisa till certifikaten från nyckelvalvet. <br><br> Ta inte bort nyckel valvet. Detta kan leda till att den virtuella datorn hamnar i ett felaktigt tillstånd. |
| WinRM-konfiguration |WinRM-konfiguration under osProfile |Windows Remote Management-konfigurationen flyttas oförändrad under migreringen. |
| Egenskap för tillgänglighetsuppsättning |Resurs för tillgänglighetsuppsättning | Specifikation för tillgänglighets uppsättning är en egenskap på den virtuella datorn i den klassiska distributions modellen. Under migreringen blir tillgänglighetsuppsättningar en resurs på toppnivå. Följande konfigurationer stöds inte: flera tillgänglighetsuppsättningar per molntjänst eller en eller flera tillgänglighetsuppsättningar tillsammans med virtuella datorer som inte tillhör en tillgänglighetsuppsättning i en molntjänst. |
| Nätverkskonfiguration på en virtuell dator |Primärt nätverksgränssnitt |Nätverkskonfigurationen på en virtuell dator visas som en resurs för primärt nätverksgränssnitt efter migreringen. För virtuella datorer som inte tillhör något virtuellt nätverk ändras den interna ip-adressen under migreringen. |
| Flera nätverksgränssnitt på en virtuell dator |Nätverksgränssnitt |Om en virtuell dator har flera nätverks gränssnitt associerade, blir varje nätverks gränssnitt en resurs på den översta nivån som en del av migreringen, tillsammans med alla egenskaper. |
| Belastningsutjämnad slutpunktsuppsättning |Lastbalanserare |I den klassiska distributionsmodellen kopplade plattformen en implicit lastbalanserare till varje tjänst i molnet. Under migreringen skapas en ny belastningsutjämnarresurs och den belastningsutjämnade slutpunktsuppsättningen blir belastningsutjämnarregler. |
| Ingående NAT-regler |Ingående NAT-regler |Indataslutpunkter som har angetts i den virtuella datorn omvandlas till inkommande regler för nätverksadressöversättning i lastbalanseraren under migreringen. |
| Virtuell ip-adress |Offentlig ip-adress med DNS-namn |Den virtuella IP-adressen blir en offentlig IP-adress och är associerad med belastningsutjämnaren. En virtuell ip-adress kan bara migreras om en indataslutpunkt är kopplad till den. |
| Virtuellt nätverk |Virtuellt nätverk |Det virtuella nätverket migreras, med alla egenskaper, till Resource Manager-distributionsmodellen. En ny resursgrupp skapas med namnet `-migrated`. |
| Reserverade ip-adresser |Offentlig ip-adress med fast fördelningsmetod |Reserverade ip-adresser som är kopplade till lastbalanseraren migreras tillsammans med molntjänsten eller den virtuella datorn. Ej kopplade reserverade IP-adresser kan migreras med hjälp av [Move-AzureReservedIP](/powershell/module/servicemanagement/azure.service/move-azurereservedip?view=azuresmps-4.0.0).  |
| Offentlig ip-adress per virtuell dator |Offentlig ip-adress med dynamisk fördelningsmetod |Den offentliga ip-adress som är kopplad till den virtuella datorn omvandlas som offentlig ip-adressresurs med fördelningsmetoden inställd på fas. |
| NSG:er |NSG:er |Nätverkssäkerhetsgrupper som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Nätverkssäkerhetsgruppen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för nätverkssäkerhetsgrupperna under migreringen. Det går inte att migrera en NSG: er som använder [Move-AzureNetworkSecurityGroup](/powershell/module/servicemanagement/azure.service/move-azurenetworksecuritygroup?view=azuresmps-4.0.0).|
| DNS-servrar |DNS-servrar |DNS-servrar som är kopplade till ett virtuellt nätverk eller den virtuella datorn migreras under motsvarande resursmigrering tillsammans med alla egenskaper. |
| Användardefinierade vägar |Användardefinierade vägar |Användardefinierade vägar som är kopplade till ett undernät klonas under migreringen till Resource Manager-distributionsmodellen. Den användardefinierade vägen i den klassiska distributionsmodellen tas inte bort under migreringen. Dock blockeras alla hanteringsplanåtgärder för den användardefinierade vägen under migreringen. Det går inte att migrera en UDR som använder [Move-AzureRouteTable](/powershell/module/servicemanagement/azure.service/Move-AzureRouteTable?view=azuresmps-4.0.0). |
| Egenskapen ip-vidarebefordran i en virtuell dators nätverkskonfiguration |Egenskapen ip-vidarebefordran på nätverkskortet |Egenskapen ip-vidarebefordran i en virtuell dator omvandlas till en egenskap i nätverksgränssnittet under migreringen. |
| Lastbalanserare med flera ip-adresser |Lastbalanserare med flera offentliga ip-resurser |Alla offentliga IP-adresser som är kopplade till belastningsutjämnaren konverteras till en offentlig IP-resurs och associeras med belastningsutjämnaren efter migreringen. |
| Interna DNS-namn på den virtuella datorn |Interna DNS-namn på nätverkskortet |Interna DNS-suffix för de virtuella datorerna migreras till en skrivskyddad egenskap med namnet ”InternalDomainNameSuffix” på nätverkskortet. Suffixet förblir oförändrat efter migreringen och VM-matchningen bör fortsätta att fungera som tidigare. |
| Virtuell nätverksgateway |Virtuell nätverksgateway |Egenskaperna för den virtuella Nätverksgatewayen migreras oförändrade. Den virtuella ip-adressen som är kopplad till gatewayen ändras inte heller. |
| Lokal nätverksplats |Lokal nätverksgateway |Egenskaperna för den lokala nätverks platsen migreras oförändrade till en ny resurs som kallas lokal nätverksgateway. Detta representerar lokala adressprefix och fjärrgatewayens IP-adress. |
| Anslutningsreferenser |Anslutning |Anslutnings referenser mellan gatewayen och den lokala nätverks platsen i nätverks konfigurationen representeras av en ny resurs som kallas anslutning. Alla egenskaper för anslutnings referensen i nätverks konfigurations filen kopieras oförändrade till anslutnings resursen. Anslutning mellan virtuella nätverk i den klassiska distributions modellen uppnås genom att du skapar två IPsec-tunnlar till lokala nätverks platser som representerar de virtuella nätverken. Detta omvandlas till anslutnings typen virtuell-nätverks-till-virtuell-nätverk i Resource Manager-modellen, utan att det krävs lokala Nätverksgatewayen. |

## <a name="changes-to-your-automation-and-tooling-after-migration"></a>Ändringar av automatiseringstjänster och verktyg efter migreringen
Som en del av migreringen av resurser från den klassiska distributions modellen till Resource Manager-distributions modellen måste du uppdatera din befintliga automatisering eller verktyg för att säkerställa att den fortsätter att fungera efter migreringen.


## <a name="next-steps"></a>Nästa steg

För Linux:

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](./linux/migration-classic-resource-manager-errors.md?toc=/azure/virtual-machines/linux/toc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

För Windows:

* [Översikt över migrering av plattformar som stöds av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Planera för migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd PowerShell för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Använd CLI för att migrera IaaS-resurser från klassisk till Azure Resource Manager](./linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [VPN Gateway klassisk till Resource Manager-migrering](../vpn-gateway/vpn-gateway-classic-resource-manager-migration.md)
* [Migrera ExpressRoute-kretsar och tillhör ande virtuella nätverk från den klassiska distributions modellen till Resource Manager](../expressroute/expressroute-migration-classic-resource-manager.md)
* [Community-verktyg för att hjälpa till med migrering av IaaS-resurser från klassisk till Azure Resource Manager](./windows/migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste migreringsfelen](./windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Granska de vanligaste frågorna om migrering av IaaS-resurser från klassisk till Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
