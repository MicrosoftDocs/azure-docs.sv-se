---
title: Distributions guide för Azure AD Secure hybrid Access med F5 | Microsoft Docs
description: Självstudie för att distribuera F5 stor IP-baserad virtuell utgåva (RA) virtuell dator i Azure IaaS för säker hybrid åtkomst
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730665"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Självstudie för att distribuera F5 stor IP Virtual Edition VM i Azure IaaS för säker hybrid åtkomst

Den här självstudien vägleder dig genom processen för att distribuera BIG-IP vitural Edition (VE) i Azure IaaS. I slutet av den här självstudien bör du ha:

- En fullständigt för beredd stor virtuell IP-dator (VM) för modellering av en säker hybrid åtkomst (SHA) proof of Concept

- En mellanlagringsdatabas som ska användas för att testa nya uppdateringar och snabb korrigeringar av BIG-IP-system

## <a name="prerequisites"></a>Förutsättningar

Föregående F5 BIG-IP-upplevelse eller kunskap är inte nödvändigt, men vi rekommenderar dock att du bekantar dig med [F5 stor IP-terminologi](https://www.f5.com/services/resources/glossary). Distribution av en stor IP-adress i Azure för SHA kräver:

- En betald Azure-prenumeration eller en kostnads fri [utvärderings prenumeration](https://azure.microsoft.com/free/)på 12 månader.

- Någon av följande F5-SKU: er med stor IP-licens

  - F5 BIG-IP® bästa paket

  - F5-fristående licens för BIG-IP Access Policy Manager™ (APM)

  - F5-tilläggsprogram för BIG-IP Access Policy Manager™ (APM) för en befintlig BIG IP F5 BIG-IP® lokal Traffic Manager™ (LTM)
  
  - 90-dagars fullständig [utvärderings licens](https://www.f5.com/trial/big-ip-trial.php)för en stor IP-funktion.

- Ett jokertecken eller ett alternativt namn för certifikat mottagare (SAN) för att publicera webb program över SSL (Secure Socket Layer). [Vi ska kryptera](https://letsencrypt.org/) erbjudanden som är kostnads fria 90 dagar för testning.

- Ett SSL-certifikat för att skydda BIG-IPs hanterings gränssnittet. Ett certifikat som används för att publicera webbappar kan användas om dess ämne motsvarar det fullständigt kvalificerade domän namnet (FQDN). Ett Wildcard-certifikat som definierats med ett ämne *. contoso.com skulle till exempel vara lämpligt för `https://big-ip-vm.contoso.com:8443`

Konfiguration av virtuella datorer och bas system tar ca. 30 minuter, där din BIG-IP-plattform är redo att implementera något av de SHA-scenarier som anges [här](f5-aad-integration.md).

I den här självstudien förutsätter vi att den stora IP-adressen distribueras till en Azure-resurs grupp som innehåller en Active Directory (AD)-miljö för att testa scenarierna. Miljön bör bestå av en domänkontrollant (DC) och webb värden (IIS) virtuella datorer. Att ha dessa servrar på andra platser till den virtuella datorn med stor IP-adress är också OK, vilket ger BIG-IP-kunskaper till var och en av de roller som krävs för att stödja ett specifikt scenario. Scenarier där den stora virtuella IP-datorn är ansluten till en annan miljö via en VPN-anslutning stöds också.

Om du inte har de objekt som nämns här för testning kan du distribuera en hel AD-domänmiljö till Azure med hjälp av det här [skriptet](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). En samling exempel test program kan också distribueras program mässigt till en IIS-webbvärd med hjälp av den här [skriptbaserade automatiseringen](https://github.com/jeevanbisht/DemoSuite).

>[!NOTE]
>[Azure Portal](https://portal.azure.com/#home) utvecklas ständigt, så vissa av stegen i den här självstudien kan skilja sig från den faktiska layouten som observerats i Azure Portal.

## <a name="azure-deployment"></a>Azure-distribution

En stor IP-adress kan distribueras i olika topologier. Den här guiden fokuserar på en enda nätverks gränssnitts distribution (NIC). Men om din BIG-IP-distribution kräver flera nätverks gränssnitt för hög tillgänglighet, nätverks åtskillnad eller mer än 1 GB data flöde, bör du överväga att använda F5's förkompilerade [Azure Resource Manager-mallar (arm)](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html).

Utför följande uppgifter för att distribuera BIG-IP VE från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Logga in på [Azure Portal](https://portal.azure.com/#home) med ett konto som har behörighet att skapa virtuella datorer. Till exempel deltagare

2. Skriv **Marketplace** i den översta sökrutan, följt av **RETUR**

3. Skriv **F5** i Marketplace-filtret, följt av **RETUR**

4. Välj **+ Lägg till** i det översta menyfliksområdet och skriv **F5** i Marketplace-filtret, följt av **RETUR**

5. Välj **F5 Big-IP Virtual Edition (BYOL)**  >  **Välj en program plan**  >  **F5 Big-IP ve-all (BYOL, 2 start platser)**

6. Välj **Skapa**.

![Bilden visar steg för att välja en program varu plan](./media/f5ve-deployment-plan/software-plan.png)

7. Gå igenom menyn **grundläggande** och Använd följande inställningar

 |  Projektinformation     |  Värde     |
 |:-------|:--------|
 |Prenumeration|Mål prenumeration för distributionen av stor IP-VM|
 |Resursgrupp | Befintlig Azure-resurs grupp den stora virtuella IP-datorn kommer att distribueras till eller skapa en. Ska vara samma resurs grupp för din DOMÄNKONTROLLANT och virtuella IIS-datorer|
 | **Instansinformation**|  |
 |Namn på virtuell dator| Exempel på BIG-IP-VM |
 |Region | Rikta in dig på Azure geo för BIG-IP-VM |
 |Alternativ för tillgänglighet| Aktivera endast om virtuell dator används i produktion|
 |Bild| F5 BIG-IP VE – alla (BYOL, 2 start platser)|
 |Azure Spot-instans| Nej, men du kan inte aktivera om det är lämpligt |
 |Storlek| Minsta specifikation ska vara 2 virtuella processorer och 8 GB minne|
 |**Administratörs konto**|  |
 |Autentiseringstyp|Välj lösen ord för tillfället. Du kan växla till ett nyckel par senare |
 |Användarnamn|Den identitet som ska skapas som ett lokalt konto för en stor IP-adress för att komma åt dess hanterings gränssnitt. Användar namnet är Skift läges känsligt.|
 |Lösenord|Skydda administratörs åtkomst med ett starkt lösen ord|
 |**Regler för inkommande portar**|  |
 |Offentliga inkommande portar|Inget|

8. Välj **Nästa: diskar** lämnar alla standardvärden och välj **Nästa: nätverk**.

9. I menyn **nätverk** slutför du inställningarna.

 |Nätverksgränssnitt|      Värde |
 |:--------------|:----------------|
 |Virtuellt nätverk|Samma Azure VNet som används av dina virtuella datorer i DOMÄNKONTROLLANTen och IIS, eller skapa ett|
 |Undernät| Samma interna Azure-undernät som DC-och IIS-VM: er, eller skapa ett|
 |Offentlig IP-adress |  Inget|
 |Nätverks säkerhets grupp för nätverkskort| Välj ingen om det Azure-undernät som du valde i föregående steg redan är associerat med en nätverks säkerhets grupp (NSG). annars väljer du grundläggande|
 |Accelerera nätverk| Av |
 |**Belastningsutjämning**|     |
 |Belastningsutjämna virtuell dator| Inga|

10. Välj **Nästa: hantering** och slutför de här inställningarna.

 |Övervakning|    Värde |
 |:---------|:-----|
 |Detaljerad övervakning| Av|
 |Startdiagnostik|Aktivera med anpassat lagrings konto. Tillåter anslutning av SSH-gränssnittet (BIG-IP Secure Shell) via alternativet för serie konsol i Azure Portal. Välj ett tillgängligt Azure Storage-konto|
 |**Identitet**|  |
 |Systemtilldelad hanterad identitet|Av|
 |Azure Active Directory|BIG-IP stöder för närvarande inte det här alternativet|
 |**Automatisk avstängning**|    |
 |Aktivera automatisk avstängning| Om testningen bör du överväga att ställa in BIG-IP-VM för avstängning varje dag|

11. Välj **Nästa: avancerade** lämna standardvärden och välj **Nästa: Taggar**.

12. Välj **Nästa: granska + skapa** för att granska dina Big-IP-VM-konfigurationer, innan du väljer **skapa** för att starta distributionen.

13. Tiden för att fullständigt distribuera en stor IP-baserad virtuell dator är vanligt vis 5 minuter. När du är klar väljer **du gå till resurs**, i stället expandera Azure Portal vänstra menyn och väljer **resurs grupper** för att navigera till din nya Big-IP-VM. Om den virtuella datorn inte kan skapas väljer du **bakåt** och **Nästa**.

## <a name="network-configuration"></a>Konfiguration av nätverk

När den stora virtuella IP-datorn startar första gången, kommer NÄTVERKSKORTet att tillhandahållas med en **primär** privat IP som utfärdats av den Dynamic Host Configuration Protocol (DHCP) för det Azure-undernät som den är ansluten till. Den här IP-adressen kommer att användas av BIG-IP: s trafik hanterings operativ system (TMOS) för att kommunicera med:

- Kommunikation med andra värdar och tjänster

- Utgående åtkomst till det offentliga Internet

- Inkommande åtkomst till BIG-IPs web config och SSH Management Interfaces

Om du exponerar något av dessa hanterings gränssnitt till Internet ökar BIG-IPs angrepps ytan, därför att BIG-IPs primära IP-adressen inte etablerades med en offentlig IP-adress under distributionen. I stället kommer en sekundär intern IP-adress och tillhör ande offentlig IP att tillhandahållas för publicerings tjänster.
Den här 1-till 1-mappningen mellan en offentlig VM-IP och en privat IP-adress gör att extern trafik når en virtuell dator En Azure NSG-regel krävs dock också för att tillåta trafiken, på ungefär samma sätt som en brand vägg.

Diagrammet visar en enda NIC-distribution av en stor IP-adress i Azure som kon figurer ATS med en primär IP-adress för allmän drift och hantering, samt en separat virtuell server-IP-adress för publicering av tjänster.
I den här ordningen tillåter en NSG-regel fjärrtrafik som är avsedd `intranet.contoso.com` att dirigeras till den offentliga IP-adressen för den publicerade tjänsten, innan den vidarebefordras till den virtuella IP-servern.

![Avbildningen visar en enskild NIC ](./media/f5ve-deployment-plan/single-nic-deployment.png) -distribution som standard, privata och offentliga IP-adresser som utfärdas till virtuella Azure-datorer är alltid dynamiska, så att de kan komma att ändras vid varje omstart av en virtuell dator. Undvik oförutsedda anslutnings problem genom att ändra BIG-IPs hanterings-IP till statisk och gör samma sak som sekundära IP-adresser som används för publicering av tjänster.

1. Från menyn för din Big-IP-dator går du till **Inställningar**  >  **nätverk**

2. I vyn nätverk väljer du länken till höger om **nätverks gränssnittet**

![Bilden visar nätverks konfigurationen](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Namn på virtuella datorer genereras slumpmässigt under distributionen.

3. Välj **IP-konfigurationer** i rutan till vänster och välj sedan **ipconfig1** rad

4. Ange alternativ för **IP-tilldelning** till statisk och om det behövs kan du ändra den primära IP-adressen för den virtuella IP-adressen. Välj sedan **Spara** och Stäng menyn ipconfig1

>[!NOTE]
>Du använder den här primära IP-adressen för att ansluta och hantera den stora IP-virtuella datorn.

5. Välj **+ Lägg till** i det översta menyfliksområdet och ange ett namn för en sekundär privat IP-adress, till exempel ipconfig2

6. Ange **fördelnings** alternativet för inställningarna för privata IP-adresser till **statisk**. Att tillhandahålla nästa högre eller lägre efterföljande IP-adress hjälper till att hålla ordning på saker.

7. Ange den offentliga IP-adressen som ska **associeras** och välj **skapa**

8. Ange ett namn för den nya offentliga IP-adressen, till exempel BIG-IP-VM_ipconfig2_Public

9. Om du uppmanas att göra det anger du **SKU** till standard

10. Om du uppmanas att  ange nivån **Global** och

11. Ange **tilldelnings** alternativet till **statisk** och välj sedan **OK** två gånger

Din BIG-IP-VM är nu redo att konfigureras med:

- **Primär privat IP**: dedikerad för att hantera den stora IP-virtuella datorn via dess webb konfigurations verktyg och SSH. Den används av BIG-IP-systemet som sin **egen IP-adress** för att ansluta till publicerade Server dels tjänster. Den ansluter också till externa tjänster, till exempel NTP, AD och LDAP.

- **Sekundär privat IP**: används när du skapar en virtuell dator med stor IP APM för att lyssna efter inkommande begäran till en publicerad tjänst (er).

- **Offentlig IP**: associerat med den sekundära privata IP-adressen, gör det möjligt för klient trafik från det offentliga Internet att komma åt den virtuella IP-servern för den eller de publicerade tjänsterna

Exemplet illustrerar 1 till 1-relationen mellan en offentlig och privat virtuell dator. Ett Azure VM-nätverkskort kan bara ha en primär IP-adress och alla IP-adresser som skapas är alltid sekundära.

>[!NOTE]
>Du behöver sekundära IP-mappningar för att publicera BIG-IP-tjänster.

![Den här bilden visar alla sekundära IP-adresser](./media/f5ve-deployment-plan/secondary-ips.png)

Om du vill implementera SHA med hjälp av den avancerade **konfigurations guiden** för stor IP-åtkomst upprepar du steg 5-11 för att skapa ytterligare privata och offentliga IP-par för varje ytterligare tjänst som du planerar att publicera via den stora IP-APM. Samma metod kan också användas om du publicerar tjänster med BIG-IPs **Avancerad konfiguration**. I det scenariot kan du dock välja att undvika offentliga IP-omkostnader genom att använda en [Server namn indikator (SNI)-](https://support.f5.com/csp/#/article/K13452) konfiguration. I den här konfigurationen kommer en virtuell IP-Server att acceptera all klient trafik den tar emot och dirigera den till dess mål.

## <a name="dns-configuration"></a>DNS-konfiguration

Det är viktigt att DNS har kon figurer ATS för att klienter ska kunna matcha dina publicerade SHA-tjänster till dina BIG-IP-VM: s offentliga IP-adresser.

Följande steg förutsätter att DNS-zonen i den offentliga domänen som används för dina SHA-tjänster hanteras i Azure. Samma DNS-principer för att skapa lokaliserings poster gäller dock fortfarande oavsett var din DNS-zon hanteras.

1. Om den inte redan är öppen expanderar du portalens vänstra meny och navigerar till din BIG-IP-VM via alternativet **resurs grupper**

2. Från menyn för din Big-IP-dator går du till **Inställningar**  >  **nätverk**

3. I vyn BIG-IP-VM-nätverk väljer du den första sekundära IP-adressen i list rutan IP-konfiguration och väljer länken för dess **offentliga IP-adress för nätverkskort**

![Skärm bild som visar offentlig IP-adress för nätverkskort](./media/f5ve-deployment-plan/networking.png)

4. Under avsnittet **Inställningar** i den vänstra rutan väljer du **konfiguration** för att Visa menyn offentliga IP-adresser och DNS-egenskaper för den valda sekundära IP-adressen

5. Välj och **skapa** Ali Aset och välj din **DNS-zon** i list rutan. Om det inte redan finns en DNS-zon kan den hanteras utanför Azure, eller så kan du skapa en för domänsuffix som du har verifierat i Azure AD.

6. Använd följande information för att skapa den första posten i DNS-Ali Aset:

 | Fält | Värde |
 |:-------|:-----------|
 |Prenumeration| Samma prenumeration som BIG-IP-VM|
 |DNS-zon| DNS-zon som är auktoritativ för det verifierade domänsuffix som de publicerade webbplatserna kommer att använda, till exempel www.contoso.com |
 |Name | Det värdnamn som du anger kommer att matcha den offentliga IP-adress som är associerad med den valda sekundära IP-adressen. Se till att definiera rätt DNS-till-IP-mappningar. Se avsnittet senaste avbildningen i avsnittet nätverks konfiguration, till exempel intranet.contoso.com > 13.77.148.215|
 | TTL-värde | 1 |
 |TTL-enheter | Tider |

7. Välj **skapa** för Azure för att spara inställningarna till offentlig DNS.

8. Lämna **DNS-namnets etikett (valfritt)** och välj **Spara** innan du stänger den offentliga IP-menyn.

9. Upprepa steg 1 till 6 för att skapa ytterligare DNS-poster för varje tjänst som du planerar att publicera med hjälp av den guidade konfigurationen av BIG-IP.

  Med DNS-poster på plats kan du använda alla online-verktyg som [DNS-kontroll](https://dnschecker.org/) för att kontrol lera att en skapad post har spridits över alla globala offentliga DNS-servrar.

  Om du hanterar DNS-domännamnområdet med en extern provider som [GoDaddy](https://www.godaddy.com/)måste du skapa poster med hjälp av en egen DNS-hanterings funktion.

>[!NOTE]
>Du kan också använda en DATORs lokala värd fil om du testar och ofta byter DNS-poster. Localhost-filen på en Windows-dator kan nås genom att trycka på Win + R på tangent bordet och skicka Word- **drivrutinerna** i rutan Kör. Bara mindful att en localhost-post endast kommer att tillhandahålla DNS-matchning för den lokala datorn, inte andra klienter.

## <a name="client-traffic"></a>Klient trafik

Som standard är Azure-virtuella nätverk och associerade undernät privata nätverk som inte kan ta emot Internet trafik. Din BIG-IP-VM-NIC måste kopplas till den NSG som du angav under distributionen. För att extern webb trafik ska kunna uppnå stor IP-VM måste du definiera en regel för inkommande NSG för att tillåta portarna 443 (HTTPS) och 80 (HTTP) via det offentliga Internet.

1. Från den stora IP-DATORns huvud **översikts** meny väljer du **nätverk**

2. Välj **Lägg till** regel för inkommande trafik för att ange följande egenskaper för NSG-regeln:

 |     Fält   |   Värde        |
 |:------------|:------------|
 |Källa| Valfri|
 |Källportintervall| *|
 |Mål-IP-adressen|Kommaavgränsad lista över alla sekundära privata IP-adresser för virtuella datorer|
 |Målportar| 80 443|
 |Protokoll| TCP |
 |Åtgärd| Tillåt|
 |Prioritet|Lägsta tillgängliga värde mellan 100 och 4096|
 |Name | Ett beskrivande namn, till exempel: `BIG-IP-VM_Web_Services_80_443`|

3. Välj **Lägg till** för att genomföra ändringarna och Stäng menyn **nätverk** .

HTTP-och HTTPS-trafik från vilken plats som helst kommer nu att kunna komma åt alla sekundära IP-VM-gränssnitt. Att tillåta port 80 är användbart när du vill att en stor IP APM ska omdirigera användare automatiskt från HTTP till HTTPS. Den här regeln kan redige ras för att lägga till eller ta bort mål-IP-adresser, när det behövs.

## <a name="manage-big-ip"></a>Hantera BIG-IP

Ett BIG-IP-system administreras via sitt webb konfigurations gränssnitt, som kan nås med någon av följande rekommenderade metoder:

- Från en dator i BIG-IP: s interna nätverk

- Från en VPN-klient som är ansluten till BIG-IP-VM: s interna nätverk

- Publicerad via [Azure AD-programproxy](./application-proxy-add-on-premises-application.md)

Du måste bestämma den lämpligaste metoden innan du kan fortsätta med de återstående konfigurationerna. Om det behövs kan du ansluta direkt till webb konfigurationen från Internet genom att konfigurera den primära IP-adressen med en offentlig IP-adress. Sedan lägger du till en NSG-regel för att tillåta 8443-trafik till den primära IP-adressen. Se till att begränsa källan till din egen betrodda IP-adress, annars kommer vem som helst att kunna ansluta.

När du är klar kontrollerar du att du kan ansluta till den virtuella IP-adressen för den virtuella datorn och logga in med de autentiseringsuppgifter som angavs under distributionen av virtuella datorer:

- Om du ansluter från en virtuell dator i det interna nätverket eller via VPN ansluter du direkt till den BIG-IPs primära IP-och webb konfigurations porten. Till exempel `https://<BIG-IP-VM_Primary_IP:8443`. Webbläsaren kommer att uppmanas om anslutningen är osäker, men du kan ignorera meddelandet tills BIG-IP har kon figurer ATS. Om webbläsaren insists blockerar åtkomsten rensar du dess cacheminne och försöker igen.

- Om du har publicerat webb konfigurationen via programproxyn använder du den URL som definierats för att få åtkomst till webb konfigurationen externt, utan att lägga till porten, till exempel `https://big-ip-vm.contoso.com` . Den interna URL: en måste definieras med hjälp av webb konfigurations porten, till exempel `https://big-ip-vm.contoso.com:8443` 

Ett BIG-IP-system kan också hanteras via dess underliggande SSH-miljö, som vanligt vis används för kommando rads aktiviteter (CLI) och åtkomst till rot nivå. Det finns flera alternativ för att ansluta till CLI, inklusive:

- [Azure skydds-tjänsten](../../bastion/bastion-overview.md): tillåter snabba och säkra anslutningar till alla virtuella datorer i ett vNET, oavsett var de befinner sig

- Anslut direkt via en SSH-klient som till exempel med metoden SparaTillFil i JIT-metoden

- Serie konsol: finns längst ned i avsnittet support och fel sökning på menyn för virtuella datorer i portalen. Den har inte stöd för fil överföringar.

- Precis som med webb konfigurationen kan du även ansluta direkt till CLI från Internet genom att konfigurera den primära IP-adressen med en offentlig IP-adress och lägga till en NSG-regel för att tillåta SSH-trafik. Kontrol lera återigen att du begränsar källan till din egen betrodda IP-adress, om du använder den här metoden.  

## <a name="big-ip-license"></a>BIG-IP-licens

Ett BIG-IP-system måste aktive ras och tillhandahållas med APM-modulen innan den kan konfigureras för publicering av tjänster och SHA.

1. Logga in på webb konfigurationen igen och välj **Aktivera** på sidan **allmänna egenskaper** .

2. I fältet **grundläggande registrerings nyckel** anger du Skift läges känslig nyckel som tillhandahålls av F5

3. Lämna **aktiverings metoden** inställt på **automatiskt** och välj **Nästa**, den stora IP-adressen validerar licensen och visar slut användar licens avtalet (EULA).

4. Välj **acceptera** och vänta tills aktiveringen har slutförts innan du väljer **Fortsätt**.

5. Logga in igen och längst ned på sidan licens översikt väljer du **Nästa**. Den stora IP-adressen visar nu en lista med moduler som tillhandahåller de olika funktioner som krävs för SHA. Om du inte ser detta går du till **system**  >  **resurs etablering** på fliken huvud.

6. Kontrol lera etablerings kolumnen för åtkomst principen (APM)

![Bilden visar åtkomst etablering](./media/f5ve-deployment-plan/access-provisioning.png)

7. Välj **Skicka** och godkänn varningen

8. Du måste vara patient och vänta tills den stora IP-adressen är klar för att se de nya funktionerna. Det kan gå flera gånger innan det initieras fullständigt. 

9. När du är klar väljer du **Fortsätt** och på fliken **om** väljer du **Kör installations verktyget**

>[!IMPORTANT]
>F5-licenser är begränsade till att konsumeras av en enda BIG-IP-instans vid ett och samma tillfälle. Det kan finnas orsaker till att du vill migrera en licens från en instans till en annan, och om du gör det måste du [återkalla](https://support.f5.com/csp/article/K41458656) utvärderings licensen på den aktiva instansen innan du inaktiverar den, annars kommer licensen att gå förlorad permanent.

## <a name="provision-big-ip"></a>Etablera BIG-IP

Att skydda hanterings trafik till och från BIG-IPs web config är av yttersta vikt. Konfigurera ett enhets hanterings certifikat för att hjälpa till att skydda webb konfigurations kanalen från kompromisser.

1. I det vänstra navigerings fältet går du till **system**  >  **Certification Management**  >  **Traffic Certification Management**  >  **SSL Certificate List**  >  **import**

2. Välj **PKCS 12 (IIS)** i list rutan **import typ** och **Välj fil**. Leta upp ett SSL-webbcertifikat som har ett ämnes namn eller SAN-nätverk som ska ta del av FQDN-namnet för att tilldela BIG-IP-VM i nästa steg

3. Ange lösen ordet för certifikatet och välj sedan **Importera**

4. Gå till **system**  >  **plattform** i det vänstra navigerings fältet

5. Konfigurera BIG-IP-VM med ett fullständigt kvalificerat värdnamn och tidszon för dess miljö, följt av **Update**

![Bilden visar allmänna egenskaper](./media/f5ve-deployment-plan/general-properties.png)

6. I det vänstra navigerings fältet går du till **system**  >  **konfiguration**  >  **enhet**  >  **NTP**

7. Ange en tillförlitlig NTP-källa och välj **Lägg till**, följt av **Uppdatera**. Till exempel `time.windows.com`

Nu behöver du en DNS-post för att lösa BIG-IPs FQDN som anges i föregående steg till dess primära privata IP-adress. En post ska läggas till i din miljös interna DNS eller till localhost-filen på en dator som ska användas för att ansluta till BIG-IP: s webb konfiguration. Oavsett hur bör webb läsar varningen inte längre visas när du ansluter till webb konfigurationen direkt. Det vill säga, inte via programproxy eller någon annan omvänd proxy.

## <a name="ssl-profile"></a>SSL-profil

Som en omvänd proxy kan ett BIG-IP-system fungera som en enkel vidarebefordrings tjänst, annars kallas en transparent proxy eller en fullständig proxy som aktivt deltar i utbyte mellan klienter och servrar.
En fullständig proxy skapar två distinkta anslutningar: en klient anslutning för TCP-klienter tillsammans med en separat server anslutning för TCP-servern, tillsammans med ett mjukt mellanrum i mitten. Klienterna ansluter till proxy-lyssnaren i ena änden, kallas även en **virtuell server** och proxyn upprättar en separat, oberoende anslutning till backend-servern. Detta är dubbelriktat på båda sidor.
I det här fullständiga proxyläge kan F5-BIG-IP-systemet inspektera trafik och därför är det också möjligt att interagera med begär Anden och svar. Vissa funktioner, till exempel belastnings utjämning och optimering av webb prestanda, samt mer avancerade trafik hanterings tjänster, till exempel program lager säkerhet, webb acceleration, sid Routning och säker fjärråtkomst, förlitar sig på den här funktionen.
När du publicerar SSL-baserade tjänster hanteras processen för att dekryptera och kryptera trafik mellan klienter och backend-tjänster av BIG-IP SSL profiler.

Det finns två typer av profiler:

- **Klient-SSL**: det vanligaste sättet att konfigurera ett stort IP-system för att publicera interna tjänster med SSL är att skapa en klient-SSL-profil. Med en klient-SSL-profil kan ett BIG-IP-system dekryptera inkommande klient begär Anden innan de skickas till en underordnad tjänst. Den krypterar sedan utgående Server dels svar innan de skickas till klienter.

- **Server-SSL**: för Server dels tjänster som kon figurer ATS för https kan du konfigurera Big-IP för att använda en Server SSL-profil. Med en Server-SSL-profil krypterar den stora IP-servern om klientbegäran innan den skickas till mål server dels tjänsten. När servern returnerar ett krypterat svar dekrypterar det stora IP-systemet och krypterar svaret igen innan det skickas till klienten via den konfigurerade klientens SSL-profil.

Etablering av både klient-och Server-SSL-profiler har förkonfigurerat och klart för alla SHA-scenarier.

1. I det vänstra navigerings fältet går du till **system**  >  **Certification Management**  >  **Traffic Certification Management**  >  **SSL Certificate List**  >  **import**

2. Välj **PKCS 12 (IIS)** i list rutan **import typ**

3. Ange ett namn för det importerade certifikatet, till exempel  `ContosoWilcardCert`

4. Välj **Välj fil** för att bläddra till det SSL-webbcertifikat vars ämnes namn motsvarar det domänsuffix som du planerar att använda för publicerade tjänster

5. Ange **lösen ordet** för det importerade certifikatet och välj sedan **Importera**

6. I det vänstra navigerings fältet går du till **lokala trafik**  >  **profiler**  >  **SSL**-  >  **klient** och väljer sedan **skapa**

7. På sidan **ny klient-SSL-profil** anger du ett unikt eget namn för den nya klientens SSL-profil och kontrollerar att den överordnade profilen är inställd på **clientssl**

![Bilden visar uppdatering av Big-IP](./media/f5ve-deployment-plan/client-ssl.png)

8. Markera kryss rutan längst till höger på raden **certifikat nyckel kedja** och välj **Lägg till**

9. I de tre List rutorna väljer du det jokertecken som du importerade utan lösen fras och väljer sedan **Lägg till**  >  **avslutad**.

![Bilden visar uppdatering av Big-IP contoso-jokertecken](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Upprepa steg 6-9 för att skapa en **SSL-Server certifikat profil**. Välj **SSL**-  >  **Server**  >  **skapa** i det översta menyfliksområdet.

11. På sidan **ny server SSL-profil** anger du ett unikt eget namn för den nya serverns SSL-profil och kontrollerar att den överordnade profilen är inställd på **serverssl**

12. Markera kryss rutan längst till höger för certifikat-och nyckel raderna och välj det importerade certifikatet i list rutan, följt av **klart**.

![Bilden visar uppdatering av Big-IP-servern alla profiler](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Despair inte om du inte kan köpa ett SSL-certifikat kan du använda den integrerade självsignerade BIG-IP-servern och klient-SSL-certifikat. Du ser bara ett certifikat fel i webbläsaren.

Ett sista steg i förbereda en BIG-IP för SHA är att se till att det går att hitta resursernas publicering och katalog tjänsten som den använder för SSO. En stor IP-adress har två källor med namn matchning, som börjar med sin lokala/.../Hosts-fil, eller om det inte går att hitta en post i det stora IP-systemet som den har kon figurer ATS med. Värd fil metoden gäller inte för APM-noder och pooler som använder ett fullständigt domän namn.

1. I webb konfigurationen går du till **system**  >  **konfiguration**  >  **enhet**  >  **DNS**

2. I **listan DNS-söknings Server** anger du IP-adressen för din MILJÖS DNS-Server

3. Välj **Lägg till**  >  **uppdatering**

Som ett separat och valfritt steg kan du överväga en [LDAP-konfiguration](https://somoit.net/f5-big-ip/authentication-using-active-directory) för att AUTENTISERA stora IP-systemadministratörer mot AD i stället för att hantera lokala Big-IP-konton.

## <a name="update-big-ip"></a>Uppdatera BIG-IP

Din BIG-IP-VM bör uppdateras för att låsa upp alla nya funktioner som beskrivs i [scenario-baserad vägledning](f5-aad-integration.md). Du kan kontrol lera systemets TMOS-version genom att hovra med mus pekaren över BIG-IPs-värdnamnet överst till vänster på huvud sidan. Vi rekommenderar att du kör v15. x och senare, vilket kan hämtas från [F5](https://downloads.f5.com/esd/productlines.jsp). Använd [vägledningen](https://support.f5.com/csp/article/K34745165) för att uppdatera huvud operativ systemets OS (TMOS).

Om det inte går att uppdatera huvud TMOS bör du överväga att minst uppgradera den guidade konfigurationen med hjälp av dessa steg.

1. Från huvud fliken i Big-IP-webbkonfigurationen **går du till**  >  **guidad konfiguration**

2. På sidan **guidad konfiguration** väljer du **Uppgradera guidad konfiguration**

![Bilden visar hur du uppdaterar Big-IP](./media/f5ve-deployment-plan/update-big-ip.png)

3. I dialog rutan **Uppgradera guidad konfiguration** **väljer du fil** för att ladda upp det nedladdade use Case-paketet och väljer **Ladda upp och installera**

4. När uppgraderingen har slutförts väljer du **Fortsätt**.

## <a name="backup-big-ip"></a>Säkerhetskopiera BIG-IP

Med det stora IP-systemet som nu är helt etablerad rekommenderar vi att du tar en fullständig säkerhets kopia av konfigurationen:

1. Gå till **system**  >  **Arkiv**  >  **skapa**

2. Ange ett unikt **fil namn** och aktivera **kryptering** med en lösen fras

3. Ange alternativet **privata nycklar** som ska **inkluderas** för att även säkerhetskopiera ENHETs-och SSL-certifikat

4. Välj **slutfört** och vänta tills processen har slutförts

5. En åtgärds status visas som ger status säkerhets kopierings resultat. Välj **OK**

6. Spara arkivet för användar konfigurations uppsättning (UCS) lokalt genom att välja länken till säkerhets kopian och välj **Hämta**.

Som ett valfritt steg kan du också ta en säkerhets kopia av hela system disken med [Azure-ögonblicksbilder](../../virtual-machines/windows/snapshot-copy-managed-disk.md), som till skillnad från säkerhets kopieringen av webb konfigurationen skulle ge viss katastrof för testning mellan TMOS-versioner eller att återställa till ett nytt system.

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>Återställa BIG-IP

Att återställa en stor IP-adress följer en liknande procedur för säkerhets kopieringen och kan även användas för att migrera konfigurationer mellan virtuella datorer med stor IP. Information om uppgraderings vägar som stöds bör observeras innan du importerar en säkerhets kopia.

1. Gå till **system**  >  **Arkiv**

2. Välj antingen en länk till en säkerhets kopia som du vill återställa eller Välj knappen **överför** för att bläddra till ett tidigare sparat UCS-arkiv som inte finns med i listan

3. Ange lösen frasen för säkerhets kopian och välj **Återställ**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>Vid tidpunkten för skrivning är AzVmSnapshot-cmdlet: en begränsad till att återställa den senaste ögonblicks bilden, baserat på datum. Ögonblicks bilder lagras i roten i den virtuella datorns resurs grupp. Tänk på att återställning av ögonblicks bilder startar om en virtuell Azure-dator, så här noggrant.

## <a name="additional-resources"></a>Ytterligare resurser

-   [Återställa BIG-IP-lösenord i Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Återställa lösen ordet utan att använda portalen](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Ändra NIC-kortet som används för BIG-IP VE-hantering](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Om vägar i en enda NIC-konfiguration](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Nästa steg

Välj ett [distributions scenario](f5-aad-integration.md) och starta implementeringen.