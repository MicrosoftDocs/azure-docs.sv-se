---
title: Självstudie – Använd OpenSSL för att skapa X. 509 test certifikat för Azure IoT Hub | Microsoft Docs
description: Självstudie – använda OpenSSL för att skapa CA-och enhets certifikat för Azure IoT Hub
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
- devx-track-azurecli
ms.openlocfilehash: 4379c8f43bbfa539179b821bf6b18a01518afad6
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106384314"
---
# <a name="tutorial-using-openssl-to-create-test-certificates"></a>Självstudie: använda OpenSSL för att skapa test certifikat

Även om du kan köpa X. 509-certifikat från en betrodd certifikat utfärdare, så räcker det att skapa en egen test certifikat-hierarki eller använda självsignerade certifikat för att testa autentisering med IoT Hub-enheter. I följande exempel används [openssl](https://www.openssl.org/) och [openssl-Cookbook](https://www.feistyduck.com/library/openssl-cookbook/online/ch-openssl.html) för att skapa en certifikat utfärdare (ca), en underordnad certifikat utfärdare och ett enhets certifikat. I exemplet signeras den underordnade certifikat utfärdaren och enhets certifikatet till en certifikathierarki. Detta visas endast i exempel syfte.

## <a name="step-1---create-the-root-ca-directory-structure"></a>Steg 1 – Skapa rot certifikat utfärdarens katalog struktur

Skapa en katalog struktur för certifikat utfärdaren.

* Katalogen **för certifikat** Arkiv lagrar nya certifikat.
* Databas **katalogen används** för certifikat databasen.
* Den **privata** katalogen lagrar certifikat utfärdarens privata nyckel.

```bash
  mkdir rootca
  cd rootca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-2---create-a-root-ca-configuration-file"></a>Steg 2 – Skapa en rot-CA-konfigurationsfil

Innan du skapar en certifikat utfärdare ska du skapa en konfigurations fil och spara den som `rootca.conf` i RootCA-katalogen.

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

## <a name="step-3---create-a-root-ca"></a>Steg 3 – skapa en rot certifikat utfärdare

Först genererar du nyckeln och certifikat signerings förfrågan (CSR) i RootCA-katalogen.

```bash
  openssl req -new -config rootca.conf -out rootca.csr -keyout private/rootca.key
```

Skapa sedan ett självsignerat CA-certifikat. Egen signering är lämplig för testning. Ange ca_ext konfigurations fil tillägg på kommando raden. Detta anger att certifikatet gäller för en rot certifikat utfärdare och kan användas för att signera certifikat och listor över återkallade certifikat (CRL). Signera certifikatet och spara det i databasen.

```bash
  openssl ca -selfsign -config rootca.conf -in rootca.csr -out rootca.crt -extensions ca_ext
```

## <a name="step-4---create-the-subordinate-ca-directory-structure"></a>Steg 4 – skapa en underordnad katalog struktur för certifikat utfärdaren

Skapa en katalog struktur för den underordnade certifikat utfärdaren.

```bash
  mkdir subca
  cd subca
  mkdir certs db private
  touch db/index
  openssl rand -hex 16 > db/serial
  echo 1001 > db/crlnumber
```

## <a name="step-5---create-a-subordinate-ca-configuration-file"></a>Steg 5 – skapa en underordnad konfigurations fil för certifikat utfärdare

Skapa en konfigurations fil och spara den som subca. conf i `subca` katalogen.

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

## <a name="step-6---create-a-subordinate-ca"></a>Steg 6 – skapa en underordnad certifikat utfärdare

Skapa ett nytt serie nummer i `rootca/db/serial` filen för det underordnade CA-certifikatet.

```bash
  openssl rand -hex 16 > db/serial
```

>[!IMPORTANT]
>Du måste skapa ett nytt serie nummer för varje underordnat CA-certifikat och varje enhets certifikat som du skapar. Olika certifikat kan inte ha samma serie nummer.

Det här exemplet visar hur du skapar en underordnad certifikat utfärdare eller registrering. Eftersom du kan använda rot certifikat utfärdaren för att signera certifikat är det inte absolut nödvändigt att skapa en underordnad certifikat utfärdare. Om du har en underordnad certifikat utfärdare, kan du dock imitera verkliga världs certifikat hierarkier där rot certifikat utfärdaren hålls offline och underordnade certifikat utfärdare utfärdar klient certifikat.

Använd konfigurations filen för att generera en nyckel och en certifikat signerings förfrågan (CSR).

```bash
  openssl req -new -config subca.conf -out subca.csr -keyout private/subca.key
```

Skicka in CSR till rot certifikat utfärdaren och Använd rot certifikat utfärdaren för att utfärda och signera det underordnade CA-certifikatet. Ange sub_ca_ext för växeln extensions på kommando raden. Tilläggen anger att certifikatet gäller för en certifikat utfärdare som kan signera certifikat och listor över återkallade certifikat (CRL). Signera certifikatet när du uppmanas till det och spara det i databasen.

```bash
  openssl ca -config ../rootca/rootca.conf -in subca.csr -out subca.crt -extensions sub_ca_ext
```

## <a name="step-7---demonstrate-proof-of-possession"></a>Steg 7 – Visa bevis på innehav

Du har nu både ett certifikat från en rot certifikat utfärdare och ett underordnat CA-certifikat. Du kan använda antingen en för att signera enhets certifikat. Den du väljer måste överföras till din IoT Hub. Följande steg förutsätter att du använder det underordnade CA-certifikatet. För att överföra och registrera ditt underordnade CA-certifikat till din IoT Hub:

1. Gå till din IoTHub i Azure Portal och välj **inställningar > certifikat**.

1. Välj **Lägg** till för att lägga till ditt nya underordnade CA-certifikat.

1. Ange ett visnings namn i fältet **certifikat namn** och välj den PEM som du skapade tidigare.

1. Välj **Spara**. Ditt certifikat visas i certifikat listan med statusen **overifierad**. Verifierings processen visar att du äger certifikatet.

   
1. Välj certifikat för att Visa dialog rutan **certifikat information** .

1. Välj **generera verifierings kod**. Mer information finns i [bevisa innehav av ett CA-certifikat](tutorial-x509-prove-possession.md).

1. Kopiera verifieringskoden till Urklipp. Du måste ange verifierings koden som certifikat ämne. Om verifierings koden till exempel är BB0C656E69AF75E3FB3C8D922C1760C58C1DA5B05AAA9D0A kan du lägga till den som ämne för ditt certifikat, som du ser i steg 9.

1. Skapa en privat nyckel.

  ```bash
    $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
  ```

9. Generera en certifikat signerings förfrågan från den privata nyckeln. Lägg till verifierings koden som certifikatets ämne.

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

10. Skapa ett certifikat med hjälp av rot certifikat utfärdarens konfigurations fil och CSR för äkthets beviset för innehavet.

  ```bash
    openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

  ```

11. Välj det nya certifikatet i vyn **certifikat information** . Du hittar PEM-filen genom att navigera till mappen för certifikat.

12. När du har laddat upp certifikatet väljer du **Verifiera**. Certifikat utfärdarens certifikat status bör ändras till **verifierad**.

## <a name="step-8---create-a-device-in-your-iot-hub"></a>Steg 8 – skapa en enhet i IoT Hub

Navigera till IoT Hub i Azure Portal och skapa en ny IoT-enhets identitet med följande värden:

1. Ange **enhets-ID: t** som matchar ämnes namnet för enhets certifikaten.

1. Välj typen **X. 509-ca signerad** autentisering.

1. Välj **Spara**.

## <a name="step-9---create-a-client-device-certificate"></a>Steg 9 – skapa ett klient enhets certifikat

Om du vill generera ett klient certifikat måste du först skapa en privat nyckel. Följande kommando visar hur du använder OpenSSL för att skapa en privat nyckel. Skapa nyckeln i subca-katalogen.

```bash
openssl genpkey -out device.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048
```

Skapa en certifikat signerings förfrågan (CSR) för nyckeln. Du behöver inte ange ett utmanings lösen ord eller ett valfritt företags namn. Du måste dock ange enhets-ID i fältet eget namn.

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

Kontrol lera att CSR är det du förväntar dig.

```bash
openssl req -text -in device.csr -noout
```

Skicka CSR till den underordnade certifikat utfärdaren för att logga in i certifikathierarkin. Ange `client_ext` i `-extensions` växeln. Observera att det `Basic Constraints` här certifikatet inte är för en certifikat utfärdare i det utfärdade certifikatet. Om du signerar flera certifikat måste du uppdatera serie numret innan du genererar varje certifikat med hjälp av `rand -hex 16 > db/serial` kommandot openssl.

```bash
openssl ca -config subca.conf -in device.csr -out device.crt -extensions client_ext
```

## <a name="next-steps"></a>Nästa steg

Gå till [testa certifikatautentisering](tutorial-x509-test-certificate.md) för att avgöra om ditt certifikat kan autentisera din enhet till din IoT Hub.
