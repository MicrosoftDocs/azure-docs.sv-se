---
title: Autentisera Azure Stream Analytics till Azure Data Lake Storage Gen1
description: I den här artikeln beskrivs hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb för att Azure Data Lake Storage Gen1 utdata.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.custom: seodec18
ms.openlocfilehash: d5b406d260b1c6518d9227c51d84ab7eb3754329
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598158"
---
# <a name="authenticate-stream-analytics-to-azure-data-lake-storage-gen1-using-managed-identities"></a>Autentisera Stream Analytics för att Azure Data Lake Storage Gen1 med hanterade identiteter

Azure Stream Analytics stöder gen1-utdata (Managed Identity Authentication) med Azure Data Lake Storage (ADLS). Identiteten är ett hanterat program som är registrerat i Azure Active Directory som representerar ett angivet Stream Analytics jobb och som kan användas för att autentisera till en mål resurs. Hanterade identiteter eliminerar begränsningar för användarbaserade autentiseringsmetoder, som att behöva autentisera på grund av lösen ords ändringar eller förfallo datum för användar-token som inträffar var 90: e dag. Dessutom kan hanterade identiteter hjälpa till med automatisering av Stream Analytics jobb distributioner som utdata till Azure Data Lake Storage Gen1.

Den här artikeln visar tre sätt att aktivera hanterad identitet för ett Azure Stream Analytics jobb som matar ut till en Azure Data Lake Storage Gen1 via Azure Portal, Azure Resource Manager mall-distribution och Azure Stream Analytics verktyg för Visual Studio.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-portal"></a>Azure Portal

1. Börja med att skapa ett nytt Stream Analytics jobb eller genom att öppna ett befintligt jobb i Azure Portal. Välj **hanterad identitet** som finns under **Konfigurera** på Meny raden på vänster sida av skärmen.

   ![Konfigurera Stream Analytics hanterad identitet](./media/stream-analytics-managed-identities-adls/stream-analytics-managed-identity-preview.png)

2. Välj **Använd systemtilldelad hanterad identitet** från fönstret som visas till höger. Klicka på **Spara** till ett huvud namn för tjänsten för Stream Analytics jobbets identitet i Azure Active Directory. Livs cykeln för den nyligen skapade identiteten hanteras av Azure. När Stream Analytics jobb tas bort, tas den tillhör ande identiteten (dvs. tjänstens huvud namn) automatiskt bort av Azure.

   När konfigurationen sparas visas objekt-ID (OID) för tjänstens huvud namn som huvud-ID: t enligt nedan:

   ![Stream Analytics tjänstens huvud namns-ID](./media/stream-analytics-managed-identities-adls/stream-analytics-principal-id.png)
 
   Tjänstens huvud namn har samma namn som Stream Analyticss jobbet. Om namnet på ditt jobb till exempel är **MyASAJob**, är namnet på det skapade tjänst huvud namnet också **MyASAJob**.

3. I fönstret utmatnings egenskaper i ADLS Gen1 utgående mottagare klickar du på list rutan autentiseringsläge och väljer **hanterad identitet**.

4. Fyll i resten av egenskaperna. Mer information om hur du skapar en ADLS-utdata finns i [skapa ett data Lake Store-utdata med Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md). När du är klar klickar du på **Spara**.

   ![Konfigurera Azure Data Lake Storage](./media/stream-analytics-managed-identities-adls/stream-analytics-configure-adls.png)
 
5. Gå till sidan Översikt i ADLS Gen1 och klicka på **data Utforskaren**.

   ![Konfigurera Data Lake Storage översikt](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-overview.png)

6. I fönstret data Utforskaren väljer du **åtkomst** och klickar på **Lägg till** i rutan åtkomst.

   ![Konfigurera Data Lake Storage åtkomst](./media/stream-analytics-managed-identities-adls/stream-analytics-adls-access.png)

