---
title: 509-baserad autentisering med X. i ett Service Fabric kluster
description: Lär dig mer om certifikatbaserad autentisering i Service Fabric kluster och hur du identifierar, minimerar och åtgärdar problem med certifikat.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: 2d94e5cc78afbabde38eb38e0c4f89381bd67167
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101729699"
---
# <a name="x509-certificate-based-authentication-in-service-fabric-clusters"></a>509-baserad autentisering med X. i Service Fabric kluster

Den här artikeln kompletterar introduktionen till [Service Fabric kluster säkerhet](service-fabric-cluster-security.md)och går till information om certifikatbaserad autentisering i Service Fabric kluster. Vi utgår från att läsaren är bekant med grundläggande säkerhets koncept och även med de kontroller som Service Fabric visar för att kontrol lera säkerheten i ett kluster.  

Ämnen som omfattas av följande rubrik:

* Grundläggande om certifikatbaserad autentisering
* Identiteter och deras respektive roller
* Certifikat konfigurations regler
* Fel sökning och vanliga frågor och svar

## <a name="certificate-based-authentication-basics"></a>Grundläggande om certifikatbaserad autentisering
Som en kort uppdatering är ett certifikat ett instrument som är avsett att binda information om en entitet (ämnet) till sitt innehav av ett par med asymmetriska kryptografiska nycklar och utgör en grundläggande konstruktion av kryptering med offentliga nycklar. De nycklar som representeras av ett certifikat kan användas för att skydda data eller för att bevisa identiteten hos nyckel innehavarna. När det används tillsammans med ett PKI-system (Public Key Infrastructure) kan ett certifikat representera ytterligare egenskaper för sitt ämne, t. ex. ägarskapet till en Internet domän eller vissa behörigheter som utfärdats av utfärdaren av certifikatet (kallas även för en certifikat utfärdare eller CA). En gemensam tillämpning av certifikat stöder kryptografi protokollet Transport Layer Security (TLS), vilket möjliggör säker kommunikation över ett dator nätverk. Mer specifikt använder klienten och servern certifikat för att säkerställa integriteten och integriteten för kommunikationen, och även för att utföra ömsesidig autentisering.

I Service Fabric bygger även det grundläggande skiktet i ett kluster (Federation) på TLS (bland annat protokoll) för att uppnå ett tillförlitligt, säkert nätverk av deltagande noder. Anslutningar till klustret via Service Fabric klient-API: er använder TLS även för att skydda trafiken och även för att fastställa parternas identiteter. När det används för autentisering i Service Fabric, kan ett certifikat användas för att bevisa följande anspråk: a) som presentatören av certifikatets autentiseringsuppgifter har till gång till certifikatets privata nyckel b) certifikatets SHA-1-hash (' tumavtryck ') matchar en deklaration som ingår i kluster definitionen eller c) certifikatets unika ämnes namn för certifikat mottagare matchar en deklaration som ingår i kluster definitionen och certifikat utfärdaren är känd eller betrodd.

I listan ovan är "b" colloquially kallat "tumavtryck för"; i det här fallet refererar deklarationen till ett särskilt certifikat och styrkan i autentiseringsschemat vilar på den lokala datorn att det är mycket omöjligt att falska ett certifikat som ger samma hash-värde som ett annat, men fortfarande är ett giltigt, välformulerat objekt i alla andra avseenden. Med alternativet "c" visas en alternativ form för att deklarera ett certifikat, där systemets hållfasthet vilar på kombinationen av certifikatets ämne och utfärdande myndighet. I det här fallet avser deklarationen en certifikat klass – alla två certifikat med samma egenskaper anses vara fullständigt likvärdiga. 

I följande avsnitt beskrivs hur Service Fabric runtime använder och validerar certifikat för att säkerställa kluster säkerheten.

## <a name="identities-and-their-respective-roles"></a>Identiteter och deras respektive roller
Innan simhopp till information om autentisering eller skydd av kommunikations kanaler är det viktigt att lista de deltagande aktörerna och de motsvarande roller som de spelar i ett kluster:
- Service Fabric runtime, som kallas "system": en uppsättning tjänster som tillhandahåller abstraktioner och funktioner som representerar klustret. När vi refererar till kommunikation i kluster mellan system instanser använder vi termen kluster identitet. När vi refererar till klustret som mottagare/mål för trafik utanför klustret, använder vi termen "Server identitet".
- värdbaserade program, kallade "program": kod som tillhandahålls av ägare av klustret, som dirigeras och körs i klustret
- klienter: entiteter som tillåts att ansluta till och köra funktioner i ett kluster enligt kluster konfigurationen. Vi skiljer mellan två nivåer av behörigheter-"användare" respektive "admin. En "användare"-klient är främst begränsad till skrivskyddade åtgärder (men inte alla skrivskyddade funktioner), medan en "admin-klient har obegränsad åtkomst till klustrets funktion. (Mer information finns [i säkerhets roller i ett Service Fabric-kluster](service-fabric-cluster-security-roles.md).)
- (Endast Azure) Service Fabric tjänster som dirigerar och visar kontroller för drift och hantering av Service Fabric kluster, som kallas enbart "service". Beroende på miljön kan tjänsten hänvisa till Azure Service Fabric Resource provider eller andra resurs leverantörer som ägs och drivs av Service Fabrics teamet.

