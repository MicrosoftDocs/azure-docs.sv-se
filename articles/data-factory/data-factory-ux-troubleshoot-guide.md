---
title: Felsöka Azure Data Factory UX
description: Lär dig hur du felsöker problem med Azure Data Factory UX.
author: ceespino
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/03/2020
ms.author: ceespino
ms.reviewer: daperlov
ms.openlocfilehash: b423b008453ca5f1b76e5c277f5a434dbf14cdb1
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382845"
---
# <a name="troubleshoot-azure-data-factory-ux-issues"></a>Felsöka problem med Azure Data Factory UX

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här artikeln utforskar vanliga fel söknings metoder för Azure Data Factory UX.

## <a name="adf-ux-not-loading"></a>ADF-UX läses inte in

> [!NOTE]
> Azure Data Factory UX har officiellt stöd för Microsoft Edge och Google Chrome. Att använda andra webbläsare kan leda till oväntad eller inte dokumenterad funktion.

### <a name="third-party-cookies-blocked"></a>Cookies från tredje part blockeras

ADF UX använder webbläsarens cookies för att spara användarsessionen och aktivera interaktiva utvecklings-och övervaknings upplevelser. Det är möjligt att webbläsaren blockerar cookies från tredje part eftersom du använder en Incognito-session eller har en ad-blockerare aktive rad. Om du blockerar cookies från tredje part kan det orsaka problem vid inläsning av portalen, till exempel att de ska omdirigeras till en tom sida, https://adf.azure.com/accesstoken.html eller få ett varnings meddelande om att cookies från tredje part blockeras. Lös problemet genom att aktivera cookies-alternativ från tredje part i webbläsaren med följande steg:

### <a name="google-chrome"></a>Google Chrome

#### <a name="allow-all-cookies"></a>Tillåt alla cookies

1. Besök **Chrome://settings/cookies** i webbläsaren.
1. Välj alternativet **Tillåt alla cookies** 

    ![Tillåt alla cookies i Chrome](media/data-factory-ux-troubleshoot-guide/chrome-allow-all-cookies.png)
1. Uppdatera ADF UX och försök igen.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Tillåt endast att ADF-UX använder cookies
Om du inte vill tillåta alla cookies kan du välja att bara tillåta ADF-UX:
1. Besök **Chrome://settings/cookies**.
1. Välj alternativet **Lägg till** under **platser som alltid kan använda cookies** 

    ![Lägg till ADF-UX till tillåtna platser i Chrome](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-1.png)
1. Lägg till **ADF.Azure.com** -plats, markera alternativet **alla cookies** och spara. 

    ![Tillåt alla cookies från ADF UX-webbplatsen](media/data-factory-ux-troubleshoot-guide/chrome-only-adf-cookies-2.png)
1. Uppdatera ADF UX och försök igen.

### <a name="microsoft-edge"></a>Microsoft Edge

1. Besök **Edge://Settings/Content/cookies** i webbläsaren.
1. Se till att **platser för att spara och läsa cookie-data** är aktiverade och att alternativet **blockera cookies från tredje part** är inaktiverat 

    ![Tillåt alla cookies i Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-all-cookies.png)
1. Uppdatera ADF UX och försök igen.

#### <a name="only-allow-adf-ux-to-use-cookies"></a>Tillåt endast att ADF-UX använder cookies

Om du inte vill tillåta alla cookies kan du välja att bara tillåta ADF-UX:

1. Besök **Edge://Settings/Content/cookies**.
1. Under avsnittet **Tillåt** väljer du **Lägg till** och Lägg till **ADF.Azure.com** -plats. 

    ![Lägg till ADF-UX till tillåtna platser i Edge](media/data-factory-ux-troubleshoot-guide/edge-allow-adf-cookies.png)
1. Uppdatera ADF UX och försök igen.

## <a name="connection-failed-on-adf-ux"></a>Anslutningen misslyckades på ADF-UX

Ibland ser du "anslutningen misslyckades" fel på ADF-UX som liknar skärm bilden nedan efter att ha klickat på **Testa anslutning**, för **hands version** osv.

![Anslutningen misslyckades](media/data-factory-ux-troubleshoot-guide/connection-failed.png)

I så fall kan du först testa samma åtgärd med InPrivate-bläddringsläge i webbläsaren.

Om det fortfarande inte fungerar går du till webbläsaren och trycker på F12 för att öppna **utvecklarverktyg**. Gå till fliken **nätverk** , markera **inaktivera cache**, försök utföra åtgärden igen och leta upp den misslyckade begäran (i rött).