7. I text rutan i fönstret **Välj användare eller grupp** skriver du namnet på tjänstens huvud namn. Kom ihåg att namnet på tjänstens huvud namn också är namnet på motsvarande Stream Analytics jobb. När du börjar skriva huvud namnet visas det under text rutan. Välj önskat tjänst huvud namn och klicka på **Välj**.

   ![Välj ett huvud namn för tjänsten](./media/stream-analytics-managed-identities-adls/stream-analytics-service-principal-name.png)
 
8. I rutan **behörigheter** kontrollerar du **Skriv** -och **körnings** behörigheterna och tilldelar den till **den här mappen och alla underordnade**. Klicka sedan på **OK**.

   ![Välj Skriv-och körnings behörighet](./media/stream-analytics-managed-identities-adls/stream-analytics-select-permissions.png)
 
9. Tjänstens huvud namn visas under **tilldelade behörigheter** i **åtkomst** fönstret som visas nedan. Nu kan du gå tillbaka och starta ditt Stream Analytics-jobb.

   ![Stream Analytics åtkomst lista i portalen](./media/stream-analytics-managed-identities-adls/stream-analytics-access-list.png)

   Mer information om Data Lake Storage Gen1 behörigheter för fil system finns i [Access Control i Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-access-control.md).

## <a name="stream-analytics-tools-for-visual-studio"></a>Stream Analytics verktyg för Visual Studio

1. I JobConfig.jspå anger du **Använd systemtilldelad identitet** till **True**.

   ![Stream Analytics hanterade identiteter för jobb konfiguration](./media/stream-analytics-managed-identities-adls/adls-mi-jobconfig-vs.png)

2. I fönstret utmatnings egenskaper i ADLS Gen1 utgående mottagare klickar du på list rutan autentiseringsläge och väljer **hanterad identitet**.

   ![ADLS utdata hanterade identiteter](./media/stream-analytics-managed-identities-adls/adls-mi-output-vs.png)

3. Fyll i resten av egenskaperna och klicka på **Spara**.

4. Klicka på **Skicka till Azure** i Frågeredigeraren.

   När du skickar jobbet gör verktygen två saker:

   * Skapar automatiskt ett huvud namn för tjänsten för Stream Analytics jobbets identitet i Azure Active Directory. Livs cykeln för den nyligen skapade identiteten hanteras av Azure. När Stream Analytics jobb tas bort, tas den tillhör ande identiteten (dvs. tjänstens huvud namn) automatiskt bort av Azure.

   * Ange **Skriv** -och **körnings** behörigheter automatiskt för ADLS gen1 sökväg för prefix som används i jobbet och tilldela den till den här mappen och alla underordnade.

5. Du kan skapa Resource Manager-mallar med följande egenskap med hjälp av [Stream Analytics CI.CD NuGet Package](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/) version 1.5.0 eller senare på en build-dator (utanför Visual Studio). Följ distributions stegen i Resource Manager-mallen i nästa avsnitt för att hämta tjänstens huvud namn och bevilja åtkomst till tjänstens huvud namn via PowerShell.

## <a name="resource-manager-template-deployment"></a>Distribution av Resource Manager-mall