I ett säkert kluster kan var och en av dessa roller konfigureras med sin egen, distinkta identitet, deklarerad som koppling av ett fördefinierat roll namn och dess motsvarande autentiseringsuppgifter. Service Fabric har stöd för att deklarera autentiseringsuppgifter som certifikat eller domänbaserade tjänst objekt. (Windows-/Kerberos-based identiteter stöds också, men omfattas inte av den här artikeln. se [Windows-baserad säkerhet i Service Fabric kluster](service-fabric-windows-cluster-windows-security.md).) I Azure-kluster kan även klient roller deklareras som [Azure Active Directory-baserade identiteter](service-fabric-cluster-creation-setup-aad.md).

Som alluded till ovan definierar Service Fabric körnings miljön två nivåer av behörighet i ett kluster: admin och user. En administratörs klient och en "system komponent" kan båda använda "admin-privilegier" och kan därför inte särskiljas från varandra. Vid upprättandet av en anslutning i/till klustret beviljas en autentiserad anropare av Service Fabric runtime en av de två rollerna som basen för den efterföljande auktoriseringen. Vi undersöker autentiseringen i djup i följande avsnitt.

## <a name="certificate-configuration-rules"></a>Certifikat konfigurations regler
### <a name="validation-rules"></a>Validerings regler
Säkerhets inställningarna för ett Service Fabric kluster beskriver i princip följande aspekter:
- autentiseringstypen; Det här är en tids gräns, oföränderlig egenskap för klustret. Exempel på sådana inställningar är "ClusterCredentialType", "ServerCredentialType" och tillåtna värden är "none", "x509" eller "Windows". Den här artikeln fokuserar på x509-typen autentisering.
- verifierings regler för (autentisering). de här inställningarna anges av kluster ägaren och beskriver vilka autentiseringsuppgifter som ska accepteras för en specifik roll. Exempel kommer att undersökas i djupet omedelbart nedan.
- inställningar som används för att anpassa eller ändra resultatet av autentiseringen. exempel här är flaggor (avvisade) begränsa verk ställandet av listor över återkallade certifikat osv.

> [!NOTE]
> Exempel på kluster konfiguration som tillhandahålls nedan är utdrag från kluster manifestet i XML-format, som det mest sammanfattade formatet som stöder direkt Service Fabric funktionerna som beskrivs i den här artikeln. Samma inställningar kan uttryckas direkt i JSON-representationerna av en kluster definition, antingen ett fristående JSON-kluster eller en Azure Resource Management-mall.

En certifikat verifierings regel består av följande element:
- motsvarande roll: klient, admin-klient (privilegie rad roll)
- autentiseringsuppgiften som godkänts för rollen, deklarerad antingen genom tumavtryck eller subjektets gemensamma namn

#### <a name="thumbprint-based-certificate-validation-declarations"></a>Deklarationer för tumavtryck-baserade certifikat validering
Om det gäller tumavtryckbaserade verifierings regler kommer de autentiseringsuppgifter som presenteras av en anropare som begär en anslutning i/till klustret att val IDE ras enligt följande:
  - autentiseringsuppgiften är ett giltigt och välformulerat certifikat: dess kedja kan skapas, signaturer matchar
  - certifikatet är Time-giltigt (NotBefore <= nu < NotAfter)
  - certifikatets SHA-1-hash matchar deklarationen, som en Skift läges okänslig sträng jämförelse, exklusive alla blank steg

Eventuella förtroende fel som påträffas under skapande eller verifiering av kedja ignoreras för tumavtryckbaserade deklarationer, förutom utgångna certifikat, även om det finns bestämmelser för det fallet. Mer specifikt, fel relaterade till: återkallnings status är okänd eller offline, ej betrodd rot, ogiltig nyckel användning, partiell kedja betraktas som icke-oåterkalleliga fel. i det här fallet är det i det här fallet att certifikatet bara är ett kuvert för ett nyckel par – säkerheten ligger i det faktum att kluster ägaren har ställt in på platser för att skydda den privata nyckeln.

Följande utdrag från ett kluster manifest exemplifies en sådan uppsättning tumavtryck-baserade verifierings regler:

```xml
<Section Name="Security">
  <Parameter Name="ClusterCredentialType" Value="X509" />
  <Parameter Name="ServerAuthCredentialType" Value="X509" />
  <Parameter Name="AdminClientCertThumbprints" Value="d5ec...4264" />
  <Parameter Name="ClientCertThumbprints" Value="7c8f...01b0" />
  <Parameter Name="ClusterCertThumbprints" Value="abcd...1234,ef01...5678" />
  <Parameter Name="ServerCertThumbprints" Value="ef01...5678" />
</Section>
```

