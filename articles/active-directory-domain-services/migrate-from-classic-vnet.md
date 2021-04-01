---
title: Migrera Azure AD Domain Services från ett klassiskt virtuellt nätverk | Microsoft Docs
description: Lär dig hur du migrerar en befintlig Azure AD Domain Services hanterad domän från den klassiska virtuella nätverks modellen till ett Resource Manager-baserat virtuellt nätverk.
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 09/24/2020
ms.author: justinha
ms.openlocfilehash: 694ed5304e838057141b7df043565d58188fc870
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013047"
---
# <a name="migrate-azure-active-directory-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>Migrera Azure Active Directory Domain Services från den klassiska virtuella nätverks modellen till Resource Manager

Azure Active Directory Domain Services (Azure AD DS) stöder en engångs flytt för kunder som för närvarande använder den klassiska virtuella nätverks modellen till den virtuella nätverks modellen i Resource Manager. Azure AD DS-hanterade domäner som använder distributions modellen för Resource Manager ger ytterligare funktioner, till exempel detaljerade lösen ords principer, gransknings loggar och konto utelåsnings skydd.

Den här artikeln beskriver överväganden för migrering, och sedan de steg som krävs för att migrera en befintlig hanterad domän. Några av fördelarna finns i [fördelar med migreringen från den klassiska distributions modellen för Resource Manager i Azure AD DS][migration-benefits].

> [!NOTE]
> I 2017 blev Azure AD Domain Services tillgänglig som värd i ett Azure Resource Manager nätverk. Sedan dess har vi kunnat bygga en säkrare tjänst med Azure Resource Manager moderna funktioner. Eftersom Azure Resource Manager distributioner fullständigt ersätter klassiska distributioner, kommer Azure AD DS klassiska virtuella nätverks distributioner att dras tillbaka den 1 mars 2023.
>
> Mer information finns i meddelandet om [officiellt utfasnings meddelande](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/).

## <a name="overview-of-the-migration-process"></a>Översikt över migreringsprocessen

Migreringsprocessen tar en befintlig hanterad domän som körs i ett klassiskt virtuellt nätverk och flyttar den till ett befintligt virtuellt Resource Manager-nätverk. Migreringen utförs med PowerShell och har två huvud stadier för körning: *förberedelse* och *migrering*.

![Översikt över migreringsprocessen för Azure AD DS](media/migrate-from-classic-vnet/migration-overview.png)

I *förberedelse* fasen tar Azure AD DS en säkerhets kopia av domänen för att få den senaste ögonblicks bilden av användare, grupper och lösen ord som synkroniseras med den hanterade domänen. Synkroniseringen inaktive ras sedan och den moln tjänst som är värd för den hanterade domänen tas bort. Under förberedelse fasen kan inte den hanterade domänen autentisera användare.

![Förberedelse steg för att migrera Azure AD DS](media/migrate-from-classic-vnet/migration-preparation.png)

I *migreringstabellen* kopieras de underliggande virtuella diskarna för domän kontrol Lanterna från den klassiska hanterade domänen till att skapa de virtuella datorerna med hjälp av distributions modellen för Resource Manager. Den hanterade domänen återskapas sedan, vilket inkluderar LDAPs och DNS-konfigurationen. Synkronisering till Azure AD startas om och LDAP-certifikat återställs. Det finns inget behov av att ansluta datorer till en hanterad domän – de fortsätter att vara anslutna till den hanterade domänen och körs utan ändringar.

![Migrering av Azure AD DS](media/migrate-from-classic-vnet/migration-process.png)

## <a name="example-scenarios-for-migration"></a>Exempel scenarier för migrering

Några vanliga scenarier för migrering av en hanterad domän är bland annat följande exempel.

