---
title: Felsöka säkerhets-och åtkomst kontroll problem
description: Lär dig hur du felsöker säkerhets-och åtkomst kontroll problem i Azure Data Factory.
services: data-factory
author: lrtoyou1223
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: lle
ms.reviewer: craigg
ms.openlocfilehash: 99c03ae4430d1a4caf575bdb9900200af0217bf1
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109779"
---
# <a name="troubleshoot-azure-data-factory-security-and-access-control-issues"></a>Felsöka problem med Azure Data Factory säkerhet och åtkomst kontroll

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för säkerhet och åtkomst kontroll i Azure Data Factory.

## <a name="common-errors-and-messages"></a>Vanliga fel och meddelanden


### <a name="connectivity-issue-of-copy-activity-in-cloud-data-store"></a>Anslutnings problem för kopierings aktivitet i moln data lager

#### <a name="symptoms"></a>Symtom

Olika typer av fel meddelanden kan returneras när ett anslutnings problem uppstod för käll-/mottagar data lagret.

#### <a name="cause"></a>Orsak 

Problemet orsakas oftast av följande faktorer:

1. Proxyinställningar i den egna IR-noden (om du använder IR med egen värd)

1. Brand Väggs inställning i nodens IR-nod (om du använder IR med egen värd)

1. Brand Väggs inställning i moln data lager

#### <a name="resolution"></a>Lösning

1. Kontrol lera följande saker först för att se till att problemet orsakas av anslutnings problem:

   - Felet genereras från källan/Sink-anslutningarna.

   - Det går inte att utföra aktiviteten i början av kopian

   - Det är ett konsekvent fel för Azure IR eller lokal IR med en nod, eftersom det kan vara ett slumpmässigt fel för en lokal IR-värd med flera noder, om bara en del av noderna har problem.