Var och en av posterna ovan refererar till en speciell identitet enligt beskrivningen ovan. varje post tillåter också att du anger flera värden som en kommaavgränsad lista med strängar. I det här exemplet kan presentatören av ett certifikat med SHA-1-5ec verifieras... 4264 kommer att beviljas admin-rollen. omvänt, en anropare som autentiserar med certifikatet "7c8f..." 01b0 tilldelas en användar roll som är begränsad till de mest skrivskyddade åtgärderna. En inkommande anropare som visar ett certifikat vars tumavtryck är "abcd..." 1234 "eller" ef01... 5678 kommer att godkännas som en peer-nod i klustret. Slutligen förväntar sig en klient som ansluter till en hanterings slut punkt för klustret att tumavtrycket för Server certifikatet ska vara ef01... 5678 '. 

Som tidigare nämnts gör Service Fabric bestämmelser för godkännande av utgångna certifikat; Anledningen är att certifikaten har en begränsad livs längd och, när de deklareras av tumavtryck (som refererar till en viss certifikat instans), vilket gör att ett certifikat upphör att gälla, kan det antingen leda till att det inte går att ansluta till klustret eller en direkt rätt komprimering av klustret. Det är för enkelt att glömma eller avhjälpa att rotera ett tumavtryck-fästa certifikat, och tyvärr är det svårt att återställa från en sådan situation.

I detta fall kan kluster ägaren uttryckligen ange att självsignerade certifikat som deklarerats med tumavtrycket anses giltiga, enligt följande:

```xml
  <Section Name="Security">
    <Parameter Name="AcceptExpiredPinnedClusterCertificate" Value="true" />
  </Section>
```
Det här beteendet utökar inte CA-utfärdade certifikat. om så är fallet kan ett återkallat, tillförlitligt certifikat som har upphört att gälla bli "giltigt" så snart det inte längre skulle hamna i certifikat utfärdarens lista över återkallade certifikat och därmed utgöra en säkerhets risk. Med självsignerade certifikat anses kluster ägaren den enda part som ansvarar för att skydda certifikatets privata nyckel, vilket inte är fallet med CA-utfärdade certifikat – kluster ägaren kanske inte känner till hur eller när deras certifikat har deklarerats som komprometterade.

#### <a name="common-name-based-certificate-validation-declarations"></a>Egna namnbaserade certifikat validerings deklarationer
Vanliga namnbaserade deklarationer gör något av följande:
- eget subjekt namn (endast)
- subjektets gemensamma namn med utgivar fäst

Låt oss först överväga ett utdrag från ett kluster manifest exemplifying båda deklarations formaten:
```xml
    <Section Name="Security/ServerX509Names">
      <Parameter Name="server.demo.system.servicefabric.azure-int" Value="" />
    </Section>
    <Section Name="Security/ClusterX509Names">
      <Parameter Name="cluster.demo.system.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```
Deklarationerna avser server-respektive kluster identiteter. Observera att de CN-baserade deklarationerna har sina egna avsnitt i kluster manifestet, separat från standard säkerhet. I båda deklarationerna representerar "namn" det unika ämnes namnet för certifikatet och fältet "värde" representerar den förväntade utfärdaren enligt följande:

- i det första fallet anger deklarationen att det gemensamma namn elementet för Server certifikatets unika ämne förväntas matcha strängen "server.demo.system. servicefabric. Azure-int"; fältet "värde" anger att roten i certifikat kedjan är betrodd på den nod/dator där Server certifikatet verifieras. i Windows innebär detta att certifikatet kan kedja upp till något av de certifikat som är installerade i arkivet för betrodda rot certifikat utfärdare.
- i det andra fallet anger deklarationen att presentatören av ett certifikat har accepterats som en peer-nod i klustret om certifikatets egna namn matchar strängen "cluster.demo.system. servicefabric. Azure-int" *och* tumavtrycket för den direkta utfärdaren av certifikatet matchar en av de kommaavgränsade posterna i fältet värde. (Den här regel typen är colloquially känd som "gemensamt namn med utfärdare".)

I båda fallen skapas certifikat kedjan och förväntas vara felfritt. det vill säga återkallnings fel, ofullständig kedja eller tid – ogiltiga förtroende fel betraktas som allvarliga och certifikat verifieringen kommer att Miss förväntas. Att fästa utfärdarna kommer att leda till att den icke-betrodda roten är status som ett icke-allvarligt fel. Trots utseendet är detta en striktare form av verifiering, eftersom det gör det möjligt för kluster ägaren att begränsa uppsättningen auktoriserade/godkända utfärdare till sin egen PKI.

När certifikat kedjan har skapats verifieras den mot en standard-TLS/SSL-princip med det deklarerade ämnet som fjärrnamn. ett certifikat anses vara en matchning om dess eget namn för certifikat mottagare eller något av dess alternativa namn matchar CN-deklarationen från kluster manifestet. Jokertecken stöds i det här fallet och sträng matchningen är Skift läges okänslig.

