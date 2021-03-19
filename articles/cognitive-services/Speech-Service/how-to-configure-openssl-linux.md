---
title: Så här konfigurerar du OpenSSL för Linux
titleSuffix: Azure Cognitive Services
description: Lär dig hur du konfigurerar OpenSSL för Linux.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104577452"
---
# <a name="configure-openssl-for-linux"></a>Konfigurera OpenSSL för Linux

När du använder en Speech SDK-version innan 1.9.0, konfigureras [openssl](https://www.openssl.org) dynamiskt till värd system versionen. I senare versioner av talet SDK är OpenSSL (version [1.1.1 b](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)) statiskt länkat till kärn biblioteket i tal-SDK: n.

Kontrol lera att OpenSSL-certifikat har installerats i systemet för att säkerställa anslutning. Kör ett kommando:
```bash
openssl version -d
```

Utdata på Ubuntu/Debian-baserade system bör vara:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Kontrol lera om det finns en `certs` under katalog under OPENSSLDIR. I exemplet ovan skulle det vara `/usr/lib/ssl/certs` .

* Om det finns `/usr/lib/ssl/certs` och innehåller många enskilda certifikatfiler (med `.crt` eller `.pem` tillägg) behöver du inte längre utföra några ytterligare åtgärder.

* Om OPENSSLDIR är något annat än `/usr/lib/ssl` och/eller om det finns en enda certifikat paket fil i stället för flera enskilda filer, måste du ange en lämplig SSL-miljövariabel för att indikera var certifikaten finns.

## <a name="examples"></a>Exempel

- OPENSSLDIR är `/opt/ssl` . Det finns en `certs` under katalog med många `.crt` eller- `.pem` filer.
Ställ in miljövariabeln på `SSL_CERT_DIR` att peka på `/opt/ssl/certs` innan ett program som använder tal-SDK: n körs. Exempel:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- OPENSSLDIR är `/etc/pki/tls` (t. ex. på RHEL/CentOS-baserade system). Det finns `certs` till exempel en under katalog med en certifikat paket fil `ca-bundle.crt` .
Ange en miljö variabel `SSL_CERT_FILE` för att peka på den filen innan du kör ett program som använder tal-SDK: n. Exempel:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Kontroller av återkallade certifikat
När du ansluter till tal tjänsten verifierar talet SDK att TLS-certifikatet som används av tal tjänsten inte har återkallats. För att genomföra den här kontrollen behöver tal-SDK: n åtkomst till distributions platserna för återkallade certifikat för certifikat utfärdare som används av Azure. Du hittar en lista över möjliga hämtnings platser för CRL: er i [det här dokumentet](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes). Om ett certifikat har återkallats eller om CRL: en inte kan laddas ned, avbryts anslutningen och den annullerade händelsen stoppas av tal-SDK: n.

I händelse av att det nätverk där tal-SDK används från har kon figurer ATS på ett sätt som inte tillåter åtkomst till listan över återkallade certifikat, kan CRL-kontrollen antingen inaktive ras eller ställas in på att Miss Missing om det inte går att hämta CRL: en. Den här konfigurationen görs via konfigurationsobjektet som används för att skapa ett tolknings objekt.

Om du vill fortsätta med anslutningen när det inte går att hämta en CRL ställer du in egenskapen OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Om det är inställt på "true" ett försök görs att hämta listan över återkallade certifikat och om hämtningen lyckas, kontrol leras om certifikatet har återkallats, om det Miss lyckas, kommer anslutningen att kunna fortsätta.

Om du vill inaktivera kontroller av återkallade certifikat fullständigt, ställer du in egenskapen OPENSSL_DISABLE_CRL_CHECK på "true".
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Det är också värt att notera att vissa distributioner av Linux inte har en definierad TMP-eller TMPDIR-miljö. Detta leder till att tal-SDK: n hämtar listan över återkallade certifikat (CRL) varje gång, i stället för att cachelagra CRL: en på disken för åter användning tills de går ut. För att förbättra den första anslutnings prestandan kan du [skapa en miljö variabel med namnet tmpdir och ange den som sökväg till den temporära katalog som du har valt.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Om Speech SDK](speech-sdk.md)
