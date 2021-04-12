---
title: Översikt över ömsesidig autentisering på Azure Application Gateway
description: Den här artikeln är en översikt över ömsesidig autentisering på Application Gateway.
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.date: 03/30/2021
ms.topic: conceptual
ms.author: caya
ms.openlocfilehash: a63697c2c9c32dfb48e77248a5e7a601677b8b3e
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231526"
---
# <a name="overview-of-mutual-authentication-with-application-gateway-preview"></a>Översikt över ömsesidig autentisering med Application Gateway (för hands version)

Ömsesidig autentisering eller klientautentisering tillåter att Application Gateway autentisera klienten som skickar begär Anden. Normalt autentiseras bara klienten Application Gateway; ömsesidig autentisering tillåter både klienten och Application Gateway att autentisera varandra. 

> [!NOTE]
> Vi rekommenderar att du använder TLS 1,2 med ömsesidig autentisering som TLS 1,2 kommer att godkännas i framtiden. 

## <a name="mutual-authentication"></a>Ömsesidig autentisering

Application Gateway stöder certifikatbaserad ömsesidig autentisering där du kan ladda upp ett betrott certifikat för certifikat utfärdare till Application Gateway och gatewayen använder certifikatet för att autentisera klienten som skickar en begäran till gatewayen. Med den ökande IoT-användningen och de ökade säkerhets kraven mellan branscher, gör ömsesidig autentisering ett sätt för dig att hantera och styra vilka klienter som kan kommunicera med din Application Gateway. 

Om du vill konfigurera ömsesidig autentisering måste ett certifikat för betrodd klient certifikat laddas upp som en del av klientens autentiserings del av en SSL-profil. SSL-profilen måste sedan kopplas till en lyssnare för att kunna slutföra konfigurationen av ömsesidig autentisering. Det måste alltid finnas ett rot certifikat för certifikat utfärdare i det klient certifikat som du överför. Du kan även ladda upp en certifikat kedja, men kedjan måste innehålla ett rot certifikat för certifikat utfärdare förutom så många mellanliggande CA-certifikat som du vill. 

Om ditt klient certifikat till exempel innehåller ett rot certifikat för certifikat utfärdare, flera mellanliggande CA-certifikat och ett löv certifikat, kontrollerar du att rot certifikat utfärdarens certifikat och alla mellanliggande CA-certifikat överförs till Application Gateway i en fil. Mer information om hur du extraherar ett certifikat från en betrodd klient certifikat utfärdare finns i [så här extraherar du betrodda klient certifikat från certifikat utfärdare](./mutual-authentication-certificate-management.md).

Om du överför en certifikat kedja med rot certifikat utfärdare och mellanliggande CA-certifikat måste certifikat kedjan laddas upp som en PEM-eller CER-fil till gatewayen. 

> [!NOTE] 
> Ömsesidig autentisering är bara tillgängligt på Standard_v2 och WAF_v2 SKU: er. 

### <a name="certificates-supported-for-mutual-authentication"></a>Certifikat som stöds för ömsesidig autentisering

Application Gateway stöder följande typer av certifikat:

- Certifikat UTFÄRDAre (certifikat utfärdare): ett CA-certifikat är ett digitalt certifikat som utfärdats av en certifikat utfärdare (CA)
- Självsignerade CA-certifikat: klient webbläsare litar inte på dessa certifikat och varnar användaren om att den virtuella tjänstens certifikat inte ingår i en förtroende kedja. Självsignerade CA-certifikat är bra för testning eller miljöer där administratörer styr klienterna och kan kringgå webbläsarens säkerhets aviseringar på ett säkert sätt. Produktions arbets belastningar bör aldrig använda självsignerade CA-certifikat.

Mer information om hur du konfigurerar ömsesidig autentisering finns i [Konfigurera ömsesidig autentisering med Application Gateway](./mutual-authentication-portal.md).

> [!IMPORTANT]
> Se till att ladda upp hela den betrodda klient certifikat UTFÄRDARens certifikat kedja till Application Gateway när du använder ömsesidig autentisering. 

## <a name="additional-client-authentication-validation"></a>Ytterligare verifiering av klientautentisering

### <a name="verify-client-certificate-dn"></a>Verifiera unikt klient certifikat

Du har möjlighet att verifiera klient certifikatets omedelbara utfärdare och bara tillåta att Application Gateway litar på utfärdaren. Det här alternativet är inaktiverat som standard, men du kan aktivera detta via portal, PowerShell eller Azure CLI. 

Om du väljer att aktivera Application Gateway för att verifiera klient certifikatets omedelbara utfärdare så här tar du reda på vad klient certifikat utfärdarens unika namn kommer att extraheras från certifikaten som laddats upp. 
* **Scenario 1:** Certifikat kedjan omfattar: rot certifikat-mellanliggande certifikat-löv certifikat 
    * *Mellanliggande certifikatets* ämnes namn är det Application Gateway extraheras som klient certifikat utfärdarens DN och kommer att verifieras mot. 
* **Scenario 2:** Certifikat kedjan omfattar: rot certifikat-intermediate1 certifikat-intermediate2 certifikat-löv certifikat
    * *Intermediate2 certifikatets* ämnes namn kommer att extraheras som klient certifikat utfärdarens DN och kommer att verifieras mot. 
* **Scenario 3:** Certifikat kedjan omfattar: rot certifikat-löv certifikat 
    * *Rot certifikatets* ämnes namn kommer att extraheras och användas som klient certifikat utfärdarens unika namn.
* **Scenario 4:** Flera certifikat kedjor av samma längd i samma fil. Kedja 1 innehåller: rot certifikat-intermediate1 certifikat-löv certifikat. Kedja 2 omfattar: rot certifikat-intermediate2 certifikat-löv certifikat. 
    * *Intermediate1 certifikatets* ämnes namn kommer att extraheras som klient certifikat utfärdarens unika namn.  
* **Scenario 5:** Flera certifikat kedjor av olika längd i samma fil. Kedja 1 innehåller: rot certifikat-intermediate1 certifikat-löv certifikat. Kedja 2 innehåller rot certifikat-intermediate2 certifikat-intermediate3 certifikat-löv certifikat. 
    * *Intermediate3 certifikatets* ämnes namn kommer att extraheras som klient certifikat utfärdarens unika namn. 

> [!IMPORTANT]
> Vi rekommenderar bara att ladda upp en certifikat kedja per fil. Detta är särskilt viktigt om du aktiverar verifiera unikt klient certifikat. Genom att överföra flera certifikat kedjor i en fil, kommer du att få i scenarion fyra eller fem och kan stöta på problem senare när klient certifikatet som visas inte stämmer överens med klient certifikat utfärdarens unika namn Application Gateway extraherat från kedjan. 

Mer information om hur du extraherar certifikat kedjor för betrodda certifikat UTFÄRDAre finns i [så här extraherar du certifikat kedjor för betrodda klient certifikat utfärdare](./mutual-authentication-certificate-management.md).

## <a name="server-variables"></a>Servervariabler 

Med ömsesidig autentisering finns ytterligare servervariabler som du kan använda för att skicka information om klient certifikatet till backend-servrarna bakom Application Gateway. Mer information om vilka servervariabler som är tillgängliga och hur de används finns i [servervariabler](./rewrite-http-headers-url.md#mutual-authentication-server-variables-preview).

## <a name="next-steps"></a>Nästa steg

När du har lärt dig om ömsesidig autentisering går du till [konfigurera Application Gateway med ömsesidig autentisering i PowerShell](./mutual-authentication-powershell.md) för att skapa ett Application Gateway med ömsesidig autentisering. 

