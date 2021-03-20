---
title: Säkerhets rutiner för tillverkare – Azure IoT Device Provisioning-tjänsten
description: Visar vanliga säkerhets metoder för OEM-tillverkare och enhets tillverkning som förbereder enheter för registrering i Azure IoT Device Provisioning-tjänsten (DPS).
author: timlt
ms.author: timlt
ms.date: 3/02/2020
ms.topic: conceptual
ms.service: iot-dps
ms.custom: iot-p0-scenario, iot-devices-deviceOEM
ms.reviewer: nberdy
ms.openlocfilehash: 788738082cbf9995fb2f7282bc3f574903275528
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90527215"
---
# <a name="security-practices-for-azure-iot-device-manufacturers"></a>Säkerhets rutiner för Azure IoT Device-tillverkare
Eftersom fler tillverkare släpper IoT-enheter är det bra att identifiera vägledning kring vanliga metoder. Den här artikeln sammanfattar rekommenderade säkerhets metoder för att tänka på när du tillverkar enheter för användning med Azure IoT Device Provisioning-tjänsten (DPS).  

> [!div class="checklist"]
> * Välja alternativ för enhetsautentisering
> * Installera certifikat på IoT-enheter
> * Integrera en Trusted Platform Module (TPM) i tillverknings processen

