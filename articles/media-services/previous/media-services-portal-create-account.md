---
title: Skapa ett Azure Media Services-konto med Azure-portalen | Microsoft Docs
description: Den här självstudien går igenom hur du skapar ett Azure Media Services-konto med Azure-portalen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: c551e158-aad6-47b4-931e-b46260b3ee4c
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 53f0063d1458c218324d867c1942ae0e044f10d0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011926"
---
# <a name="create-a-media-services-account-using-the-azure-portal"></a>Skapa ett Media Services-konto med hjälp av Azure-portalen

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Azure-portalen är ett sätt att snabbt skapa ett Azure Media Services-konto (AMS). Du kan använda ditt konto för att få åtkomst till Media Services för att lagra, kryptera, koda, hantera och strömma medieinnehåll i Azure. När du skapar ett Media Services-konto skapar du också ett associerat lagringskonto (eller använder ett befintligt). Om du tar bort ett Media Services-konto raderas inte blobbarna på ditt relaterade lagringskonto.

Media Services-kontot och alla associerade lagringskonton måste finnas i samma Azure-prenumeration. Vi rekommenderar starkt att du använder lagringskonton på samma plats som Media Services-kontot för att undvika extra kostnader för latens och utgående datatrafik.

I den här artikeln beskrivs hur man skapar ett Media Services-konto i Azure-portalen.

> [!NOTE]
> Information om tillgänglighet för Azure Media Services funktioner i olika regioner finns i [AMS-funktioner i flera regioner](availability-regions-v-2.md).

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure-konto för att genomföra kursen. Mer information om den kostnadsfria utvärderingsversionen av Azure finns [Kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="create-an-ams-account"></a>Skapa ett AMS-konto

Stegen i det här avsnittet visar hur du skapar ett AMS-konto.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **+ skapa en resurs**  >  **medie**  >  **Media Services**.
3. Ange de erfordrade värdena i **SKAPA MEDIA SERVICES-KONTO**.

   1. Ange namnet på det nya AMS-kontot vid **Kontonamn**. Namnet på ett Media Services-konto består av gemena bokstäver eller siffror utan blanksteg och 3 till 24 tecken.
   2. Vid Prenumeration väljer du mellan de olika Azure-prenumerationer som du har åtkomst till.
   3. I **Resursgrupp** väljer du ny eller befintlig resurs.  En resursgrupp är en samling resurser som delar livscykel, behörigheter och principer. Läs mer [här](../../azure-resource-manager/management/overview.md#resource-groups).
   4. För **Plats** väljer du den geografiska region som ska användas för att lagra media och metadataposter för ditt Media Services-konto. Den här regionen används för att bearbeta och strömma dina media. Endast de tillgängliga Media Services-regionerna visas i listrutan. 
   5. Vid **Storage-konto** väljer du ett lagringskonto för att tillhandahålla Blob Storage av medieinnehållet från ditt Media Services-konto. Du kan välja ett befintligt lagringskonto i samma geografiska region som ditt Media Services-konto eller skapa ett lagringskonto. Ett nytt lagringskonto skapas i samma region. Reglerna för namn på lagringskonton är desamma som för Media Services-konton.

       Mer information om lagring finns [här](../../storage/common/storage-introduction.md).
   6. Välj **PIN-kod för instrumentpanelen** för att se förloppet för kontodistributionen.
4. Klicka på **Skapa** längst ned i formuläret.

    När kontot har skapats läses översiktssidan in. I tabellen med slutpunkter för direktuppspelning har kontot en standardslutpunkt för direktuppspelning med tillståndet **Stoppad**. 

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 
   
## <a name="to-manage-your-ams-account"></a>Hantera AMS-kontot

Om du vill hantera ditt AMS-konto (till exempel ansluta till AMS-API:et via programmering, överföra videoklipp, koda tillgångar, konfigurera innehållsskydd och övervaka jobbförlopp) väljer du **Inställningar** på vänster sida av portalen. I **Inställningar** navigerar du till något av de tillgängliga bladen (till exempel **API-åtkomst**, **Tillgångar**, **Jobb** eller **Innehållsskydd**).

## <a name="next-steps"></a>Nästa steg

Du kan nu överföra filer till AMS-kontot. Mer information finns i [Överföra filer](media-services-portal-upload-files.md).

Om du tänker ansluta till AMS-API:et via programmering kan du läsa [Access the Azure Media Services API with Azure AD authentication](media-services-use-aad-auth-to-access-ams-api.md) (Ansluta API:et för Azure Media Services med Azure AD-autentisering).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
