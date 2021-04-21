---
title: Metoder för att skapa certifikat
description: Lär dig mer om olika alternativ för att skapa eller importera Key Vault certifikat i Azure Key Vault. Det finns flera sätt att skapa ett Key Vault certifikat.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 72ff2a1a7b8bcff768248833183ce03a169f9a4d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752128"
---
# <a name="certificate-creation-methods"></a>Metoder för att skapa certifikat

 Ett Key Vault (KV)-certifikat kan antingen skapas eller importeras till ett nyckelvalv. När ett KV-certifikat skapas skapas den privata nyckeln i nyckelvalvet och exponeras aldrig för certifikatägaren. Följande är sätt att skapa ett certifikat i Key Vault:  

-   **Skapa ett själv signerat certifikat:** Detta skapar ett offentligt/privat nyckelpar och associerar det med ett certifikat. Certifikatet signeras med en egen nyckel.  

-    **Skapa ett nytt certifikat manuellt:** Detta skapar ett offentligt/privat nyckelpar och genererar en X.509-certifikatsigneringsbegäran. Signeringsbegäran kan signeras av din registreringsutfärdare eller certifikatutfärdare. Det signerade x509-certifikatet kan sammanfogas med det väntande nyckelparet för att slutföra KV-certifikatet i Key Vault. Även om den här metoden kräver fler steg får du bättre säkerhet eftersom den privata nyckeln skapas i och begränsas till Key Vault. Detta förklaras i diagrammet nedan.  

![Skapa ett certifikat med din egen certifikatutfärdare](../media/certificate-authority-1.png)  

Följande beskrivningar motsvarar de gröna stegen i föregående diagram.

1. I diagrammet ovan skapar programmet ett certifikat, vilket börjar internt med skapandet av en nyckel i ditt nyckelvalv.
2. Key Vault returnerar en begäran om certifikatsignering (CSR) till ditt program
3. Ditt program skickar CSR till den certifikatutfärdare som du har valt.
4. Den valda certifikatutfärdaren svarar med ett X509-certifikat.
5. Programmet slutför skapandet av det nya certifikatet genom en sammanslagning av X509-certifikatet från certifikatutfärdaren.

-   **Skapa ett certifikat med en känd utfärdarprovider:** Den här metoden kräver att du gör en enda uppgift för att skapa ett utfärdarobjekt. När ett utfärdarobjekt har skapats i nyckelvalvet kan dess namn refereras till i principen för KV-certifikatet. En begäran om att skapa ett sådant KV-certifikat skapar ett nyckelpar i valvet och kommunicerar med utfärdarprovidertjänsten med hjälp av informationen i det refererade utfärdarobjektet för att hämta ett x509-certifikat. X509-certifikatet hämtas från utfärdartjänsten och sammanfogas med nyckelparet för att slutföra skapandet av KV-certifikatet.  

![Skapa ett certifikat med en Key Vault en partner-certifikatutfärdare](../media/certificate-authority-2.png)  

Följande beskrivningar motsvarar de gröna stegen i diagrammet ovan.

1. I diagrammet ovan skapar programmet ett certifikat, vilket börjar internt med skapandet av en nyckel i ditt nyckelvalv.
2. Key Vault skickar en TLS-/SSL-certifikatbegäran till certifikatutfärdaren.
3. Programmet avsöker, i en loopa-och-vänta-process, ditt nyckelvalv för slutförande av certifikatet. Skapandet av certifikat är klar när Key Vault tar emot certifikatutfärdarens svar med X.509-certifikat.
4. Certifikatutfärdaren svarar Key Vault TLS/SSL-certifikatbegäran med ett TLS/SSL X.509-certifikat.
5. Ditt nya certifikat skapas i samband med sammanslagningen av TLS/SSL X.509-certifikatet för certifikatutfärdaren.

