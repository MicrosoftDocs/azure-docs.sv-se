---
title: 'ML Studio (klassisk): hantera webb tjänster – Azure'
description: Hantera dina Machine Learning nya och klassiska webb tjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom de klassiska webb tjänsterna och de nya webb tjänsterna baseras på olika underliggande tekniker, har du något annat hanterings möjligheter för var och en av dem.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-ms.author=yahajiza, previous-author=YasinMSFT
ms.date: 02/28/2017
ms.openlocfilehash: 8b04119f4a9ff702356f942cec5bebed1e23926b
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100515945"
---
# <a name="manage-a-web-service-using-the-azure-machine-learning-studio-classic-web-services-portal"></a>Hantera en webb tjänst med hjälp av webb tjänst portalen Azure Machine Learning Studio (klassisk)

**gäller för:** ![ Gäller för. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klassisk) ![ gäller inte för.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  


Du kan hantera dina Machine Learning nya och klassiska webb tjänster med hjälp av Microsoft Azure Machine Learning Web Services-portalen. Eftersom de klassiska webb tjänsterna och de nya webb tjänsterna baseras på olika underliggande tekniker, har du något annat hanterings möjligheter för var och en av dem.

I Machine Learning Web Services-portalen kan du:

* Övervaka hur webb tjänsten används.
* Konfigurera beskrivningen, uppdatera nycklarna för webb tjänsten (endast ny), uppdatera din lagrings konto nyckel (endast ny), aktivera loggning och aktivera eller inaktivera exempel data.
* Ta bort webb tjänsten.
* Skapa, ta bort eller uppdatera fakturerings planer (endast nya).
* Lägga till och ta bort slut punkter (endast klassiskt)

>[!NOTE]
>Du kan också hantera klassiska webb tjänster i [Machine Learning Studio (klassisk)](https://studio.azureml.net) på fliken **webb tjänster** .

## <a name="permissions-to-manage-new-resources-manager-based-web-services"></a>Behörigheter för att hantera nya resurs hanterarens baserade webb tjänster

Nya webb tjänster distribueras som Azure-resurser. Därför måste du ha rätt behörighet för att distribuera och hantera nya webb tjänster.  Om du vill distribuera eller hantera nya webb tjänster måste du tilldelas rollen deltagare eller administratör för den prenumeration som webb tjänsten har distribuerats till. Om du bjuder in en annan användare till en Machine Learning-arbetsyta måste du tilldela dem till en deltagare eller administratörs roll i prenumerationen innan de kan distribuera eller hantera webb tjänster. 

Om användaren inte har rätt behörighet för att komma åt resurser i Azure Machine Learning Web Services-portalen, får de följande fel meddelande när du försöker distribuera en webb tjänst:

*Webb tjänst distributionen misslyckades. Det här kontot har inte tillräcklig åtkomst till den Azure-prenumeration som innehåller arbets ytan. För att kunna distribuera en webb tjänst till Azure måste samma konto bjudas in till arbets ytan och ges åtkomst till den Azure-prenumeration som innehåller arbets ytan.*

Mer information om hur du skapar en arbets yta finns i [skapa och dela en Azure Machine Learning Studio (klassisk)-arbets yta](create-workspace.md).

Mer information om hur du ställer in åtkomst behörighet finns i [lägga till eller ta bort Azure-roll tilldelningar med hjälp av Azure Portal](../../role-based-access-control/role-assignments-portal.md).


## <a name="manage-new-web-services"></a>Hantera nya webb tjänster
Så här hanterar du dina nya webb tjänster:

1. Logga in på [Microsoft Azure Machine Learning Web Services-](https://services.azureml.net/quickstart) portalen med ditt Microsoft Azure-konto – Använd det konto som är associerat med Azure-prenumerationen.
2. På menyn klickar du på **webb tjänster**.

Visar en lista över distribuerade webb tjänster för din prenumeration. 

Klicka på webb tjänster för att hantera en webb tjänst. På sidan webb tjänster kan du:

* Klicka på webb tjänsten för att hantera den.
* Klicka på fakturerings planen för webb tjänsten för att uppdatera den.
* Ta bort en webb tjänst.
* Kopiera en webb tjänst och distribuera den till en annan region.

När du klickar på en webb tjänst öppnas sidan snabb start för webb tjänst. Webb tjänstens snabb starts sida har två meny alternativ som gör att du kan hantera din webb tjänst:

* **Instrument panel** – gör att du kan visa webb tjänst användning.
* **Konfigurera** – tillåter att du lägger till beskrivande text, uppdaterar nyckeln för lagrings kontot som är kopplat till webb tjänsten och aktiverar eller inaktiverar exempel data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webb tjänsten används
Klicka på fliken **instrument panel** .

På instrument panelen kan du Visa den övergripande användningen av din webb tjänst under en viss tids period. Du kan välja den period som du vill visa i list rutan period längst upp till höger i användnings diagrammen. Instrument panelen visar följande information:

* **Begär anden över tid** visar ett steg diagram över antalet begär Anden under den valda tids perioden. Det kan hjälpa dig att identifiera om du får toppar i användning.
* Begär **Anden om svars svar** visar det totala antalet Request-Response samtal tjänsten har tagit emot under den valda tids perioden och hur många av dem som misslyckades.
* **Genomsnittlig Request-Response beräknings tid** visar ett genomsnitt av tiden som krävs för att köra mottagna begär Anden.
* **Batch-begäranden** visar det totala antalet batch-begäranden som tjänsten har tagit emot under den valda tids perioden och hur många av dem som misslyckades.
* **Genomsnittlig jobb svars** tid visar ett genomsnitt av tiden som krävs för att köra mottagna begär Anden.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webb tjänsten.
* **Tjänste kostnader** visar avgifterna för den fakturerings plan som är associerad med tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webb tjänsten
Klicka på meny alternativet **Konfigurera** .

Du kan uppdatera följande egenskaper:

* Med **Beskrivning** kan du ange en beskrivning av webb tjänsten.
* Med **rubrik** kan du ange en titel för webb tjänsten
* Med **nycklar** kan du rotera dina primära och sekundära API-nycklar.
* Med **lagrings konto nyckeln** kan du uppdatera nyckeln för lagrings kontot som är kopplat till webb tjänstens ändringar. 
* **Aktivera exempel data** gör att du kan tillhandahålla exempel data som du kan använda för att testa Request-Response tjänsten. Om du har skapat webb tjänsten i Machine Learning Studio (klassisk) hämtas exempel data från de data som används för att träna din modell. Om du skapade tjänsten program mässigt hämtas data från de exempel data som du angav som en del av JSON-paketet.

### <a name="managing-billing-plans"></a>Hantera fakturerings planer
Klicka på meny alternativet **planer** på snabb starts sidan för webb tjänster. Du kan också klicka på den plan som är kopplad till en speciell webb tjänst för att hantera planen.

* Med **nytt** kan du skapa en ny plan.
* Med **Lägg till/ta bort plan-instans** kan du skala ut en befintlig plan för att lägga till kapacitet.
* Med **uppgradering/nedgradering** kan du skala upp en befintlig plan för att lägga till kapacitet.
* Med **ta bort** kan du ta bort en plan.

Klicka på en plan om du vill visa dess instrument panel. Instrument panelen ger dig ögonblicks bilder eller planerar användning under en vald tids period. Välj den tids period som du vill visa genom att klicka på list rutan **period** längst upp till höger i instrument panelen. 

Plan-instrumentpanelen innehåller följande information:

* I **plan beskrivningen** visas information om kostnaderna och kapaciteten som är kopplade till planen.
* **Plan användning** visar antalet transaktioner och beräknings timmar som har debiterats för planen.
* **Webb tjänster** visar antalet webb tjänster som använder den här planen.
* **Främsta webb tjänster per anrop** visar de fyra viktigaste webb tjänsterna som gör samtal som debiteras mot planen.
* De **främsta webb tjänsterna per beräkning i timmar** visar de fyra viktigaste webb tjänsterna som använder beräknings resurser som debiteras enligt planen.

## <a name="manage-classic-web-services"></a>Hantera klassiska webb tjänster
> [!NOTE]
> Procedurerna i det här avsnittet är relevanta för att hantera klassiska webb tjänster via Azure Machine Learning Web Services-portalen. Information om hur du hanterar klassiska webb tjänster via Machine Learning Studio (klassisk) och Azure Portal finns i [hantera en Azure Machine Learning Studio (klassisk)-arbets yta](manage-workspace.md).
> 
> 

Så här hanterar du dina klassiska webb tjänster:

1. Logga in på [Microsoft Azure Machine Learning Web Services-](https://services.azureml.net/quickstart) portalen med ditt Microsoft Azure-konto – Använd det konto som är associerat med Azure-prenumerationen.
2. På menyn klickar du på **klassiska webb tjänster**.

Klicka på **klassiska webb tjänster** om du vill hantera en klassisk webb tjänst. På sidan klassiska webb tjänster kan du:

* Klicka på webb tjänsten för att visa de associerade slut punkterna.
* Ta bort en webb tjänst.

När du hanterar en klassisk webb tjänst hanterar du varje slut punkt separat. När du klickar på en webb tjänst på sidan webb tjänster öppnas listan över slut punkter som är kopplade till tjänsten. 

På den klassiska webb tjänstens slut punkts sida kan du lägga till och ta bort slut punkter för tjänsten. Mer information om hur du lägger till slut punkter finns i [skapa slut punkter](create-endpoint.md).

Klicka på en av slut punkterna för att öppna snabb starts sidan för webb tjänsten. På snabb starts sidan finns det två meny alternativ som gör att du kan hantera din webb tjänst:

* **Instrument panel** – gör att du kan visa webb tjänst användning.
* **Konfigurera** – tillåter att du lägger till beskrivande text, aktiverar och inaktiverar fel loggning, uppdaterar nyckeln för det lagrings konto som är associerat med webb tjänsten och aktiverar och inaktiverar exempel data.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Övervaka hur webb tjänsten används
Klicka på fliken **instrument panel** .

På instrument panelen kan du Visa den övergripande användningen av din webb tjänst under en viss tids period. Du kan välja den period som du vill visa i list rutan period längst upp till höger i användnings diagrammen. Instrument panelen visar följande information:

* **Begär anden över tid** visar ett steg diagram över antalet begär Anden under den valda tids perioden. Det kan hjälpa dig att identifiera om du får toppar i användning.
* Begär **Anden om svars svar** visar det totala antalet Request-Response samtal tjänsten har tagit emot under den valda tids perioden och hur många av dem som misslyckades.
* **Genomsnittlig Request-Response beräknings tid** visar ett genomsnitt av tiden som krävs för att köra mottagna begär Anden.
* **Batch-begäranden** visar det totala antalet batch-begäranden som tjänsten har tagit emot under den valda tids perioden och hur många av dem som misslyckades.
* **Genomsnittlig jobb svars** tid visar ett genomsnitt av tiden som krävs för att köra mottagna begär Anden.
* **Fel** visar det sammanlagda antalet fel som har inträffat på anrop till webb tjänsten.
* **Tjänste kostnader** visar avgifterna för den fakturerings plan som är associerad med tjänsten.

### <a name="configuring-the-web-service"></a>Konfigurera webb tjänsten
Klicka på meny alternativet **Konfigurera** .

Du kan uppdatera följande egenskaper:

* Med **Beskrivning** kan du ange en beskrivning av webb tjänsten. Beskrivning är ett obligatoriskt fält.
* Med **loggning** kan du aktivera eller inaktivera fel loggning på slut punkten. Mer information om loggning finns i Aktivera [loggning för Machine Learning-webbtjänster](web-services-logging.md).
* **Aktivera exempel data** gör att du kan tillhandahålla exempel data som du kan använda för att testa Request-Response tjänsten. Om du har skapat webb tjänsten i Machine Learning Studio (klassisk) hämtas exempel data från de data som används för att träna din modell. Om du skapade tjänsten program mässigt hämtas data från de exempel data som du angav som en del av JSON-paketet.