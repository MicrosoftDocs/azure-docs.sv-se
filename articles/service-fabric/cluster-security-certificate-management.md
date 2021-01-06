---
title: Certifikat hantering i ett Service Fabric kluster
description: Lär dig mer om att hantera certifikat i ett Service Fabric kluster som skyddas med X. 509-certifikat.
ms.topic: conceptual
ms.date: 04/10/2020
ms.custom: sfrev
ms.openlocfilehash: 722c84c25cb5188e45dd96363bab9af6ff93f6dc
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901274"
---
# <a name="certificate-management-in-service-fabric-clusters"></a>Certifikat hantering i Service Fabric kluster

Den här artikeln tar itu med hanterings aspekterna av certifikat som används för att skydda kommunikationen i Azure Service Fabric-kluster, och kompletterar introduktionen till [Service Fabric kluster säkerhet](service-fabric-cluster-security.md) samt förklaringen om [509-baserad autentisering i X. i Service Fabric](cluster-security-certificates.md). Vi utgår från att läsaren är bekant med grundläggande säkerhets koncept och även med de kontroller som Service Fabric visar för att konfigurera säkerheten för ett kluster.  

Aspekter som omfattas av denna rubrik:

* Vad exakt är "certifikat hantering"?
* Roller och entiteter som ingår i certifikat hantering
* Resan för ett certifikat
* Djupgående att gå in i ett exempel
* Fel sökning och vanliga frågor och svar

Men första fri skrivning: den här artikeln försöker para ihop den teoretiska sidan med praktiska exempel, vilket kräver, även vissa tjänster, tekniker och så vidare. Eftersom en betydande del av mål gruppen är Microsoft-Internal, kommer vi att hänvisa till tjänster, tekniker och produkter som är speciella för Microsoft Azure. Skriv gärna i avsnittet kommentarer för klargöranden eller vägledning, där den information som hör till Microsoft inte gäller i ditt fall.

## <a name="defining-certificate-management"></a>Definiera certifikat hantering
Som vi har sett i den [medföljande artikeln](cluster-security-certificates.md)är ett certifikat ett kryptografiskt objekt som huvudsakligen binder ett asymmetriskt nyckel par med attribut som beskriver den entitet som det representerar. Men det är också ett ömtåligt-objekt, i så fall att dess livs längd är begränsad och är utsatt för att kompromissa – oavsiktligt utlämnande eller en lyckad utnyttjande kan göra att ett certifikat inte kan användas av säkerhets synpunkt. Detta innebär att du behöver ändra certifikat – antingen rutinmässigt eller som svar på en säkerhets incident. En annan aspekt av hantering (och ett helt ämne på egen hand) är att skydda certifikatets privata nycklar, eller av hemligheter som skyddar identiteterna hos de enheter som ingår i anskaffning och etablering av certifikat. Vi refererar till de processer och procedurer som används för att hämta certifikat och för att på ett säkert sätt transportera dem till den plats där de behövs som "certifikat hantering". Några av hanterings åtgärderna – till exempel registrering, princip inställning och verifierings kontroller – är utöver den här artikelns omfattning. Fortfarande andra – till exempel etablering, förnyelse, omgenerering eller åter kallelse – är endast incidenter som är relaterade till Service Fabric. Vi kommer dock att adressera dem här till en viss grad, eftersom vi kan hjälpa dem att skydda ett kluster på ett säkert sätt. 

Målet är att automatisera certifikat hanteringen så mycket som möjligt för att säkerställa att klustrets tillgänglighet är oavbrutet och ger säkerhets garantier, förutsatt att processen är helt användarvänlig. Det här målet kan uppnås för närvarande i Azure Service Fabric-kluster; resten av artikeln dekonstruerar först certifikat hanteringen, och senare fokuserar på att aktivera autoförnyelse.

Mer specifikt är ämnena i omfattning:
  - antaganden som rör separering av tilldelningar mellan ägare och plattform, i samband med hantering av certifikat
  - lång pipeline för certifikat från utfärdande till förbrukning
  - certifikat rotation – varför, hur och när
  - Vad kan eventuellt gå fel?

Aspekt som att skydda/hantera domän namn, registrera i certifikat eller ställa in verifierings kontroller för att genomdriva utfärdande av certifikat omfattas inte av den här artikeln. Referera till registrerings utfärdaren (RA) i din favorit tjänst för offentliga nycklar (PKI). Microsoft-interna konsumenter: kontakta Azure-säkerheten.

