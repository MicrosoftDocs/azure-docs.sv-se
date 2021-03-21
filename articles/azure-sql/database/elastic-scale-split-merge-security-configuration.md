---
title: Säkerhets konfiguration för delad sammanslagning
description: Konfigurera x409-certifikat för kryptering med delnings-/sammanslagnings tjänsten för elastisk skalning.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793440"
---
# <a name="split-merge-security-configuration"></a>Säkerhets konfiguration för delad sammanslagning
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Om du vill använda delnings-/sammanslagnings tjänsten måste du konfigurera säkerheten korrekt. Tjänsten är en del av den elastiska skalnings funktionen i Azure SQL Database. Mer information finns i [självstudierna dela och sammanfoga tjänst i elastisk skala](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurera certifikat

Certifikat konfigureras på två sätt. 

1. [Så här konfigurerar du TLS/SSL-certifikatet](#to-configure-the-tlsssl-certificate)
2. [Konfigurera klient certifikat](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Hämta certifikat

Certifikat kan hämtas från offentliga certifikat utfärdare (ca: er) eller från [Windows Certificate service](/windows/win32/seccrypto/certificate-services). Det här är de bästa metoderna för att hämta certifikat.

Om dessa alternativ inte är tillgängliga kan du generera **självsignerade certifikat**.

## <a name="tools-to-generate-certificates"></a>Verktyg för att skapa certifikat

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Köra verktygen

* Från en kommando tolk för utvecklare för Visual Studios, se [Visual Studio kommando tolk](/dotnet/framework/tools/developer-command-prompt-for-vs) 
  
    Om det är installerat går du till:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Hämta WDK från [Windows 8,1: Ladda ned paket och verktyg](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Så här konfigurerar du TLS/SSL-certifikatet

Ett TLS/SSL-certifikat krävs för att kryptera kommunikationen och autentisera servern. Välj de tre scenarierna nedan och kör alla steg:

### <a name="create-a-new-self-signed-certificate"></a>Skapa ett nytt självsignerat certifikat

1. [Skapa ett Self-Signed certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för Self-Signed TLS/SSL-certifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Överför TLS/SSL-certifikat till moln tjänsten](#upload-tlsssl-certificate-to-cloud-service)
4. [Uppdatera TLS/SSL-certifikat i tjänst konfigurations filen](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importera TLS/SSL-certifikatutfärdare](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Använda ett befintligt certifikat från certifikat arkivet
1. [Exportera TLS/SSL-certifikat från certifikat arkivet](#export-tlsssl-certificate-from-certificate-store)
2. [Överför TLS/SSL-certifikat till moln tjänsten](#upload-tlsssl-certificate-to-cloud-service)
3. [Uppdatera TLS/SSL-certifikat i tjänst konfigurations filen](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Använda ett befintligt certifikat i en PFX-fil
1. [Överför TLS/SSL-certifikat till moln tjänsten](#upload-tlsssl-certificate-to-cloud-service)
2. [Uppdatera TLS/SSL-certifikat i tjänst konfigurations filen](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Konfigurera klient certifikat
Klient certifikat krävs för att kunna autentisera begär anden till tjänsten. Välj de tre scenarierna nedan och kör alla steg:

### <a name="turn-off-client-certificates"></a>Inaktivera klient certifikat
1. [Inaktivera klient Certificate-Based autentisering](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Utfärda nya självsignerade klient certifikat
1. [Skapa en Self-Signed certifikat utfärdare](#create-a-self-signed-certification-authority)
2. [Ladda upp CA-certifikat till moln tjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i tjänst konfigurations filen](#update-ca-certificate-in-service-configuration-file)
4. [Utfärda klient certifikat](#issue-client-certificates)
5. [Skapa PFX-filer för klient certifikat](#create-pfx-files-for-client-certificates)
6. [Importera klient certifikat](#import-client-certificate)
7. [Kopiera tumavtrycken för klient certifikat](#copy-client-certificate-thumbprints)
8. [Konfigurera tillåtna klienter i tjänst konfigurations filen](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Använd befintliga klient certifikat
1. [Hitta offentlig nyckel för CA](#find-ca-public-key)
2. [Ladda upp CA-certifikat till moln tjänsten](#upload-ca-certificate-to-cloud-service)
3. [Uppdatera CA-certifikat i tjänst konfigurations filen](#update-ca-certificate-in-service-configuration-file)
4. [Kopiera tumavtrycken för klient certifikat](#copy-client-certificate-thumbprints)
5. [Konfigurera tillåtna klienter i tjänst konfigurations filen](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurera återkallnings kontroll av klient certifikat](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Åtkomst till tjänstens slut punkter kan begränsas till vissa intervall av IP-adresser.

## <a name="to-configure-encryption-for-the-store"></a>Konfigurera kryptering för butiken
Ett certifikat krävs för att kryptera de autentiseringsuppgifter som lagras i metadatalagret. Välj de tre scenarierna nedan och kör alla steg:

### <a name="use-a-new-self-signed-certificate"></a>Använd ett nytt självsignerat certifikat
1. [Skapa ett Self-Signed certifikat](#create-a-self-signed-certificate)
2. [Skapa PFX-fil för Self-Signed krypterings certifikat](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Ladda upp krypterings certifikat till moln tjänsten](#upload-encryption-certificate-to-cloud-service)
4. [Uppdatera krypterings certifikat i tjänst konfigurations filen](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Använd ett befintligt certifikat från certifikat arkivet
1. [Exportera krypterings certifikat från certifikat arkivet](#export-encryption-certificate-from-certificate-store)
2. [Ladda upp krypterings certifikat till moln tjänsten](#upload-encryption-certificate-to-cloud-service)
3. [Uppdatera krypterings certifikat i tjänst konfigurations filen](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Använda ett befintligt certifikat i en PFX-fil
1. [Ladda upp krypterings certifikat till moln tjänsten](#upload-encryption-certificate-to-cloud-service)
2. [Uppdatera krypterings certifikat i tjänst konfigurations filen](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Standard konfigurationen
Standard konfigurationen nekar all åtkomst till HTTP-slutpunkten. Detta är den rekommenderade inställningen eftersom begär anden till dessa slut punkter kan innehålla känslig information som autentiseringsuppgifter för databasen.
Standard konfigurationen ger all åtkomst till HTTPS-slutpunkten. Den här inställningen kan begränsas ytterligare.

### <a name="changing-the-configuration"></a>Ändra konfigurationen
Gruppen med åtkomst kontroll regler som gäller för och slut punkten konfigureras i **\<EndpointAcls>** avsnittet i **tjänst konfigurations filen**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Reglerna i en åtkomst kontroll grupp konfigureras i ett \<AccessControl name=""> avsnitt i tjänst konfigurations filen. 

Formatet förklaras i dokumentationen för nätverks Access Control listor.
Om du till exempel vill tillåta att endast IP-adresser i intervallet 100.100.0.0 till 100.100.255.255 får åtkomst till HTTPS-slutpunkten skulle reglerna se ut så här:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Denial of service-skydd
Det finns två olika mekanismer som stöds för att identifiera och förhindra denial of Service-attacker:

* Begränsa antalet samtidiga förfrågningar per fjärran sluten värd (av som standard)
* Begränsa åtkomst nivån per fjärran sluten värd (aktiverat som standard)

Dessa baseras på de funktioner som dokumenteras i dynamisk IP-säkerhet i IIS. När du ändrar den här konfigurationen följer följande faktorer:

* Beteendet för proxyservrar och NAT-enheter (Network Address Translation) över fjär värd information
* Varje begäran till en resurs i webb rollen anses vara (till exempel att läsa in skript, bilder osv.)

## <a name="restricting-number-of-concurrent-accesses"></a>Begränsa antalet samtidiga åtkomster
Inställningarna som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Aktivera det här skyddet genom att ändra DynamicIpRestrictionDenyByConcurrentRequests till true.

## <a name="restricting-rate-of-access"></a>Begränsa åtkomst frekvensen
Inställningarna som konfigurerar det här beteendet är:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurera svaret på en nekad begäran
Följande inställning konfigurerar svaret till en nekad begäran:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

I dokumentationen för dynamisk IP-säkerhet i IIS hittar du andra värden som stöds.

## <a name="operations-for-configuring-service-certificates"></a>Åtgärder för att konfigurera tjänst certifikat
Det här avsnittet är endast för referens. Följ de konfigurations steg som beskrivs i:

* Konfigurera TLS/SSL-certifikatet
* Konfigurera klient certifikat

## <a name="create-a-self-signed-certificate"></a>Skapa ett självsignerat certifikat
Köra

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Så här anpassar du:

* -n med tjänstens URL. Jokertecken ("CN = *. cloudapp. net") och alternativa namn ("CN = myservice1. cloudapp. net, CN = myservice2. cloudapp. net") stöds.
* -e med certifikatet upphör att gälla skapar du ett starkt lösen ord och anger det när du uppmanas att göra det.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Skapa PFX-fil för självsignerat TLS/SSL-certifikat
Köra

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Ange ett lösen ord och exportera certifikatet med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Exportera TLS/SSL-certifikat från certifikat arkivet
* Hitta certifikat
* Klicka på åtgärder-> alla aktiviteter-> exportera...
* Exportera certifikat till en. PFX-fil med följande alternativ:
  * Ja, exportera den privata nyckeln
  * Ta med alla certifikat i certifierings Sök vägen om möjligt * exportera alla utökade egenskaper

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Överför TLS/SSL-certifikat till moln tjänsten
Ladda upp certifikatet med befintlig eller genererad. PFX-fil med TLS-nyckel paret:

* Ange lösen ordet för att skydda informationen om den privata nyckeln

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Uppdatera TLS/SSL-certifikat i tjänst konfigurations filen
Uppdatera tumavtrycket för följande inställning i tjänst konfigurations filen med tumavtrycket för det certifikat som har överförts till moln tjänsten:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Importera TLS/SSL-certifikatutfärdare
Följ de här stegen i alla konton/datorer som ska kommunicera med tjänsten:

* Dubbelklicka på. CER-fil i Utforskaren
* I dialog rutan certifikat klickar du på Installera certifikat...
* Importera certifikat till arkivet för betrodda rot certifikat utfärdare

## <a name="turn-off-client-certificate-based-authentication"></a>Inaktivera autentisering baserad på klient certifikat
Endast klientens certifikatbaserade autentisering stöds och inaktive ras ger offentlig åtkomst till tjänstens slut punkter, om inte andra mekanismer är på plats (till exempel Microsoft Azure Virtual Network).

Ändra inställningarna till falskt i tjänst konfigurations filen för att stänga av funktionen:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Kopiera sedan samma tumavtryck som TLS/SSL-certifikatet i inställningen för certifikat utfärdarens certifikat:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Skapa en självsignerad certifikat utfärdare
Utför följande steg för att skapa ett självsignerat certifikat som ska fungera som en certifikat utfärdare:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Så här anpassar du det

* -e med certifikatets förfallo datum

## <a name="find-ca-public-key"></a>Hitta offentlig nyckel för CA
Alla klient certifikat måste ha utfärdats av en certifikat utfärdare som är betrodd av tjänsten. Hitta den offentliga nyckeln till den certifikat utfärdare som utfärdade de klient certifikat som ska användas för autentisering för att överföra den till moln tjänsten.

Om filen med den offentliga nyckeln inte är tillgänglig, så exportera den från certifikat arkivet:

* Hitta certifikat
  * Sök efter ett klient certifikat utfärdat av samma certifikat utfärdare
* Dubbelklicka på certifikatet.
* Välj fliken certifierings Sök väg i dialog rutan certifikat.
* Dubbelklicka på CA-posten i sökvägen.
* Anteckna certifikat egenskaperna.
* Stäng dialog rutan **certifikat** .
* Hitta certifikat
  * Sök efter den certifikat utfärdare som anges ovan.
* Klicka på åtgärder-> alla aktiviteter-> exportera...
* Exportera certifikat till en. CER med följande alternativ:
  * **Nej, exportera inte den privata nyckeln**
  * Ta med alla certifikat i certifierings Sök vägen om möjligt.
  * Exportera alla utökade egenskaper.

## <a name="upload-ca-certificate-to-cloud-service"></a>Ladda upp CA-certifikat till moln tjänsten
Ladda upp certifikatet med befintlig eller genererad. CER-fil med certifikat utfärdarens offentliga nyckel.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Uppdatera CA-certifikat i tjänst konfigurations filen
Uppdatera tumavtrycket för följande inställning i tjänst konfigurations filen med tumavtrycket för det certifikat som har överförts till moln tjänsten:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Uppdatera värdet för följande inställning med samma tumavtryck:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Utfärda klient certifikat
Varje enskild person som har behörighet att komma åt tjänsten bör ha ett klient certifikat utfärdat för sin exklusiva användning och bör välja sina egna starka lösen ord för att skydda dess privata nyckel. 

Följande steg måste utföras på samma dator där det självsignerade CA-certifikatet har genererats och lagrats:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Egna

* -n med ett ID för till klienten som ska autentiseras med det här certifikatet
* -e med certifikatets förfallo datum
* MyID. PVK och MyID. cer med unika fil namn för det här klient certifikatet

Det här kommandot kommer att uppmanas att ange ett lösen ord som ska skapas och sedan användas en gång. Använd ett starkt krypterat lösenord.

## <a name="create-pfx-files-for-client-certificates"></a>Skapa PFX-filer för klient certifikat
Kör följande för varje genererat klient certifikat:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Egna

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Ange ett lösen ord och exportera certifikatet med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Den person som det här certifikatet utfärdas för bör välja export lösen ordet

## <a name="import-client-certificate"></a>Importera klient certifikat
Varje person som ett klient certifikat har utfärdats för bör importera nyckel paret på de datorer som ska användas för att kommunicera med tjänsten:

* Dubbelklicka på. PFX-fil i Utforskaren
* Importera certifikat till det personliga arkivet med minst det här alternativet:
  * Ta med alla markerade utökade egenskaper

## <a name="copy-client-certificate-thumbprints"></a>Kopiera tumavtrycken för klient certifikat
Varje person som ett klient certifikat har utfärdats för måste följa dessa steg för att erhålla tumavtrycket för certifikatet, som läggs till i tjänst konfigurations filen:

* Kör certmgr.exe
* Välj fliken personligt
* Dubbelklicka på det klient certifikat som ska användas för autentisering
* I dialog rutan certifikat öppnas väljer du fliken information
* Kontrol lera att Visa visar alla
* Välj fältet tumavtryck i listan
* Kopiera värdet för tumavtrycket
  * Ta bort icke-synliga Unicode-tecken framför den första siffran
  * Ta bort alla blank steg

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurera tillåtna klienter i tjänst konfigurations filen
Uppdatera värdet för följande inställning i tjänst konfigurations filen med en kommaavgränsad lista över tumavtrycken för klient certifikat som har åtkomst till tjänsten:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurera återkallnings kontroll av klient certifikat
Standardinställningen kontrollerar inte certifikat utfärdaren för återkallnings status för klient certifikat. Om du vill aktivera kontrollerna, om den certifikat utfärdare som utfärdade klient certifikaten stöder sådana kontroller, ändrar du följande inställning med något av värdena som definieras i X509RevocationMode-uppräkningen:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Skapa PFX-fil för självsignerade krypterings certifikat
För ett krypterings certifikat kör du:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Egna

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Ange ett lösen ord och exportera certifikatet med följande alternativ:

* Ja, exportera den privata nyckeln
* Exportera alla utökade egenskaper
* Du behöver lösen ordet när du laddar upp certifikatet till moln tjänsten.

## <a name="export-encryption-certificate-from-certificate-store"></a>Exportera krypterings certifikat från certifikat arkivet
* Hitta certifikat
* Klicka på åtgärder-> alla aktiviteter-> exportera...
* Exportera certifikat till en. PFX-fil med följande alternativ: 
  * Ja, exportera den privata nyckeln
  * Ta med alla certifikat i certifierings Sök vägen om möjligt 
* Exportera alla utökade egenskaper

## <a name="upload-encryption-certificate-to-cloud-service"></a>Ladda upp krypterings certifikat till moln tjänsten
Ladda upp certifikatet med befintlig eller genererad. PFX-fil med krypterings nyckel paret:

* Ange lösen ordet för att skydda informationen om den privata nyckeln

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Uppdatera krypterings certifikat i tjänst konfigurations filen
Uppdatera tumavtrycket för följande inställningar i tjänst konfigurations filen med tumavtrycket för det certifikat som har överförts till moln tjänsten:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Vanliga certifikat åtgärder
* Konfigurera TLS/SSL-certifikatet
* Konfigurera klient certifikat

## <a name="find-certificate"></a>Hitta certifikat
Följ de här stegen:

1. Kör mmc.exe.
2. Fil-> Lägg till/ta bort snapin-modul...
3. Välj **Certifikat**.
4. Klicka på **Lägg till**.
5. Välj plats för certifikat arkiv.
6. Klicka på **Finish**.
7. Klicka på **OK**.
8. Expandera **certifikat**.
9. Expandera noden certifikat arkiv.
10. Expandera noden certifikatets underordnade.
11. Välj ett certifikat i listan.

## <a name="export-certificate"></a>Exportera certifikatet
I **guiden Exportera certifikat**:

1. Klicka på **Nästa**.
2. Välj **Ja** och sedan **Exportera den privata nyckeln**.
3. Klicka på **Nästa**.
4. Välj önskat utdatatyps fil format.
5. Markera önskade alternativ.
6. Kontrol lera **lösen ordet**.
7. Ange ett starkt lösen ord och bekräfta det.
8. Klicka på **Nästa**.
9. Skriv eller bläddra i ett fil namn där certifikatet ska lagras (Använd en. PFX-tillägg).
10. Klicka på **Nästa**.
11. Klicka på **Finish**.
12. Klicka på **OK**.

## <a name="import-certificate"></a>Importera certifikatet
I guiden Importera certifikat:

1. Välj lagrings plats.
   
   * Välj **Aktuell användare** om endast processer som körs under aktuell användare kommer åt tjänsten
   * Välj **lokal dator** om andra processer på den här datorn kommer att få åtkomst till tjänsten
2. Klicka på **Nästa**.
3. Om du importerar från en fil, bekräftar du sökvägen till filen.
4. Om du importerar en. PFX-fil:
   1. Ange lösen ordet som skyddar den privata nyckeln
   2. Välj import alternativ
5. Välj "placera" certifikat i följande Arkiv
6. Klicka på **Browse** (Bläddra).
7. Välj önskat arkiv.
8. Klicka på **Finish**.
   
   * Om arkivet Betrodda rot certifikat utfärdare valdes klickar du på **Ja**.
9. Klicka på **OK** i alla dialog fönster.

## <a name="upload-certificate"></a>Överför certifikat
I [Azure Portal](https://portal.azure.com/)

1. Välj **Cloud Services**.
2. Välj moln tjänst.
3. Klicka på **certifikat** på den översta menyn.
4. Klicka på **överför** i det nedre fältet.
5. Välj certifikat filen.
6. Om det är en. PFX-fil anger du lösen ordet för den privata nyckeln.
7. När du är klar kopierar du tumavtryck för certifikatet från den nya posten i listan.

## <a name="other-security-considerations"></a>Andra säkerhets aspekter
TLS-inställningarna som beskrivs i det här dokumentet krypterar kommunikation mellan tjänsten och dess klienter när HTTPS-slutpunkten används. Detta är viktigt eftersom autentiseringsuppgifter för databas åtkomst och eventuellt annan känslig information finns i kommunikationen. Observera dock att tjänsten behåller intern status, inklusive autentiseringsuppgifter, i de interna tabellerna i databasen i Azure SQL Database som du har angett för metadata-lagring i din Microsoft Azure-prenumeration. Den databasen har definierats som en del av följande inställning i tjänst konfigurations filen (. CSCFG-fil): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Autentiseringsuppgifterna som lagras i den här databasen är krypterade. Vi rekommenderar dock att både webb-och arbets roller för dina tjänst distributioner hålls uppdaterade och säkra att de båda har åtkomst till metadata-databasen och certifikatet som används för kryptering och dekryptering av lagrade autentiseringsuppgifter. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]