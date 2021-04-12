---
title: Felsöka ömsesidig autentisering på Azure Application Gateway
description: Lär dig hur du felsöker ömsesidig autentisering på Application Gateway
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 623addd253b3eb28bdf70db02ddfbe4b320cbae7
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231509"
---
# <a name="troubleshooting-mutual-authentication-errors-in-application-gateway-preview"></a>Felsöka ömsesidiga autentiseringsfel i Application Gateway (för hands version)

Lär dig hur du felsöker problem med ömsesidig autentisering när du använder Application Gateway. 

## <a name="overview"></a>Översikt 

När du har konfigurerat ömsesidig autentisering på en Application Gateway kan det finnas ett antal fel som visas när du försöker använda ömsesidig autentisering. Några vanliga orsaker till fel är:

* Överfört ett certifikat eller en certifikat kedja utan certifikat från en rot certifikat utfärdare
* Överfört en certifikat kedja med flera certifikat från rot certifikat utfärdare
* Överfört en certifikat kedja som bara innehåller ett löv certifikat utan CA-certifikat 
* Verifierings fel på grund av felaktig matchning av utfärdares-DN  

Vi går igenom olika scenarier som du kan köra i och felsöka dessa scenarier. Vi kommer sedan att adressera felkoder och förklara sannolika orsaker till vissa felkoder som du kan se med ömsesidig autentisering. 

## <a name="scenario-troubleshooting---configuration-problems"></a>Scenario fel sökning – konfigurations problem
Det finns ett par scenarier som du kan vara inriktad på när du försöker konfigurera ömsesidig autentisering. Vi går igenom hur du felsöker några av de vanligaste fall GRO par. 

### <a name="self-signed-certificate"></a>Självsignerat certifikat

#### <a name="problem"></a>Problem 

Det klient certifikat som du överförde är ett självsignerat certifikat och resulterar i felkoden ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate.

#### <a name="solution"></a>Lösning 

Kontrol lera att det självsignerade certifikat som du använder har fil namns tillägget *BasicConstraintsOid* = "2.5.29.19" som anger att ämnet kan fungera som en certifikat utfärdare. Detta säkerställer att certifikatet som används är ett CA-certifikat. Mer information om hur du genererar självsignerade klient certifikat finns i [betrodda klient certifikat](./mutual-authentication-certificate-management.md).

## <a name="scenario-troubleshooting---connectivity-problems"></a>Scenario fel sökning – anslutnings problem

Du kan ha kunnat konfigurera ömsesidig autentisering utan några problem, men du får problem när du skickar förfrågningar till din Application Gateway. Vi löser några vanliga problem och lösningar i följande avsnitt. Du kan hitta egenskapen *sslClientVerify* i åtkomst loggarna för din Application Gateway. 

### <a name="sslclientverify-is-none"></a>SslClientVerify är ingen

#### <a name="problem"></a>Problem 

Egenskapen *sslClientVerify* visas som "ingen" i dina åtkomst loggar. 

#### <a name="solution"></a>Lösning 

Detta visas när klienten inte skickar ett klient certifikat när en begäran skickas till Application Gateway. Detta kan inträffa om klienten som skickar begäran till Application Gateway inte har kon figurer ATS korrekt för att använda klient certifikat. Ett sätt att kontrol lera att installationen av klientautentisering på Application Gateway fungerar som förväntat är genom följande OpenSSL-kommando:

```
openssl s_client -connect <hostname:port> -cert <path-to-certificate> -key <client-private-key-file> 
```

`-cert`Flaggan är löv certifikatet, `-key` flaggan är den privata klient nyckel filen. 