1. Kontrol lera proxy-, brand Väggs-och nätverks inställningarna om du använder **IR med egen värd** och att det går att köra till samma data lager i Azure IR. Se följande länkar för fel sökning:

   [IR-portar och brand väggar](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime#ports-and-firewalls) 
    med egen värd [ADLS-anslutning](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-store)
  
1. Om du använder **Azure IR** kan du prova med att inaktivera brand Väggs inställningen för data lager. På så sätt kan problemet i följande två omständigheter åtgärdas:
  
   * [Azure IR IP-adresser](https://docs.microsoft.com/azure/data-factory/azure-integration-runtime-ip-addresses) finns inte i listan över tillåtna.

   * *Tillåt att betrodda Microsoft-tjänster har åtkomst till den här lagrings konto* funktionen är inaktive rad för [Azure Blob Storage](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage#supported-capabilities) och [ADLS gen 2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage#supported-capabilities).

   * *Tillåt åtkomst till Azure-tjänster* är inte aktiverat för ADLS gen1.

1. Kontakta Microsoft om du behöver hjälp om ovanstående metoder inte fungerar.


### <a name="invalid-or-empty-authentication-key-issue-after-public-network-access-is-disabled"></a>Ogiltigt eller tomt problem med autentiserings nyckel efter att offentlig nätverks åtkomst har inaktiverats

#### <a name="symptoms"></a>Symtom

När du har inaktiverat den offentliga nätverks åtkomsten för Data Factory, eftersom integration runtime med egen värd genererar följande fel: "autentiseringsnyckel är ogiltig eller tom."

#### <a name="cause"></a>Orsak

Problemet beror troligen på problem med DNS-matchning, eftersom inaktive ring av offentliga anslutningar och etablering av en privat slut punkt inte hjälper till att återansluta.

Du kan följa stegen nedan för att kontrol lera om Data Factory FQDN har matchats till den offentliga IP-adressen:

1. Bekräfta att du har skapat den virtuella Azure-datorn i samma VNET som Data Factory privata slut punkten.

2. Kör PsPing och ping från virtuell Azure-dator till Data Factory FQDN:

   `ping <dataFactoryName>.<region>.datafactory.azure.net`

   `psping.exe <dataFactoryName>.<region>.datafactory.azure.net:443`

   > [!Note]
   > En port måste anges för PsPing-kommandot, medan 443-porten inte är en måste.

3. Kontrol lera om båda kommandona har matchats till en offentlig ADF-IP-adress som baseras på specificerad region (format xxx. xxx. xxx. 0).

#### <a name="resolution"></a>Lösning

- Du kan referera till artikeln i den [privata Azure-länken för Azure Data Factory](https://docs.microsoft.com/azure/data-factory/data-factory-private-link#dns-changes-for-private-endpoints). Instruktionen är att konfigurera den privata DNS-zonen/-servern att matcha Data Factory FQDN till privat IP-adress.

- Om du inte vill konfigurera den privata DNS-zonen/-servern för närvarande ska du följa stegen nedan som temporär lösning. Anpassad DNS rekommenderas dock fortfarande som långsiktig lösning.

  1. Ändra värd filen i Windows och mappa privat IP (ADF, privat slut punkt) till ADF FQDN.
  
     Gå till sökvägen "C:\Windows\System32\drivers\etc" i Azure VM och öppna **värd** filen med anteckningar. Lägg till raden med mappningens privata IP-adress i FQDN i slutet av filen och spara ändringen.
     
     ![Lägg till mappning till värden](media/self-hosted-integration-runtime-troubleshoot-guide/add-mapping-to-host.png)

  1. Kör samma kommandon i ovanstående verifierings steg för att kontrol lera svaret, som ska innehålla den privata IP-adressen.

  1. Registrera den egna värdbaserade integrerings körningen igen så bör du lösa problemet.
 

### <a name="unable-to-register-ir-authentication-key-on-self-hosted-vms-due-to-private-link"></a>Det går inte att registrera IR-autentisering på lokala datorer med egen värd på grund av en privat länk

#### <a name="symptoms"></a>Symtom

Det går inte att registrera IR-autentisering på den lokala virtuella datorn på grund av att en privat länk är aktive rad.

Fel informationen visas som nedan:

`
Failed to get service token from ADF service with key *************** and time cost is: 0.1250079 seconds, the error code is: InvalidGatewayKey, activityId is: XXXXXXX and detailed error message is Client IP address is not valid private ip Cause Data factory couldn’t access the public network thereby not able to reach out to the cloud to make the successful connection.
`

#### <a name="cause"></a>Orsak

Problemet kan bero på att den virtuella datorn som du försöker installera IR med egen värd är installerad på. För att ansluta till molnet bör du se till att åtkomst till offentligt nätverk är aktiverat.

#### <a name="resolution"></a>Lösning

 **Lösning 1:** Du kan följa stegen nedan för att lösa problemet:

1. Gå till sidan [fabriker – uppdatera](https://docs.microsoft.com/rest/api/datafactory/Factories/Update) .

1. I det övre högra hörnet väljer du knappen **prova** .

1. Under **parametrar**, Fyll i den information som krävs. 

1. Under **brödtext** klistrar du in följande egenskap:
    ```
    { "tags": { "publicNetworkAccess":"Enabled" } }
    ``` 

1. Välj **Kör** för att köra funktionen. 

1. Bekräfta att **svars koden: 200** visas. Den egenskap du klistrade in ska även visas i JSON-definitionen.

1. Lägg till IR-autentiseringsnyckel igen i integration Runtime.


**Lösning 2:** Du kan läsa följande artikel för lösningen:

https://docs.microsoft.com/azure/data-factory/data-factory-private-link

Försök att aktivera offentlig nätverks åtkomst i användar gränssnittet, som visas på följande skärm bild:

![Aktivera offentlig nätverks åtkomst](media/self-hosted-integration-runtime-troubleshoot-guide/enable-public-network-access.png)

## <a name="next-steps"></a>Nästa steg

Om du behöver hjälp med fel sökning kan du prova följande resurser:

*  [Privat länk för Data Factory](data-factory-private-link.md)
*  [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure-videor](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Microsoft Q&en sida](/answers/topics/azure-data-factory.html)
*  [Stack Overflow-forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
