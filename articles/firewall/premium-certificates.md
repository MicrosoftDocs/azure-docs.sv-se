---
title: För hands versions certifikat för Azure Firewall Premium
description: För att kunna konfigurera TLS-inspektion på Azure Firewall Premium Preview måste du konfigurera och installera mellanliggande CA-certifikat.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: conceptual
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 31948d5e98ea3024c838bf0fa4b05609a5662ec5
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485528"
---
# <a name="azure-firewall-premium-preview-certificates"></a>För hands versions certifikat för Azure Firewall Premium 

> [!IMPORTANT]
> Azure Firewall Premium är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

 Om du vill konfigurera Azure Firewall Premium-förhands granskning TLS-inspektion måste du ange ett giltigt mellanliggande CA-certifikat och sätta det i Azure Key Vault.

## <a name="certificates-used-by-azure-firewall-premium-preview"></a>Certifikat som används av Azure Firewall Premium Preview

Det finns tre typer av certifikat som används i en typisk distribution:

- **Mellanliggande CA-certifikat (CA-certifikat)**

   En certifikat utfärdare (CA) är en organisation som är betrodd att signera digitala certifikat. En certifikat utfärdare verifierar identiteten och giltighet för ett företag eller enskilda som begär ett certifikat. Om verifieringen lyckas utfärdar certifikat utfärdaren ett signerat certifikat. När servern visar certifikatet för klienten (till exempel din webbläsare) under en SSL/TLS-handskakning försöker klienten verifiera signaturen mot en lista över *kända fungerande* signaturer. Webbläsare levereras normalt med listor över de ca: er som de implicit litar på för att identifiera värdar. Om utfärdaren inte finns med i listan, som på vissa platser som signerar sina egna certifikat, varnar webbläsaren användaren om att certifikatet inte har signerats av en känd auktoritet och ber användaren om att de vill fortsätta med kommunikationen med overifierade webbplatser.

- **Server certifikat (webbplats certifikat)**

   Ett certifikat som är kopplat till ett angivet domän namn. Om en webbplats har ett giltigt certifikat, innebär det att en certifikat utfärdare har vidtagit åtgärder för att kontrol lera att webb adressen verkligen tillhör organisationen. När du skriver en URL eller följer en länk till en säker webbplats kontrollerar webbläsaren certifikatet efter följande egenskaper:
   - Webb adressen matchar adressen i certifikatet.
   - Certifikatet är signerat av en certifikat utfärdare som webbläsaren känner igen som *betrodd* utfärdare.
   
   Ibland kan användare ansluta till en server med ett ej betrott certifikat. Azure-brandväggen tar bort anslutningen som om servern avslutade anslutningen.

- **Rot certifikat för certifikat UTFÄRDAre (rot certifikat)**

   En certifikat utfärdare kan utfärda flera certifikat i form av en träd struktur. Ett rot certifikat är det översta certifikatet i trädet.

För hands versionen av Azure Firewall Premium kan fånga utgående HTTP/S-trafik och automatiskt generera ett Server certifikat för `www.website.com` . Det här certifikatet genereras med det mellanliggande CA-certifikat som du anger. Slutanvändarens webbläsare och klient program måste ha förtroende för din organisations rotcertifikatutfärdarcertifikat eller mellanliggande CA-certifikat för att den här proceduren ska fungera. 

:::image type="content" source="media/premium-certificates/certificate-process.png" alt-text="Certifikat process":::

## <a name="intermediate-ca-certificate-requirements"></a>Krav för mellanliggande certifikat UTFÄRDAre

Se till att CA-certifikatet uppfyller följande krav:

- När du distribueras som en Key Vault hemlighet måste du använda lösen ords lös PFX (PKCS12) med ett certifikat och en privat nyckel.

- Det måste vara ett enskilt certifikat och bör inte innehålla hela kedjan med certifikat.  

- Det måste vara giltigt i ett år framåt.  

- Det måste vara en privat RSA-nyckel med en minimal storlek på 4096 byte.  

