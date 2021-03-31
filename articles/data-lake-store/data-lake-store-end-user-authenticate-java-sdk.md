---
title: Autentisering med slutanvändare – Java med Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår autentisering för slutanvändare med Azure Data Lake Storage Gen1 att använda Azure Active Directory med Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: ece996f7edb8d37cea47756c5b7e635e02409b35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87318869"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Autentisering med slutanvändare med Azure Data Lake Storage Gen1 med Java
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

I den här artikeln får du lära dig hur du använder Java SDK för att utföra autentisering med slutanvändare med Azure Data Lake Storage Gen1. För tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Java SDK, se [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "internt"-program**. Du måste ha slutfört stegen i [slut användar autentisering med data Lake Storage gen1 med hjälp av Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att skapa utan att använda ett build-system som Maven eller Gradle, gör de här systemen det mycket enklare att hantera beroenden.

* (Valfritt) Och IDE-liknande [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) eller [Eclipse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="end-user-authentication"></a>Slutanvändarautentisering
1. Skapa ett Maven-projekt med [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) från kommandoraden eller med hjälp av en IDE. Anvisningar för hur du skapar ett Java-projekt med IntelliJ finns [här](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Anvisningar för hur du skapar ett Java-projekt med Eclipse finns [här](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Lägg till följande beroenden till din Maven **pom.xml**-fil. Lägg till följande kodfragment före **\</project>** taggen:
   
    ```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-data-lake-store-sdk</artifactId>
        <version>2.2.3</version>
      </dependency>
      <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-nop</artifactId>
        <version>1.7.21</version>
      </dependency>
    </dependencies>
    ```
   
    Det första beroendet är att använda Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) från maven-lagringsplatsen. Det andra beroendet är för att ange vilket loggningsramverk (`slf4j-nop`) som ska användas för programmet. I Data Lake Storage Gen1 SDK används [SLF4J](https://www.slf4j.org/) Logging fasad, vilket gör att du kan välja mellan ett antal populära loggnings ramverk, t. ex. log4j, Java-loggning, logback osv. eller ingen loggning. I det här exemplet inaktiverar vi loggning, därför använder vi **slf4j-nop** bindning. Om du vill använda andra alternativ för loggning i din app, se [här](https://www.slf4j.org/manual.html#projectDep).

3. Lägg till följande importuttryck i programmet.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;
    ```

4. Använd följande kodfragment i ditt Java-program för att hämta token för det Active Directory inbyggda programmet som du skapade tidigare med hjälp av `DeviceCodeTokenProvider` . Ersätt **Fill – här** med de faktiska värdena för det Azure Active Directory inbyggda programmet.

    ```java
    private static String nativeAppId = "FILL-IN-HERE";
            
    AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   
    ```

Data Lake Storage Gen1 SDK tillhandahåller praktiska metoder som låter dig hantera de säkerhetstoken som behövs för att kommunicera med Data Lake Storage Gen1-kontot. Dock tvingar inte SDK:n dig att använda enbart de här metoderna. Du kan använda valfria andra metoder för att hämta token, som att använda [Azure Active Directory SDK:n](https://github.com/AzureAD/azure-activedirectory-library-for-java) eller din egna anpassade kod.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder autentisering med slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med Java SDK. Nu kan du titta på följande artiklar som talar om hur du använder Java SDK för att arbeta med Azure Data Lake Storage Gen1.

* [Data åtgärder på Data Lake Storage Gen1 med Java SDK](data-lake-store-get-started-java-sdk.md)