## <a name="roles-and-entities-involved-in-certificate-management"></a>Roller och entiteter som ingår i certifikat hantering
Säkerhets metoden i ett Service Fabric-kluster är ett fall av "kluster ägaren deklarerar det, Service Fabric runtime tillämpar det". Det innebär att nästan inget av certifikaten, nycklarna eller andra autentiseringsuppgifter för identiteter som deltar i ett kluster fungerar från själva tjänsten. de deklareras alla av klustrets ägare. Dessutom ansvarar även kluster ägaren för etablering av certifikaten i klustret, förnyar dem efter behov och säkerställer säkerheten för certifikaten hela tiden. Mer specifikt måste kluster ägaren se till att:
  - certifikat som har deklarerats i NodeType-avsnittet i kluster manifestet finns på varje nod av den typen, enligt [presentations reglerna](cluster-security-certificates.md#presentation-rules)
  - certifikat som anges ovan installeras inklusive deras motsvarande privata nycklar
  - certifikat som har deklarerats i presentations reglerna ska klara [validerings reglerna](cluster-security-certificates.md#validation-rules) 

Service Fabric, för dess del, antar ansvars områdena för:
  - Hitta/hitta certifikat som matchar deklarationerna i kluster definitionen  
  - bevilja åtkomst till motsvarande privata nycklar till Service Fabric-styrda entiteter på ett behovs underlag
  - verifiera certifikat i strikt enlighet med etablerade säkerhets metoder och kluster definitionen
  - att öka aviseringar när certifikaten upphör att gälla, eller om det inte går att utföra de grundläggande stegen för certifikat validering
  - verifierar (till viss grad) att de certifikat som är relaterade till kluster definitionen uppfylls av den underliggande konfigurationen av värdarna 

Det följer på att certifikat hanteringen för belastningen (som aktiva åtgärder) bara ingår i kluster ägaren. I följande avsnitt tar vi en närmare titt på varje hanterings åtgärd, med tillgängliga mekanismer och deras inverkan på klustret.

## <a name="the-journey-of-a-certificate"></a>Resan för ett certifikat
Låt oss snabbt gå tillbaka till ett certifikats förlopp från utfärdande till förbrukning i samband med ett Service Fabric kluster:

  1. En domän ägare registrerar sig för en PKI en domän eller ett ämne som de vill associera med de certifikat som följer. certifikaten kommer, i sin tur, att utgöra bevis på ägande rätt till domänen eller ämnet
  2. Domän ägaren utser också i RA identiteterna för auktoriserade begär Anden – enheter som är berättigade att begära registrering av certifikat med den angivna domänen eller ämnet. i Microsoft Azure är standard identitets leverantören Azure Active Directory och auktoriserade beställare utses genom sin motsvarande AAD-identitet (eller via säkerhets grupper)
  3. En auktoriserad beställare registrerar sedan i ett certifikat via en tjänst för hemliga hantering. i Microsoft Azure är SMS-valet Azure Key Vault (AKV), som på ett säkert sätt lagrar och tillåter att hemligheter/certifikat hämtas av auktoriserade entiteter. AKV förnyar också/förnyar certifikatet enligt konfigurationen i den associerade certifikat principen. (AKV använder AAD som identitets leverantör.)
  4. En auktoriserad hämtare – som vi refererar till som en etablerings agent – hämtar certifikatet, inklusive den privata nyckeln, från valvet och installerar det på de datorer som är värdar för klustret
  5. Service Fabrics tjänsten (som körs med förhöjd behörighet på varje nod) beviljar åtkomst till certifikatet till tillåtna Service Fabric entiteter. de anges av lokala grupper och delas mellan ServiceFabricAdministrators och ServiceFabricAllowedUsers
  6. Service Fabric runtime får åtkomst till och använder certifikatet för att upprätta federationen, eller för att autentisera till inkommande begär Anden från auktoriserade klienter
  7. Etablerings agenten övervakar valvet certifikat och utlöser etablerings flödet vid identifiering av förnyelse. därefter uppdaterar kluster ägaren kluster definitionen, om det behövs, för att ange vilken avsikt som ska användas för att återställa certifikatet.
  8. Etablerings agenten eller kluster ägaren ansvarar också för att rensa/ta bort oanvända certifikat
  
I vårt syfte är de två första stegen i sekvensen ovan i stort sett orelaterade. den enda anslutningen är att certifikatets gemensamma ämnes namn är det DNS-namn som deklarerats i kluster definitionen.

Dessa steg illustreras nedan. Observera skillnaderna i etableringen mellan certifikat som har deklarerats med tumavtryck respektive eget namn.

*Bild 1.* Utfärdande och etablerings flöde för certifikat som deklareras av tumavtryck.
![Etablering av certifikat som deklarerats av tumavtryck][Image1]

*Figur 2.* Utfärdande och etablerings flödet av certifikat som har deklarerats som eget namn för certifikat mottagare.
![Etablering av certifikat som deklarerats av subjektets nätverks namn][Image2]

### <a name="certificate-enrollment"></a>Certifikat registrering
Det här avsnittet beskrivs i detalj i Key Vault- [dokumentationen](../key-vault/certificates/create-certificate.md). Vi inkluderar en sammanfattning här för kontinuitet och enklare referens. Om du fortsätter med Azure som kontext och använder Azure Key Vault som hemlig hanterings tjänst måste en auktoriserad certifikat utfärdare ha minst certifikat hanterings behörigheter för valvet, vilket beviljas av valv ägaren. beställaren registreras sedan i ett certifikat på följande sätt:
    - skapar en certifikat princip i Azure Key Vault (AKV), som anger domän/ämne för certifikatet, önskad utfärdare, nyckel typ och längd, avsedd nyckel användning och mer. Mer information finns i [certifikat i Azure Key Vault](../key-vault/certificates/certificate-scenarios.md) . 
    - skapar ett certifikat i samma valv med principen som anges ovan. Detta skapar i sin tur ett nyckel par som valv objekt, en begäran om certifikat signering som signerats med den privata nyckeln och som sedan vidarebefordras till den utsedda utfärdaren för signering
    - När utfärdaren (certifikat utfärdaren) svarar med det signerade certifikatet, slås resultatet samman i valvet och certifikatet är tillgängligt för följande åtgärder:
      - under {vaultUri}/certificates/{Name}: certifikatet inklusive offentlig nyckel och metadata
      - under {vaultUri}/Keys/{Name}: certifikatets privata nyckel, som är tillgänglig för kryptografiska åtgärder (wrap/unwrap, sign/verify)
      - under {vaultUri}/Secrets/{Name}: certifikatet inkluderar den privata nyckeln som är tillgänglig för nedladdning som en oskyddad PFX-eller PEM-fil  
    Kom ihåg att ett valv certifikat är i själva verket en kronologisk linje med certifikat instanser som delar en princip. Certifikat versioner skapas enligt principens livs längd och förnyelse attribut. Vi rekommenderar starkt att valv certifikat inte delar ämnen eller domäner/DNS-namn; Det kan vara störande i ett kluster för att etablera certifikat instanser från olika valv certifikat, med identiska ämnen men väsentligen olika andra attribut, till exempel utfärdare, nyckel användning osv.

I det här läget finns ett certifikat i valvet som är redo för användning. Vidare till:

### <a name="certificate-provisioning"></a>Certifikat etablering
Vi nämnde en etablerings agent, som är den entitet som hämtar certifikatet, inklusive den privata nyckeln, från valvet och installerar den på alla värdar i klustret. (Kom ihåg att Service Fabric inte etablerar certifikat.) I vårt sammanhang kommer klustret att finnas i en samling virtuella Azure-datorer och/eller skalnings uppsättningar för virtuella datorer. I Azure kan etablering av ett certifikat från ett valv till en VM/VMSS uppnås med följande mekanismer – förutsatt att etablerings agenten tidigare har beviljats "Get"-behörighet för valvet av valv ägaren: 
  - ad hoc: en operatör hämtar certifikatet från valvet (som PFX/PKCS #12 eller pem) och installerar det på varje nod
  - som en virtuell dators skalnings uppsättning "hemlighet" under distributionen: beräknings tjänsten hämtar, använder sin första part identitet för operatören, certifikatet från ett mall-distributions-aktiverat valv och installerar det på varje nod i den virtuella datorns skalnings uppsättning ([t. ex](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).). Observera att detta endast tillåter etablering av versions bara hemligheter
  - använda [Key Vault VM-tillägget](../virtual-machines/extensions/key-vault-windows.md); Detta möjliggör etablering av certifikat med hjälp av versions lösa deklarationer, med regelbunden uppdatering av observerade certifikat. I detta fall förväntas VM/VMSS ha en [hanterad identitet](../virtual-machines/security-policy.md#managed-identities-for-azure-resources), en identitet som har beviljats åtkomst till de valv som innehåller observerade certifikat.

Ad hoc-mekanismen rekommenderas inte av flera orsaker, från säkerhet till tillgänglighet och beskrivs inte här längre. Mer information finns [i certifikat i skalnings uppsättningar för virtuella datorer](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#certificates).

VMSS-/Compute-based-etableringen visar säkerhets-och tillgänglighets fördelarna, men det visar även begränsningar. Det kräver – genom att utforma certifikat som har en versions hemligheter som gör det lämpligt endast för kluster som skyddas med certifikat som deklareras av tumavtryck. Däremot kommer den Key Vault VM-baserade etableringen alltid att installera den senaste versionen av varje observerat certifikat, vilket gör att det bara är lämpligt för kluster som skyddas med certifikat som har deklarerats som eget namn. För att framhäva, ska du inte använda en metod för att skapa en Autouppdatering (t. ex. KVVM-tillägget) för certifikat som har deklarerats av en instans (det vill säga av tumavtrycket) – risken att förlora tillgänglighet är mycket stor.

Andra etablerings metoder kan finnas. ovanstående är för närvarande godkända för Azure Service Fabric-kluster.

### <a name="certificate-consumption-and-monitoring"></a>Certifikat användning och övervakning
Som tidigare nämnts är Service Fabric runtime ansvarig för att hitta och använda de certifikat som har deklarerats i kluster definitionen. Artikeln om [certifikatbaserad autentisering](cluster-security-certificates.md) förklaras i detalj hur Service Fabric implementerar presentations-och verifierings reglerna, och kommer inte att finnas på igen. Vi ska titta på åtkomst-och behörighets beviljande, samt övervakning.

Kom ihåg att certifikat i Service Fabric används för en mängd olika orsaker, från ömsesidig autentisering i Federations skiktet till TLS-autentisering för hanterings slut punkter. Detta kräver att olika komponenter eller system tjänster har åtkomst till certifikatets privata nyckel. Service Fabric runtime söker regelbundet igenom certifikat arkivet och söker efter matchningar för var och en av de kända presentations reglerna. för var och en av de matchande certifikaten finns motsvarande privata nyckel och den diskreta åtkomst kontrol listan uppdateras för att inkludera behörigheter – vanligt vis läsa och köra-beviljad till respektive identitet som kräver dem. (Den här processen kallas för "ACLing".) Processen körs på en takt på 1 minut och omfattar även "program"-certifikat, till exempel de som används för att kryptera inställningar eller som slut punkts certifikat. ACLing följer presentations reglerna, så det är viktigt att tänka på att certifikat som deklarerats av tumavtryck och som uppdateras automatiskt utan att uppdateringen av kluster konfigurationen kommer att vara tillgängliga.    

### <a name="certificate-rotation"></a>Certifikat rotation
Som sido anteckning: IETF [RFC 3647](https://tools.ietf.org/html/rfc3647) formellt definierar [förnyelse](https://tools.ietf.org/html/rfc3647#section-4.4.6) som utfärdande av ett certifikat med samma attribut som det certifikat som byts ut – utfärdaren, ämnets offentliga nyckel och information bevaras och [återskapas](https://tools.ietf.org/html/rfc3647#section-4.4.7) som utfärdande av ett certifikat med ett nytt nyckel par, och inga begränsningar för huruvida utfärdaren kan ändras eller inte. Med tanke på att åtskillnaden kan vara viktig (det vill säga om certifikat som har deklarerats av subjektets gemensamma namn med utgivart fästs) väljer vi den neutrala termen rotation för att se båda scenarierna. (Tänk på att när du har använt "förnyelse" i det här fallet, refererar "förnyelse", i själva verket, för att skapa en ny nyckel.) 

Vi har sett tidigare att Azure Key Vault stöder automatisk certifikat rotation: den associerade certifikat principen definierar tidpunkten, om dagar före förfallo datum eller procent andel av den totala livs längden, när certifikatet roteras i valvet. Etablerings agenten måste anropas efter den här tidpunkten och innan det nu föregående certifikatet upphör att gälla, så att det nya certifikatet distribueras till alla noder i klustret. Service Fabric hjälper till att öka hälso varningar när ett certifikats förfallo datum (och som för närvarande används i klustret) inträffar tidigare än ett förinställt intervall. En automatisk etablerings agent (dvs. det virtuella nyckel valvets tillägg), som kon figurer ATS för att kontrol lera valv certifikatet, avsöker valvet med jämna mellanrum, identifierar rotationen och hämtar och installerar det nya certifikatet. Etableringen som görs via funktionen "hemligheter" i VM/VMSS kräver en auktoriserad operatör för att uppdatera VM/VMSS med den version av nyckel valvets URI som motsvarar det nya certifikatet.

I båda fallen allokeras det roterade certifikatet till alla noder och vi har benämnt mekanismen Service Fabric använder för att identifiera rotationer. Låt oss undersöka vad som händer härnäst – under förutsättning att den rotation som används för kluster certifikatet som deklarerats av subjektets eget namn
  - för nya anslutningar inom, och i klustret, kommer Service Fabric runtime att hitta och välja det senast utfärdade matchnings certifikatet (det största värdet för egenskapen ' NotBefore '). Observera att detta är en förändring från tidigare versioner av Service Fabric Runtime.
  - befintliga anslutningar hålls levande/tillåtna till naturlig upphör ande eller avbryts på annat sätt. en intern hanterare får ett meddelande om att det finns en ny matchning

> [!NOTE] 
> Före version 7.2.445 (7,2 CU4) har Service Fabric valt det certifikat som är längst ut (certifikatet med den längst bort egenskapen NotAfter)

Detta översätter till följande viktiga observationer:
  - Förnyelse certifikatet kan ignoreras om dess förfallo datum är tidigare än det för det certifikat som används för närvarande.
  - Tillgängligheten för klustret eller de värdbaserade programmen prioriteras över direktivet för att rotera certifikatet. klustret konvergerar vid det nya certifikatet, men utan tids garanti. Den följer:
  - Det kan inte omedelbart vara uppenbart för en observatör att det roterade certifikatet fullständigt ersattes med föregående. Det enda sättet att se till att det är (för kluster certifikat) att starta om värd datorerna. Observera att det inte räcker att starta om Service Fabric-noderna, eftersom kernel-läge-komponenter som utgör låne anslutningar i ett kluster inte kommer att påverkas. Observera också att omstart av den virtuella datorn/VMSS kan orsaka tillfälligt förlust av tillgänglighet. (För program certifikat räcker det att starta om respektive program instans.)
  - Om du introducerar ett certifikat som inte uppfyller validerings reglerna kan det effektivt bryta klustret. Det vanligaste exemplet på detta är fallet med en oväntad utfärdare: kluster certifikaten deklareras av subjektets gemensamma namn med utfärdarens fast låsning, men det roterade certifikatet utfärdades av en ny/odeklarerad utfärdare.     

### <a name="certificate-cleanup"></a>Certifikat rensning
För närvarande finns det inga bestämmelser i Azure för explicit borttagning av certifikat. Det är ofta en icke-trivial uppgift att avgöra om ett angivet certifikat används vid en specifik tidpunkt. Detta är svårare för program certifikat än för kluster certifikat. Service Fabric som inte är etablerings agenten kommer inte att ta bort ett certifikat som har deklarerats av användaren under någon omständighet. För standard etablerings metoder:
  - Certifikat som har deklarerats som VM/VMSS-hemligheter kommer att tillhandahållas så länge de refereras till i definitionerna för VM/VMSS och de kan hämtas från valvet (om du tar bort ett valv hemlighet/certifikat kommer att Miss klar med efterföljande VM/VMSS-distributioner
  - Tidigare versioner av certifikat som tillhandahålls via det virtuella dator tillägget för nyckel valvet kan vara inaktuella på en VM/VMSS-nod. Agenten hämtar och installerar bara den aktuella versionen och tar inte bort några certifikat. Genom att ändra avbildningen av en nod (som normalt sker varje månad) återställs certifikat arkivet till innehållet i operativ system avbildningen och tidigare versioner kommer implicit att tas bort. Tänk på att när du skalar upp en skalnings uppsättning för virtuella datorer leder det bara till att den aktuella versionen av observerade certifikat installeras. anta inte homogena noder med avseende på installerade certifikat.   

## <a name="simplifying-management---an-autorollover-example"></a>Förenkla hanteringen – ett exempel på en autoöverrullning
Vi har beskrivit mekanismer, begränsningar, fördelade invecklade regler och definitioner och gjort ödesdigra förutsägelser av avbrott. Det kan t. ex. vara dags att visa hur du konfigurerar automatisk certifikat hantering för att undvika alla dessa problem. Vi gör detta i samband med ett Azure Service Fabric-kluster som körs på en skal uppsättning för virtuella PaaSv2-datorer, med Azure Key Vault för hemligheter och hantering av hanterade identiteter, enligt följande:
  - Valideringen av certifikat har ändrats från tumavtryck till ämne + utfärdare fästa: alla certifikat med ett angivet ämne från en specifik utfärdare är jämnt betrodda
    - Certifikat registreras i och hämtas från ett betrott arkiv (Key Vault) och uppdateras av en agent – i det här fallet det virtuella nyckel valvets tillägg
    - Etablering av certifikat ändras från distributions tid och versions hantering (som utförs av ComputeRP) för att publicera och använda versions lösa URI: er för nyckel valv
    - Åtkomst till nyckel valv beviljas via användarspecifika hanterade identiteter. UA-identiteten skapas och tilldelas till skalnings uppsättningen för den virtuella datorn under distributionen
    - Efter distributionen kommer agenten (KV VM-tillägget) att söka efter och uppdatera observerade certifikat på varje nod i skalnings uppsättningen för den virtuella datorn. certifikat rotationen är därför helt automatiserad, eftersom SF automatiskt kommer att hämta det längst bort giltiga certifikatet

Sekvensen är fullständigt skriptad/automatiserad och gör det möjligt att göra en första distribution av ett kluster som har kon figurer ATS för automatisk förnyelse av certifikat. Detaljerade anvisningar finns nedan. Vi använder en blandning av PowerShell-cmdletar och fragment med JSON-mallar. Samma funktioner kan bara användas med alla metoder som stöds för att interagera med Azure.

> [!NOTE]
> Det här exemplet förutsätter att det redan finns ett certifikat i valvet. att registrera och förnya ett certifikat som hanteras med ett valv kräver nödvändiga manuella steg enligt beskrivningen ovan i den här artikeln. För produktions miljöer, Använd Key Vault-hanterade certifikat – ett exempel skript som är speciellt för en Microsoft-intern PKI ingår nedan.

> [!NOTE]
> Förnyelse av certifikat är bara begripligt för CA-utfärdade certifikat. Om du använder självsignerade certifikat, inklusive de som genererades när du distribuerar ett Service Fabric kluster i Azure Portal, är nonsensical, men fortfarande möjligt för lokala/utvecklare som är värdbaserade distributioner, genom att deklarera utfärdarens tumavtryck som är samma som för löv certifikatet.

### <a name="starting-point"></a>Start punkt
För det kortfattat kommer vi att anta följande start tillstånd:
  - Service Fabric-klustret finns och skyddas med ett CA-utfärdat certifikat som deklareras av tumavtryck.
  - Certifikatet lagras i ett valv och tillhandahålls som en hemlighet för skalnings uppsättning för virtuella datorer
  - Samma certifikat kommer att användas för att konvertera klustret till gemensamma namnbaserade certifikat deklarationer och kan därför val IDE ras av ämne och utfärdare. om detta inte är fallet kan du hämta det CA-utfärdade certifikatet som är avsett för detta ändamål och lägga till det i kluster definitionen med tumavtrycket enligt beskrivningen [här](service-fabric-cluster-security-update-certs-azure.md)

Här är ett JSON-utdrag från en mall som motsvarar en sådan tillstånds notering detta utesluter många nödvändiga inställningar och visar bara de aspekter som är relaterade till certifikatet:
```json
  "resources": [
    {   ## VMSS definition
      "apiVersion": "[variables('vmssApiVersion')]",
      "type": "Microsoft.Compute/virtualMachineScaleSets",
      "name": "[variables('vmNodeTypeName')]",
      "location": "[variables('computeLocation')]",
      "properties": {
        "virtualMachineProfile": {
          "extensionProfile": {
            "extensions": [
            {
                "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
                "properties": {
                  "type": "ServiceFabricNode",
                  "autoUpgradeMinorVersion": true,
                  "publisher": "Microsoft.Azure.ServiceFabric",
                  "settings": {
                    "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                    "nodeTypeRef": "[variables('vmNodeTypeName')]",
                    "dataPath": "D:\\SvcFab",
                    "durabilityLevel": "Bronze",
                    "certificate": {
                        "thumbprint": "[parameters('primaryClusterCertificateTP')]",
                        "x509StoreName": "[parameters('certificateStoreValue')]"
                    }
                  },
                  "typeHandlerVersion": "1.1"
                }
            },}},
          "osProfile": {
            "adminPassword": "[parameters('adminPassword')]",
            "adminUsername": "[parameters('adminUsername')]",
            "secrets": [
            {
                "sourceVault": {
                    "id": "[resourceId('Microsoft.KeyVault/vaults', parameters('keyVaultName'))]"
                },
                "vaultCertificates": [
                {
                    "certificateStore": "[parameters('certificateStoreValue')]",
                    "certificateUrl": "[parameters('clusterCertificateUrlValue')]"
                },
            ]}]
        },
    },
    {   ## cluster definition
        "apiVersion": "[variables('sfrpApiVersion')]",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "certificate": {
            "thumbprint": "[parameters('primaryClusterCertificateTP')]",
            "x509StoreName": "[parameters('certificateStoreValue')]"
        },
    }
  ]
```   

Ovan säger vi att certifikat med tumavtryck ```json [parameters('primaryClusterCertificateTP')] ``` och hittas i nyckel valvets URI ```json [parameters('clusterCertificateUrlValue')] ``` deklareras som klustrets enda certifikat, efter tumavtryck. Härnäst ska vi ställa in de ytterligare resurser som krävs för att säkerställa att certifikatet förnyas.

### <a name="setting-up-prerequisite-resources"></a>Konfigurera nödvändiga resurser
Som nämnts tidigare hämtas ett certifikat som tillhandahålls som en hemlighet för en virtuell dators skalnings uppsättning från valvet av tjänsten Microsoft. Compute Resource Provider, med sin första part identitet och för distributions operatörens räkning. Vid autoförnyelse, som ändras – kommer vi att växla till att använda en hanterad identitet, tilldelad till den virtuella datorns skalnings uppsättning och som beviljas behörighet till valvets hemligheter.

Alla efterföljande utdrag bör distribueras concomitantly – de anges individuellt för analys och förklaringar av Play-för-Play.

Definiera först en tilldelad identitet (standardvärden ingår som exempel) – Läs den [officiella dokumentationen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity) för uppdaterad information:
```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "userAssignedIdentityName": {
      "type": "string",
      "defaultValue": "sftstuaicus",
      "metadata": {
        "description": "User-assigned managed identity name"
      }
    },
  },
  "variables": {
      "vmssApiVersion": "2018-06-01",
      "sfrpApiVersion": "2018-02-01",
      "miApiVersion": "2018-11-30",
      "kvApiVersion": "2018-02-14",
      "userAssignedIdentityResourceId": "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('userAssignedIdentityName'))]"
  },    
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('userAssignedIdentityName')]",
      "apiVersion": "[variables('miApiVersion')]",
      "location": "[resourceGroup().location]"
    },
  ]}
```

Ge sedan denna identitets åtkomst till valv hemligheterna – Läs den [officiella dokumentationen](/rest/api/keyvault/vaults/updateaccesspolicy) för aktuell information:
```json
  "resources":
  [{
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "name": "[concat(parameters('keyVaultName'), '/add')]",
      "apiVersion": "[variables('kvApiVersion')]",
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).tenantId]",
            "objectId": "[reference(variables('userAssignedIdentityResourceId'), variables('miApiVersion')).principalId]",
            "dependsOn": [
              "[variables('userAssignedIdentityResourceId')]"
            ],
            "permissions": {
              "secrets": [
                "get",
                "list"
              ]}}]}}]
```

I nästa steg ska vi:
  - tilldela den användardefinierade identiteten till den virtuella datorns skal uppsättning
  - deklarera skalnings uppsättningen för den virtuella datorn beroende på hur den hanterade identiteten skapas och på resultatet av att ge åtkomst till valvet
  - deklarera det virtuella nyckel valvets tillägg, vilket kräver att det hämtar observerade certifikat vid start ([officiell dokumentation](../virtual-machines/extensions/key-vault-windows.md))
  - uppdatera definitionen för Service Fabric VM-tillägget till beroende av KVVM-tillägget och för att konvertera kluster certifikatet till ett eget namn (vi gör dessa ändringar i ett enda steg eftersom de befinner sig under samma resurs omfång).

```json
  "parameters": {
    "kvvmextPollingInterval": {
      "type": "string",
      "defaultValue": "3600",
      "metadata": {
        "description": "kv vm extension polling interval in seconds"
      }
    },
    "kvvmextLocalStoreName": {
      "type": "string",
      "defaultValue": "MY",
      "metadata": {
        "description": "kv vm extension local store name"
      }
    },
    "kvvmextLocalStoreLocation": {
      "type": "string",
      "defaultValue": "LocalMachine",
      "metadata": {
        "description": "kv vm extension local store location"
      }
    },
    "kvvmextObservedCertificates": {
      "type": "array",
      "defaultValue": [
                "https://sftestcus.vault.azure.net/secrets/sftstcncluster",
                "https://sftestcus.vault.azure.net/secrets/sftstcnserver"
            ],
      "metadata": {
        "description": "kv vm extension observed certificates versionless uri"
      }
    },
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
  },
  "resources": [
  {
    "apiVersion": "[variables('vmssApiVersion')]",
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[variables('vmNodeTypeName')]",
    "location": "[variables('computeLocation')]",
    "dependsOn": [
      "[variables('userAssignedIdentityResourceId')]",
      "[concat('Microsoft.KeyVault/vaults/', concat(parameters('keyVaultName'), '/accessPolicies/add'))]"
    ],
    "identity": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "[variables('userAssignedIdentityResourceId')]": {}
      }
    },
    "virtualMachineProfile": {
      "extensionProfile": {
        "extensions": [
        {
          "name": "KVVMExtension",
          "properties": {
            "publisher": "Microsoft.Azure.KeyVault",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": "[parameters('kvvmextPollingInterval')]",
                    "linkOnRenewal": false,
                    "observedCertificates": "[parameters('kvvmextObservedCertificates')]",
                    "requireInitialSync": true
                }
            }
          }
        },
        {
        "name": "[concat('ServiceFabricNodeVmExt','_vmNodeTypeName')]",
        "properties": {
          "type": "ServiceFabricNode",
          "provisionAfterExtensions" : [ "KVVMExtension" ],
          "publisher": "Microsoft.Azure.ServiceFabric",
          "settings": {
            "certificate": {
                "commonNames": [
                    "[parameters('certificateCommonName')]"
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            }
            },
            "typeHandlerVersion": "1.0"
          }
        },
  ] } ## extension profile
  },  ## VM profile
  "osProfile": {
    "adminPassword": "[parameters('adminPassword')]",
    "adminUsername": "[parameters('adminUsername')]",
  } 
  }
  ]
```
Observera, men som inte uttryckligen anges ovan, att vi har tagit bort URL: en för valv certifikatet från avsnittet ' OsProfile ' i den virtuella datorns skal uppsättning.
Det sista steget är att uppdatera kluster definitionen för att ändra certifikat deklarationen från tumavtryck till gemensamt namn – här fäster vi även utfärdaren tumavtrycken:

```json
  "parameters": {
    "certificateCommonName": {
      "type": "string",
      "defaultValue": "cus.cluster.sftstcn.system.servicefabric.azure-int",
      "metadata": {
        "description": "Certificate Common name"
      }
    },
    "certificateIssuerThumbprint": {
      "type": "string",
      "defaultValue": "1b45ec255e0668375043ed5fe78a09ff1655844d,d7fe717b5ff3593764f4d90654d86e8362ec26c8,3ac7c3cac8de0dd392c02789c8be97474f456960,96ea05926e2e42cc207e358668be2c316857fb5e",
      "metadata": {
        "description": "Certificate issuer thumbprints separated by comma"
      }
    },
  },
  "resources": [
    {
      "apiVersion": "[variables('sfrpApiVersion')]",
      "type": "Microsoft.ServiceFabric/clusters",
      "name": "[parameters('clusterName')]",
      "location": "[parameters('clusterLocation')]",
      "properties": {
        "certificateCommonNames": {
          "commonNames": [{
              "certificateCommonName": "[parameters('certificateCommonName')]",
              "certificateIssuerThumbprint": "[parameters('certificateIssuerThumbprint')]"
          }],
          "x509StoreName": "[parameters('certificateStoreValue')]"
        },
  ]
```

Nu kan du köra de uppdateringar som nämns ovan i en enda distribution. för sin del delar Service Fabric Resource Provider-tjänsten kluster uppgraderingen i flera steg, enligt beskrivningen i segmentet om [att konvertera kluster certifikat från tumavtryck till eget namn](cluster-security-certificates.md#converting-a-cluster-from-thumbprint--to-common-name-based-certificate-declarations).

### <a name="analysis-and-observations"></a>Analys och observationer
Det här avsnittet är en Catch-allt för att förklara de steg som beskrivs ovan, samt för att dra uppmärksamheten till viktiga aspekter.

#### <a name="certificate-provisioning-explained"></a>Certifikat etablering, förklarad
KVVM-tillägget, som en etablerings agent, körs kontinuerligt enligt en fördefinierad frekvens. Om det inte går att hämta ett observerat certifikat fortsätter det att fortsätta till nästa rad, och sedan i vilo läge tills nästa cykel. SFVM-tillägget, som klustrets start agent, kommer att kräva de deklarerade certifikaten innan klustret kan formas. Detta innebär i sin tur att SFVM-tillägget endast kan köras efter att det lyckats att hämta kluster certifikaten, som anges här av- ```json "provisionAfterExtensions" : [ "KVVMExtension" ]"``` satsen och med inställningen för KeyVaultVM-tillägget ```json "requireInitialSync": true``` . Detta anger det KVVM-tillägg som körs i den första körningen (efter distributionen eller omstart) måste det gå igenom de observerade certifikaten tills alla har laddats ned. Om den här parametern anges till falskt, tillsammans med ett fel vid hämtningen av kluster certifikaten, uppstår ett fel i kluster distributionen. Omvänt, vilket kräver en inledande synkronisering med en felaktig/ogiltig lista över observerade certifikat, skulle resultera i ett fel i KVVM-tillägget och så så, så, ett fel vid distributionen av klustret.  

#### <a name="certificate-linking-explained"></a>Certifikat länkning, förklarad
Du kanske har märkt flaggan "linkOnRenewal" för KVVM-tillägget och det faktum att det är inställt på false. Vi riktar in dig på djupet beteende som styrs av den här flaggan och dess konsekvenser för ett klusters drift. Observera att det här beteendet är speciellt för Windows.

Enligt dess [definition](../virtual-machines/extensions/key-vault-windows.md#extension-schema):
```json
"linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
```

Certifikat som används för att upprätta en TLS-anslutning [erhålls vanligt vis som en referens](/windows/win32/api/sspi/nf-sspi-acquirecredentialshandlea) via S-Channel Security Support Provider – det vill säga att klienten inte direkt kommer åt den privata nyckeln för själva certifikatet. S-Channel stöder omdirigering (länkning) av autentiseringsuppgifter i form av ett certifikat tillägg ([CERT_RENEWAL_PROP_ID](/windows/win32/api/wincrypt/nf-wincrypt-certsetcertificatecontextproperty#cert_renewal_prop_id)): om den här egenskapen anges representerar dess värde tumavtrycket för certifikatet "förnyelse", och så att S-kanal istället försöker läsa in det länkade certifikatet. I själva verket kommer den här länkade (och förhoppnings vis acykliska) listan att gå till tills den slutar med det slutgiltiga certifikatet – ett utan ett förnyelse märke. Den här funktionen, när den används sparsamt, är en bra minskning mot förlust av tillgänglighet som orsakas av utgångna certifikat (till exempel). I andra fall kan det vara orsaken till avbrott som är svåra att diagnostisera och minska. S-Channel kör en genom gång av certifikat i sina förnyelse egenskaper utan villkorliga, oberoende av ämne, utfärdare eller andra speciella attribut som deltar i verifieringen av det resulterande certifikatet av klienten. Det är möjligt att det resulterande certifikatet inte har någon associerad privat nyckel eller att nyckeln inte har ACLed till sin potentiella konsument. 
 
Om länkning är aktive rad försöker det virtuella dator tillägget för det virtuella nyckel valvet, vid hämtning av ett observerat certifikat från valvet, försöka hitta matchande befintliga certifikat för att koppla dem via egenskapen förnya tillägg. Matchningen är (enbart) baserat på alternativt namn för certifikat mottagare (SAN) och fungerar som WINS nedan.
Anta två befintliga certifikat enligt följande: A: CN = "Alices tillbehör", SAN = {"alice.universalexports.com"}, förnyelse = ' ' B: CN = "Bob ' s bitar", SAN = {"bob.universalexports.com", "bob.universalexports.net"}, förnyelse = ""
 
Anta att ett certifikat C hämtas av KVVM ext: CN = "Mallory ' s Malware", SAN = {"alice.universalexports.com", "bob.universalexports.com", "mallory.universalexports.com"}
 
En SAN-lista ingår fullständigt i C och så A. förnyelse = C. tumavtryck; B s SAN-lista har en gemensam skärnings punkt med C, men ingår inte fullt ut, så B. förnyelsen är tom.
 
Alla försök att anropa AcquireCredentialsHandle (S-Channel) i det här läget på certifikat A kommer faktiskt att skicka C till fjärran sluten. När det gäller Service Fabric, använder [transport del systemet](service-fabric-architecture.md#transport-subsystem) i ett kluster S-Channel för ömsesidig autentisering, så det beteende som beskrivs ovan påverkar klustrets grundläggande kommunikation direkt. Att fortsätta med exemplet ovan och förutsatt att ett är kluster certifikatet, vad som händer härnäst beror:
  - om C: s privata nyckel inte är ACLd till det konto som infrastrukturen körs på, ser vi att det inte går att hämta den privata nyckeln (SEC_E_UNKNOWN_CREDENTIALS eller liknande)
  - om C: s privata nyckel är tillgänglig kommer vi att se auktoriseringsfel som returneras av de andra noderna (CertificateNotMatched, obehöriga osv.) 
 
I båda fallen Miss lyckas transporten och klustret kan gå nedåt. symptomen varierar. För att göra det ännu sämre är länkningen beroende av förnyelse ordningen – som styrs av ordningen på listan över observerade certifikat för KVVM-tillägget, förnyelse schemat i valvet eller till och med tillfälliga fel som skulle ändra ordningen på hämtningen.

För att undvika sådana incidenter rekommenderar vi att:
  - Blanda inte San-nätverk för olika valv certifikat; varje valv certifikat bör ha ett distinkt syfte och deras ämne och SAN bör avspegla att med särskildhet
  - ta med det unika ämnes namnet i SAN-listan (som, bokstavligen, "CN = <subject common name> ")  
  - Om du inte är säker inaktiverar du länkar vid förnyelse för certifikat som har skapats med tillägget KVVM 

#### <a name="why-use-a-user-assigned-managed-identity-what-are-the-implications-of-using-it"></a>Varför ska jag använda en användare som tilldelats en hanterad identitet? Vad är konsekvenserna av att använda den?
Som det framställs från JSON-kodfragmenten ovan, krävs en bestämd sekvensering av åtgärder/uppdateringar för att garantera att konverteringen lyckades och för att bevara klustrets tillgänglighet. Mer specifikt deklarerar resursen skalnings uppsättning för virtuella datorer och använder sin identitet för att hämta hemligheter i en enda (från användarens perspektiv) uppdatering. Service Fabric VM-tillägget (som startar klustret) beror på att det virtuella nyckel valvets tillägg har slutförts, vilket beror på lyckad hämtning av observerade certifikat. KVVM-tillägget använder den virtuella datorns skalnings uppsättnings identitet för att komma åt valvet, vilket innebär att åtkomst principen i valvet måste ha uppdaterats innan distributionen av den virtuella datorns skal uppsättning. 

Om du vill ta bort en hanterad identitet eller tilldela den till en annan resurs måste distributions operatören ha den nödvändiga rollen (ManagedIdentityOperator) i prenumerationen eller resurs gruppen, förutom de roller som krävs för att hantera de andra resurserna som refereras till i mallen. 

Från säkerhets synpunkt kan du återkalla att den virtuella datorn (skalnings uppsättning) betraktas som en säkerhets gräns med avseende på dess Azure-identitet. Det innebär att alla program som finns på den virtuella datorn, i princip, erhåller en åtkomsttoken som representerar de VM-hanterade identitets åtkomst-token som hämtas från den oautentiserade IMDS-slutpunkten. Om du anser att den virtuella datorn är en delad miljö eller en miljö med flera innehavare, är kanske den här metoden för att hämta kluster certifikat inte angiven. Det är dock den enda etablerings mekanism som är lämplig för förnyelse av certifikat.

## <a name="troubleshooting-and-frequently-asked-questions"></a>Fel sökning och vanliga frågor och svar

*F*: Hur registreras program mässigt i ett certifikat som hanteras med nyckel valv?
*A*: ta reda på namnet på utfärdaren från dokumentationen för nyckel valvet och Ersätt det sedan i skriptet nedan.  
```PowerShell
  $issuerName=<depends on your PKI of choice>
    $clusterVault="sftestcus"
    $clusterCertVaultName="sftstcncluster"
    $clusterCertCN="cus.cluster.sftstcn.system.servicefabric.azure-int"
    Set-AzKeyVaultCertificateIssuer -VaultName $clusterVault -Name $issuerName -IssuerProvider $issuerName
    $distinguishedName="CN=" + $clusterCertCN
    $policy = New-AzKeyVaultCertificatePolicy `
        -IssuerName $issuerName `
        -SubjectName $distinguishedName `
        -SecretContentType 'application/x-pkcs12' `
        -Ekus "1.3.6.1.5.5.7.3.1", "1.3.6.1.5.5.7.3.2" `
        -ValidityInMonths 4 `
        -KeyType 'RSA' `
        -RenewAtPercentageLifetime 75        
    Add-AzKeyVaultCertificate -VaultName $clusterVault -Name $clusterCertVaultName -CertificatePolicy $policy
    
    # poll the result of the enrollment
    Get-AzKeyVaultCertificateOperation -VaultName $clusterVault -Name $clusterCertVaultName 
```

*F*: Vad händer när ett certifikat utfärdas av en odeklarerad/ospecificerad utfärdare? Var kan jag få en fullständig lista över aktiva utfärdare av en specifik PKI?
*A*: om certifikat deklarationen anger tumavtrycken och den direkta utfärdaren av certifikatet inte finns med i listan över fästa utfärdare, kommer certifikatet att anses vara ogiltigt, oavsett om dess rot är betrodd av klienten eller inte. Det är därför viktigt att se till att listan över utfärdare är aktuell/uppdaterad. Introduktionen av en ny utfärdare är en sällsynt händelse och bör vara allmänt offentlig innan den börjar utfärda certifikat. 

I allmänhet kommer en PKI att publicera och underhålla en certifierings metod i enlighet med IETF [RFC 7382](https://tools.ietf.org/html/rfc7382). Bland annan information tas alla aktiva utfärdare med. Hämtning av den här listan kan skilja sig från en PKI till en annan.   

För Microsoft-interna PKI: er, se den interna dokumentationen om slut punkter/SDK: er som används för att hämta auktoriserade utfärdare; Det är kluster ägarens ansvar att avsöka denna lista regelbundet och se till att deras kluster definition innehåller *alla* förväntade utfärdare.

*F*: stöds flera PKI: er? 
*A*: Ja; du får inte deklarera flera CN-poster i kluster manifestet med samma värde, men kan visa en lista över utfärdare från flera PKI: er som motsvarar samma CN. Det är inte en rekommenderad metod och certifikatets genomskinlighets metoder kan förhindra att sådana certifikat utfärdas. Men som ett sätt att migrera från en PKI till en annan, är detta en acceptabel mekanism.

*F*: Vad händer om det aktuella kluster certifikatet inte är ca-utfärdat eller saknar avsett ämne? 
S *: skaffa* ett certifikat med det avsedda ämnet och Lägg till det i klustrets definition som en sekundär, efter tumavtryck. När uppgraderingen har slutförts initierar du en annan kluster konfigurations uppgradering för att konvertera certifikats deklarationen till ett eget namn. 

[Image1]:./media/security-cluster-certificate-mgmt/certificate-journey-thumbprint.png
[Image2]:./media/security-cluster-certificate-mgmt/certificate-journey-common-name.png