## <a name="selecting-device-authentication-options"></a>Välja alternativ för enhetsautentisering
Det ultimata syftet med en säkerhets åtgärd för IoT-enheter är att skapa en säker IoT-lösning. Men problem som maskin varu begränsningar, kostnader och nivåer av säkerhets kunskaper påverkar vilka alternativ du väljer. Vidare påverkar din metod för säkerhet hur dina IoT-enheter ansluter till molnet. Även om det finns [flera element i IoT-säkerhet](https://www.microsoft.com/research/publication/seven-properties-highly-secure-devices/) att tänka på, är det ett viktigt element som varje kund påträffar, vilken autentiseringstyp som ska användas. 

De tre vanligaste typerna av autentisering är X. 509-certifikat, TPM (Trusted Platform modules) och symmetriska nycklar. Andra autentiseringstyper finns, men de flesta kunder som skapar lösningar på Azure IoT använder en av dessa tre typer. Resten av den här artikeln underhåller våra experter och nack delar med att använda varje autentiseringstyp.

### <a name="x509-certificate"></a>X. 509-certifikat
X. 509-certifikat är en typ av digital identitet som du kan använda för autentisering. Certifikat standarden X. 509 dokumenteras i [IETF RFC 5280](https://tools.ietf.org/html/rfc5280). I Azure IoT finns det två sätt att autentisera certifikat:
- Begäran. En tumavtryck körs på ett certifikat för att generera en hexadecimal sträng. Den genererade strängen är en unik identifierare för certifikatet. 
- CA-autentisering baserat på en fullständig kedja. En certifikat kedja är en hierarkisk lista över alla certifikat som behövs för att autentisera ett slutentitets-certifikat (EE). För att autentisera ett EE-certifikat måste du autentisera varje certifikat i kedjan, inklusive en betrodd rot certifikat utfärdare. 

-Proffs för X. 509:
- X. 509 är den säkraste typen av autentisering som stöds i Azure IoT.
- X. 509 tillåter en hög kontroll nivå för certifikat hanteringens syfte.
- Många leverantörer är tillgängliga för att tillhandahålla X. 509-baserade lösningar för autentisering.

Nack delar för X. 509:
- Många kunder kan behöva förlita sig på externa leverantörer för sina certifikat.
- Certifikat hanteringen kan vara kostsam och adderas till total lösnings kostnad.
- Certifikat livs cykel hantering kan vara svårt om logistik inte är bra beskrivande. 

### <a name="trusted-platform-module-tpm"></a>Trusted Platform Module (TPM)
TPM, som även kallas [ISO/IEC 11889](https://www.iso.org/standard/66510.html), är en standard för att skapa och lagra kryptografiska nycklar på ett säkert sätt. TPM refererar också till en virtuell eller fysisk I/O-enhet som samverkar med moduler som implementerar standarden. En TPM-enhet kan finnas som diskret maskin vara, integrerad maskin vara, en inbyggd program vara baserad modul eller en programvarubaserad modul. 

Det finns två viktiga skillnader mellan TPM: er och symmetriska nycklar: 
- TPM-chips kan också lagra X. 509-certifikat.
- TPM-attestering i DPS använder TPM-bekräftelse nyckeln (EK), en form av asymmetrisk autentisering. Med asymmetrisk autentisering används en offentlig nyckel för kryptering och en separat privat nyckel används för dekryptering. Symmetriska nycklar använder däremot symmetrisk autentisering, där den privata nyckeln används för både kryptering och dekryptering. 

För TPM:
- TPM ingår som standard maskin vara på många Windows-enheter, med inbyggt stöd för operativ systemet. 
- TPM-attestering är enklare att skydda än den token för token för delad åtkomst (SAS) som baseras på kryptografiska nycklar.
- Du kan enkelt förfalla och förnya eller registrera autentiseringsuppgifter för enheten. DPS slår automatiskt IoT Hub autentiseringsuppgifter när en TPM-enhet förfaller för att etablera.

Nack delar för TPM: 
- TPM är komplexa och kan vara svårt att använda. 
- Program utveckling med TPM är svår om du inte har en fysisk TPM eller en kvalitets emulator.
- Du kan behöva utforma om kortet på enheten för att inkludera en TPM i maskin varan. 
- Om du rullar in EK på en TPM förstör den TPM-identiteten och skapar en ny. Även om det fysiska kretsen fortfarande är detsamma, har den en ny identitet i din IoT-lösning.

### <a name="symmetric-key"></a>Symmetrisk nyckel
Med symmetriska nycklar används samma nyckel för att kryptera och dekryptera meddelanden. Därför är samma nyckel känd för både enheten och tjänsten som autentiserar den. Azure IoT stöder SAS-baserade symmetriska nyckel anslutningar med SAS-token. Symmetrisk nyckel autentisering kräver betydande ägar ansvar för att skydda nycklarna och uppnå en likvärdig säkerhets nivå med X. 509-autentisering. Om du använder symmetriska nycklar är den rekommenderade metoden att skydda nycklarna med hjälp av en maskin varu säkerhetsmodul (HSM).

Tekniker för symmetrisk nyckel:
- Att använda symmetriska nycklar är det enklaste, lägsta sättet att komma igång med autentisering.
- Om symmetriska nycklar används effektiviseras processen eftersom det inte finns något extra att generera. 

Nack delar för symmetrisk nyckel: 
- Symmetriska nycklar tar en stor del av arbetet för att skydda nycklarna. Samma nyckel delas mellan enheten och molnet, vilket innebär att nyckeln måste skyddas på två platser. Utmaningen med TPM-och X. 509-certifikat förväntar sig däremot innehavandet av den offentliga nyckeln utan att avslöja den privata nyckeln.
- Symmetriska nycklar gör det enkelt att följa dåliga säkerhets metoder. En vanlig tendens med symmetriska nycklar är att hårdkoda de okrypterade nycklarna på enheterna. Även om den här metoden är praktisk är det att nycklarna är sårbara. Du kan minska risken genom att säkert lagra den symmetriska nyckeln på enheten. Men om prioriteten är i princip i stället för bekvämlighet använder du X. 509-certifikat eller TPM för autentisering. 

### <a name="shared-symmetric-key"></a>Delad symmetrisk nyckel
Det finns en variant av autentisering med symmetrisk nyckel som kallas delad symmetrisk nyckel. Den här metoden innebär att använda samma symmetriska nyckel i alla enheter. Rekommendationen är att undvika att använda delade symmetriska nycklar på dina enheter. 

Pro för delad symmetrisk nyckel:
- Enkel att implementera och billigt att producera i stor skala. 

Nack delar med delad symmetrisk nyckel: 
- Mycket sårbart för angrepp. Fördelen med enkel implementering är mycket utsatt för risken. 
- Vem som helst kan personifiera dina enheter om de får den delade nyckeln.
- Om du använder en delad symmetrisk nyckel som blir komprometterad förlorar du förmodligen kontrollen över enheterna. 

### <a name="making-the-right-choice-for-your-devices"></a>Gör rätt val för dina enheter
Du kan välja en autentiseringsmetod genom att ta hänsyn till fördelarna och kostnaderna för varje metod för din unika tillverknings process.  För enhetsautentisering är det vanligt vis en inverterad relation mellan hur säker en specifik metod är och hur bekvämt den är.  

## <a name="installing-certificates-on-iot-devices"></a>Installera certifikat på IoT-enheter
Om du använder X. 509-certifikat för att autentisera dina IoT-enheter ger det här avsnittet vägledning om hur du integrerar certifikat i din tillverknings process. Du måste fatta flera beslut.  Detta inkluderar beslut om vanliga certifikat-variabler, när du ska skapa certifikat och när du ska installera dem. 

Om du använder lösen ord kan du fråga varför du inte kan använda samma certifikat i alla dina enheter, på samma sätt som du kan använda samma lösen ord på alla dina enheter. Börja med att använda samma lösen ord överallt är farligt. I övningen har du exponerat företag för större DDoS-attacker, inklusive det som tog upp DNS på östra USA-kusten för varje år sedan. Använd aldrig samma lösen ord överallt, även med personliga konton. Ett annat certifikat är inte ett lösen ord, det är en unik identitet. Ett lösen ord är som en hemlig kod som alla kan använda för att öppna en dörr i en säker byggnad.  Det är något du känner till och du kan ge lösen ordet till vem som helst för att få till gång till dem.  Ett certifikat är som en driv rutins licens med ditt foto och annan information, som du kan visa för att skydda dig mot en säker byggnad. Den är knuten till vem du är.  Förutsatt att säkerhets identifieraren exakt matchar personer med driv Rutinens licenser, kan du använda din licens (identitet) för att få till gång. 

### <a name="variables-involved-in-certificate-decisions"></a>Variabler som ingår i certifikat beslut
Överväg följande variabler och hur var och en påverkar den övergripande tillverknings processen. 

#### <a name="where-the-certificate-root-of-trust-comes-from"></a>Där certifikat roten för förtroendet kommer från
Det kan vara kostsamt och komplicerat för att hantera en PKI (Public Key Infrastructure).  Särskilt om ditt företag inte har någon erfarenhet av att hantera en PKI. Alternativen är:
- Använd en PKI från tredje part. Du kan köpa mellanliggande signerings certifikat från en certifikat leverantör från tredje part. Eller så kan du använda en privat certifikat utfärdare (CA). 
- Använd en självhanterad PKI. Du kan underhålla ditt eget PKI-system och generera egna certifikat.
- Använd tjänsten [Azure Sphere](https://azure.microsoft.com/services/azure-sphere/) Security. Det här alternativet gäller endast för Azure Sphere enheter. 

#### <a name="where-certificates-are-stored"></a>Där certifikat lagras
Det finns några faktorer som påverkar beslutet om var certifikat lagras. Dessa faktorer omfattar typ av enhet, förväntade vinst marginaler (oavsett om du kan erbjuda säker lagring), enhets kapacitet och befintlig säkerhets teknik på den enhet som du kan använda. Överväg följande alternativ:
- I en modul för maskin varu säkerhet (HSM). Användning av en HSM rekommenderas starkt. Kontrol lera om enhetens kontroll panel redan har en HSM installerad. Om du vet att du inte har en HSM kan du arbeta med maskin varu tillverkaren för att identifiera en HSM som uppfyller dina behov.
- På en säker plats på en disk som en betrodd körnings miljö (TEE).
- I det lokala fil systemet eller i ett certifikat arkiv. Till exempel Windows certifikat arkiv. 

#### <a name="connectivity-at-the-factory"></a>Anslutning till fabriken
Anslutningen på fabriken avgör hur och när du ska få certifikaten att installeras på enheterna. Anslutnings alternativen är följande:
- Koppling. Om anslutningen är optimal, effektiviseras processen att generera certifikat lokalt. 
- Ingen anslutning. I det här fallet använder du ett signerat certifikat från en certifikat utfärdare för att generera enhets certifikat lokalt och offline. 
- Ingen anslutning. I det här fallet kan du hämta certifikat som har genererats i förväg. Du kan också använda en offline-PKI för att generera certifikat lokalt.

#### <a name="audit-requirement"></a>Gransknings krav
Beroende på vilken typ av enheter som du skapar kan du ha ett regel krav för att skapa en Gransknings logg för hur enhets identiteter installeras på dina enheter. Granskning lägger till betydande produktions kostnader. I de flesta fall behöver du bara göra det om det behövs. Om du är osäker på om en granskning krävs kan du kontakta företagets juridiska avdelning. Gransknings alternativ: 
- Inte en känslig bransch. Ingen granskning krävs.
- Känslig bransch. Certifikaten bör installeras i ett säkert rum enligt certifierings kraven för efterlevnad. Om du behöver ett säkert rum för att installera certifikat är du förmodligen redan medveten om hur certifikaten installeras på dina enheter. Och du har förmodligen redan ett gransknings system på plats. 

#### <a name="length-of-certificate-validity"></a>Certifikatets giltighets tid
Som en driv rutins licens har certifikat ett utgångs datum som anges när de skapas. Här följer alternativen för certifikatets giltighets tid:
- Förnyelse krävs inte.  Den här metoden använder en lång förnyelse period, så du behöver aldrig förnya certifikatet under enhetens livs längd. En sådan metod är praktisk, men den är också riskfylld.  Du kan minska risken genom att använda säker lagring som en HSM på dina enheter. Den rekommenderade metoden är dock att undvika att använda certifikat med lång livs längd.
- Förnyelse krävs.  Du måste förnya certifikatet under enhetens livs längd. Certifikatets giltighets tid beror på kontexten och du behöver en strategi för förnyelse.  Strategin bör omfatta var du får certifikat, och vilken typ av över-luft-funktioner som enheterna måste använda i förnyelse processen. 

### <a name="when-to-generate-certificates"></a>När du ska skapa certifikat
Funktionerna för Internet anslutning på fabriken påverkar din process för att skapa certifikat. Du har flera alternativ för när du ska skapa certifikat: 

- Förinstallerade certifikat.  Vissa HSM-leverantörer erbjuder en premium-tjänst där HSM-leverantören installerar certifikat för kunden. Först ger kunderna åtkomst till HSM-leverantören till ett signerings certifikat. Sedan installerar HSM-leverantören certifikat som signerats av signerings certifikatet på varje HSM som kunden köper. Allt kunden behöver göra är att installera HSM på enheten. Tjänsten lägger till kostnader och hjälper till att effektivisera din tillverknings process.  Och det löser frågan om när certifikat ska installeras.
- Enhets genererade certifikat.  Om enheterna genererar certifikat internt måste du extrahera det offentliga X. 509-certifikatet från enheten för att registrera det i DPS. 
- Ansluten fabrik.  Om fabriken har anslutning kan du generera enhets certifikat när du behöver dem.
- Offline-fabrik med din egen PKI. Om fabriken inte har någon anslutning och du använder din egen PKI med offline-support, kan du generera certifikaten när du behöver dem.
- Offline-fabrik med PKI från tredje part. Om fabriken inte har någon anslutning och du använder en tredjeparts-PKI måste du generera certifikaten i förväg. Du måste då skapa certifikaten från en plats som har anslutning. 

### <a name="when-to-install-certificates"></a>När du ska installera certifikat
När du har genererat certifikat för dina IoT-enheter kan du installera dem på enheterna. 

Om du använder förinstallerade certifikat med en HSM förenklas processen. När HSM har installerats i enheten kan enhets koden komma åt den. Sedan anropar du HSM-API: er för att komma åt certifikatet som lagras i HSM. Den här metoden är den lämpligaste för din tillverknings process. 

Om du inte använder ett förinstallerat certifikat måste du installera certifikatet som en del av produktions processen. Det enklaste sättet är att installera certifikatet i HSM på samma gång som du blinkar den första avbildningen av den inbyggda program varan. Processen måste lägga till ett steg för att installera avbildningen på varje enhet. Efter det här steget kan du köra slutgiltiga kvalitets kontroller och andra steg innan du paketerar och levererar enheten. 

Det finns tillgängliga program varu verktyg som låter dig köra installations processen och den slutliga kvalitets kontrollen i ett enda steg. Du kan ändra dessa verktyg för att skapa ett certifikat eller hämta ett certifikat från ett förgenererat certifikat arkiv. Sedan kan program varan installera det certifikat där du behöver installera det. Med program varu verktyg av den här typen kan du köra tillverkning av produktions kvalitet i stor skala. 

När du har certifikat installerade på dina enheter är nästa steg att lära dig hur du registrerar enheter med [DPS](about-iot-dps.md). 

## <a name="integrating-a-tpm-into-the-manufacturing-process"></a>Integrera en TPM i tillverknings processen
Om du använder en TPM för att autentisera dina IoT-enheter ger det här avsnittet vägledning. Vägledningen beskriver de vanligaste TPM 2,0-enheter som har hash-baserad nyckel stöd för meddelandeautentisering (HMAC). TPM-specifikationen för TPM-chips är en ISO-standard som underhålls av Trusted Computing Group. Mer information om TPM finns i specifikationer för [tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) och [ISO/IEC 11889](https://www.iso.org/standard/66510.html). 

### <a name="taking-ownership-of-the-tpm"></a>Bli ägare till TPM: en
Ett viktigt steg i att tillverka en enhet med ett TPM-chip är att bli ägare till TPM: en. Det här steget krävs för att du ska kunna ange en nyckel till enhetens ägare. Det första steget är att extrahera bekräftelse nyckeln (EK) från enheten. Nästa steg är att faktiskt kräva ägande rätt. Hur du gör detta beror på vilken TPM och vilket operativ system du använder. Om det behövs kan du kontakta TPM-tillverkaren eller utvecklaren av enhetens operativ system för att avgöra hur du ska göra anspråk på ägarskap. 

I tillverknings processen kan du extrahera EK-och anspråk-ägarskapet vid olika tidpunkter, vilket ökar flexibiliteten. Många tillverkare drar nytta av den här flexibiliteten genom att lägga till en modul för maskin varu säkerhet (HSM) för att förbättra säkerheten på sina enheter. Det här avsnittet innehåller information om när du ska extrahera EK, när du ska kräva ägarskap för TPM och överväganden för att integrera de här stegen i en arbets tids linje. 

> [!IMPORTANT]
> Följande rikt linjer förutsätter att du använder en diskret, inbyggd program vara eller integrerad TPM. På platser där det är tillämpligt lägger vägledningen till anteckningar om att använda en icke-diskret eller program varu TPM. Om du använder en program vara för TPM kan det finnas ytterligare steg som den här vägledningen inte innehåller. Program varu-TPM: er har en mängd olika implementeringar utöver den här artikelns omfattning.  I allmänhet är det möjligt att integrera en program-TPM i följande generella arbets tids linje. Men en programemulerad TPM är lämplig för prototyper och testning, men kan inte ge samma säkerhets nivå som en diskret, inbyggd program vara eller integrerad TPM. Undvik att använda en programvaru-TPM i produktion som allmän praxis.

### <a name="general-manufacturing-timeline"></a>Arbets tids linje för allmän tillverkning
Följande tids linje visar hur en TPM går igenom en produktions process och slutar på en enhet. Varje tillverknings process är unik och den här tids linjen visar de vanligaste mönstren. Tids linjen ger vägledning om när du ska vidta vissa åtgärder med nycklarna. 

#### <a name="step-1-tpm-is-manufactured"></a>Steg 1: TPM tillverkas
- Om du köper TPM från en tillverkare för användning i dina enheter kan du se om de extraherar offentliga bekräftelse nycklar (EK_pubs) åt dig. Det är bra om tillverkaren tillhandahåller en lista över EK_pubs med de levererade enheterna. 
    > [!NOTE]
    > Du kan ge TPM-tillverkaren skriv åtkomst till registrerings listan genom att använda delade åtkomst principer i etablerings tjänsten.  Med den här metoden kan du lägga till TPM: en i registrerings listan åt dig.  Men det är tidigt i tillverknings processen och kräver förtroende i TPM-tillverkaren. Gör det på egen risk. 

- Om du tillverkar TPM: er för att sälja till enhets tillverkare bör du överväga att ge kunderna en lista över EK_pubs tillsammans med deras fysiska TPM.  Att förse kunder med EK_pubs sparar ett steg i sin process. 
- Om du tillverkar TPM: er som ska användas med dina egna enheter kan du identifiera vilken punkt i processen som är mest praktisk för att extrahera EK_pub. Du kan extrahera EK_pub på någon av de återstående punkterna i tids linjen. 

#### <a name="step-2-tpm-is-installed-into-a-device"></a>Steg 2: TPM installeras i en enhet
Vid den här tidpunkten i produktions processen bör du veta vilken DPS-instans enheten ska användas med. Det innebär att du kan lägga till enheter i registrerings listan för automatisk etablering. Mer information om automatisk enhets etablering finns i [DPS-dokumentationen](about-iot-dps.md).
- Om du inte har extraherat EK_pub har du nu en lämplig tid att göra det. 
- Beroende på installations processen för TPM: en är det här steget också en lämplig tid att bli ägare till TPM: en. 

#### <a name="step-3-device-has-firmware-and-software-installed"></a>Steg 3: enheten har inbyggd program vara och program vara installerad
I det här skedet installerar du DPS-klienten tillsammans med ID-omfånget och den globala URL: en för etablering.
- Nu är den sista chansen att extrahera EK_pub. Om en tredje part kommer att installera program varan på enheten, är det en bra idé att först extrahera den EK_pub.
- Den här punkten i tillverknings processen är idealisk för att bli ägare till TPM: en.  
    > [!NOTE]
    > Om du använder en program vara för TPM kan du installera den nu.  Extrahera EK_pub samtidigt.

#### <a name="step-4-device-is-packaged-and-sent-to-the-warehouse"></a>Steg 4: enheten paketeras och skickas till lagret
En enhet kan sitta i ett lager i 6-12 månader innan den distribueras. 

#### <a name="step-5-device-is-installed-into-the-location"></a>Steg 5: enheten installeras på platsen
När enheten har nått sin slutgiltiga plats går den igenom automatiserad etablering med DPS.

Mer information finns i [etablering](about-iot-dps.md#provisioning-process) och [TPM-attestering](concepts-tpm-attestation.md). 

## <a name="resources"></a>Resurser

Utöver de rekommenderade säkerhets metoderna i den här artikeln tillhandahåller Azure IoT resurser som hjälper dig att välja säker maskin vara och skapa säkra IoT-distributioner: 
- Rekommendationer för Azure IoT- [säkerhet](../iot-fundamentals/security-recommendations.md) för att vägleda distributions processen. 
- [Azure Security Center](https://azure.microsoft.com/services/security-center/) erbjuder en tjänst som hjälper dig att skapa säkra IoT-distributioner. 
- Information om hur du utvärderar din maskin varu miljö finns i rapporten om att [utvärdera din IoT-säkerhet](https://download.microsoft.com/download/D/3/9/D3948E3C-D5DC-474E-B22F-81BA8ED7A446/Evaluating_Your_IOT_Security_whitepaper_EN_US.pdf). 
- Information om hur du väljer säker maskin vara finns i [rätt säker maskin vara för din IoT-distribution](https://download.microsoft.com/download/C/0/5/C05276D6-E602-4BB1-98A4-C29C88E57566/The_right_secure_hardware_for_your_IoT_deployment_EN_US.pdf). 