(Vi bör klargöra att den ordning som beskrivs ovan kan utföras för varje typ av nyckel användning som deklareras av certifikatet. om certifikatet anger användning av klientautentisering skapas kedjan och utvärderas först för en klient roll. Om det lyckas är utvärderingen slutförd och verifieringen lyckades. Om certifikatet inte har användningen av klientautentisering, eller om verifieringen misslyckades, kommer Service Fabric runtime att bygga och utvärdera kedjan för serverautentisering.)

För att slutföra exemplet visar följande utdrag att deklarera klient certifikat efter eget namn:
```xml
    <Section Name="Security/AdminClientX509Names">
      <Parameter Name="admin.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
    <Section Name="Security/ClientX509Names">
      <Parameter Name="user.demo.client.servicefabric.azure-int" Value="1b45...844d,d7fe...26c8,3ac7...6960,96ea...fb5e" />
    </Section>
```

Deklarationerna ovan motsvarar administratörs-och användar identiteterna. validering av certifikat som har deklarerats på det här sättet är exakt så som beskrivs i föregående exempel, för kluster-och Server certifikat.

> [!NOTE]
> Vanliga namnbaserade deklarationer är avsedda att förenkla rotationen och generellt sett hantering av kluster certifikat. Vi rekommenderar dock att följa rekommendationerna nedan för att säkerställa fortsatt tillgänglighet och säkerhet för klustret:
  * föredra att utfärdaren fäster för att förlita sig på betrodda rötter
  * Undvik att blanda utfärdare från olika PKI: er
  * Se till att alla förväntade utfärdare finns med i certifikat deklarationen. en felmatchad utfärdare leder till att verifieringen misslyckades
  * kontrol lera att PKI: ns certifikat princip slut punkter är synliga, tillgängliga och tillgängliga – detta innebär att AIA-, CRL-eller OCSP-slutpunkterna deklareras i löv certifikatet och att de är tillgängliga så att certifikat kedjan kan slutföras.

Att knyta samman allt, vid mottagande av en begäran om en anslutning i ett kluster som skyddas med X. 509-certifikat, använder Service Fabric runtime klustrets säkerhets inställningar för att verifiera autentiseringsuppgifterna för den fjärranslutna parten enligt beskrivningen ovan. om det lyckas anses anrops tjänsten/fjärrparten vara autentiserad. Om autentiseringsuppgiften matchar flera validerings regler ger körningen anroparen den högsta privilegierade rollen för någon av de matchade reglerna. 

### <a name="presentation-rules"></a>Presentations regler
I föregående avsnitt beskrivs hur autentisering fungerar i ett certifikat säkrat kluster. i det här avsnittet förklaras hur Service Fabric runtime identifierar och läser in de certifikat som används för kommunikation i klustret. Vi kallar dessa "presentations regler".

Precis som med validerings reglerna anger presentations reglerna en roll och den tillhör ande deklarationen för autentiseringsuppgifter, uttryckt antingen efter tumavtryck eller eget namn. Till skillnad från verifierings reglerna har inte gemensamma namnbaserade deklarationer bestämmelser för utfärdande av utfärdare. Detta ger större flexibilitet och bättre prestanda. Presentations reglerna deklareras i "NodeType"-avsnittet/-avsnitten i kluster manifestet för varje distinkt nodtyp. inställningarna delas upp från säkerhets avsnitten i klustret så att varje nodtyp får fullständig konfiguration i ett enda avsnitt. I Azure Service Fabric-kluster har nodtypen certifikat deklarationer som standard till sina motsvarande inställningar i säkerhets avsnittet i definitionen av klustret.

#### <a name="thumbprint-based-certificate-presentation-declarations"></a>Deklarationer för tumavtryck-baserade certifikat presentationer
Som tidigare beskrivits skiljer Service Fabric körningen mellan sin roll som peer för andra noder i klustret och som server för kluster hanterings åtgärder. I princip kan de här inställningarna konfigureras tydligt, men i praktiken tenderar de att justeras. I resten av den här artikeln antar vi inställningarna för enkelhetens skull.

Låt oss ta en titt på följande utdrag från ett kluster manifest:
```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindByThumbprint" X509FindValue="cc71...1984" X509FindValueSecondary="49e2...19d6" X509StoreName="my" Name="ClusterCertificate" />
        <ServerCertificate X509FindValue="cc71...1984" Name="ServerCertificate" />
        <ClientCertificate X509FindValue="cc71...1984" Name="ClientCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```
Elementet "ClusterCertificate" visar det fullständiga schemat, inklusive valfria parametrar (' X509FindValueSecondary ') eller de med lämpliga standardvärden (' X509StoreName '). de andra deklarationerna visar ett förkortat formulär. Deklarationen för kluster certifikat ovan anger att säkerhets inställningarna för noder av typen ' nt1vm ' initieras med certifikatet ' cc71.. 1984 ' som primärt, och ' 49e2.. 19d6 "certifikat som sekundärt; båda certifikaten förväntas finnas i LocalMachine \' My ' Certificate Store (eller motsvarande Linux-sökväg, *var/lib/sfcerts*).

