---
title: Kopiera nya och ändrade filer efter LastModifiedDate
description: Lär dig hur du använder en lösnings mall för att kopiera nya och ändrade filer genom att LastModifiedDate med Azure Data Factory.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 94d09dc96d017dba3b16cc5fe113272a9393741d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362088"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Kopiera nya och ändrade filer genom LastModifiedDate med Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver en lösnings mall som du kan använda för att kopiera nya och ändrade filer endast av LastModifiedDate från en filbaserad lagring till ett mål lager. 

## <a name="about-this-solution-template"></a>Om den här lösnings mal len

Den här mallen väljer först de nya och ändrade filerna enbart efter deras attribut **LastModifiedDate** och kopierar sedan de valda filerna från data käll arkivet till data destinations lagret.

Mallen innehåller en aktivitet:
- **Kopiera** för att kopiera nya och ändrade filer enbart av LastModifiedDate från ett fil lager till ett mål lager.

Mallen definierar sex parametrar:
-  *FolderPath_Source* är sökvägen till mappen där du kan läsa filerna från käll arkivet. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *Directory_Source* är sökvägen till undermappen där du kan läsa filerna från käll arkivet. Du måste ersätta standardvärdet med din egen sökväg för undermappen.
-  *FolderPath_Destination* är mappsökvägen dit du vill kopiera filer till mål lagret. Du måste ersätta standardvärdet med din egen mappsökväg.
-  *Directory_Destination* är sökvägen till undermappen där du vill kopiera filer till mål lagret. Du måste ersätta standardvärdet med din egen sökväg för undermappen.
-  *LastModified_From* används för att markera de filer vars LastModifiedDate-attribut är efter eller lika med detta datetime-värde.  För att kunna välja endast nya filer, som inte har kopierats senaste gången, kan det här datetime-värdet vara den tid då pipelinen utlöstes senast. Du kan ersätta standardvärdet "2019-02-01T00:00:00Z" till din förväntade LastModifiedDate i UTC-tidszonen. 
-  *LastModified_To* används för att markera de filer vars LastModifiedDate-attribut är före detta datetime-värde. För att kunna välja endast nya filer, som inte har kopierats senast, kan det här datetime-värdet vara aktuell tid.  Du kan ersätta standardvärdet "2019-02-01T00:00:00Z" till din förväntade LastModifiedDate i UTC-tidszonen. 

## <a name="how-to-use-this-solution-template"></a>Så här använder du den här lösnings mal len

1. Gå till mall **Kopiera endast nya filer av LastModifiedDate**. Skapa en **ny** anslutning till ditt käll lagrings lager. Käll lagrings lagret är den plats där du vill kopiera filer.

    ![Skapa en ny anslutning till källan](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png)
    
2. Skapa en **ny** anslutning till mål arkivet. Mål lagret är dit du vill kopiera filer till. 

    ![Skapa en ny anslutning till målet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png)

3. Välj **Använd den här mallen**.

    ![Använd den här mallen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png)
    
4. Du ser pipelinen tillgänglig i panelen, som du ser i följande exempel:

    ![Visa pipelinen](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png)

5. Välj **Felsök**, Skriv värdet för **parametrarna** och välj **Slutför**.  I bilden nedan anger vi parametrarna som följer.
   - **FolderPath_Source** = SourceFolder
   - **Directory_Source** = undermapp
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = undermapp
   - **LastModified_From** = 2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    Exemplet indikerar att filerna, som senast ändrades i TimeSpan (**2019-02-01T00:00:00Z** till **2019-03-01T00:00:00Z**) kommer att kopieras från käll Sök vägen **SourceFolder/undermappen** till mål Sök vägen **destinationfolder/undermappen**.  Du kan ersätta dessa med dina egna parametrar.

    ![Köra en pipeline](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png)

6. Granska resultatet. Du kommer bara att se de filer som senast ändrades inom det konfigurerade TimeSpan som har kopierats till mål lagret.

    ![Granska resultatet](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png)
    
7. Nu kan du lägga till en rullande Windows-utlösare för att automatisera den här pipelinen, så att pipelinen alltid kan kopiera nya och ändrade filer endast av LastModifiedDate med jämna mellanrum.  Välj **Lägg till utlösare** och välj **nytt/redigera**.

    ![Skärm bild som visar meny alternativet nytt/redigera som visas när du väljer Lägg till utlösare.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png)
    
8. I fönstret **Lägg till utlösare** väljer du **+ ny**.

9. Välj **rullande-fönstret** för utlösnings typen, ange **var 15: e minut** som upprepning (du kan ändra till valfri intervall tid). Välj **Ja** för aktive rad rutan och välj sedan **OK**.

    ![Skapa utlösaren](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png)    
    
10. Ange värdet för **utlösarens körnings parametrar** enligt följande och välj **Slutför**.
    - **FolderPath_Source**  =  **SourceFolder**.  Du kan ersätta med din mapp i käll data lagret.
    - **Directory_Source**  =  **undermappen**.  Du kan ersätta med din undermapp i käll data lagret.
    - **FolderPath_Destination**  =  **destinationfolder**.  Du kan ersätta med din mapp i mål data lagret.
    - **Directory_Destination**  =  **undermappen**.  Du kan ersätta med din undermapp i mål data lagret.
    - **LastModified_From**  =   **\@ trigger (). outputs. windowStartTime**.  Det är en system variabel från utlösaren som fastställer tidpunkten då pipelinen utlöstes senast.
    - **LastModified_To**  =  **\@ trigger (). outputs. windowEndTime**.  Det är en system variabel från utlösaren som fastställer tidpunkten då pipelinen utlöses för tillfället.
    
    ![Indataparametrar](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png)
    
11. Välj **Publicera alla**.
    
    ![Publicera alla](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png)

12. Skapa nya filer i din källmapp i data käll arkivet.  Du väntar nu på att pipelinen ska utlösas automatiskt och endast de nya filerna kopieras till mål lagret.

13. Välj fliken **övervaka** i den vänstra navigerings panelen och vänta i cirka 15 minuter om upprepningen av utlösaren har angetts till var 15: e minut. 

14. Granska resultatet. Du ser att din pipeline aktive ras automatiskt var 15: e minut, och bara nya eller ändrade filer från käll arkivet kopieras till mål lagret i varje pipeline-körning.

    ![Skärm bild som visar resultatet som returneras när pipelinen utlöses.](media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png)
    
## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Data Factory](introduction.md)