Mer information om hur du använder OpenSSL `s_client` -kommandot finns på deras [manuella sida](https://www.openssl.org/docs/man1.0.2/man1/openssl-s_client.html).

### <a name="sslclientverify-is-failed"></a>SslClientVerify misslyckades

#### <a name="problem"></a>Problem

Egenskapen *sslClientVerify* visas som "misslyckades" i dina åtkomst loggar. 

#### <a name="solution"></a>Lösning

Det finns ett antal möjliga orsaker till fel i åtkomst loggarna. Nedan visas en lista över vanliga orsaker till fel:
* **Det gick inte att hämta utfärdarens certifikat:** Det gick inte att hitta utfärdarens certifikat för klient certifikatet. Det innebär vanligt vis att den betrodda certifikat UTFÄRDARens certifikat kedja inte har slutförts på Application Gateway. Kontrol lera att certifikat kedjan för den betrodda klient certifikat utfärdaren som laddats upp på Application Gateway är klar.  
* **Det gick inte att hämta det lokala utfärdarens certifikat:** På liknande sätt som det inte gick att hämta utfärdarcertifikat, gick det inte att hitta utfärdarens certifikat för klient certifikatet. Det innebär vanligt vis att den betrodda certifikat UTFÄRDARens certifikat kedja inte har slutförts på Application Gateway. Kontrol lera att certifikat kedjan för den betrodda klient certifikat utfärdaren som laddats upp på Application Gateway är klar.
* **Det gick inte att verifiera det första certifikatet:** Det gick inte att verifiera klient certifikatet. Det här felet inträffar specifikt när klienten endast presenterar löv certifikatet, vars utfärdare inte är betrodd. Kontrol lera att certifikat kedjan för den betrodda klient certifikat utfärdaren som laddats upp på Application Gateway är klar.
* **Det gick inte att verifiera klient certifikat utfärdaren:** Felet uppstår när konfigurationen *VerifyClientCertIssuerDN* har angetts till true. Detta inträffar vanligt vis när utfärdarens DN för klient certifikatet inte matchar den *ClientCertificateIssuerDN* som extraheras från den betrodda klient certifikat utfärdarens certifikat kedja som överförs av kunden. Mer information om hur Application Gateway extraherar *ClientCertificateIssuerDN* finns [Application Gateway EXTRAHERA utfärdarens unika namn](./mutual-authentication-overview.md#verify-client-certificate-dn). Vi rekommenderar att du laddar upp en certifikat kedja per fil för att Application Gateway. 

Mer information om hur du extraherar hela den betrodda certifikat UTFÄRDARens certifikat kedja för att överföra till Application Gateway finns i [så här extraherar du certifikat kedjor för betrodda klient certifikat utfärdare](./mutual-authentication-certificate-management.md).

## <a name="error-code-troubleshooting"></a>Fel kod fel sökning
Om du ser någon av följande felkoder har vi några rekommenderade lösningar för att hjälpa dig att lösa problemet. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustspecifydata"></a>Felkod: ApplicationGatewayTrustedClientCertificateMustSpecifyData

#### <a name="cause"></a>Orsak

Det finns certifikat data som saknas. Det överförda certifikatet kan ha varit en tom fil utan certifikat data. 

#### <a name="solution"></a>Lösning

Kontrol lera att den uppladdade certifikat filen inte innehåller några data som saknas. 

### <a name="error-code-applicationgatewaytrustedclientcertificatemustnothaveprivatekey"></a>Felkod: ApplicationGatewayTrustedClientCertificateMustNotHavePrivateKey

#### <a name="cause"></a>Orsak

Det finns en privat nyckel i certifikat kedjan. Det får inte finnas någon privat nyckel i certifikat kedjan. 

#### <a name="solution"></a>Lösning

Dubbelklicka på den certifikat kedja som överfördes och ta bort den privata nyckeln som tillhör kedjan. Ladda upp kedjan utan den privata nyckeln. 

### <a name="error-code-applicationgatewaytrustedclientcertificateinvaliddata"></a>Felkod: ApplicationGatewayTrustedClientCertificateInvalidData

#### <a name="cause"></a>Orsak

Det finns två möjliga orsaker bakom den här felkoden.
1. Parsningen misslyckades på grund av att kedjan inte visas i rätt format. Application Gateway förväntar sig att en certifikat kedja ska vara i PEM-format och det förväntar sig också att enskilda certifikat data ska avgränsas. 
2. Parsern hittade inget att parsa. Den överförda filen kunde eventuellt ha haft avgränsare men inga certifikat data. 

#### <a name="solution"></a>Lösning

Beroende på orsaken till felet finns det två möjliga lösningar. 
* Kontrol lera att certifikat kedjan som laddats upp har rätt format (PEM) och att certifikatets data har avgräns ATS korrekt. 
* Kontrol lera att certifikat filen som laddats upp innehåller certifikat data förutom avgränsarna. 

### <a name="error-code-applicationgatewaytrustedclientcertificatedoesnotcontainanycacertificate"></a>Felkod: ApplicationGatewayTrustedClientCertificateDoesNotContainAnyCACertificate

#### <a name="cause"></a>Orsak

Det certifikat som laddats upp innehöll bara ett löv certifikat utan CA-certifikat. Att ladda upp en certifikat kedja med CA-certifikat och ett löv certifikat är acceptabelt eftersom löv certifikatet bara skulle ignoreras, men ett certifikat måste ha en certifikat utfärdare. 

#### <a name="solution"></a>Lösning 

Kontrol lera att den certifikat kedja som överfördes innehöll mer än bara löv certifikatet. Tillägget *BasicConstraintsOid* = "2.5.29.19" måste finnas och ange att ämnet kan fungera som en certifikat utfärdare.

### <a name="error-code-applicationgatewayonlyonerootcaallowedintrustedclientcertificate"></a>Felkod: ApplicationGatewayOnlyOneRootCAAllowedInTrustedClientCertificate

#### <a name="cause"></a>Orsak

Certifikat kedjan innehåller flera certifikat från rot certifikat utfärdare *eller* innehöll inga rot certifikat utfärdare. 

#### <a name="solution"></a>Lösning

Certifikat som överförs måste innehålla exakt ett rot certifikat för certifikat utfärdare (och flera mellanliggande CA-certifikat efter behov).


