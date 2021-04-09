---
title: Microsoft Azure StorSimple Data Manager användar gränssnitt
description: Lär dig hur du kan använda StorSimple Data Manager användar gränssnitt för att transformera data som finns på StorSimple 8000-seriens enheter.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 802f732e8d62f5df861be525316b3c31ab4d0655
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94957936"
---
# <a name="manage-the-storsimple-data-manager-service-in-azure-portal"></a>Hantera tjänsten StorSimple Data Manager i Azure Portal

Den här artikeln förklarar hur du kan använda StorSimple Data Manager användar gränssnittet för att transformera data som finns på StorSimple 8000-seriens enheter. Transformerade data kan sedan användas av andra Azure-tjänster som Azure Media Services, Azure HDInsight, Azure Machine Learning och Azure-Kognitiv sökning.


## <a name="use-storsimple-data-transformation"></a>Använd StorSimple data Transformation

StorSimple Data Manager är den resurs som data omvandlingen instansieras inom. Med data Transformation service kan du omvandla data från StorSimple-formatet till inbyggt format i blobbar eller Azure Files. Om du vill omvandla StorSimple ursprungliga format data måste du ange information om din StorSimple 8000-serie enhet och de data som du vill transformera.

### <a name="create-a-storsimple-data-manager-service"></a>Skapa en StorSimple Data Manager-tjänst

Utför följande steg för att skapa en StorSimple Data Manager-tjänst.