#### <a name="common-name-based-certificate-presentation-declarations"></a>Vanliga namn på certifikat presentations deklarationer
Nodtypen certifikat kan också deklareras efter eget ämnes namn som WINS nedan:

```xml
  <NodeTypes>
    <NodeType Name="nt1vm">
      <Certificates>
        <ClusterCertificate X509FindType="FindBySubjectName" X509FindValue="demo.cluster.azuredocpr.system.servicefabric.azure-int" Name="ClusterCertificate" />
      </Certificates>
    </NodeType>
  </NodeTypes>
```

För båda typerna av deklaration kommer en Service Fabric-nod att läsa konfigurationen vid start, hitta och läsa in de angivna certifikaten och sortera dem i fallande ordning efter sina NotBefore-attribut; certifikat som har upphört att gälla ignoreras och det första elementet i listan är markerat som klientens autentiseringsuppgifter för alla Service Fabric-anslutningar som görs av den här noden. (I praktiken prioriteras Service Fabric det senast utfärdade certifikatet.)

> [!NOTE]
> Före version 7.2.445 (7,2 CU4) har Service Fabric valt det certifikat som är längst ut (certifikatet med den längst bort egenskapen NotAfter)

Observera att för vanliga namnbaserade presentations deklarationer anses ett certifikat vara en matchning om dess eget ämnes namn är lika med X509FindValue (eller X509FindValueSecondary) i deklarationen som en Skift läges känslig, exakt sträng jämförelse. Detta är i motsats med validerings reglerna, som stöder matchning av jokertecken, samt Skift läges känsliga sträng jämförelser.  

### <a name="miscellaneous-certificate-configuration-settings"></a>Konfigurations inställningar för diverse certifikat
Det nämndes tidigare att säkerhets inställningarna för ett Service Fabric-kluster också tillåter en föränderlig ändring av verifierings kodens beteende. Även om artikeln om [Service Fabric kluster inställningar](service-fabric-cluster-fabric-settings.md) representerar den omfattande och mest uppdaterade listan med inställningar, kommer vi att utökas enligt ett urval av säkerhets inställningar här, för att slutföra den fullständiga exponerade autentiseringen på certifikatbaserad autentisering. För varje inställning förklarar vi avsikten, standardvärdet/beteendet, hur det påverkar autentiseringen och vilka värden som är acceptabla.

