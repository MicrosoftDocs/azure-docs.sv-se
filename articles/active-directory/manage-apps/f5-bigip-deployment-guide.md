---
title: Distributionsguide för säker hybridåtkomst i Azure AD med F5 | Microsoft Docs
description: Självstudie om att distribuera en virtuell dator med F5 BIG-IP Virtual Edition (VE) i Azure IaaS för säker hybridåtkomst
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
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 139009c55573e1e115a22069671f66e93695a635
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783331"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>Självstudie om att distribuera en virtuell F5 BIG-IP-dator med virtuell utgåva i Azure IaaS för säker hybridåtkomst

I den här självstudien går vi igenom hela processen för att distribuera BIG-IP Vit azure edition (VE) i Azure IaaS. I slutet av den här självstudien bör du ha:

- Ett fullständigt förberett BIG-IP Virtual Machine (VM) för modellering av ett SHA-konceptbevis (Secure Hybrid Access)

- En mellanlagringsinstans som ska användas för att testa nya BIG-IP-systemuppdateringar och snabbkorrigeringar

## <a name="prerequisites"></a>Förutsättningar

Tidigare erfarenhet av F5 BIG-IP eller kunskap är inte nödvändigt, men vi rekommenderar att du bekantar dig [med F5 BIG-IP-terminologin](https://www.f5.com/services/resources/glossary). Distribution av en BIG-IP i Azure för SHA kräver:

- En betald Azure-prenumeration eller en kostnadsfri utvärderingsprenumeration på 12 [månader.](https://azure.microsoft.com/free/)

- Någon av följande F5 BIG-IP-licens-SKU:er

  - F5 BIG-IP® bästa paketet

  - F5 BIG-IP Access Policy Manager™ (APM) fristående licens

  - F5 BIG-IP Access Policy Manager™(APM)-tilläggslicens på en befintlig BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM)
  
  - 90-dagars BIG-IP-licens för fullständig [utvärderingsversion](https://www.f5.com/trial/big-ip-trial.php)av funktionen .

- Ett jokertecken eller SAN-certifikat (Alternativt namn för certifikatutfärdare) för att publicera webbprogram över SSL (Secure Socket Layer). [Vi krypterar erbjuder](https://letsencrypt.org/) kostnadsfria 90-dagarscertifikat för testning.

- Ett SSL-certifikat för att skydda BIG-IPs hanteringsgränssnittet. Ett certifikat som används för att publicera webbappar kan användas, om dess ämne motsvarar BIG-IP:s fullständiga domännamn (FQDN). Till exempel skulle ett jokerteckencertifikat som definierats med ett ämne *.contoso.com vara lämpligt för `https://big-ip-vm.contoso.com:8443`

Distribution av virtuella datorer och bassystemkonfigurationer tar cirka 30 minuter, och då är din BIG-IP-plattform redo för implementering av de SHA-scenarier som anges [här.](f5-aad-integration.md)

För att testa scenarierna förutsätter den här självstudien att BIG-IP kommer att distribueras till en Azure-resursgrupp som innehåller en Active Directory-miljö (AD). Miljön bör bestå av en domänkontrollant (DC) och en webbvärds (IIS) virtuella datorer. Det går också bra att ha dessa servrar på andra platser till den virtuella DATORN BIG-IP, förutsatt att BIG-IP har synen för var och en av de roller som krävs för att stödja ett visst scenario. Scenarier där den virtuella datorn BIG-IP är ansluten till en annan miljö via en VPN-anslutning stöds också.

Om du inte har de objekt som nämns här för testning kan du distribuera en hel AD-domänmiljö till Azure med hjälp av det här [skriptet](https://github.com/Rainier-MSFT/Cloud_Identity_Lab). En samling exempeltestprogram kan också distribueras programmatiskt till en IIS-webbvärd med hjälp av den [här skriptbaserade automatiseringen.](https://github.com/jeevanbisht/DemoSuite)

>[!NOTE]
>Den [Azure Portal](https://portal.azure.com/#home) utvecklas hela tiden, så vissa av stegen i den här självstudien kan skilja sig från den faktiska layout som observerats i Azure Portal.

## <a name="azure-deployment"></a>Azure-distribution

En BIG-IP kan distribueras i olika topologier. Den här guiden fokuserar på en distribution med ett enda nätverksgränssnitt (NIC). Men om din BIG-IP-distribution kräver flera nätverksgränssnitt för hög tillgänglighet, nätverkssegregering eller mer än 1 GB dataflöde, bör du överväga att använda F5:s förkompilerade [ARM-mallar (Azure Resource Manager).](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)

Utför följande uppgifter för att distribuera BIG-IP VE från [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps).

1. Logga in på [Azure Portal](https://portal.azure.com/#home) med ett konto som har behörighet att skapa virtuella datorer. Till exempel Deltagare

2. I sökrutan längst upp i menyfliksområdet skriver **du marketplace** följt av **Retur**

3. Skriv **F5** i Marketplace-filtret följt av **Retur**

4. Välj **+ Lägg till** i det övre menyfliksområdet och skriv **F5** i Marketplace-filtret, följt av **Retur**

5. Välj **F5 BIG-IP Virtual Edition (BYOL)** Välj en  >  **programvaruplan**  >  **F5 BIG-IP VE – ALLA (BYOL, 2 startplatser)**

6. Välj **Skapa**.

![Bilden visar steg för att välja en programvaruplan](./media/f5ve-deployment-plan/software-plan.png)

7. Gå igenom **menyn Grundläggande** inställningar och använd följande inställningar

 |  Projektinformation     |  Värde     |
 |:-------|:--------|
 |Prenumeration|Målprenumeration för distributionen av den virtuella datorn BIG-IP|
 |Resursgrupp | Den befintliga Azure-resursgruppen som den virtuella datorn BIG-IP distribueras till eller skapar en. Bör vara samma resursgrupp för dina domänkontrollanter och virtuella IIS-datorer|
 | **Instansinformation**|  |
 |Namn på virtuell dator| Exempel på BIG-IP-VM |
 |Region | Ange Azure Geo som mål för BIG-IP-VM |
 |Alternativ för tillgänglighet| Aktivera endast om du använder en virtuell dator i produktion|
 |Bild| F5 BIG-IP VE – ALLA (BYOL, 2 startplatser)|
 |Azure Spot-instans| Nej, men du kan aktivera om det är lämpligt |
 |Storlek| Minsta specifikation ska vara 2 virtuella processorer och 8 GB minne|
 |**Administratörskonto**|  |
 |Autentiseringstyp|Välj lösenord för tillfället. Du kan växla till ett nyckelpar senare |
 |Användarnamn|Den identitet som ska skapas som ett lokalt BIG-IP-konto för åtkomst till dess hanteringsgränssnitt. Användarnamnet är CASE-känsligt.|
 |Lösenord|Säker administratörsåtkomst med ett starkt lösenord|
 |**Regler för inkommande portar**|  |
 |Offentliga inkommande portar|Inget|

8. Välj **Nästa: Diskar lämnar** alla standardvärden och väljer **Nästa: Nätverk.**

9. På menyn **Nätverk** slutför du de här inställningarna.

 |Nätverksgränssnitt|      Värde |
 |:--------------|:----------------|
 |Virtuellt nätverk|Samma virtuella Azure-nätverk som används av din domänkontrollant och virtuella IIS-datorer, eller skapa ett|
 |Undernät| Samma interna Azure-undernät som din domänkontrollant och virtuella IIS-datorer, eller skapa ett|
 |Offentlig IP-adress |  Inget|
 |Nätverkssäkerhetsgrupp för nätverkskort| Välj Ingen om det Azure-undernät som du valde i föregående steg redan är associerat med en nätverkssäkerhetsgrupp (NSG); annars väljer du Basic|
 |Accelerera nätverk| Av |
 |**Belastningsutjämning**|     |
 |Belastningsutjämna virtuell dator| No|

10. Välj **Nästa: Hantering** och slutför inställningarna.

 |Övervakning|    Värde |
 |:---------|:-----|
 |Detaljerad övervakning| Av|
 |Startdiagnostik|Aktivera med anpassat lagringskonto. Tillåter anslutning till SSH-gränssnittet (BIG-IP Secure Shell) via alternativet Seriekonsol i Azure Portal. Välj ett tillgängligt Azure Storage-konto|
 |**Identitet**|  |
 |System tilldelad hanterad identitet|Av|
 |Azure Active Directory|BIG-IP stöder för närvarande inte det här alternativet|
 |**Automatisk nedstängning**|    |
 |Aktivera automatisk avstängning| Om du testar bör du överväga att ställa in BIG-IP-VM så att den stängs av dagligen|

11. Välj **Nästa: Avancerat** och lämna alla standardvärden och välj **Nästa: Taggar**.

12. Välj **Nästa: Granska + skapa för** att granska dina BIG-IP-VM-konfigurationer innan du väljer **Skapa** för att starta distributionen.

13. Det tar normalt 5 minuter att distribuera en virtuell BIG-IP-dator. När du är klar väljer du inte Gå till resurs **,** expandera Azure Portal  på den vänstra menyn och välj Resursgrupper för att navigera till din nya BIG-IP-VM. Om det inte går att skapa den virtuella datorn väljer **du Tillbaka** och **Nästa.**

## <a name="network-configuration"></a>Konfiguration av nätverk

När den virtuella datorn BIG-IP startas först etableras  nätverkskortet med en primär privat IP-adress som utfärdats av tjänsten Dynamic Host Configuration Protocol (DHCP) för det Azure-undernät som den är ansluten till. Den här IP-adressen används av BIG-IP:ns TMOS (Traffic Management Operating System) för att kommunicera med:

- Kommunicera med andra värdar och tjänster

- Utgående åtkomst till det offentliga Internet

- Inkommande åtkomst till BIG-IPs webbkonfiguration och SSH-hanteringsgränssnitt

Om du exponerar något av dessa hanteringsgränssnitt för Internet ökar risken för BIG-IPs-attacker, vilket gör att BIG-IPs primära IP-adressen inte etablerades med en offentlig IP-adress under distributionen. I stället etableras en sekundär intern IP-adress och associerad offentlig IP-adress för publiceringstjänster.
Den här mappningen mellan en virtuell dators offentliga IP-adress och den privata IP-adressen gör att extern trafik kan nå en virtuell dator. En Azure NSG-regel krävs dock också för att tillåta trafiken på ungefär samma sätt som en brandvägg.

Diagrammet visar en enda NIC-distribution av en BIG-IP VE i Azure, konfigurerad med en primär IP-adress för allmänna åtgärder och hantering, samt separata VIRTUELLA server-IP-adresser för publiceringstjänster.
I det här arrangemanget tillåter en NSG-regel fjärrtrafik som är avsedd för att dirigera till den offentliga IP-adressen för den publicerade tjänsten, innan den vidarebefordras till den virtuella `intranet.contoso.com` BIG-IP-servern.

![Bilden visar den enskilda nic-distributionen Som standard är privata och offentliga IP-adresser som utfärdats till virtuella Azure-datorer alltid dynamiska, så de ändras troligen vid varje omstart av ](./media/f5ve-deployment-plan/single-nic-deployment.png) en virtuell dator. Undvik oförutsedda anslutningsproblem genom att ändra ipBIG-IPs hanterings-IP-adressen till statisk och gör samma sak med sekundära IP-adresser som används för att publicera tjänster.

1. Gå till Inställningar Nätverk på menyn för din virtuella **BIG-IP-dator**  >  

2. I nätverksvyn väljer du länken till höger om **Nätverksgränssnitt**

![Bilden visar nätverkskonfigurationen](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>Namn på virtuella datorer genereras slumpmässigt under distributionen.

3. I den vänstra rutan väljer du **IP-konfigurationer** och sedan **ipconfig1-rad**

4. Ange alternativet **IP-tilldelning** till statisk och ändra vid behov den primära IP-adressen för de virtuella BIG-IP-datorerna. Välj sedan **Spara** och stäng menyn ipconfig1

>[!NOTE]
>Du använder den här primära IP-adressen för att ansluta och hantera den virtuella datorn BIG-IP-VM.

5. Välj **+ Lägg** till i det övre menyfliksområdet och ange ett namn för en sekundär privat IP-adress, till exempel ipconfig2

6. Ange **Allokeringsalternativ** för inställningarna för privata IP-adresser till **Statisk.** Genom att tillhandahålla nästa högre eller lägre ip-adress i följd kan du hålla ordning på saker och ting.

7. Ange Den offentliga IP-adressen till **Associera** och välj **Skapa**

8. Ange ett namn för den nya offentliga IP-adressen, till exempel BIG-IP-VM_ipconfig2_Public

9. Om du uppmanas till det ställer du **in SKU:n** på Standard

10. Om du uppmanas till det anger **du nivå** **till Global** och

11. Ange alternativet **Tilldelning** till **Statisk och** välj sedan **OK två** gånger

Din BIG-IP-VM är nu redo att konfigureras med:

- **Primär privat IP:** Dedikerad för att hantera BIG-IP-VM via dess webbkonfigurationsverktyg och SSH. Big-IP-systemet använder det som sin **själv-IP-adress för** att ansluta till publicerade backend-tjänster. Dessutom ansluter den till externa tjänster som NTP, AD och LDAP.

- **Sekundär privat IP:** Används när du skapar en virtuell BIG-IP APM-server för att lyssna efter inkommande förfrågningar till publicerade tjänster.

- **Offentlig IP:** Kopplas till den sekundära privata IP-adressen och gör att klienttrafik från det offentliga Internet kan nå den virtuella BIG-IP-servern för de publicerade tjänsterna

Exemplet illustrerar relationen mellan 1 och 1 mellan en virtuell dators offentliga och privata IP-adresser. Ett nätverkskort för en virtuell Azure-dator kan bara ha en primär IP-adress, och alla ytterligare skapade IP-adresser kallas alltid sekundära.

>[!NOTE]
>Du behöver de sekundära IP-mappningarna för publicering av BIG-IP-tjänster.

![Den här bilden visar alla sekundära IP-adresser](./media/f5ve-deployment-plan/secondary-ips.png)

Om du vill implementera SHA med hjälp av den guidade konfigurationen för BIG-IP-åtkomst upprepar du steg 5–11 för att skapa ytterligare privata och offentliga IP-par för varje ytterligare tjänst som du planerar att publicera via BIG-IP APM. Samma metod kan också användas om du publicerar tjänster med hjälp BIG-IPs **Advanced Configuration**. I det scenariot kan du dock välja att undvika offentliga IP-omkostnader genom att använda en [SNI-konfiguration (Server Name Indicator).](https://support.f5.com/csp/#/article/K13452) I den här konfigurationen accepterar en virtuell BIG-IP-server all klienttrafik som den tar emot och dirigerar den vidare till sitt mål.

## <a name="dns-configuration"></a>DNS-konfiguration

Det är viktigt att DNS har konfigurerats för klienter för att matcha dina publicerade SHA-tjänster till din BIG-IP-VM:s offentliga IP-adresser.

Följande steg förutsätter att DNS-zonen för den offentliga domän som används för dina SHA-tjänster hanteras i Azure. Samma DNS-principer för att skapa positionerarpost gäller dock fortfarande oavsett var din DNS-zon hanteras.

1. Om den inte redan är öppen expanderar du portalens vänstra meny och går till din BIG-IP-VM via **alternativet Resursgrupper**

2. Gå till Inställningar Nätverk på menyn för din virtuella **BIG-IP-dator**  >  

3. I nätverksvyn BIG-IP-VMs väljer du den första sekundära IP-adressen i listrutan IP-konfiguration och väljer länken för dess offentliga IP-adress för **nätverkskortet**

![Skärmbild som visar den offentliga IP-adressen för nätverkskortet](./media/f5ve-deployment-plan/networking.png)

4. Under avsnittet **Inställningar** i det vänstra  fönstret väljer du Konfiguration för att öppna menyn för offentliga IP- och DNS-egenskaper för den valda sekundära IP-adressen

5. Välj och **Skapa** aliaspost och välj **din DNS-zon** i listrutan. Om det inte redan finns någon DNS-zon kan den hanteras utanför Azure, eller så kan du skapa en för domänsuffixet som du skulle ha verifierat i Azure AD.

6. Använd följande information för att skapa den första DNS-aliasposten:

 | Fält | Värde |
 |:-------|:-----------|
 |Prenumeration| Samma prenumeration som den virtuella datorn BIG-IP-VM|
 |DNS-zon| DNS-zonen som är auktoritativ för det verifierade domänsuffixet som dina publicerade webbplatser använder, till exempel www.contoso.com |
 |Name | Det värdnamn som du anger matchar den offentliga IP-adress som är associerad med den valda sekundära IP-adressen. Se till att definiera rätt DNS till IP-mappningar. Se den sista avbildningen i avsnittet Nätverkskonfigurationer, till exempel intranet.contoso.com > 13.77.148.215|
 | TTL-värde | 1 |
 |TTL-enheter | Tider |

7. Välj **Skapa** för Azure för att spara inställningarna i offentlig DNS.

8. Lämna **DNS-namnetiketten (valfritt)** och välj **Spara innan** du stänger menyn Offentlig IP.

9. Upprepa steg 1 till 6 för att skapa ytterligare DNS-poster för varje tjänst som du planerar att publicera med hjälp av BIG-IP:s guidade konfiguration.

  Med DNS-poster på plats kan du använda [](https://dnschecker.org/) något av onlineverktygen, till exempel DNS-kontrollen, för att verifiera att en skapad post har spridits över alla globala offentliga DNS-servrar.

  Om du hanterar dns-domännamnsrymden med hjälp av en extern provider som [GoDaddy](https://www.godaddy.com/)måste du skapa poster med hjälp av en egen DNS-hanteringsfunktion.

>[!NOTE]
>Du kan också använda en dators lokala värdfil vid testning och ofta växla DNS-poster. Filen localhosts på en Windows-dator kan nås genom att trycka på Win + R på tangentbordet och skicka ordet **drivrutiner** i körningsrutan. Tänk bara på att en localhost-post endast ger DNS-upplösning för den lokala datorn, inte andra klienter.

## <a name="client-traffic"></a>Klienttrafik

Som standard är virtuella Azure-nätverk och associerade undernät privata nätverk som inte kan ta emot Internettrafik. Nätverkskortet för den virtuella datorn BIG-IP-VM bör kopplas till den NSG som du angav under distributionen. För att extern webbtrafik ska nå big-IP-VM måste du definiera en inkommande NSG-regel för att tillåta portarna 443 (HTTPS) och 80 (HTTP) via det offentliga Internet.

1. På den virtuella big-IP-datorns **huvudmeny För** översikt väljer du **Nätverk**

2. Välj **Lägg till** inkommande regel för att ange följande NSG-regelegenskaper:

 |     Fält   |   Värde        |
 |:------------|:------------|
 |Källa| Valfri|
 |Källportintervall| *|
 |Mål-IP-adressen|Kommaavgränsad lista över alla sekundära privata IP-adresser för BIG-IP-VM|
 |Målportar| 80 443|
 |Protokoll| TCP |
 |Åtgärd| Tillåt|
 |Prioritet|Lägst tillgängliga värde mellan 100 och 4096|
 |Name | Ett beskrivande namn, till exempel: `BIG-IP-VM_Web_Services_80_443`|

3. Välj **Lägg** till för att genomföra ändringarna och stäng **menyn** Nätverk.

HTTP- och HTTPS-trafik från valfri plats kommer nu att kunna nå alla dina sekundära gränssnitt för BIG-IP-VM: er. Att tillåta port 80 är användbart för att göra det möjligt för BIG-IP APM att automatiskt omdirigera användare från HTTP till HTTPS. Den här regeln kan redigeras för att lägga till eller ta bort mål-IP-adresser när det behövs.

## <a name="manage-big-ip"></a>Hantera BIG-IP

Ett BIG-IP-system administreras via dess användargränssnitt för webbkonfiguration, som kan nås med någon av följande rekommenderade metoder:

- Från en dator i BIG-IP:s interna nätverk

- Från en VPN-klient som är ansluten till big-IP-VM:ens interna nätverk

- Publicerat via [Azure AD-Programproxy](./application-proxy-add-on-premises-application.md)

Du måste välja den lämpligaste metoden innan du kan fortsätta med de återstående konfigurationerna. Om det behövs kan du ansluta direkt till webbkonfigurationen från Internet genom att konfigurera BIG-IP:ens primära IP-adress med en offentlig IP-adress. Lägg sedan till en NSG-regel för att tillåta 8443-trafiken till den primära IP-adressen. Se till att begränsa källan till din egen betrodda IP-adress, annars kan vem som helst ansluta.

När du är klar bekräftar du att du kan ansluta till den virtuella big-IP-datorns webbkonfiguration och logga in med de autentiseringsuppgifter som angavs under VM-distributionen:

- Om du ansluter från en virtuell dator i dess interna nätverk eller via VPN ansluter du direkt till den BIG-IPs IP-adressen och webbkonfigurationsporten. Till exempel `https://<BIG-IP-VM_Primary_IP:8443`. Webbläsaren kommer att fråga om anslutningen är osäker, men du kan ignorera uppmaningen tills BIG-IP har konfigurerats. Om webbläsaren kräver att åtkomsten blockeras rensar du cacheminnet och försöker igen.

- Om du har publicerat webbkonfigurationen via Programproxy använder du den URL som definierats för att få åtkomst till webbkonfigurationen externt, utan att lägga till porten, till exempel `https://big-ip-vm.contoso.com` . Den interna URL:en måste definieras med hjälp av webbkonfigurationsporten, till exempel `https://big-ip-vm.contoso.com:8443` 

Ett BIG-IP-system kan också hanteras via dess underliggande SSH-miljö, som vanligtvis används för kommandoradsuppgifter (CLI) och åtkomst på rotnivå. Det finns flera alternativ för att ansluta till CLI, inklusive:

- [Azure Bastion tjänst:](../../bastion/bastion-overview.md)Tillåter snabba och säkra anslutningar till alla virtuella datorer i ett virtuellt nätverk, från valfri plats

- Ansluta direkt via en SSH-klient som PuTTY via JIT-metoden

- Seriekonsol: Erbjuds längst ned i avsnittet Support och felsökning på vm-menyn i portalen. Det stöder inte filöverföringar.

- Precis som med webbkonfigurationen kan du också ansluta direkt till CLI från Internet genom att konfigurera BIG-IP:ns primära IP-adress med en offentlig IP-adress och lägga till en NSG-regel för att tillåta SSH-trafik. Se återigen till att begränsa källan till din egen betrodda IP-adress om du använder den här metoden.  

## <a name="big-ip-license"></a>BIG-IP-licens

Ett BIG-IP-system måste aktiveras och etableras med APM-modulen innan det kan konfigureras för publiceringstjänster och SHA.

1. Logga in på webbkonfigurationen igen och välj **Aktivera på** sidan Allmänna **egenskaper**

2. I fältet **Basregistreringsnyckel** anger du den ärendekänsliga nyckel som tillhandahålls av F5

3. Låt **aktiveringsmetoden vara** inställd **på** Automatisk och välj **Nästa.** BIG-IP validerar licensen och visar licensavtalet (EULA).

4. Välj **Acceptera** och vänta tills aktiveringen har slutförts innan du väljer **Fortsätt.**

5. Logga in igen och välj Nästa längst ned på sidan **Licenssammanfattning.** BIG-IP visar nu en lista över moduler som tillhandahåller de olika funktioner som krävs för SHA. Om du inte ser detta går du till Systemresursetablering **på**  >  **huvudfliken.**

6. Kontrollera etableringskolumnen för Åtkomstprincip (APM)

![Bilden visar åtkomstetablering](./media/f5ve-deployment-plan/access-provisioning.png)

7. Välj **Skicka** och acceptera varningen

8. Ha tålamod och vänta tills BIG-IP är klar med att lysa upp de nya funktionerna. Den kan gå flera gånger innan den initieras helt. 

9. När du är **klar väljer** du Fortsätt och **på fliken** Om väljer du **Kör konfigurationsverktyget**

>[!IMPORTANT]
>F5-licenser är begränsade till att användas av en enda BIG-IP VE-instans åt gången. Det kan finnas skäl till att vilja migrera en licens från en [](https://support.f5.com/csp/article/K41458656) instans till en annan, och om du gör det måste du återkalla din utvärderingslicens på den aktiva instansen innan du inaktiverar den, annars går licensen förlorad permanent.

## <a name="provision-big-ip"></a>Etablera BIG-IP

Att skydda hanteringstrafik till och BIG-IPs en webbkonfiguration är avgörande. Konfigurera ett certifikat för enhetshantering för att skydda webbkonfigurationskanalen mot kompromettering.

1. I det vänstra navigeringsfältet går du till **System**  >  **Certificate Management** Traffic Certificate  >  **Management** SSL Certificate  >  **List**  >  **Import**

2. I **listrutan Importtyp** väljer du **PKCS 12(IIS) och** **Välj fil**. Leta upp ett SSL-webbcertifikat som har ett ämnesnamn eller SAN som täcker det fullständiga domännamnet som du tilldelar den virtuella datorn BIG-IP-VM i de kommande stegen

3. Ange lösenordet för certifikatet och välj sedan **Importera**

4. I det vänstra navigeringsfältet går du till  >  **Systemplattform**

5. Konfigurera BIG-IP-VM med ett fullständigt kvalificerat värdnamn och tidszonen för dess miljö, följt av **Uppdatering**

![Bilden visar allmänna egenskaper](./media/f5ve-deployment-plan/general-properties.png)

6. I det vänstra navigeringsfältet går du till **SYSTEM**  >  **Configuration**  >  **Device**  >  **NTP**

7. Ange en tillförlitlig NTP-källa och välj **Lägg till** följt av **Uppdatera**. Till exempel `time.windows.com`

Nu behöver du en DNS-post för att BIG-IPs det FQDN som angavs i föregående steg till dess primära privata IP-adress. En post ska läggas till i miljöns interna DNS eller i localhost-filen på en dator som ska användas för att ansluta till BIG-IP-webbkonfigurationen. Oavsett vilket bör webbläsarvarningen inte längre visas när du ansluter till webbkonfigurationen direkt. Det vill säga inte via Programproxy eller någon annan omvänd proxy.

## <a name="ssl-profile"></a>SSL-profil

Som en omvänd proxy kan ett BIG-IP-system fungera som en enkel vidarebefordranstjänst, även kallat transparent proxy, eller en fullständig proxy som aktivt deltar i utbyten mellan klienter och servrar.
En fullständig proxy skapar två distinkta anslutningar: en TCP-klientanslutning på klientsidan tillsammans med en separat TCP-serveranslutning på serversidan, tillsammans med en mjuk lucka i mitten. Klienter ansluter till proxylyssnaren i ena änden, även kallat virtuell **server,** och proxyn upprättar en separat, oberoende anslutning till serverservern. Detta är dubbelriktad på båda sidor.
I det här fullständiga proxyläget kan F5 BIG-IP-systemet inspektera trafik, och därför är det också möjligt att interagera med begäranden och svar. Vissa funktioner, till exempel belastningsutjämning och optimering av webbprestanda, samt mer avancerade trafikhanteringstjänster som säkerhet på programlager, webbacceleration, siddirigering och säker fjärråtkomst, förlitar sig på den här funktionen.
När du publicerar SSL-baserade tjänster hanteras processen för att dekryptera och kryptera trafik mellan klienter och backend-tjänster av BIG-IP SSL profiler.

Det finns två typer av profiler:

- **Klient-SSL:** Det vanligaste sättet att konfigurera ett BIG-IP-system för att publicera interna tjänster med SSL är att skapa en SSL-klientprofil. Med en SSL-klientprofil kan ett BIG-IP-system dekryptera inkommande klientbegäranden innan de skickas vidare till en underordnad tjänst. Den krypterar sedan utgående backend-svar innan de skickas till klienter.

- **Server SSL:** För servertjänster som konfigurerats för HTTPS kan du konfigurera BIG-IP för att använda en Server SSL-profil. Med en Server SSL-profil krypterar BIG-IP om klientbegäran innan den skickas till målserverservertjänsten. När servern returnerar ett krypterat svar dekrypterar och krypterar BIG-IP-systemet svaret igen innan det skickas till klienten via den konfigurerade SSL-klientens profil.

Om du etablerar både klient- och server-SSL-profiler är BIG-IP förkonfigurerat och redo för alla SHA-scenarier.

1. I det vänstra navigeringsfältet går du till **System**  >  **Certificate Management** Traffic Certificate  >  **Management** SSL Certificate  >  **List**  >  **Import**

2. I **listrutan Importtyp** väljer du **PKCS 12(IIS)**

3. Ange ett namn för det importerade certifikatet, till exempel  `ContosoWilcardCert`

4. Välj **Välj fil för** att bläddra till SSL-webbcertifikatet som ämnesnamnet motsvarar det domänsuffix som du planerar att använda för publicerade tjänster

5. Ange lösenordet **för** det importerade certifikatet och välj sedan **Importera**

6. I det vänstra navigeringsfältet går du till **SSL-klienten för lokala**  >    >    >  **trafikprofiler** och väljer sedan **Skapa**

7. På sidan **Ny klient SSL-profil** anger du ett unikt eget namn för den nya klientens SSL-profil och ser till att den överordnade profilen är inställd på **clientssl**

![Bilden visar uppdateringen big-ip](./media/f5ve-deployment-plan/client-ssl.png)

8. Markera kryssrutan längst till höger på raden **Certifikatnyckelkedja och** välj Lägg **till**

9. I listrutan tre väljer du det jokerteckencertifikat som du importerade utan lösenfras och väljer sedan Lägg **till**  >  **slutfört.**

![Bilden visar uppdatering av big-ip contoso-jokertecken](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. Upprepa steg 6–9 för att skapa en **SSL-servercertifikatprofil.** Välj **SSL-server** Skapa i det översta  >    >  **menyfliksområdet.**

11. På sidan **Ny server SSL-profil** anger du ett unikt eget namn för den nya serverns SSL-profil och ser till att överordnad profil är inställd **på serverssl**

12. Markera kryssrutan längst till höger för raderna Certifikat och Nyckel och välj ditt importerade certifikat i listrutan följt av **Slutfört.**

![Bilden visar en uppdatering av big-ip-servern för hela profilen](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>Om du inte kan köpa ett SSL-certifikat kan du använda de integrerade själv signerade BIG-IP-servrarna och SSL-klientcertifikaten. Du ser bara ett certifikatfel i webbläsaren.

Ett sista steg i att förbereda en BIG-IP för SHA är att se till att den kan hitta de resurser som publiceringen och katalogtjänsten den förlitar sig på för enkel inloggning. En BIG-IP har två källor för namnmatchning, som börjar med dess lokala/.../värdfil, eller om en post inte hittas använder BIG-IP-systemet den DNS-tjänst som den har konfigurerats med. Filmetoden hosts gäller inte för APM-noder och pooler som använder ett FQDN.

1. I webbkonfigurationen går du till **DNS** för  >    >  **systemkonfigurationsenheten**  >  

2. I **DNS Lookup Server List (DNS-uppslagsserverlista)** anger du IP-adressen för din miljös DNS-server

3. Välj **Lägg till**  >  **uppdatering**

Som ett separat och valfritt steg kan du överväga en [LDAP-konfiguration](https://somoit.net/f5-big-ip/authentication-using-active-directory) för att autentisera BIG-IP-sysadmins mot AD i stället för att hantera lokala BIG-IP-konton.

## <a name="update-big-ip"></a>Uppdatera BIG-IP

Din BIG-IP-VM bör uppdateras för att låsa upp alla nya funktioner som omfattas av [den scenariobaserade vägledningen.](f5-aad-integration.md) Du kan kontrollera systemets TMOS-version med muspekaren över BIG-IPs värdnamn längst upp till vänster på huvudsidan. Vi rekommenderar att du kör v15.x och högre och kan hämtas från [F5](https://downloads.f5.com/esd/productlines.jsp)hämta . Använd [vägledningen](https://support.f5.com/csp/article/K34745165) för att uppdatera huvudsystemets operativsystem (TMOS).

Om det inte går att uppdatera huvud-TMOS bör du överväga att åtminstone uppgradera den guidade konfigurationen med hjälp av dessa steg.

1. Från huvudfliken i BIG-IP-webbkonfigurationen går du till **Åtkomst**  >  **till guidad konfiguration**

2. På sidan **Interaktiv konfiguration** väljer du Uppgradera **guidad konfiguration**

![Bilden visar hur du uppdaterar big-ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. I dialogrutan **Uppgradera guidad konfiguration väljer** du Fil **för** att ladda upp det nedladdade användningsfallspaketet och väljer Ladda upp **och installera**

4. När uppgraderingen är klar väljer du **Fortsätt.**

## <a name="backup-big-ip"></a>Säkerhetskopiera BIG-IP

Nu när BIG-IP-systemet är helt etablerat rekommenderar vi att du tar en fullständig säkerhetskopia av dess konfiguration:

1. Gå till  >  **Systemarkiv**  >  **Skapa**

2. Ange ett unikt **filnamn och** aktivera **kryptering** med en lösenfras

3. Ange alternativet **Privata nycklar till** Inkludera för **att** även hanteraren för enhets- och SSL-certifikat

4. Välj **Klar** och vänta tills processen har slutförts

5. En åtgärdsstatus visas med statusen säkerhetskopieringsresultat. Välj **OK**

6. Spara arkivet för användarkonfigurationsuppsättningen (UCS) lokalt genom att välja länken för säkerhetskopieringen och välja **Ladda ned**.

Som ett valfritt steg kan du också göra en säkerhetskopia av hela systemdisken med hjälp av Azure-ögonblicksbilder , vilket till skillnad från [webbkonfigurationssäkerhetskopieringen](../../virtual-machines/windows/snapshot-copy-managed-disk.md)skulle ge viss möjlighet att testa mellan TMOS-versioner eller återställa till ett nytt system.

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

Återställning av en BIG-IP följer en liknande procedur som säkerhetskopieringen och kan även användas för att migrera konfigurationer mellan virtuella BIG-IP-datorer. Information om uppgraderingsvägar som stöds bör observeras innan du importerar en säkerhetskopia.

1. Gå till  >  **Systemarkiv**

2. Välj antingen länken för en säkerhetskopia som  du vill återställa eller välj knappen Ladda upp för att bläddra till ett tidigare sparat UCS-arkiv som inte finns i listan

3. Ange lösenfrasen för säkerhetskopieringen och välj **Återställ**

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>När detta skrivs är cmdleten AzVmSnapshot begränsad till att återställa den senaste ögonblicksbilden baserat på datum. Ögonblicksbilder lagras i roten för den virtuella datorns resursgrupp. Tänk på att återställning av ögonblicksbilder startar om en virtuell Azure-dator, så det här tar lång tid.

## <a name="additional-resources"></a>Ytterligare resurser

-   [Återställa BIG-IP VE-lösenord i Azure](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [Återställa lösenordet utan att använda portalen](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [Ändra nätverkskortet som används för BIG-IP VE-hantering](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [Om vägar i en enda NIC-konfiguration](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure: Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>Nästa steg

Välj ett [distributionsscenario](f5-aad-integration.md) och starta implementeringen.