1. Använd dina Microsoft-kontouppgifter för att logga in på [Azure Portal](https://portal.azure.com/).

2. Klicka på **+ skapa en resurs** och sök efter StorSimple Data Manager.

    ![Skapa en StorSimple Data Manager tjänst 1](./media/storsimple-data-manager-ui/create-service-1.png)

3. Klicka på StorSimple Data Manager och klicka sedan på **skapa**.
    
    ![Skapa en StorSimple Data Manager tjänst 2](./media/storsimple-data-manager-ui/create-service-3.png)

3. För den nya tjänsten anger du följande:

   1. Ange ett unikt **tjänst namn** för din StorSimple Data Manager. Det är ett eget namn som kan användas för att identifiera tjänsten. Namnet kan innehålla mellan 3 och 24 tecken som kan vara bokstäver, siffror och bindestreck. Namnet måste börja och sluta med en bokstav eller en siffra.

   2. Välj en **prenumeration** i list rutan. Prenumerationen är kopplad till ditt faktureringskonto. Det här fältet fylls i automatiskt (och går inte att markera) om du bara har en prenumeration.

   3. Välj en befintlig resurs grupp eller skapa en ny grupp. Mer information finns i avsnittet om [Azure-resursgrupper](../azure-resource-manager/management/manage-resource-groups-portal.md).

   4. Ange **platsen** för din tjänst som hus dina lagrings konton och din StorSimple Data Manager-tjänst. Din StorSimple-Enhetshanteraren tjänst, Data Manager tjänst och det associerade lagrings kontot bör vara i de regioner som stöds.
    
   5. Om du vill hämta en länk till den här tjänsten på instrument panelen väljer du **Fäst på instrument panelen**.
    
   6. Klicka på **Skapa**.

      ![Skapa en StorSimple Data Manager tjänst 3](./media/storsimple-data-manager-ui/create-service-4.png)

Det tar några minuter att skapa tjänsten. Du ser ett meddelande när tjänsten har skapats och den nya tjänsten visas.

### <a name="create-a-data-transformation-job-definition"></a>Skapa en definition för ett dataomvandlingsjobb

I en StorSimple Data Manager tjänst måste du skapa en data omvandlings jobb definition. En jobb definition anger information om de StorSimple-data som du är intresse rad av att flytta till ett lagrings konto i det interna formatet. När du har skapat en jobb definition kan du köra det här jobbet igen med olika körnings inställningar.

Utför följande steg för att skapa en jobb definition.

1. Navigera till den tjänst som du har skapat. Gå till **hanterings > jobb definitioner**.

2. Klicka på **+ jobb definition**.

    ![Klicka på + jobb definition](./media/storsimple-data-manager-ui/create-job-definition-1.png)

3. Ange ett namn för jobb definitionen. Namnet kan innehålla mellan 3 och 63 tecken. Namnet får innehålla versaler, gemener, siffror och bindestreck.

4. Ange en plats där jobbet körs. Den här platsen kan vara en annan än den plats där tjänsten distribueras.

5. Klicka på **källa** för att ange käll data lagrings platsen.

    ![Konfigurera käll data lagrings platsen](./media/storsimple-data-manager-ui/create-job-definition-2.png)

6. Eftersom det här är en ny Data Manager tjänst konfigureras inga data lager. I **Konfigurera data källa** anger du information om din enhet med StorSimple 8000-serien och data som är av intresse.

   Om du vill lägga till StorSimple Enhetshanteraren som data lager klickar du på **Lägg till nytt** i list rutan data lager och klickar sedan på **Lägg till data lager**.

    ![Lägg till ny data lagrings platsen](./media/storsimple-data-manager-ui/create-job-definition-3.png)
  
   1. Välj **StorSimple 8000 Series Manager** som typ av data lager.
    
   2. Ange ett eget namn på käll data lagrings platsen.
    
   3. I list rutan väljer du en prenumeration som är kopplad till din StorSimple Enhetshanteraren-tjänst.
    
   4. Ange namnet på StorSimple Enhetshanteraren för **resursen**.

   5. Ange **krypterings nyckeln för tjänst data** för StorSimple Enhetshanteraren-tjänsten. 

      ![Konfigurera käll data lagrings platsen 1](./media/storsimple-data-manager-ui/create-job-definition-4.png)

      Klicka på **OK** när du är färdig. Detta sparar data lagrings platsen. Återanvänd den här StorSimple-Enhetshanteraren i andra jobb definitioner utan att ange parametrarna igen. Det tar några sekunder efter att du har klickat på **OK** för att den nyligen skapade käll data lagrings platsen ska visas i list rutan.

7. I list rutan för **data lager** väljer du den data lagrings plats som du skapade. 

   1. Ange namnet på den StorSimple 8000-serie enhet som innehåller data som är av intresse.

   2. Ange namnet på den volym som finns på den StorSimple-enhet som har dina data av intresse.

   3. I underavsnittet **filter** anger du rot katalogen som innehåller dina data av intresse i _\MyRootDirectory\Data_ -format. Enhets bokstäver som t. ex. _\Data_ stöds inte. Du kan också lägga till eventuella fil filter här.

   4. Data Transformations tjänsten fungerar bara på den senaste ögonblicks bilden av de data som skickas upp till Azure.

   5. Klicka på **OK**.

      ![Konfigurera käll data lagrings platsen 2](./media/storsimple-data-manager-ui/create-job-definition-8.png)

8. Sedan måste mål data centralen konfigureras. Välj lagrings konton för att lagra filer i blobbar i det kontot. I list rutan väljer du **Lägg till ny** och **konfigurerar sedan inställningar**.

9. Välj den typ av mål databas som du vill lägga till och de andra parametrarna som är kopplade till lagrings platsen.

    Om du väljer ett mål för lagrings konto typ kan du ange ett eget namn, en prenumeration (Välj samma som för tjänsten eller annan) och ett lagrings konto.
        ![Konfigurera mål data lagrings platsen 1](./media/storsimple-data-manager-ui/create-job-definition-10.png)

    En lagrings kö skapas när jobbet körs. Den här kön fylls med meddelanden om transformerade blobbar i takt med att de är klara. Namnet på den här kön är detsamma som namnet på jobbdefinitionen.
    
10. När du har lagt till data lagrings platsen väntar du några minuter.
    
    1. Välj den lagrings plats som du skapade som mål i list rutan i **mål kontots namn**.

    2. Välj lagrings typ som blobbar eller filer. Ange namnet på lagrings behållaren där transformerade data finns. Klicka på **OK**.

        ![Konfigurera mål data lagrings platsen lagrings konto](./media/storsimple-data-manager-ui/create-job-definition-16.png)

11. Du kan också markera alternativet om du vill visa en uppskattning av jobb varaktigheten innan du kör jobbet. Skapa jobb definitionen genom att klicka på **OK** . Din jobb definition är nu slutförd. Du kan använda den här jobb definitionen flera gånger via användar gränssnittet med olika körnings inställningar.

    ![Slutför jobb definition](./media/storsimple-data-manager-ui/create-job-definition-13.png)

    Den nyligen skapade jobb definitionen läggs till i listan över jobb definitioner för den här tjänsten.

### <a name="run-the-job-definition"></a>Köra jobbdefinitionen

När du behöver flytta data från StorSimple till det lagrings konto som du har angett i jobb definitionen måste du köra det. Vid körning kan vissa parametrar anges på olika sätt. Stegen är följande:

1. Välj din StorSimple Data Manager-tjänst och gå till **hanterings > jobb definitioner**. Markera och klicka på den jobb definition som du vill köra.
     
     ![Starta jobb körning 1](./media/storsimple-data-manager-ui/start-job-run1.png)

2. Klicka på **Kör nu**.
     
     ![Starta jobb körning 2](./media/storsimple-data-manager-ui/start-job-run2.png)

3. Klicka på **Kör inställningar** om du vill ändra de inställningar som du kanske vill ändra för jobb körningen. Klicka på **OK** och sedan på **Kör** för att starta jobbet.

    ![Starta jobb körning 3](./media/storsimple-data-manager-ui/start-job-run3.png)

4. Om du vill övervaka jobbet går du till **jobb** i din StorSimple Data Manager. Förutom övervakning i bladet **jobb** kan du också lyssna på lagrings kön där ett meddelande läggs till varje gång en fil flyttas från StorSimple till lagrings kontot.

    ![Starta jobb körning 4](./media/storsimple-data-manager-ui/start-job-run4.png)

### <a name="view-logs-after-job-completion"></a>Visa loggar efter slut för ande av jobb

När du har slutfört ett jobb kan du se jobbets status. Jobb status kan vara **slutförd**, **delvis lyckades** och **misslyckades**. Du kan visa listan över filer som har kopierats och filer som inte kunde kopieras. Listorna är tillgängliga i en behållare med namnet **"StorSimple-Data-Manager-joblogs"** på ditt mål lagrings konto. I den här behållaren kan du leta efter en mapp med samma namn som din jobb definition. I detta skapas en mapp för varje jobb körning som innehåller dina listor. Namnet på den här mappen är jobbets GUID, som du kan hämta från sidan jobb information. I de flesta fall visas också en länk för kopierings loggarna på själva jobb sidan.
Det finns två uppsättningar CSV-filer som du ser i den här mappen. Alla filer som börjar med **copiedfilelist...** kommer att innehålla listan över filer som har kopierats. Alla filer som börjar med **failedfilelist...** innehåller filer som inte kunde kopieras, tillsammans med ett fel meddelande.


## <a name="next-steps"></a>Nästa steg

[Använd .NET SDK för att starta StorSimple Data Manager-jobb](storsimple-data-manager-dotnet-jobs.md).