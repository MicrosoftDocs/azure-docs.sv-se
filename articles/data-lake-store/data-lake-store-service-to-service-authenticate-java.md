---
title: Tjänst-till-tjänst-autentisering – Data Lake Storage Gen1 – Java SDK
description: Lär dig hur du uppnår tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 att använda Azure Active Directory med Java
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.custom: devx-track-java
ms.author: twooley
ms.openlocfilehash: 0e320557a7372af6a41038d9b3196db23d2496c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000393"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Java

> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

I den här artikeln får du lära dig hur du använder Java SDK för att utföra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. Slut användar autentisering med Data Lake Storage Gen1 med Java SDK stöds inte.

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att skapa utan att använda ett build-system, t. ex. maven eller Gradle, gör dessa system det mycket enklare att hantera beroenden.

* Valfritt En IDE som [IntelliJ-idé](https://www.jetbrains.com/idea/download/) eller [Sol förmörkelse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

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

    Det första beroendet är att använda Data Lake Storage Gen1 SDK ( `azure-data-lake-store-sdk` ) från maven-lagringsplatsen. Det andra beroendet är för att ange vilket loggningsramverk (`slf4j-nop`) som ska användas för programmet. I Data Lake Storage Gen1 SDK används [slf4j](https://www.slf4j.org/) Logging fasad, vilket gör att du kan välja mellan ett antal populära loggnings ramverk, t. ex. log4j, Java-loggning, logback osv. eller ingen loggning. I det här exemplet inaktiverar vi loggning, därför använder vi **slf4j-nop** bindning. Om du vill använda andra alternativ för loggning i din app, se [här](https://www.slf4j.org/manual.html#projectDep).

3. Lägg till följande importuttryck i programmet.

    ```java
    import com.microsoft.azure.datalake.store.ADLException;
    import com.microsoft.azure.datalake.store.ADLStoreClient;
    import com.microsoft.azure.datalake.store.DirectoryEntry;
    import com.microsoft.azure.datalake.store.IfExists;
    import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
    import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;
    ```

4. Använd följande kodfragment i Java-programmet för att hämta token för Active Directory-webbprogram som du skapade tidigare med hjälp av en av underklasserna i `AccessTokenProvider` (följande exempel använder `ClientCredsTokenProvider` ). Tokenleverantören cachelagrar autentiseringsuppgifter som används för att hämta token i minnet och förnyar automatiskt token när de håller på att gå ut. Det är möjligt att skapa egna underklasser av `AccessTokenProvider` så att token erhålls av kund koden. Nu ska vi bara använda den som angavs i SDK.

    Ersätt **FILL-IN-HERE** med de faktiska värdena för Azure Active Directory-webbappen.

    ```java
    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   
    ```

Data Lake Storage Gen1 SDK tillhandahåller praktiska metoder som låter dig hantera de säkerhetstoken som behövs för att kommunicera med Data Lake Storage Gen1-kontot. Dock tvingar inte SDK:n dig att använda enbart de här metoderna. Du kan använda valfria andra metoder för att hämta token, som att använda [Azure Active Directory SDK:n](https://github.com/AzureAD/azure-activedirectory-library-for-java) eller din egna anpassade kod.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder autentisering med slutanvändare för att autentisera med Data Lake Storage Gen1 med Java SDK. Nu kan du titta på följande artiklar som talar om hur du använder Java SDK för att arbeta med Data Lake Storage Gen1.

* [Data åtgärder på Data Lake Storage Gen1 med Java SDK](data-lake-store-get-started-java-sdk.md)
