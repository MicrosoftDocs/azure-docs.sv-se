---
title: Lägg till Key Vault stöd för ditt ASP.NET-projekt med Visual Studio-Azure Key Vault | Microsoft Docs
description: I den här självstudien får du lära dig hur du lägger till Key Vault support till ett ASP.NET-eller ASP.NET Core-webbprogram.
services: key-vault
author: ghogen
manager: jillfra
ms.service: key-vault
ms.custom: vs-azure
ms.topic: how-to
ms.date: 08/07/2019
ms.author: ghogen
ms.openlocfilehash: 52c9584ca94117db58a5427c46269d7f2612861a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588491"
---
# <a name="add-key-vault-to-your-web-application-by-using-visual-studio-connected-services"></a>Lägg till Key Vault i ditt webb program med hjälp av Visual Studio Connected Services

I den här självstudien får du lära dig hur du enkelt kan lägga till allt du behöver för att börja använda Azure Key Vault för att hantera dina hemligheter för webb projekt i Visual Studio, oavsett om du använder ASP.NET Core eller någon typ av ASP.NET-projekt. Genom att använda funktionen anslutna tjänster i Visual Studio kan du låta Visual Studio automatiskt lägga till alla NuGet-paket och konfigurations inställningar som du behöver för att ansluta till Key Vault i Azure.

Mer information om de ändringar som anslutna tjänster gör i projektet för att aktivera Key Vault finns i [Key Vault Connected service – vad hände med mitt ASP.NET 4.7.1-projekt](#how-your-aspnet-framework-project-is-modified) eller [Key Vault Connected service – vad hände med mitt ASP.net Core-projekt](#how-your-aspnet-core-project-is-modified).

## <a name="prerequisites"></a>Förutsättningar

- **En Azure-prenumeration**. Om du inte har någon prenumeration kan du registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/pricing/free-trial/).
- **Visual Studio 2019 version 16,3** eller senare [Ladda ned den nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).


## <a name="add-key-vault-support-to-your-project"></a>Lägg till Key Vault stöd för ditt projekt

Innan du börjar ska du kontrol lera att du är inloggad i Visual Studio. Logga in med samma konto som du använder för din Azure-prenumeration. Öppna sedan en ASP.NET 4.7.1 eller senare eller ASP.NET Core 2,0-webbprojektet och utför följande steg:

1. I **Solution Explorer**högerklickar du på det projekt som du vill lägga till Key Vault support till och väljer **Lägg till**  >  **ansluten tjänst**  >  **Lägg**till.
   Sidan Ansluten tjänst visas med tjänster som du kan lägga till i projektet.
1. I menyn med tillgängliga tjänster väljer du **Azure Key Vault** och klickar på **Nästa**.

   ![Välj "Azure Key Vault"](../media/vs-key-vault-add-connected-service/key-vault-connected-service.png)

1. Välj den prenumeration som du vill använda och välj sedan en befintlig Key Vault och klicka på **Slutför**. 

   ![Välj din prenumeration](../media/vs-key-vault-add-connected-service/key-vault-connected-service-select-vault.png)

Nu är anslutningen till Key Vault upprättad och du kan komma åt dina hemligheter i kod. Nästa steg varierar beroende på om du använder ASP.NET 4.7.1 eller ASP.NET Core.

## <a name="access-your-secrets-in-code-aspnet-core"></a>Få åtkomst till dina hemligheter i kod (ASP.NET Core)

1. Öppna en av växlingsfilerna, till exempel *index.cshtml.cs* , och skriv följande kod:
   1. Inkludera en referens till `Microsoft.Extensions.Configuration` med hjälp av direktivet:

       ```csharp
       using Microsoft.Extensions.Configuration;
       ```

   1. Lägg till konfigurations variabeln.

      ```csharp
      private static IConfiguration _configuration;
      ```

   1. Lägg till den här konstruktorn eller Ersätt den befintliga konstruktorn med följande:

       ```csharp
       public IndexModel(IConfiguration configuration)
       {
           _configuration = configuration;
       }
       ```

   1. Uppdatera `OnGet`-metoden. Uppdatera plats hållarens värde som visas här med det hemliga namn som du skapade i kommandona ovan.

       ```csharp
       public void OnGet()
       {
           ViewData["Message"] = "My key val = " + _configuration["<YourSecretNameStoredInKeyVault>"];
       }
       ```

   1. Du bekräftar värdet vid körning genom att lägga till kod som ska visas i `ViewData["Message"]` *. cshtml* -filen för att Visa hemligheten i ett meddelande.

      ```cshtml
          <p>@ViewData["Message"]</p>
      ```

Du kan köra appen lokalt för att kontrol lera att hemligheten har hämtats från Key Vault.

## <a name="access-your-secrets-aspnet"></a>Få åtkomst till dina hemligheter (ASP.NET)
Du kan konfigurera konfigurationen så att web.config-filen har ett dummy-värde i `appSettings` elementet som ersätts av det sanna värdet vid körning. Du kan sedan komma åt det via `ConfigurationManager.AppSettings` data strukturen.