Som nämnts innebär certifikat valideringen alltid att skapa och utvärdera certifikat kedjan. För CA-utfärdade certifikat innebär detta ett enkelt OS API-anrop vanligt vis flera utgående anrop till olika slut punkter för den utfärdande PKI: n, cachelagring av svar och så vidare. Med tanke på att certifikat verifierings anropen visas i ett Service Fabric-kluster, kan eventuella problem i PKI: ns slut punkter leda till minskad tillgänglighet för klustret eller att uppnå en korrekt uppdelning. Även om utgående samtal inte kan undertryckas (se nedan i avsnittet Vanliga frågor och svar) kan du använda följande inställningar för att maskera verifierings fel som orsakas av misslyckade CRL-anrop.

  * CrlCheckingFlag – under avsnittet "säkerhet" är strängen konverterad till UINT. Värdet för den här inställningen används av Service Fabric för att maskera fel i certifikat kedjans status genom att ändra beteendet för skapande av kedja. den skickas till Win32 CryptoAPI [CertGetCertificateChain](/windows/win32/api/wincrypt/nf-wincrypt-certgetcertificatechain) -anropet som parametern "dwFlags" och kan ställas in på en giltig kombination av flaggor som accepteras av funktionen. Värdet 0 tvingar Service Fabric körningen att ignorera eventuella förtroende status fel – detta rekommenderas inte, eftersom användningen skulle utgöra en betydande säkerhets exponering. Standardvärdet är 0x40000000 (CERT_CHAIN_REVOCATION_CHECK_CHAIN_EXCLUDE_ROOT).

  När du ska använda: för lokal testning, med självsignerade certifikat eller certifikat för utvecklare som inte är fullständigt utformade/inte har en korrekt infrastruktur för offentliga nycklar för att stödja certifikaten. Kan också användas som en minskning i Air-gapped miljöer under över gången mellan PKI: er.

  Så här använder du: vi tar ett exempel som tvingar återkallnings kontroll att endast komma åt cachelagrade URL: er. Förutsatt att
  ```C++
  #define CERT_CHAIN_REVOCATION_CHECK_CACHE_ONLY         0x80000000
  ```
  sedan blir deklarationen i kluster manifestet:
  ```xml
    <Section Name="Security">
      <Parameter Name="CrlCheckingFlag" Value="0x80000000" />
    </Section>
  ```

  * IgnoreCrlOfflineError – under avsnittet "säkerhet", booleskt värde med standardvärdet FALSE. Representerar en genväg för att undertrycka en fel status för återkallnings skapande av offline-kedja (eller efterföljande Status verifierings fel).

  När du ska använda: lokal testning eller med utvecklarresurser som inte backas upp av en riktig PKI. Använd som lösning i Air – gapped miljöer eller när PKI: n är känd för att bli oåtkomlig.

  Så här använder du:
  ```xml
    <Section Name="Security">
      <Parameter Name="IgnoreCrlOfflineError" Value="true" />
    </Section>
  ```

  Andra viktiga inställningar (alla under avsnittet "säkerhet"):
  * AcceptExpiredPinnedClusterCertificate – beskrivs i avsnittet dedikerat till tumavtryck för certifikat verifiering; tillåter att inaktuella självsignerade kluster certifikat accepteras. 
  * CertificateExpirySafetyMargin-Interval, uttryckt i minuter före certifikatets NotAfter-tidsstämpel, och under vilka certifikatet anses vara utsatt för förfallo datum. Service Fabric övervakar kluster certifikat och utvärderar regelbundet hälso rapporter om deras återstående tillgänglighet. Inom säkerhets intervallet är dessa hälso rapporter förhöjda med status varning. Standardvärdet är 30 dagar.
  * CertificateHealthReportingInterval – styr frekvensen för hälso rapporter avseende återstående giltighets tid för kluster certifikat. Rapporter genereras bara en gång per det här intervallet. Värdet uttrycks i sekunder, med standardvärdet 8 timmar.
  * EnforcePrevalidationOnSecurityChanges-Boolean, styr beteendet för kluster uppgraderingen vid identifiering av ändringar av säkerhets inställningar. Om det är inställt på "true" försöker kluster uppgraderingen se till att minst ett certifikat som matchar någon av presentations reglerna kan skicka en motsvarande verifierings regel. För valideringen utförs innan de nya inställningarna tillämpas på alla noder, men körs bara på noden som är värd för den primära repliken av kluster hanterarens tjänst vid tidpunkten för initieringen av uppgraderingen. Vid skrivning har inställningen värdet "false" och ställs in på "true" för nya Azure Service Fabric-kluster med en körnings version som börjar med 7,1.
 
### <a name="end-to-end-scenario-examples"></a>Scenario för slut punkt till slut punkt (exempel)
Vi har tittat på presentations regler, verifierings regler och justeringar av flaggor, men hur fungerar allt tillsammans? I det här avsnittet kommer vi att arbeta genom två exempel från slut punkt till slut punkt som demonstrerar hur säkerhets inställningarna kan utnyttjas för säkra kluster uppgraderingar. Observera att detta inte är avsett att vara en omfattande Dissertation för korrekt certifikat hantering i Service Fabric, leta efter en kompletterande artikel i det avsnittet.

Separationen av presentations-och validerings regler utgör den uppenbara frågan (eller oro) över om de kan skilja sig åt, och vad konsekvenserna skulle vara. Det är faktiskt möjligt att en nods val av ett autentiseringscertifikat inte klarar validerings reglerna för en annan nod. Den här avvikelsen är i själva verket den primära orsaken till de autentiseringsfel som är relaterade till autentisering. Vid samma tidpunkt gör avgränsningen av dessa regler att ett kluster kan fortsätta att fungera under en uppgradering som ändrar klustrets säkerhets inställningar. Tänk på att genom att utöka första validerings reglerna som ett första steg konvergerar alla klustrets noder på de nya inställningarna samtidigt som de fortfarande använder de aktuella autentiseringsuppgifterna. 

I ett Service Fabric-kluster går en uppgradering vidare genom (upp till 5) uppgraderings domäner eller UDs. Endast noder i den aktuella UD uppgraderas/ändras vid en specifik tidpunkt och uppgraderingen fortsätter sedan till nästa UD endast om klustrets tillgänglighet tillåter det. (Mer information finns i [Service Fabric kluster uppgraderingar](service-fabric-cluster-upgrade.md) och andra artiklar i samma ämne.) Certifikat/säkerhets ändringar är särskilt riskfyllda, eftersom de kan isolera noder från klustret, eller lämna klustret på gränsen för att förlora kvorum.

Vi kommer att använda följande notation för att beskriva en nods säkerhets inställningar:

Tomma: {P:{TP = A}, V:{TP = A}}, där:
  - ' Tomma ' representerar en nod i uppgraderings domän *k*
  - ' P ' representerar nodens aktuella presentations regler (förutsatt att vi bara refererar till kluster certifikat). 
  - V representerar nodens aktuella verifierings regler (endast kluster certifikat)
  - ' TP = A ' representerar en tumavtryck-baserad deklaration (TP), med ' A ' som tumavtryck för certifikatet
  - ' CN = B ' representerar en gemensam namnbaserade deklaration (CN), med ' B ' som certifikatets ämnes namn för certifikat mottagare 