## <a name="asynchronous-process"></a>Asynkron process
Att skapa KV-certifikat är en asynkron process. Den här åtgärden skapar en KV-certifikatbegäran och returnerar http-statuskoden 202 (accepterad). Status för begäran kan spåras genom avsökning av det väntande objektet som skapats av den här åtgärden. Den fullständiga URI:en för det väntande objektet returneras i LOCATION-huvudet.  

När en begäran om att skapa ett KV-certifikat har slutförts ändras statusen för det väntande objektet till "slutförd" från "pågår" och en ny version av KV-certifikatet skapas. Detta blir den aktuella versionen.  

## <a name="first-creation"></a>Första skapandet
 När ett KV-certifikat skapas för första gången skapas även en adresserbar nyckel och hemlighet med samma namn som certifikatet. Om namnet redan används misslyckas åtgärden med http-statuskoden 409 (konflikt).
Den adresserbara nyckeln och hemligheten hämtar sina attribut från KV-certifikatattributen. Den adresserbara nyckeln och hemligheten som skapas på det här sättet markeras som hanterade nycklar och hemligheter vars livslängd hanteras av Key Vault. Hanterade nycklar och hemligheter är skrivskyddade. Obs! Om ett KV-certifikat upphör att gälla eller inaktiveras blir motsvarande nyckel och hemlighet oanvändbar.  

 Om det här är den första åtgärden för att skapa ett KV-certifikat krävs en princip.  En princip kan också tillhandahållas med efterföljande åtgärder för att ersätta principresursen. Om en princip inte anges används principresursen i tjänsten för att skapa en nästa version av KV-certifikatet. Observera att medan en begäran om att skapa en nästa version pågår, förblir det aktuella KV-certifikatet och motsvarande adresserbar nyckel och hemlighet oförändrade.  

## <a name="self-issued-certificate"></a>Själv utfärdat certifikat
 Om du vill skapa ett själv utfärdat certifikat anger du utfärdarnamnet som "Själv" i certifikatprincipen enligt följande kodfragment från certifikatprincipen.  

```  
"issuer": {  
       "name": "Self"  
    }  

```  

 Om utfärdarens namn inte anges anges utfärdarens namn till "Okänt". När utfärdaren är "Okänd" måste certifikatägaren manuellt hämta ett x509-certifikat från den utfärdare som han eller hon väljer och sedan sammanfoga det offentliga x509-certifikatet med nyckelvalvscertifikatet som väntar på objektet för att slutföra skapandet av certifikatet.

```  
"issuer": {  
       "name": "Unknown"  
    }  

```  

## <a name="partnered-ca-providers"></a>Partner-CA-leverantörer
Du kan skapa certifikat manuellt eller använda en "Själv"-utfärdare. Key Vault samarbetar också med vissa utfärdarproviders för att förenkla skapandet av certifikat. Följande typer av certifikat kan beställas för nyckelvalv med dessa partnerutfärdarproviders.  

|Leverantör|Certifikattyp|Konfigurationskonfiguration  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault erbjuder OV- eller EV SSL-certifikat med DigiCert| [Integrationsguide](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault erbjuder OV- eller EV SSL-certifikat med GlobalSign| [Integrationsguide](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

 En certifikatutfärdare är en entitet som representeras Azure Key Vault (KV) som en CertificateIssuer-resurs. Den används för att tillhandahålla information om källan för ett KV-certifikat. utfärdarens namn, provider, autentiseringsuppgifter och annan administrativ information.

Observera att när en beställning har gjorts hos utfärdarprovidern kan den respektera eller åsidosätta x509-certifikattilläggen och certifikatets giltighetsperiod baserat på typ av certifikat.  

 Auktorisering: Kräver certifikat/skapa-behörighet.

## <a name="see-also"></a>Se även

 - Guide för att skapa certifikat i en Key Vault [portal,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-portal) [Azure CLI,](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-cli) [Azure PowerShell](https://docs.microsoft.com/azure/key-vault/certificates/quick-create-powershell)
 - [Övervaka och hantera processen för att skapa certifikat](create-certificate-scenarios.md)