1. I Solution Explorer högerklickar du på projektet och väljer Hantera NuGet-paket. Leta upp och installera [Microsoft.Configuration.ConfigurationBuilders. Azure](https://www.nuget.org/packages/Microsoft.Configuration.ConfigurationBuilders.Azure/) på fliken Bläddra.
 
1. Öppna web.config-filen och skriv följande kod:
    1. Lägg till `configSections` och `configBuilders` :
        ```xml
         <configSections>
            <section
                name="configBuilders"
                type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a"
                restartOnExternalChanges="false"
                requirePermission="false" />
         </configSections>
         <configBuilders>
            <builders>
            <add
                    name="AzureKeyVault"
                    vaultName="vaultname"
                    type="Microsoft.Configuration.ConfigurationBuilders.AzureKeyVaultConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Azure, Version=1.0.0.0, Culture=neutral"
                    vaultUri="https://vaultname.vault.azure.net" />
            </builders>
         </configBuilders>
        ```
    1. Leta upp taggen appSettings, Lägg till ett attribut `configBuilders="AzureKeyVault"` och Lägg till en rad:
        ```xml
         <add key="<secretNameInYourKeyVault>" value="dummy"/>
        ```

1. Redigera `About` metoden i *HomeController.cs*för att visa värdet för bekräftelse.

   ```csharp
   public ActionResult About()
   {
       ViewBag.Message = "Key vault value = " + ConfigurationManager.AppSettings["<secretNameInYourKeyVault>"];
   }
   ```
1. Kör appen lokalt under fel söknings programmet, växla till fliken **om** och kontrol lera att värdet från Key Vault visas.

## <a name="troubleshooting"></a>Felsökning

Om din Key Vault körs på en annan Microsoft-konto än den som du är inloggad på Visual Studio (till exempel att Key Vault körs på ditt arbets konto, men Visual Studio använder ditt privata konto) får du ett fel i din Program.cs-fil som Visual Studio inte får åtkomst till Key Vault. Så här åtgärdar du problemet:

1. Gå till [Azure Portal](https://portal.azure.com) och öppna din Key Vault.

1. Välj **åtkomst principer**, **Lägg till åtkomst princip**och välj det konto som du är inloggad på som huvud konto.

1. I Visual Studio väljer du **fil**  >  **konto inställningar**.
Välj **Lägg till ett konto** från avsnittet **alla konton** . Logga in med det konto som du har valt som huvud konto för din åtkomst princip.

1. Välj **verktyg**  >  **alternativ**och leta efter **Azure-tjänsteautentisering**. Välj sedan det konto som du precis har lagt till i Visual Studio.

När du nu felsöker ditt program ansluter Visual Studio till det konto som Key Vault finns på.

## <a name="how-your-aspnet-core-project-is-modified"></a>Så här ändras ditt ASP.NET Core-projekt

Det här avsnittet identifierar de exakta ändringar som gjorts i ett ASP.NET-projekt när du lägger till den Key Vault anslutna tjänsten med Visual Studio.

### <a name="added-references-for-aspnet-core"></a>Tillagda referenser för ASP.NET Core

Påverkar projekt filens .NET-referenser och NuGet-paket referenser.

| Typ | Referens |
| --- | --- |
| NuGet | Microsoft. AspNetCore. AzureKeyVault. HostingStartup |

### <a name="added-files-for-aspnet-core"></a>Filer har lagts till för ASP.NET Core

- `ConnectedService.json` tillagt, som innehåller information om den anslutna tjänst leverantören, versionen och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-core"></a>Projekt fil ändringar för ASP.NET Core

- De anslutna tjänsterna ItemGroup och File har lagts till `ConnectedServices.json` .

### <a name="launchsettingsjson-changes-for-aspnet-core"></a>launchsettings.jsvid ändringar av ASP.NET Core

- Följande miljövariabel poster har lagts till i både den IIS Express profilen och den profil som matchar ditt webb projekt namn:

    ```json
      "environmentVariables": {
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONENABLED": "true",
        "ASPNETCORE_HOSTINGSTARTUP__KEYVAULT__CONFIGURATIONVAULT": "<your keyvault URL>"
      }
    ```

### <a name="changes-on-azure-for-aspnet-core"></a>Ändringar på Azure för ASP.NET Core

- Skapade en resurs grupp (eller använt en befintlig).
- Skapade en Key Vault i den angivna resurs gruppen.

## <a name="how-your-aspnet-framework-project-is-modified"></a>Så här ändras ditt ASP.NET Framework-projekt

Det här avsnittet identifierar de exakta ändringar som gjorts i ett ASP.NET-projekt när du lägger till den Key Vault anslutna tjänsten med Visual Studio.

### <a name="added-references-for-aspnet-framework"></a>Tillagda referenser för ASP.NET Framework

Påverkar projekt filens .NET-referenser och `packages.config` (NuGet-referenser).

| Typ | Referens |
| --- | --- |
| Nettotid NuGet | Azure. Identity |
| Nettotid NuGet | Azure. Security. nyckel valv. nycklar |
| Nettotid NuGet | Azure. Security. nyckel valv. hemligheter |

### <a name="added-files-for-aspnet-framework"></a>Filer har lagts till för ASP.NET Framework

- `ConnectedService.json` tillagt, som registrerar viss information om den anslutna tjänst leverantören, versionen och en länk till dokumentationen.

### <a name="project-file-changes-for-aspnet-framework"></a>Projekt fil ändringar för ASP.NET Framework

- De anslutna tjänsterna ItemGroup och ConnectedServices.jspå filen har lagts till.
- Referenser till .NET-sammansättningar som beskrivs i avsnittet [tillagda referenser](#added-references-for-aspnet-framework) .

## <a name="next-steps"></a>Nästa steg

Om du har följt den här självstudien konfigureras Key Vault behörigheter för att köras med din egen Azure-prenumeration, men det kanske inte är önskvärt för ett produktions scenario. Du kan skapa en hanterad identitet för att hantera Key Vault åtkomst för din app. Se [tillhandahålla Key Vault autentisering med en hanterad identitet](/azure/key-vault/managed-identity).

Läs mer om hur du Key Vault utveckling genom att läsa [Key Vault Developer ' s guide](developers-guide.md).