#### <a name="rotating-a-cluster-certificate-declared-by-thumbprint"></a>Rotera ett kluster certifikat som deklarerats av tumavtrycket
Följande sekvens beskriver hur en 2-stegs uppgradering kan användas för att på ett säkert sätt införa ett sekundärt kluster certifikat, deklarerat av tumavtryck; i den första fasen införs den nya certifikat deklarationen i verifierings reglerna och den andra fasen introducerar den i presentations reglerna:
  - initialt tillstånd: N0 = {P:{TP = A}, V:{TP = A}},... Tomma = {P:{TP = A}, V:{TP = A}} – klustret är i vila, alla noder delar en gemensam konfiguration
  - När du har slutfört uppgraderings domän 0: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Tomma = {P:{TP = A}, V:{TP = A}}-noder i UD0 kommer att presentera certifikat A och acceptera certifikat A eller B; alla andra noder finns och accepterar bara certifikat A
  - När du har slutfört den senaste uppgraderings domänen: N0 = {P:{TP = A}, V:{TP = A, TP = B}},... Tomma = {P:{TP = A}, V:{TP = A, TP = B}}-alla noder visar certifikat A, alla noder accepterar antingen certifikat A eller B
      
I det här läget är klustret återigen i jämvikt och den andra fasen i uppgraderings-och ändrings säkerhets inställningarna kan påbörjas:
  - När du har slutfört uppgraderings domän 0: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Tomma = {P:{TP = A}, V:{TP = A, TP = B}}-noder i UD0 kommer att börja presentera B, vilket accepteras av en annan nod i klustret.
  - vid slutförandet av den senaste uppgraderings domänen: N0 = {P:{TP = A, TP = B}, V:{TP = A, TP = B}},... Tomma = {P:{TP = A, TP = B}, V:{TP = A, TP = B}}-alla noder har växlat till att presentera certifikat B. certifikat A kan nu dras tillbaka/tas bort från kluster definitionen med en efterföljande uppsättning uppgraderingar.

#### <a name="converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations"></a>Konvertera ett kluster från tumavtryck till vanliga namnbaserade certifikat deklarationer
På samma sätt kommer att ändra typ av certifikat deklaration (från tumavtryck till eget namn) enligt samma mönster som ovan. Observera att validerings reglerna tillåter att du deklarerar certifikaten för en specifik roll enligt både tumavtryck och eget namn i samma kluster definition. Däremot tillåter presentations reglerna bara en form av deklaration. En säker metod för att konvertera ett kluster certifikat från tumavtryck till eget namn är att presentera det avsedda mål certifikatet först efter tumavtryck och sedan ändra deklarationen till ett gemensamt namn-baserat. I följande exempel förutsätter vi att tumavtrycket "A" och subjektets gemensamma namn "B" refererar till samma certifikat. 

  - initialt tillstånd: N0 = {P:{TP = A}, V:{TP = A}},... Tomma = {P:{TP = A}, V:{TP = A}} – klustret är i vila, alla noder delar en gemensam konfiguration med ett som primärt tumavtryck för certifikatet
  - När du har slutfört uppgraderings domän 0: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Tomma = {P:{TP = A}, V:{TP = A}}-noder i UD0 kommer att presentera certifikat A och acceptera certifikat med antingen tumavtryck A eller eget namn B; alla andra noder finns och accepterar bara certifikat A
  - När du har slutfört den senaste uppgraderings domänen: N0 = {P:{TP = A}, V:{TP = A, CN = B}},... Tomma = {P:{TP = A}, V:{TP = A, CN = B}}-alla noder visar certifikat A, alla noder accepterar antingen Certificate A (TP) eller B (CN)

I det här läget kan vi fortsätta med att ändra presentations reglerna med en efterföljande uppgradering:
  - När du har slutfört uppgraderings domän 0: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Tomma = {P:{TP = A}, V:{TP = A, CN = B}}-noder i UD0 kommer att presentera certifikat B som påträffas av CN och acceptera certifikat med antingen tumavtryck A eller eget namn B; alla andra noder finns och accepterar endast certifikat A, valt per tumavtryck
  - vid slutförandet av den senaste uppgraderings domänen: N0 = {P:{CN = B}, V:{TP = A, CN = B}},... Tomma = {P:{CN = B}, V:{TP = A, CN = B}}-alla noder som finns certifikat B påträffade av CN. alla noder accepterar antingen Certificate A (TP) eller B (CN)
    
Vid avslutning av fas 2 märks även konverteringen av klustret till vanliga namnbaserade certifikat. de tumavtryckbaserade verifierings deklarationerna kan tas bort i en efterföljande kluster uppgradering.

> [!NOTE]
> I Azure Service Fabric-kluster dirigeras de arbets flöden som anges ovan av Service Fabric Resource Provider. kluster ägaren ansvarar fortfarande för etablering av certifikat i klustret enligt de angivna reglerna (presentation eller validering) och rekommenderas att utföra ändringar i flera steg.