1. Du kan skapa en *Microsoft. StreamAnalytics/streamingjobs-* resurs med en hanterad identitet genom att inkludera följande egenskap i resurs avsnittet i Resource Manager-mallen:

    ```json
    "Identity": {
      "Type": "SystemAssigned",
    },
    ```

   Den här egenskapen anger Azure Resource Manager för att skapa och hantera identiteten för ditt Azure Stream Analytics-jobb.

   **Exempel jobb**
   
   ```json
   {
     "Name": "AsaJobWithIdentity",
     "Type": "Microsoft.StreamAnalytics/streamingjobs",
     "Location": "West US",
     "Identity": {
       "Type": "SystemAssigned",
     },
     "properties": {
       "sku": {
         "name": "standard"
       },
       "outputs": [
         {
           "name": "string",
           "properties":{
             "datasource": {
               "type": "Microsoft.DataLake/Accounts",
               "properties": {
                 "accountName": "myDataLakeAccountName",
                 "filePathPrefix": "cluster1/logs/{date}/{time}",
                 "dateFormat": "YYYY/MM/DD",
                 "timeFormat": "HH",
                 "authenticationMode": "Msi"
             }
           }
         }
       }
     }
   }
   ```
  
   **Exempel på jobb svar**

   ```json
   {
    "Name": "mySAJob",
    "Type": "Microsoft.StreamAnalytics/streamingjobs",
    "Location": "West US",
    "Identity": {
      "Type": "SystemAssigned",
        "principalId": "GUID",
        "tenantId": "GUID",
      },
      "properties": {
        "sku": {
          "name": "standard"
        },
     }
   }
   ```

   Anteckna ägar-ID: t från jobb svaret för att ge åtkomst till den nödvändiga ADLS-resursen.

   **Klient-ID** är ID: t för den Azure Active Directory klient där tjänstens huvud namn skapades. Tjänstens huvud namn skapas i den Azure-klient som är betrodd av prenumerationen.

   **Typen** anger typen av hanterad identitet enligt beskrivningen i typer av hanterade identiteter. Endast systemets tilldelade typ stöds.

2. Ge åtkomst till tjänstens huvud namn med hjälp av PowerShell. Kör följande kommando för att ge åtkomst till tjänstens huvud namn via PowerShell:

   ```powershell
   Set-AzDataLakeStoreItemAclEntry -AccountName <accountName> -Path <Path> -AceType User -Id <PrinicpalId> -Permissions <Permissions>
   ```

   **PrincipalId** är objekt-ID: t för tjänstens huvud namn och visas på Portal skärmen när tjänstens huvud namn har skapats. Om du har skapat jobbet med en distribution av en Resource Manager-mall visas objekt-ID: t i identitets egenskapen för jobb svaret.

   **Exempel**

   ```powershell
   PS > Set-AzDataLakeStoreItemAclEntry -AccountName "adlsmsidemo" -Path / -AceType
   User -Id 14c6fd67-d9f5-4680-a394-cd7df1f9bacf -Permissions WriteExecute
   ```

   Mer information om PowerShell-kommandot ovan finns i dokumentationen för [set-AzDataLakeStoreItemAclEntry](/powershell/module/az.datalakestore/set-azdatalakestoreitemaclentry) .

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Den hanterade identitet som skapats för ett Stream Analytics jobb tas bara bort när jobbet tas bort. Det finns inget sätt att ta bort den hanterade identiteten utan att ta bort jobbet. Om du inte längre vill använda den hanterade identiteten kan du ändra autentiseringsmetoden för utdata. Den hanterade identiteten finns kvar tills jobbet tas bort och kommer att användas om du väljer att använda hanterad identitetsautentisering igen.

## <a name="limitations"></a>Begränsningar
Den här funktionen har inte stöd för följande:

1. **Åtkomst till flera klienter**: tjänstens huvud namn som skapats för ett angivet Stream Analytics jobb finns på den Azure Active Directory klient som jobbet skapades på och kan inte användas mot en resurs som finns på en annan Azure Active Directory-klient. Därför kan du bara använda MSI på ADLS gen 1-resurser som är inom samma Azure Active Directory-klient som ditt Azure Stream Analytics-jobb. 

2. **[Användarens tilldelade identitet](../active-directory/managed-identities-azure-resources/overview.md)**: stöds inte. Det innebär att användaren inte kan ange sitt eget tjänst huvud namn som ska användas av deras Stream Analytics-jobb. Tjänstens huvud namn genereras av Azure Stream Analytics.

## <a name="next-steps"></a>Nästa steg

* [Skapa ett data Lake Store-utdata med Stream Analytics](../data-lake-store/data-lake-store-stream-analytics.md)
* [Testa Stream Analytics frågor lokalt med Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Testa Live data lokalt med Azure Stream Analytics verktyg för Visual Studio](stream-analytics-live-data-local-testing.md) 