![Misslyckad begäran](media/data-factory-ux-troubleshoot-guide/failed-request.png)

Leta sedan upp **värd namnet** (i det här fallet **Dpnortheurope.svc.DataFactory.Azure.com**) från **begär ande-URL:** en för den misslyckade begäran.

Skriv **värd namnet** direkt i webbläsarens Adress fält. Om du ser 404 i webbläsaren innebär det vanligt vis att din klient sida är OK och att problemet är på sidan för ADF-tjänsten. File a Support Ticket med **aktivitets-ID: t** från fel meddelandet i ADF-UX.

![Resursen hittades inte](media/data-factory-ux-troubleshoot-guide/status-code-404.png)

Om inte, eller om du ser liknande fel nedan i webbläsaren, innebär detta vanligt vis att du har vissa problem på klient sidan. Följ fel söknings stegen.

![Fel på klient Sidan](media/data-factory-ux-troubleshoot-guide/client-side-error.png)

Öppna **kommando tolken** och skriv **nslookup-dpnortheurope.svc.DataFactory.Azure.com**. Ett normalt svar bör se ut så här:

![Kommando svar 1](media/data-factory-ux-troubleshoot-guide/command-response-1.png)

Om du ser ett normalt DNS-svar kan du kontakta din lokala IT-support för att kontrol lera brand Väggs inställningarna på om HTTPS-anslutningen till värd namnet är blockerad eller inte. Om problemet inte kunde lösas, File a Support Ticket med **aktivitets-ID: t** från fel meddelandet i ADF-UX.

Om du ser något annat än så innebär detta vanligt vis att det är något fel på DNS-servern när DNS-namnet matchas. Det kan vanligt vis vara en möjlig lösning att ändra ISP (Internet Service Provider) eller DNS (t. ex. till Google DNS-8.8.8.8). Om problemet kvarstår kan du ytterligare prova **nslookup-DataFactory.Azure.com** och nslookup- **Azure.com** för att se på vilken nivå som DNS-matchningen misslyckades och skicka all information till din lokala IT-support eller din Internet leverantör för fel sökning. Om de tror att problemet fortfarande finns på Microsoft, kan du skicka in ett support ärende med **aktivitets-ID: t** från fel meddelandet i ADF-UX.

![Kommando svar 2](media/data-factory-ux-troubleshoot-guide/command-response-2.png)

## <a name="change-linked-service-type-in-datasets"></a>Ändra länkad tjänst typ i data uppsättningar

Data uppsättningen för fil formatet kan användas med alla filbaserade kopplingar, du kan till exempel konfigurera en Parquet-datauppsättning på Azure Blob eller Azure Data Lake Storage Gen2. Observera att varje koppling stöder olika uppsättningar av data lagers inställningar för aktiviteten och med olika program modeller. 

När du använder en fil formats data uppsättning i en aktivitet, inklusive kopiera, leta upp, GetMetadata, ta bort aktiviteter och i data uppsättning som du vill peka på en länkad tjänst av annan typ från den aktuella (t. ex. växla från fil system till ADLS Gen2) visas följande varnings meddelande. För att se till att det är en ren växel, vid ditt medgivande, kommer de pipeliner och aktiviteter som refererar till den här data uppsättningen att ändras till att även använda den nya typen, och alla befintliga inställningar för data lager som är inkompatibla med den nya typen rensas eftersom de inte längre gäller.

Om du vill veta mer om vilka data lagrings inställningar som stöds för varje koppling kan du gå till motsvarande kopplings artikel-> kopiera aktivitets egenskaper för att se den detaljerade egenskaps listan. Se [Amazon S3](connector-amazon-simple-storage-service.md), [azure BLOB](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [fil system](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [http](connector-http.md)och [SFTP](connector-sftp.md).

![Varnings meddelande](media/data-factory-ux-troubleshoot-guide/warning-message.png)

## <a name="next-steps"></a>Nästa steg

Om du vill ha mer fel söknings hjälp kan du prova följande resurser:

* [Data Factory blogg](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory funktions begär Anden](https://feedback.azure.com/forums/270578-data-factory)
* [Stack Overflow forum för Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-information om Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videor](https://azure.microsoft.com/resources/videos/index/)
* [Sidan Microsoft Q&en fråga](/answers/topics/azure-data-factory.html)