---
title: Självstudie – Använda OpenSSL för att skapa X.509-testcertifikat för Azure IoT Hub| Microsoft Docs
description: Självstudie – Använda OpenSSL för att skapa CERTIFIKATutfärdare och enhetscertifikat för Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 0843e5d3a5e91cb4acdf18ad6bdf6f4f0c214f72
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378303"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Självstudie: Använda OpenSSL för att skapa testcertifikat

Även om du kan köpa X.509-certifikat från en betrodd certifikatutfärdare räcker det att skapa en egen testcertifikathierarki eller använda själv signerade certifikat för att testa IoT Hub-enhetsautentisering. I följande exempel används [OpenSSL](https://www.openssl.org/) och [OpenSSL Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) för att skapa en certifikatutfärdare (CA), en underordnad certifikatutfärdare och ett enhetscertifikat. Exemplet signerar sedan den underordnade certifikatutfärdaren och enhetscertifikatet i en certifikathierarki. Detta visas endast i exempelsyfte.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Steg 1 – Skapa rot-CA-katalogstrukturen

Skapa en katalogstruktur för certifikatutfärdaren.

* Katalogen **certs** lagrar nya certifikat.
* **Db-katalogen** används för certifikatdatabasen.
* Den **privata** katalogen lagrar den privata CA-nyckeln.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Steg 2 – Skapa en rotcertifikatutfärdares konfigurationsfil

Innan du skapar en certifikatutfärdare skapar du en konfigurationsfil och sparar den `rootca.conf` som i rootca-katalogen.

```xml
[default]
name                     = rootca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Root CA"

[ca_default]
home                     = ../rootca 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = none
default_days             = 3650
default_crl_days         = 365
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash

```

## <a name="step-3---create-a-root-ca"></a>Steg 3 – Skapa en rotcertifikatutfärdare

Generera först nyckeln och certifikatsigneringsbegäran (CSR) i katalogen rootca.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Skapa sedan ett själv signerat CA-certifikat. Självsignering lämpar sig för testning. Ange ca_ext för konfigurationsfiltillägg på kommandoraden. Dessa anger att certifikatet är för en rotcertifikatutfärdare och kan användas för att signera certifikat och listor över återkallade certifikat (CRL). Signera certifikatet och genomför det i databasen.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Steg 4 – Skapa den underordnade CA-katalogstrukturen

Skapa en katalogstruktur för den underordnade certifikatutfärdaren.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Steg 5 – Skapa en underordnad CA-konfigurationsfil

Skapa en konfigurationsfil och spara den som subca.conf i `subca` katalogen .

```bash
[default]
name                     = subca
domain_suffix            = example.com
aia_url                  = http://$name.$domain_suffix/$name.crt
crl_url                  = http://$name.$domain_suffix/$name.crl
default_ca               = ca_default
name_opt                 = utf8,esc_ctrl,multiline,lname,align

[ca_dn]
commonName               = "Test Subordinate CA"

[ca_default]
home                     = . 
database                 = $home/db/index
serial                   = $home/db/serial
crlnumber                = $home/db/crlnumber
certificate              = $home/$name.crt
private_key              = $home/private/$name.key
RANDFILE                 = $home/private/random
new_certs_dir            = $home/certs
unique_subject           = no
copy_extensions          = copy 
default_days           
default_crl_days         = 90 
default_md               = sha256
policy                   = policy_c_o_match

[policy_c_o_match]
countryName              = optional
stateOrProvinceName      = optional
organizationName         = optional
organizationalUnitName   = optional
commonName               = supplied
emailAddress             = optional

[req]
default_bits             = 2048
encrypt_key              = yes
default_md               = sha256
utf8                     = yes
string_mask              = utf8only
prompt                   = no
distinguished_name       = ca_dn
req_extensions           = ca_ext

[ca_ext]
basicConstraints         = critical,CA:true
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[sub_ca_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:true,pathlen:0
extendedKeyUsage         = clientAuth,serverAuth
keyUsage                 = critical,keyCertSign,cRLSign
subjectKeyIdentifier     = hash

[client_ext]
authorityKeyIdentifier   = keyid:always
basicConstraints         = critical,CA:false
extendedKeyUsage         = clientAuth
keyUsage                 = critical,digitalSignature
subjectKeyIdentifier     = hash
```

## <a name="step-6---create-a-subordinate-ca"></a>Steg 6 – Skapa en underordnad certifikatutfärdare

Skapa ett nytt serienummer i filen för `rootca/db/serial` det underordnade CA-certifikatet.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Du måste skapa ett nytt serienummer för varje underordnat certifikatutfärdarcertifikat och varje enhetscertifikat som du skapar. Olika certifikat kan inte ha samma serienummer.

Det här exemplet visar hur du skapar en underordnad certifikatutfärdare eller registrerings-CA. Eftersom du kan använda rotcertifikatutfärdaren för att signera certifikat är det inte absolut nödvändigt att skapa en underordnad certifikatutfärdare. Att ha en underordnad certifikatutfärdare efterliknar dock verkliga certifikathierarkier där rotcertifikatutfärdaren hålls offline och underordnade CA:er utfärdar klientcertifikat.

Använd konfigurationsfilen för att generera en nyckel och en certifikatsigneringsbegäran (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Skicka CSR till rotcertifikatutfärdaren och använd rotcertifikatutfärdaren för att utfärda och signera det underordnade CA-certifikatet. Ange sub_ca_ext för växeln för tillägg på kommandoraden. Tilläggen anger att certifikatet gäller för en certifikatutfärdare som kan signera certifikat och listor över återkallade certifikat (CRL). När du uppmanas signerar du certifikatet och genomför det i databasen.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Steg 7 – Visa bevis på innehav

Nu har du både ett rotcertifikatutfärdarcertifikat och ett underordnat CA-certifikat. Du kan använda någon av dem för att signera enhetscertifikat. Den du väljer måste laddas upp till din IoT Hub. Följande steg förutsätter att du använder det underordnade CA-certifikatet. Så här laddar du upp och registrerar ditt underordnade CA-certifikat till IoT Hub:

1. I dialogrutan Azure Portal du till din IoTHub och väljer **Inställningar för > Certifikat**.

1. Välj **Lägg till** för att lägga till ditt nya underordnade CA-certifikat.

1. Ange ett visningsnamn i **fältet Certifikatnamn** och välj pem-certifikatfilen som du skapade tidigare.

1. Välj **Spara**. Certifikatet visas i certifikatlistan med statusen **Overifierad.** Verifieringsprocessen bevisar att du äger certifikatet.

   
1. Välj certifikatet för att visa **dialogrutan Certifikatinformation.**

1. Välj **Generera verifieringskod.** Mer information finns i Bevisa [innehavet av ett CA-certifikat.](tutorial-x509-prove-possession.md)

1. Kopiera verifieringskoden till Urklipp. Du måste ange verifieringskoden som certifikatämne. Om verifieringskoden till exempel är BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A lägger du till det som ämne för certifikatet enligt steg 9.

1. Generera en privat nyckel.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Generera en certifikatsigneringsbegäran (CSR) från den privata nyckeln. Lägg till verifieringskoden som ämne för ditt certifikat.

  ```bash
  openssl req -new -key pop.key -out pop.csr
  
    -----
    Country Name (2 letter code) [XX]:.
    State or Province Name (full name) []:.
    Locality Name (eg, city) [Default City]:.
    Organization Name (eg, company) [Default Company Ltd]:.
    Organizational Unit Name (eg, section) []:.
    Common Name (eg, your name or your server hostname) []:BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A
    Email Address []:

    Please enter the following 'extra' attributes
    to be sent with your certificate request
    A challenge password []:
    An optional company name []:
 
  ```

10. Skapa ett certifikat med hjälp av rotcertifikatutfärdarkonfigurationsfilen och CSR för innehavarcertifikatet.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Välj det nya certifikatet i **vyn Certifikatinformation.** Du hittar PEM-filen genom att gå till mappen certs.

12. När certifikatet har laddats upp väljer du **Verifiera**. Ca-certifikatstatusen bör ändras till **Verifierad.**

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Steg 8 – Skapa en enhet i IoT Hub

Gå till ditt IoT Hub i Azure Portal och skapa en ny IoT-enhetsidentitet med följande värden:

1. Ange det **enhets-ID** som matchar ämnesnamnet för dina enhetscertifikat.

1. Välj **autentiseringstypen X.509 CA-signerad.**

1. Välj **Spara**.

## <a name="step-9---create-a-client-device-certificate"></a>Steg 9 – Skapa ett klientenhetscertifikat

Om du vill generera ett klientcertifikat måste du först generera en privat nyckel. Följande kommando visar hur du använder OpenSSL för att skapa en privat nyckel. Skapa nyckeln i underca-katalogen.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Skapa en certifikatsigneringsförfrågan (CSR) för nyckeln. Du behöver inte ange ett lösenord för utmaningen eller ett valfritt företagsnamn. Du måste dock ange enhets-ID:t i fältet eget namn.

```bash
openssl req -new -key device.key -out device.csr

-----
Country Name (2 letter code) [XX]:.
State or Province Name (full name) []:.
Locality Name (eg, city) [Default City]:.
Organization Name (eg, company) [Default Company Ltd]:.
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server hostname) []:`<your device ID>`
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:

```

Kontrollera att CSR är det du förväntar dig.

```bash
openssl req -text -in device.csr -noout
```

Skicka CSR till den underordnade certifikatutfärdaren för inloggning i certifikathierarkin. Ange `client_ext` i `-extensions` växeln. Observera att i `Basic Constraints` det utfärdade certifikatet anger att det här certifikatet inte är för en CERTIFIKATUTFÄRDARE. Om du signerar flera certifikat måste du uppdatera serienumret innan du genererar varje certifikat med kommandot `rand -hex 16 > db/serial` openssl.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Nästa steg

Gå till [Testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om certifikatet kan autentisera enheten till IoT Hub.