> [!NOTE]
> Konvertera inte det klassiska virtuella nätverket förrän du har bekräftat en lyckad migrering. Att konvertera det virtuella nätverket tar bort alternativet att återställa eller återställa den hanterade domänen om det uppstår några problem under migreringen och verifierings faserna.

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Migrera Azure AD DS till ett befintligt virtuellt Resource Manager-nätverk (rekommenderas)

Ett vanligt scenario är att du redan har flyttat andra befintliga klassiska resurser till en distributions modell för Resource Manager och ett virtuellt nätverk. Peering används sedan från det virtuella Resource Manager-nätverket till det klassiska virtuella nätverket som fortsätter att köra Azure AD DS. Den här metoden gör att Resource Manager-program och-tjänster använder autentiserings-och hanterings funktionerna i den hanterade domänen i det klassiska virtuella nätverket. När den har migrerats körs alla resurser med distributions modellen för Resource Manager och det virtuella nätverket.

![Migrera Azure AD DS till ett befintligt virtuellt Resource Manager-nätverk](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

De avancerade steg som ingår i det här exemplet för migrering omfattar följande delar:

1. Ta bort befintliga VPN-gatewayer eller peering för virtuella nätverk som kon figurer ATS i det klassiska virtuella nätverket.
1. Migrera den hanterade domänen med hjälp av stegen som beskrivs i den här artikeln.
1. Testa och bekräfta en lyckad migrering och ta sedan bort det klassiska virtuella nätverket.

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Migrera flera resurser inklusive Azure AD DS

I det här exempel scenariot migrerar du Azure AD DS och andra associerade resurser från den klassiska distributions modellen till distributions modellen för Resource Manager. Om vissa resurser fortsätter att köras i det klassiska virtuella nätverket tillsammans med den hanterade domänen, kan de alla ha nytta av att migrera till distributions modellen för Resource Manager.

![Migrera flera resurser till distributions modellen för Resource Manager](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

De avancerade steg som ingår i det här exemplet för migrering omfattar följande delar:

1. Ta bort befintliga VPN-gatewayer eller peering för virtuella nätverk som kon figurer ATS i det klassiska virtuella nätverket.
1. Migrera den hanterade domänen med hjälp av stegen som beskrivs i den här artikeln.
1. Konfigurera virtuell nätverks-peering mellan det klassiska virtuella nätverket och Resource Manager-nätverket.
1. Testa och bekräfta en lyckad migrering.
1. [Flytta ytterligare klassiska resurser som virtuella datorer][migrate-iaas].

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Migrera Azure AD DS men behåll andra resurser i det klassiska virtuella nätverket

I det här exempel scenariot har du minsta möjliga stillestånds tid i en session. Du migrerar bara Azure AD DS till ett virtuellt Resource Manager-nätverk och behåller befintliga resurser i den klassiska distributions modellen och det virtuella nätverket. I en följande underhålls period kan du migrera ytterligare resurser från den klassiska distributions modellen och det virtuella nätverket som du vill.

![Migrera endast Azure AD DS till distributions modellen för Resource Manager](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

De avancerade steg som ingår i det här exemplet för migrering omfattar följande delar:

1. Ta bort befintliga VPN-gatewayer eller peering för virtuella nätverk som kon figurer ATS i det klassiska virtuella nätverket.
1. Migrera den hanterade domänen med hjälp av stegen som beskrivs i den här artikeln.
1. Konfigurera peering för virtuellt nätverk mellan det klassiska virtuella nätverket och det nya virtuella Resource Manager-nätverket.
1. Migrera sedan [de ytterligare resurserna][migrate-iaas] från det klassiska virtuella nätverket efter behov.

## <a name="before-you-begin"></a>Innan du börjar

När du förbereder och sedan migrerar en hanterad domän finns det några överväganden kring tillgängligheten för autentiserings-och hanterings tjänster. Den hanterade domänen är inte tillgänglig under en viss tids period under migreringen. Program och tjänster som är beroende av drift störningar i Azure AD DS-upplevelsen under migreringen.

> [!IMPORTANT]
> Läs all den här artikel och vägledning för migrering innan du påbörjar migreringsprocessen. Migreringsprocessen påverkar tillgängligheten för Azure AD DS-domänkontrollanter under tids perioder. Användare, tjänster och program kan inte autentisera mot den hanterade domänen under migreringsprocessen.

### <a name="ip-addresses"></a>IP-adresser

Domänkontrollantens IP-adresser för en hanterad domän ändras efter migreringen. Den här ändringen omfattar den offentliga IP-adressen för den säkra LDAP-slutpunkten. De nya IP-adresserna ligger inom adress intervallet för det nya under nätet i det virtuella Resource Manager-nätverket.

Om du behöver återställa igen kan IP-adresserna ändras efter att de återställts.

Azure AD DS använder vanligt vis de två första tillgängliga IP-adresserna i adress intervallet, men detta är inte garanterat. Du kan för närvarande inte ange de IP-adresser som ska användas efter migreringen.

### <a name="downtime"></a>Driftstopp

Migreringsprocessen innebär att domän kontrol Lanterna är offline under en viss tids period. Det går inte att komma åt domän kontrol Lanterna när Azure AD DS migreras till distributions modellen för Resource Manager och det virtuella nätverket.

I genomsnitt är nedtid cirka 1 till 3 timmar. Den här tids perioden är från när domän kontrol Lanterna kopplas från till den tidpunkt då den första domänkontrollanten kommer tillbaka online. Detta genomsnitt omfattar inte den tid det tar för den andra domänkontrollanten att replikeras, eller hur lång tid det tar att migrera ytterligare resurser till distributions modellen för Resource Manager.

### <a name="account-lockout"></a>Konto utelåsning

Hanterade domäner som körs på klassiska virtuella nätverk har inga AD-utelåsnings principer på plats. Om de virtuella datorerna exponeras för Internet kan angriparna använda metoder för att använda lösen ord för att dela med sig av sitt sätt i konton. Det finns ingen konto utelåsnings princip för att stoppa dessa försök. För hanterade domäner som använder distributions modellen för Resource Manager och virtuella nätverk skyddar AD-konto utelåsnings principerna mot dessa angrepp vid lösen ords spridning.

Som standard är 5 dåliga lösen ords försök på 2 minuter att låsa ett konto i 30 minuter.

Det går inte att använda ett utelåst konto för att logga in, vilket kan störa möjligheten att hantera den hanterade domänen eller de program som hanteras av kontot. När en hanterad domän har migrerats kan konton uppleva vad som verkar som en permanent utelåsning på grund av upprepade misslyckade försök att logga in. Två vanliga scenarier efter migreringen är följande:

* Ett tjänst konto som använder ett lösen ord som har upphört att gälla.
    * Tjänst kontot försöker upprepade gånger logga in med ett lösen ord som har upphört att gälla, vilket låser ut kontot. Åtgärda detta genom att leta upp programmet eller den virtuella datorn med inaktuella autentiseringsuppgifter och uppdatera lösen ordet.
* En skadlig entitet använder brute-force-försök för att logga in på konton.
    * När virtuella datorer exponeras för Internet försöker angripare ofta använda vanliga användar namn och lösen ord när de försöker signera. Dessa upprepade misslyckade inloggnings försök kan låsa kontona. Du bör inte använda administratörs konton med generiska namn som *administratör* eller *administratör*, till exempel för att minimera administrativa konton från att låsas upp.
    * Minimera antalet virtuella datorer som exponeras för Internet. Du kan använda [Azure-skydds][azure-bastion] för att på ett säkert sätt ansluta till virtuella datorer med hjälp av Azure Portal.

Om du misstänker att vissa konton kan vara utelåsta efter migreringen beskriver du de slutliga stegen för att aktivera granskning eller ändra detaljerade lösen ords princip inställningar.

### <a name="roll-back-and-restore"></a>Återställa och återställa

Om migreringen inte lyckas finns det en process för att återställa eller återställa en hanterad domän. Rollback är ett självbetjänings alternativ för att omedelbart returnera statusen för den hanterade domänen till innan migreringen. Azure-support tekniker kan också återställa en hanterad domän från en säkerhets kopia som en sista utväg. Mer information finns i återställa [eller återställa från en misslyckad migrering](#roll-back-and-restore-from-migration).

### <a name="restrictions-on-available-virtual-networks"></a>Begränsningar för tillgängliga virtuella nätverk

Det finns vissa begränsningar för de virtuella nätverk som en hanterad domän kan migreras till. Mål resurs hanterarens virtuella nätverk måste uppfylla följande krav:

* Det virtuella Resource Manager-nätverket måste finnas i samma Azure-prenumeration som det klassiska virtuella nätverket som Azure AD DS för närvarande är distribuerat i.
* Det virtuella Resource Manager-nätverket måste finnas i samma region som det klassiska virtuella nätverk som Azure AD DS för närvarande är distribuerat i.
* Det virtuella nätverkets undernät i Resource Manager måste ha minst 3-5 tillgängliga IP-adresser.
* Det virtuella nätverkets undernät i Resource Manager ska vara ett dedikerat undernät för Azure AD DS och bör inte vara värd för några andra arbets belastningar.

Mer information om krav för virtuella nätverk finns i [design överväganden för virtuella nätverk och konfigurations alternativ][network-considerations].

Du måste också skapa en nätverks säkerhets grupp för att begränsa trafiken i det virtuella nätverket för den hanterade domänen. En Azure standard Load Balancer skapas under migreringsprocessen som kräver att dessa regler placeras. Den här nätverks säkerhets gruppen säkrar Azure AD DS och krävs för att den hanterade domänen ska fungera korrekt.

Mer information om vilka regler som krävs finns i [nätverks säkerhets grupper för Azure AD DS och de portar som krävs](network-considerations.md#network-security-groups-and-required-ports).

### <a name="ldaps-and-tlsssl-certificate-expiration"></a>LDAP-och TLS/SSL-certifikat upphör att gälla

Om din hanterade domän har kon figurer ATS för LDAPs kontrollerar du att ditt aktuella TLS/SSL-certifikat är giltigt i mer än 30 dagar. Ett certifikat som går ut inom de närmaste 30 dagarna gör att migreringsprocessen Miss fungerar. Om det behövs förnyar du certifikatet och tillämpar det på din hanterade domän och startar sedan migreringsprocessen.

## <a name="migration-steps"></a>Migreringsanvisningar

Migreringen till distributions modellen för Resource Manager och det virtuella nätverket delas upp i 5 huvud steg:

| Steg    | Utförd genom  | Beräknad tid  | Driftstopp  | Återställa/återställa? |
|---------|--------------------|-----------------|-----------|-------------------|
| [Steg 1 – uppdatera och leta upp det nya virtuella nätverket](#update-and-verify-virtual-network-settings) | Azure Portal | 15 minuter | Ingen stillestånds tid krävs | Ej tillämpligt |
| [Steg 2 – förbereda den hanterade domänen för migrering](#prepare-the-managed-domain-for-migration) | PowerShell | 15 – 30 minuter i genomsnitt | Stillestånds tiden för Azure AD DS startar när kommandot har slutförts. | Återställa och återställa tillgängligt. |
| [Steg 3 – flytta den hanterade domänen till ett befintligt virtuellt nätverk](#migrate-the-managed-domain) | PowerShell | 1 – 3 timmar i genomsnitt | En domänkontrollant är tillgänglig när kommandot har slutförts. | Vid ett haveri är både återställning (självbetjäning) och återställning tillgängligt. |
| [Steg 4 – testa och vänta på replik domänkontrollanten](#test-and-verify-connectivity-after-the-migration)| PowerShell och Azure Portal | 1 timme eller mer, beroende på antalet tester | Båda domän kontrol Lanterna är tillgängliga och bör fungera normalt, men nedtid upphör. | Ej tillämpligt. När den första virtuella datorn har migrerats finns det inget alternativ för att återställa eller återställa. |
| [Steg 5 – valfria konfigurations steg](#optional-post-migration-configuration-steps) | Azure Portal och virtuella datorer | Ej tillämpligt | Ingen stillestånds tid krävs | Ej tillämpligt |

> [!IMPORTANT]
> Du undviker ytterligare nedtid genom att läsa igenom all den här artikel och vägledningen för migrering innan du påbörjar migreringsprocessen. Migreringsprocessen påverkar tillgängligheten för Azure AD DS-domänkontrollanter under en viss tids period. Användare, tjänster och program kan inte autentisera mot den hanterade domänen under migreringsprocessen.

## <a name="update-and-verify-virtual-network-settings"></a>Uppdatera och verifiera inställningarna för virtuella nätverk

Innan du påbörjar migreringsprocessen slutför du följande inledande kontroller och uppdateringar. De här stegen kan inträffa när som helst innan migreringen och påverkar inte driften av den hanterade domänen.

1. Uppdatera din lokala Azure PowerShell-miljö till den senaste versionen. Du måste ha minst version *2.3.2* för att slutföra migreringen.

    Information om hur du kontrollerar och uppdaterar din PowerShell-version finns i [Azure PowerShell översikt][azure-powershell].

1. Skapa eller Välj ett befintligt virtuellt Resource Manager-nätverk.

    Kontrol lera att nätverks inställningarna inte blockerar nödvändiga portar som krävs för Azure AD DS. Portarna måste vara öppna både i det klassiska virtuella nätverket och i det virtuella Resource Manager-nätverket. De här inställningarna inkluderar routningstabeller (även om det inte rekommenderas att använda routningstabeller) och nätverks säkerhets grupper.

    Azure AD DS behöver en nätverks säkerhets grupp för att skydda de portar som behövs för den hanterade domänen och blockera all annan inkommande trafik. Den här nätverks säkerhets gruppen fungerar som ett extra skydds lager för att låsa åtkomsten till den hanterade domänen. Om du vill visa de portar som krävs, se [nätverks säkerhets grupper och nödvändiga portar][network-ports].

    Om du använder säker LDAP lägger du till en regel i nätverks säkerhets gruppen för att tillåta inkommande trafik för *TCP* -port *636*. Mer information finns i [låsa säker LDAP-åtkomst via Internet](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet)

    Anteckna den här mål resurs gruppen, målets virtuella nätverk och målets virtuella nätverks under nät. Dessa resurs namn används under migreringsprocessen.

1. Kontrol lera den hanterade domänens hälso tillstånd i Azure Portal. Om du har några aviseringar för den hanterade domänen måste du lösa dem innan du påbörjar migreringsprocessen.
1. Om du planerar att flytta andra resurser till distributions modellen för Resource Manager och det virtuella nätverket, kontrollerar du att resurserna kan migreras. Mer information finns i [plattforms stöd för migrering av IaaS-resurser från klassisk till Resource Manager][migrate-iaas].

    > [!NOTE]
    > Konvertera inte det klassiska virtuella nätverket till ett virtuellt Resource Manager-nätverk. Om du gör det finns inget alternativ för att återställa eller återställa den hanterade domänen.

## <a name="prepare-the-managed-domain-for-migration"></a>Förbered den hanterade domänen för migrering

Azure PowerShell används för att förbereda den hanterade domänen för migrering. Dessa steg omfattar att säkerhetskopiera, Pausa synkronisering och ta bort moln tjänsten som är värd för Azure AD DS. När det här steget är klart tas Azure AD DS offline under en viss tids period. Om förberedelse steget Miss lyckas kan du [återställa till föregående tillstånd](#roll-back).

Slutför följande steg för att förbereda den hanterade domänen för migrering:

1. Installera `Migrate-Aaads` skriptet från [PowerShell-galleriet][powershell-script]. Detta PowerShell-migreringsjobb är ett digitalt signerat av Azure AD Engineering-teamet.

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. Skapa en variabel som ska innehålla autentiseringsuppgifterna för av migreringsprocessen med cmdleten [Get-Credential][get-credential] .

    Det användar konto som du anger måste ha *globala administratörs* behörigheter i Azure AD-klienten för att aktivera Azure AD DS och sedan *deltagar* privilegier i din Azure-prenumeration för att skapa nödvändiga Azure AD DS-resurser.

    När du uppmanas till det anger du ett lämpligt användar konto och lösen ord:

    ```powershell
    $creds = Get-Credential
    ```
    
1. Definiera en variabel för ditt Azure-prenumerations-ID. Om det behövs kan du använda cmdleten [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) för att visa och Visa dina prenumerations-ID: n. Ange ditt eget prenumerations-ID i följande kommando:

   ```powershell
   $subscriptionId = 'yourSubscriptionId'
   ```

1. Kör nu `Migrate-Aadds` cmdleten med parametern *-prepare* . Ange *-ManagedDomainFqdn* för din egen hanterade domän, t. ex. *aaddscontoso.com*:

    ```powershell
    Migrate-Aadds `
        -Prepare `
        -ManagedDomainFqdn aaddscontoso.com `
        -Credentials $creds `
        -SubscriptionId $subscriptionId
    ```

## <a name="migrate-the-managed-domain"></a>Migrera den hanterade domänen

Domänen kan migreras med den hanterade domänen för beredd och säkerhets kopie ras. I det här steget återskapas de virtuella datorerna i Azure AD DS-domänkontrollanten med hjälp av distributions modellen för Resource Manager. Det här steget kan ta 1 till 3 timmar att slutföra.

Kör `Migrate-Aadds` cmdleten med parametern *-commit* . Ange *-ManagedDomainFqdn* för din egen hanterade domän som förbereds i föregående avsnitt, t. ex. *aaddscontoso.com*:

Ange mål resurs gruppen som innehåller det virtuella nätverk som du vill migrera Azure AD DS till, till exempel *myResourceGroup*. Ange det virtuella mål nätverket, till exempel *myVnet* och under nätet, till exempel *DomainServices*.

När det här kommandot har körts kan du inte återställa igen:

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn aaddscontoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

När skriptet har verifierat att den hanterade domänen är för beredd för migrering, anger du *Y* för att starta migreringsprocessen.

> [!IMPORTANT]
> Konvertera inte det klassiska virtuella nätverket till ett virtuellt Resource Manager-nätverk under migreringsprocessen. Om du konverterar det virtuella nätverket kan du inte längre återställa eller återställa den hanterade domänen eftersom det ursprungliga virtuella nätverket inte finns längre.

Varannan minut under migreringsprocessen rapporterar en förlopps indikator den aktuella statusen, som visas i följande exempel på utdata:

![Förlopps indikator för migreringen av Azure AD DS](media/migrate-from-classic-vnet/powershell-migration-status.png)

Migreringsprocessen fortsätter att köras, även om du avslutar PowerShell-skriptet. I Azure Portal kan statusen för den hanterade domänen rapporteras som *migrering*.

När migreringen är klar kan du Visa den första domänkontrollantens IP-adress i Azure Portal eller genom Azure PowerShell. En tids uppskattning på den andra domänkontrollanten som är tillgänglig visas också.

I det här skedet kan du också flytta andra befintliga resurser från den klassiska distributions modellen och det virtuella nätverket. Eller så kan du behålla resurserna i den klassiska distributions modellen och peer-koppla de virtuella nätverken till varandra när Azure AD DS-migreringen är klar.

## <a name="test-and-verify-connectivity-after-the-migration"></a>Testa och verifiera anslutningen efter migreringen

Det kan ta lite tid innan den andra domänkontrollanten har distribuerats och är tillgänglig för användning i den hanterade domänen. Den andra domänkontrollanten bör vara tillgänglig 1-2 timmar efter att migrerings-cmdleten har slutförts. Med distributions modellen Resource Manager visas nätverks resurserna för den hanterade domänen i Azure Portal eller Azure PowerShell. Kontrol lera om den andra domänkontrollanten är tillgänglig genom att titta på **egenskaps** sidan för den hanterade domänen i Azure Portal. Om två IP-adresser visas är den andra domänkontrollanten klar.

När den andra domänkontrollanten är tillgänglig slutför du följande konfigurations steg för nätverks anslutning med virtuella datorer:

* **Uppdatera DNS-serverinställningar** Om du vill låta andra resurser i det virtuella Resource Manager-nätverket lösa och använda den hanterade domänen, uppdaterar du DNS-inställningarna med IP-adresserna för de nya domän kontrol Lanterna. Azure Portal kan automatiskt konfigurera inställningarna åt dig.

    Mer information om hur du konfigurerar det virtuella Resource Manager-nätverket finns i [Uppdatera DNS-inställningar för det virtuella Azure-nätverket][update-dns].
* **Starta om domänanslutna virtuella datorer (valfritt)** Som DNS-serverns IP-adresser för Azure AD DS-domänkontrollanter ändras kan du starta om alla domänanslutna virtuella datorer så att de använder de nya DNS-serverinställningarna. Om program eller virtuella datorer har konfigurerat DNS-inställningar manuellt, uppdaterar du dem manuellt med de nya IP-adresserna för DNS-servern för domän kontrol Lanterna som visas i Azure Portal. Genom att starta om domänanslutna virtuella datorer förhindrar du problem med anslutningen som orsakas av IP-adresser som inte uppdateras.

Testa nu den virtuella nätverks anslutningen och namn matchningen. På en virtuell dator som är ansluten till det virtuella Resource Manager-nätverket eller som peer-kopplas till den, kan du prova följande nätverks kommunikations test:

1. Kontrol lera om du kan pinga IP-adressen för en av domän kontrol Lanterna, till exempel `ping 10.1.0.4`
    * IP-adresserna för domän kontrol Lanterna visas på **egenskaps** sidan för den hanterade domänen i Azure Portal.
1. Verifiera namn matchning för den hanterade domänen, till exempel `nslookup aaddscontoso.com`
    * Ange DNS-namnet för din egen hanterade domän för att kontrol lera att DNS-inställningarna är korrekta och att de löses.

Mer information om andra nätverks resurser finns i [nätverks resurser som används av Azure AD DS][network-resources].

## <a name="optional-post-migration-configuration-steps"></a>Valfria konfigurations steg efter migreringen

När migreringen har slutförts, inkluderar vissa valfria konfigurations steg aktivering av gransknings loggar eller e-postmeddelanden eller uppdatering av detaljerade lösen ords principer.

### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Prenumerera på gransknings loggar med Azure Monitor

Azure AD DS exponerar gransknings loggar för att hjälpa till att felsöka och Visa händelser på domän kontrol Lanterna. Mer information finns i [Aktivera och använda gransknings loggar][security-audits].

Du kan använda mallar för att övervaka viktig information som visas i loggarna. Till exempel kan mallen för gransknings logg boken övervaka möjliga konto utelåsning på den hanterade domänen.

### <a name="configure-email-notifications"></a>Konfigurera e-postaviseringar

Om du vill få ett meddelande när ett problem upptäcks på den hanterade domänen uppdaterar du inställningarna för e-postaviseringar i Azure Portal. Mer information finns i [Konfigurera meddelande inställningar][notifications].

### <a name="update-fine-grained-password-policy"></a>Uppdatera principen för detaljerad lösen ords princip

Vid behov kan du uppdatera den detaljerade lösen ords principen så att den är mindre restriktiv än standard konfigurationen. Du kan använda gransknings loggarna för att avgöra om en mindre begränsande inställning är meningsfull och sedan konfigurera principen efter behov. Använd följande övergripande steg för att granska och uppdatera princip inställningarna för konton som är upprepade gånger utlåst efter migreringen:

1. [Konfigurera lösen ords principen][password-policy] för färre begränsningar på den hanterade domänen och observera händelserna i gransknings loggarna.
1. Om några tjänst konton använder utgångna lösen ord som identifieras i gransknings loggarna uppdaterar du dessa konton med rätt lösen ord.
1. Om en virtuell dator exponeras för Internet kan du läsa om allmänna konto namn som *administratör*, *användare* eller *gäst* med höga inloggnings försök. Uppdatera om möjligt de virtuella datorerna så att de använder mindre generiska, namngivna konton.
1. Använd ett nätverks spår på den virtuella datorn för att hitta källan till angreppen och blockera de här IP-adresserna från att kunna försöka logga in.
1. Om det uppstår minimala problem med utelåsning bör du uppdatera den detaljerade lösen ords principen så restriktivt som det behövs.

## <a name="roll-back-and-restore-from-migration"></a>Återställa och återställa från migrering

Upp till en viss tidpunkt i migreringsprocessen kan du välja att återställa eller återställa den hanterade domänen.

### <a name="roll-back"></a>Återställ

Om det uppstår ett fel när du kör PowerShell-cmdleten för att förbereda migreringen i steg 2 eller för migreringen i steg 3, kan den hanterade domänen återställa till den ursprungliga konfigurationen. Det ursprungliga klassiska virtuella nätverket krävs för att återställa den här återställningen. IP-adresserna kan fortfarande ändras efter återställningen.

Kör `Migrate-Aadds` cmdleten med parametern *-abort* . Ange *-ManagedDomainFqdn* för din egen hanterade domän för beredd i ett tidigare avsnitt, till exempel *aaddscontoso.com*, och det klassiska virtuella nätverks namnet, t. ex. *myClassicVnet*:

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn aaddscontoso.com `
    -ClassicVirtualNetworkName myClassicVnet `
    -Credentials $creds `
    -SubscriptionId $subscriptionId
```

### <a name="restore"></a>Återställ

Som en sista utväg kan Azure AD Domain Services återställas från den senaste tillgängliga säkerhets kopian. En säkerhets kopia görs i steg 1 i migreringen för att säkerställa att den senaste säkerhets kopian är tillgänglig. Säkerhets kopian lagras i 30 dagar.

Om du vill återställa den hanterade domänen från säkerhets kopia [öppnar du en support ärende biljett med hjälp av Azure Portal][azure-support]. Ange katalog-ID, domän namn och orsaken till återställningen. Support-och återställnings processen kan ta flera dagar att slutföra.

## <a name="troubleshooting"></a>Felsökning

Om du har problem efter migreringen till distributions modellen för Resource Manager kan du granska några av följande vanliga fel söknings områden:

* [Felsök problem med domän anslutning][troubleshoot-domain-join]
* [Felsök problem med konto utelåsning][troubleshoot-account-lockout]
* [Felsöka konto inloggnings problem][troubleshoot-sign-in]
* [Felsök problem med säker LDAP-anslutning][tshoot-ldaps]

## <a name="next-steps"></a>Nästa steg

Med din hanterade domän migrerad till distributions modellen för Resource Manager, [skapar du och domän ansluter till en virtuell Windows-dator][join-windows] och [installerar sedan hanterings verktyg][tutorial-create-management-vm].

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential
[migration-benefits]: concepts-migration-benefits.md

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/