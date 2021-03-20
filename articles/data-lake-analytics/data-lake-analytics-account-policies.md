---
title: Hantera Azure Data Lake Analytics konto principer
description: Lär dig hur du använder konto principer för att styra användningen av ett Data Lake Analytics konto, till exempel maximalt antal och maximalt antal jobb.
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.topic: how-to
ms.date: 04/30/2018
ms.openlocfilehash: ba69098f32e131714a15923aef64c3f6ba17e18f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96013317"
---
# <a name="manage-azure-data-lake-analytics-using-account-policies"></a>Hantera Azure Data Lake Analytics att använda konto principer

Med konto principer kan du styra hur resurser ett Azure Data Lake Analytics konto ska användas. Med dessa principer kan du kontrol lera kostnaden för att använda Azure Data Lake Analytics. Med dessa principer kan du till exempel förhindra oväntade kostnads toppar genom att begränsa hur många centrala användare som kontot kan använda samtidigt. # # principer på konto nivå

Dessa principer gäller för alla jobb i ett Data Lake Analytics-konto.

## <a name="maximum-number-of-aus-in-a-data-lake-analytics-account"></a>Maximalt antal Australien i ett Data Lake Analytics konto

En princip styr det totala antalet analys enheter (Australien) som ditt Data Lake Analyticss konto kan använda. Som standard anges värdet till 250. Om det här värdet till exempel är inställt på 250 Australien, kan du ha ett jobb som körs med 250-appen tilldelad eller 10 jobb som körs med 25 Australien. Ytterligare jobb som skickas placeras i kö tills de pågående jobben har avslut ATS. När jobben har körts klart frigörs mappen för de köade jobben som ska köras.

Så här ändrar du antalet Australien för ditt Data Lake Analytics-konto:

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **gränser och principer**.
3. Under **maximal Australien** flyttar du skjutreglaget för att välja ett värde eller anger värdet i text rutan.
4. Klicka på **Spara**.

   > [!NOTE]
   > Om du behöver mer än standard-Australien (250) klickar du på **Hjälp + Support** för att skicka en supportbegäran i portalen. Det går att öka antalet tillgängliga Australien i ditt Data Lake Analytics konto.

## <a name="maximum-number-of-jobs-that-can-run-simultaneously"></a>Maximalt antal jobb som kan köras samtidigt

Den här principen begränsar hur många jobb som kan köras samtidigt. Som standard är det här värdet inställt på 20. Om din Data Lake Analytics har tillgänglig Australien, kommer nya jobb att köras omedelbart tills det totala antalet jobb som körs når värdet för den här principen. När du når det maximala antalet jobb som kan köras samtidigt placeras efterföljande jobb i prioritetsordning tills ett eller flera pågående jobb slutförs (beroende på tillgänglig Australien).

Ändra antalet jobb som kan köras samtidigt:

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **gränser och principer**.
3. Under **maximalt antal jobb som körs** flyttar du skjutreglaget för att välja ett värde eller anger värdet i text rutan.
4. Klicka på **Spara**.

   > [!NOTE]
   > Om du behöver köra fler än standard antalet jobb, klickar du på **Hjälp + Support** för att skicka en supportbegäran i portalen. Det går att öka antalet jobb som kan köras samtidigt i ditt Data Lake Analytics konto.

## <a name="how-long-to-keep-job-metadata-and-resources"></a>Hur lång tid det tar att bevara jobbets metadata och resurser

När användarna kör U-SQL-jobb, behåller Data Lake Analytics tjänsten alla relaterade filer. Filerna innehåller U-SQL-skriptet, DLL-filerna som refereras till i U-SQL-skriptet, kompilerade resurser och statistik. Filerna finns i mappen/system/för standard Azure Data Lake Storage-kontot. Den här principen styr hur länge dessa resurser lagras innan de tas bort automatiskt (Standardvärdet är 30 dagar). Du kan använda dessa filer för fel sökning och för prestanda justering av jobb som du kommer att köra igen i framtiden.

Ändra hur lång tid det tar att behålla jobbets metadata och resurser:

1. I Azure Portal går du till ditt Data Lake Analytics-konto.
2. Klicka på **gränser och principer**.
3. Under **dagar för att behålla jobb frågor**, flytta skjutreglaget för att välja ett värde eller ange värdet i text rutan.  
4. Klicka på **Spara**.

## <a name="job-level-policies"></a>Principer för jobb nivå

Med principer för jobbnivå kan du kontrol lera den maximala Australien och den högsta prioritet som enskilda användare (eller medlemmar i specifika säkerhets grupper) kan ange för jobb som de skickar. Med den här principen kan du kontrol lera de kostnader som användarna har ådragit sig. Du kan också styra vilken inverkan schemalagda jobb kan ha på hög prioriterade produktions jobb som körs i samma Data Lake Analytics konto.

Data Lake Analytics har två principer som du kan ange på jobb nivå:

- **Au-gräns per jobb**: användare kan bara skicka jobb som har upp till det här antalet Australien. Som standard är den här gränsen densamma som den maximala AU-gränsen för kontot.

- **Prioritet**: användare kan bara skicka jobb som har en prioritet som är lägre än eller lika med det här värdet. En högre siffra anger en lägre prioritet. Som standard är den här gränsen inställd på 1, vilket är den högsta möjliga prioriteten.

Det finns en standard princip uppsättning för varje konto. Standard principen gäller för alla användare av kontot. Du kan skapa ytterligare principer för vissa användare och grupper.

> [!NOTE]
> Principer på konto nivå och principer för jobbnivå gäller samtidigt.

## <a name="add-a-policy-for-a-specific-user-or-group"></a>Lägga till en princip för en speciell användare eller grupp

1. I Azure Portal går du till ditt Data Lake Analytics-konto.

2. Klicka på **gränser och principer**.

3. Under **jobb överförings gränser** klickar du på knappen **Lägg till princip** . Välj eller ange följande inställningar:

   1. **Namn på beräknings princip**: Ange ett princip namn för att påminna dig om syftet med principen.

   2. **Välj användare eller grupp**: Välj den användare eller grupp som den här principen gäller för.

   3. **Ange tröskelvärde för jobbets au**: Ange den au-gräns som gäller för den valda användaren eller gruppen.

   4. **Ange prioritets gräns**: Ange den prioritets gräns som gäller för den valda användaren eller gruppen.

4. Klicka på **OK**.

5. Den nya principen visas i **standard** princip tabellen under **jobb överförings gränser**.

## <a name="delete-or-edit-an-existing-policy"></a>Ta bort eller redigera en befintlig princip

1. I Azure Portal går du till ditt Data Lake Analytics-konto.

2. Klicka på **gränser och principer**.

3. Under **jobb överförings gränser** söker du efter den princip som du vill redigera.

4. Om du vill se alternativen för att **ta bort** och **Redigera** i kolumnen längst till höger i tabellen, klickar du på `...` . # # ytterligare resurser för jobb principer

- [Blogg inlägg för princip översikt](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-overview)
- [Blogg inlägg på konto nivå principer](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-account-level-policy)
- [Blogg inlägg på jobb nivå principer](/archive/blogs/azuredatalake/managing-your-azure-data-lake-analytics-compute-resources-job-level-policy)

## <a name="next-steps"></a>Nästa steg

- [Översikt över Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Kom igång med Data Lake Analytics med hjälp av Azure Portal](data-lake-analytics-get-started-portal.md)
- [Hantera Azure Data Lake Analytics med Azure PowerShell](data-lake-analytics-manage-use-powershell.md)