I en separat artikel kommer vi att adressera avsnittet om att hantera och tillhandahålla certifikat till ett Service Fabric-kluster.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Fel sökning och vanliga frågor och svar
Det är inte enkelt att felsöka autentiseringsfel i Service Fabric kluster, men vi är Hopeful följande tips och tips. Det enklaste sättet att starta undersökningar är att undersöka Service Fabric händelse loggar på noderna i klustret – inte nödvändigt vis bara de som visar symtom, men även noder som är upp till varandra, men som inte kan ansluta till någon av deras grannar. I Windows loggas Events signifikans vanligt vis under "program-och tjänst Logs\Microsoft-ServiceFabric\Admin" eller "drifts kanaler". Ibland kan det vara bra att [Aktivera CAPI2-loggning](/archive/blogs/benjaminperkins/enable-capi2-event-logging-to-troubleshoot-pki-and-ssl-certificate-issues)för att samla in mer information om certifikat validering, hämtning av CRL/CTL osv. (kom ihåg att inaktivera det när du har slutfört återskapnings, men det kan vara ganska utförligt.)

Vanliga symptom som manifestet i ett kluster som har problem med autentisering är: 
  - noderna är nere/arbetscykler 
  - anslutnings försök nekas
  - tids gränsen för anslutnings försök

Var och en av symptomen kan bero på olika problem och samma rotor saken kan visa olika godsspecifikationer. Därför visar vi bara ett litet exempel på vanliga problem, med rekommendationer för att åtgärda dem. 

* Noder kan utbyta meddelanden men kan inte ansluta. En möjlig orsak till att anslutnings försöken avbryts är felet "certifikatet matchar inte". en av parterna i ett Service Fabric-till-Service Fabric-anslutningar presenterar ett certifikat som inte kan vara mottagarens validerings regler. Kan åtföljas av något av följande fel: 
  ```C++
  0x80071c44    -2147017660 FABRIC_E_SERVER_AUTHENTICATION_FAILED
  ```
  För att diagnostisera/undersöka ytterligare: på varje nod som försöker ansluta, bestäm vilka certifikat som presenteras. Undersök certifikatet och försök att emulera verifierings reglerna (kontrol lera om tumavtryck eller gemensamma namn är lika, kontrol lera Issuer-tumavtrycken om det angetts).

  En annan gemensam medföljande felkod kan vara:
  ```C++
  0x800b0109    -2146762487 CERT_E_UNTRUSTEDROOT
  ```
  I det här fallet deklareras certifikatet efter eget namn och något av följande gäller:
    - utfärdarna har inte fästs och rot certifikatet är inte betrott, eller
    - utfärdarna är fästa men deklarationen innehåller inte tumavtrycket för den direkta utfärdaren av det här certifikatet

* En nod är upp, men kan inte ansluta till andra noder. andra noder tar inte emot inkommande trafik från den felaktiga noden. I det här fallet är det möjligt att inläsningen av certifikatet Miss lyckas på den lokala noden. Leta efter följande fel:
  - Det gick inte att hitta certifikatet – se till att de certifikat som har deklarerats i presentations reglerna kan lösas genom att innehållet i LocalMachine\My (eller det angivna) certifikat arkivet visas. 
    Möjliga orsaker till fel kan vara: 
      - ogiltiga tecken i tumavtryck-deklarationen
      - certifikatet är inte installerat
      - certifikatet har upphört att gälla
      - den gemensamma namn deklarationen innehåller prefixet "CN ="
      - deklarationen anger ett jokertecken och ingen exakt matchning finns i certifikat arkivet (deklaration: CN = *. domän. com, faktiskt certifikat: CN = Server. domän. com)

  - okända autentiseringsuppgifter – indikerar att en privat nyckel som saknas motsvarar certifikatet, vanligt vis åtföljd av felkod: 
    ```C++ 
    0x8009030d  -2146893043 SEC_E_UNKNOWN_CREDENTIALS
    0x8009030e  -2146893042 SEC_E_NO_CREDENTIALS
    ```
    För att åtgärda det, kontrol lera förekomsten av den privata nyckeln. kontrol lera att SFAdmins har beviljats behörigheten läsa | kör till den privata nyckeln.

  - felaktig providertyp – anger ett CNG-certifikat (New Provider för kryptering av en ny generation) ("Microsoft Software Key Storage Provider"). för tillfället stöder Service Fabric endast certifikat för CAPI1. Normalt åtföljs av felkod:
    ```C++
    0x80090014  -2146893804 NTE_BAD_PROV_TYPE
    ```
    Du kan åtgärda detta genom att återskapa kluster certifikatet med hjälp av en CAPI1 (t. ex. "Microsoft Enhanced RSA and AES Cryptographic Provider"). Mer information om krypto-providers finns i [förstå kryptografiproviders](/windows/win32/seccertenroll/understanding-cryptographic-providers)