- Det måste ha `KeyUsage` tillägget markerat som kritiskt med `KeyCertSign` flaggan (RFC 5280; 4.2.1.3 Key Usage).

- Tillägget måste vara `BasicContraints` markerat som kritiskt (RFC 5280; 4.2.1.9 Basic-begränsningar).  

- `CA`Flaggan måste anges till true.

- Sök vägens längd måste vara större än eller lika med en.

## <a name="azure-key-vault"></a>Azure Key Vault

[Azure Key Vault](../key-vault/general/overview.md) är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Firewall Premium stöder integrering med Key Vault för Server certifikat som är kopplade till en brand Väggs princip.
 
Så här konfigurerar du nyckel valvet:

- Du måste importera ett befintligt certifikat med dess nyckel par till nyckel valvet. 
- Du kan också använda en nyckel valvs hemlighet som lagras som en lösen ords fri, bas-64-kodad PFX-fil.  En PFX-fil är ett digitalt certifikat som innehåller både privat nyckel och offentlig nyckel.
- Vi rekommenderar att du använder en import av CA-certifikat eftersom du kan konfigurera en avisering baserat på certifikatets förfallo datum.
- När du har importerat ett certifikat eller en hemlighet måste du definiera åtkomst principer i nyckel valvet för att ge identiteten behörighet att få åtkomst till certifikatet/hemligheten.
- Det angivna CA-certifikatet måste vara betrott av din Azure-arbetsbelastning. Se till att de distribueras korrekt.

Du kan antingen skapa eller återanvända en befintlig användare som tilldelats en hanterad identitet, som Azure-brandväggen använder för att hämta certifikat från Key Vault åt dig. Mer information finns i [Vad är hanterade identiteter för Azure-resurser?](../active-directory/managed-identities-azure-resources/overview.md) 

## <a name="configure-a-certificate-in-your-policy"></a>Konfigurera ett certifikat i principen

Om du vill konfigurera ett CA-certifikat i brand Väggs Premium principen väljer du din princip och väljer sedan **TLS-kontroll (för hands version)**. Välj **aktive rad** på sidan **TLS-kontroll** . Välj sedan certifikat UTFÄRDARens certifikat i Azure Key Vault, som du ser i följande figur:

:::image type="content" source="media/premium-certificates/tls-inspection.png" alt-text="Översikts diagram över Azure Firewall Premium":::
 
> [!IMPORTANT]
> Om du vill se och konfigurera ett certifikat från Azure Portal måste du lägga till ditt Azure-användarkonto i Key Vault åtkomst princip. Ge ditt användar konto **Get** och **lista** under **hemliga behörigheter**.
   :::image type="content" source="media/premium-certificates/secret-permissions.png" alt-text="Azure Key Vault åtkomst princip":::


## <a name="create-your-own-self-signed-ca-certificate"></a>Skapa ditt eget självsignerade CA-certifikat

För att hjälpa dig att testa och verifiera TLS-inspektion kan du använda följande skript för att skapa din egen självsignerade rot certifikat utfärdare och mellanliggande certifikat utfärdare.

> [!IMPORTANT]
> För produktion bör du använda företagets PKI för att skapa ett mellanliggande CA-certifikat. En företags-PKI utnyttjar den befintliga infrastrukturen och hanterar rot certifikat utfärdarens distribution till alla slut punkts datorer.

Det finns två versioner av det här skriptet:
- ett bash-skript `cert.sh` 
- ett PowerShell-skript `cert.ps1` 

 Dessutom använder båda skripten `openssl.cnf` konfigurations filen. Om du vill använda skripten kopierar du innehållet i `openssl.cnf` och `cert.sh` eller `cert.ps1` till din lokala dator.

Skripten genererar följande filer:
- rootCA. CRT/rootCA. nyckel rot certifikat UTFÄRDARens offentliga certifikat och privat nyckel.
- interCA. CRT/interCA. nyckel-mellanliggande CA offentligt certifikat och privat nyckel
- interCA. pfx-mellanliggande CA PKCS12-paket som ska användas av brand väggen

> [!IMPORTANT]
> rootCA. Key ska lagras på en säker plats som är offline. Skripten genererar ett certifikat med en giltighet på 1024 dagar.

När certifikaten har skapats distribuerar du dem till följande platser:
- rootCA. CRT – distribuera på slut punkts datorer (endast offentligt certifikat).
- interCA. pfx-importera som certifikat på en Key Vault och tilldela till brand Väggs princip.

### <a name="opensslcnf"></a>**openssl. cnf**
```
[ req ]
default_bits        = 4096
distinguished_name  = req_distinguished_name
string_mask         = utf8only
default_md          = sha512

[ req_distinguished_name ]
countryName                     = Country Name (2 letter code)
stateOrProvinceName             = State or Province Name
localityName                    = Locality Name
0.organizationName              = Organization Name
organizationalUnitName          = Organizational Unit Name
commonName                      = Common Name
emailAddress                    = Email Address

[ rootCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ interCA_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:true, pathlen:1
keyUsage = critical, digitalSignature, cRLSign, keyCertSign

[ server_ext ]
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid:always,issuer
basicConstraints = critical, CA:false
keyUsage = critical, digitalSignature
extendedKeyUsage = serverAuth
```

###  <a name="bash-script---certsh"></a>Bash-skript – cert.sh 
```bash
#!/bin/bash

# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 1024 -out rootCA.crt -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA" -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj "/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA"

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 1024 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password "pass:"

echo ""
echo "================"
echo "Successfully generated root and intermediate CA certificates"
echo "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
echo "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
echo "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
echo "================"
```

### <a name="powershell---certps1"></a>PowerShell – cert.ps1
```powershell
# Create root CA
openssl req -x509 -new -nodes -newkey rsa:4096 -keyout rootCA.key -sha256 -days 3650 -out rootCA.crt -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Root CA' -config openssl.cnf -extensions rootCA_ext

# Create intermediate CA request
openssl req -new -nodes -newkey rsa:4096 -keyout interCA.key -sha256 -out interCA.csr -subj '/C=US/ST=US/O=Self Signed/CN=Self Signed Intermediate CA'

# Sign on the intermediate CA
openssl x509 -req -in interCA.csr -CA rootCA.crt -CAkey rootCA.key -CAcreateserial -out interCA.crt -days 3650 -sha256 -extfile openssl.cnf -extensions interCA_ext

# Export the intermediate CA into PFX
openssl pkcs12 -export -out interCA.pfx -inkey interCA.key -in interCA.crt -password 'pass:'

Write-Host ""
Write-Host "================"
Write-Host "Successfully generated root and intermediate CA certificates"
Write-Host "   - rootCA.crt/rootCA.key - Root CA public certificate and private key"
Write-Host "   - interCA.crt/interCA.key - Intermediate CA public certificate and private key"
Write-Host "   - interCA.pfx - Intermediate CA pkcs12 package which could be uploaded to Key Vault"
Write-Host "================"

```

## <a name="troubleshooting"></a>Felsökning

Om CA-certifikatet är giltigt, men du inte kan komma åt FQDN eller URL: er under TLS-kontroll, kontrollerar du följande objekt:

- Se till att webb Server certifikatet är giltigt.  

- Se till att rot certifikat utfärdarens certifikat är installerat på klientens operativ system.  

- Se till att webbläsaren eller HTTPS-klienten innehåller ett giltigt rot certifikat. Firefox och vissa andra webbläsare kan ha särskilda certifierings principer.  

- Se till att URL-måltypen i din program regel täcker rätt sökväg och eventuella andra hyperlänkar som är inbäddade på HTML-sidan för målet. Du kan använda jokertecken för att enkelt täcka hela den obligatoriska URL-sökvägen.  


## <a name="next-steps"></a>Nästa steg

- [Läs mer om Azure Firewall Premium-funktioner](premium-features.md)
