---
title: Skydda Azure Remote Rendering och modellagring
description: Härda ett Remote Rendering för att skydda innehåll
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 6e595f7ff313ff85a12209e8c124b9aa376b20b6
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739754"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Självstudie: Skydda Azure Remote Rendering och modelllagring

I den här guiden får du lära dig att:

> [!div class="checklist"]
>
> * Säker Azure Blob Storage som innehåller Azure Remote Rendering modeller
> * Autentisera med Azure AD för att få åtkomst Azure Remote Rendering instans
> * Använda Azure-autentiseringsuppgifter för Azure Remote Rendering autentisering

## <a name="prerequisites"></a>Förutsättningar

* Den här självstudien bygger [på Självstudie: Förfina material, belysning och effekter](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Varför krävs ytterligare säkerhet

Programmets aktuella tillstånd och dess åtkomst till dina Azure-resurser ser ut så här:

![Inledande säkerhet](./media/security-one.png)

Både "AccountID + AccountKey" och "URL + SAS-token" lagrar båda i stort sett ett användarnamn och lösenord tillsammans. Om till exempel "AccountID + AccountKey" exponerades skulle det vara enkelt för en angripare att använda dina ARR-resurser utan din tillåtelse på din kostnad.

## <a name="securing-your-content-in-azure-blob-storage"></a>Skydda ditt innehåll i Azure Blob Storage

Azure Remote Rendering på ett säkert sätt komma åt innehållet i Azure Blob Storage med rätt konfiguration. Se [Så här länkar du lagringskonton för att](../../../how-tos/create-an-account.md#link-storage-accounts) konfigurera din Azure Remote Rendering instans med dina Blob Storage-konton.

När du använder en länkad bloblagring använder du något olika metoder för att läsa in modeller:

```cs
var loadModelParams = new LoadModelFromSasOptions(modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelFromSasAsync(loadModelParams);
```

Raderna ovan använder versionen `FromSas` av parameter- och sessionsåtgärden. De måste konverteras till icke-SAS-versioner:

```cs
var loadModelParams = new LoadModelOptions(storageAccountPath, blobContainerName, modelPath, modelEntity);
var task = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams);
```

Nu ska vi ändra **RemoteRenderingCoordinator för att** läsa in en anpassad modell från ett länkat bloblagringskonto.

1. Om du inte redan har gjort det slutför du [how-to:link-lagringskonton](../../../how-tos/create-an-account.md#link-storage-accounts) för att ge ARR-instansen behörighet att komma åt din Blob Storage instans.
1. Lägg till följande ändrade **LoadModel-metod** **i RemoteRenderingCoordinator precis** under den aktuella **LoadModel-metoden:**

    ```cs
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, Action<float> progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Connection.CreateEntity();

        //Get the game object representation of this entity
        var modelGameObject = modelEntity.GetOrCreateGameObject(UnityCreationMode.DoNotCreateUnityComponents);

        //Ensure the entity will sync its transform with the server
        var sync = modelGameObject.GetComponent<RemoteEntitySyncObject>();
        sync.SyncEveryFrame = true;

        //Parent the new object under the defined parent
        if (parent != null)
        {
            modelGameObject.transform.SetParent(parent, false);
            modelGameObject.name = parent.name + "_Entity";
        }

    #if UNITY_WSA
        //Anchor the model in the world, prefer anchoring parent if there is one
        if (parent != null)
        {
            parent.gameObject.AddComponent<WorldAnchor>();
        }
        else
        {
            modelGameObject.AddComponent<WorldAnchor>();
        }
    #endif

        //Load a model that will be parented to the entity
        var loadModelParams = new LoadModelOptions($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Connection.LoadModelAsync(loadModelParams, progress);
        var result = await loadModelAsync;
        return modelEntity;
    }
    ```

    I de flesta fall är den här koden identisk med den ursprungliga metoden, men vi har ersatt SAS-versionen av metod-anropen med `LoadModel` icke-SAS-versioner.

    Ytterligare indata `storageAccountName` och har också lagts till i `blobContainerName` argumenten. Vi anropar den här nya **LoadModel-metoden** från en annan metod som liknar den första **LoadTestModel-metoden** som vi skapade i den första självstudien.

1. Lägg till följande metod **i RemoteRenderingCoordinator precis** efter **LoadTestModel**

    ```cs
    private bool loadingLinkedCustomModel = false;

    [SerializeField]
    private string storageAccountName;
    public string StorageAccountName {
        get => storageAccountName.Trim();
        set => storageAccountName = value;
    }

    [SerializeField]
    private string blobContainerName;
    public string BlobContainerName {
        get => blobContainerName.Trim();
        set => blobContainerName = value;
    }

    [SerializeField]
    private string modelPath;
    public string ModelPath {
        get => modelPath.Trim();
        set => modelPath = value;
    }

    [ContextMenu("Load Linked Custom Model")]
    public async void LoadLinkedCustomModel()
    {
        if (CurrentCoordinatorState != RemoteRenderingState.RuntimeConnected)
        {
            Debug.LogError("Please wait for the runtime to connect before loading the test model. Try again later.");
            return;
        }
        if (loadingLinkedCustomModel)
        {
            Debug.Log("Linked Test model already loading or loaded!");
            return;
        }
        loadingLinkedCustomModel = true;

        // Create a parent object to use for positioning
        GameObject testParent = new GameObject("LinkedCustomModel");
        testParent.transform.position = new Vector3(0f, 0f, 3f);

        await LoadModel(StorageAccountName, BlobContainerName, ModelPath, testParent.transform, (progressValue) => Debug.Log($"Loading Test Model progress: {Math.Round(progressValue * 100, 2)}%"));
    }
    ```

    Den här koden lägger till ytterligare tre strängvariabler i **komponenten RemoteRenderingCoordinator.**
    ![Skärmbild som visar lagringskontots namn, blobcontainerns namn och modellsökvägen för komponenten RemoteRenderingCoordinator.](./media/storage-account-linked-model.png)

1. Lägg till dina värden i **komponenten RemoteRenderingCoordinator.** När du har [följt snabbstarten för modellkonvertering](../../../quickstarts/convert-model.md)bör dina värden vara:

    * **Namn på lagringskonto:** Namnet på ditt lagringskonto, det globalt unika namn som du väljer för ditt lagringskonto. I snabbstarten var *det arrtutorialstorage*. Ditt värde kommer att vara annorlunda.
    * **Blobcontainerns** namn: arroutput, Blob Storage containern
    * **Modellsökväg:** Kombinationen av "outputFolderPath" och "outputAssetFileName" som definieras *iarrconfig.json-filen.* I snabbstarten var detta "outputFolderPath":"converted/robot", "outputAssetFileName": "robot.arrAsset". Vilket skulle resultera i modellsökvägens värde "converted/robot/robot.arrAsset", så kommer ditt värde att vara annorlunda.

    >[!TIP]
    > Om du kör [ **Conversion.ps1-skriptet**](../../../quickstarts/convert-model.md#run-the-conversion) utan argumentet "-UseContainerSas" matar skriptet ut alla ovanstående värden för din i stället för SAS-token. ![Länkad modell](./media/converted-output.png)
1. För närvarande tar du bort eller inaktiverar GameObject **TestModel** för att göra plats för din anpassade modell att läsa in.
1. Spela upp scenen och anslut till en fjärrsession.
1. Högerklicka på din **RemoteRenderingCoordinator och välj** Läs **in länkad anpassad modell.**
    ![Läsa in länkad modell](./media/load-linked-model.png)

De här stegen har ökat säkerheten för programmet genom att ta bort SAS-token från det lokala programmet.

Nu ser det aktuella tillståndet för programmet och dess åtkomst till dina Azure-resurser ut så här:

![Bättre säkerhet](./media/security-two.png)

Vi har ytterligare ett "lösenord", AccountKey, som ska tas bort från det lokala programmet. Detta kan göras med hjälp Azure Active Directory (AAD)-autentisering.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD)-autentisering

Med AAD-autentisering kan du avgöra vilka individer eller grupper som använder ARR på ett mer kontrollerat sätt. ARR har inbyggt stöd för att acceptera [åtkomsttoken i](../../../../active-directory/develop/access-tokens.md) stället för att använda en kontonyckel. Du kan tänka på åtkomsttoken som en tidsbegränsad, användarspecifik nyckel som bara låser upp vissa delar av den specifika resurs som den begärdes för.

Skriptet **RemoteRenderingCoordinator** har en delegat med namnet **ARRCredentialGetter**, som innehåller en metod som returnerar ett **SessionConfiguration-objekt** som används för att konfigurera fjärrsessionshanteringen. Vi kan tilldela en annan metod **till ARRCredentialGetter,** så att vi kan använda ett Azure-inloggningsflöde, vilket genererar ett **SessionConfiguration-objekt** som innehåller en Azure-åtkomsttoken. Denna åtkomsttoken är specifik för den användare som loggar in.

1. Följ [anvisningarna i How To: Configure authentication - Authentication for deployed applications](../../../how-tos/authentication.md#authentication-for-deployed-applications)(Så här gör du: Konfigurera autentisering – autentisering för distribuerade program). Mer specifikt följer du anvisningarna i Azure Spatial Anchors-användarautentisering för [Azure AD.](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication) Det innebär att registrera ett nytt Azure Active Directory och konfigurera åtkomst till arr-instansen.
1. När du har konfigurerat det nya AAD-programmet kontrollerar du att ditt AAD-program ser ut som följande bilder:

    **AAD-program - > autentisering** ![ Appautentisering](./media/app-authentication-public.png)

    **AAD-program - > API-behörigheter** ![ App-API:er](./media/request-api-permissions-step-five.png)

1. När du har Remote Rendering ditt konto kontrollerar du att konfigurationen ser ut som på följande bild:

    **AAR -> AccessControl (IAM)** ![ ARR-roll](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > En *ägarroll* räcker inte för att hantera sessioner via klientprogrammet. För varje användare som du vill ge möjlighet att hantera sessioner måste du ange rollen **Remote Rendering Client**. För varje användare som du vill hantera sessioner och konvertera modeller måste du ange rollen **som Remote Rendering administratör.**

Nu när Azure-sidan är på plats behöver vi ändra hur koden ansluter till AAR-tjänsten. Det gör vi genom att implementera en instans av **BaseARRAuthentication**, som returnerar ett nytt **SessionConfiguration-objekt.** I det här fallet konfigureras kontoinformationen med Azure-åtkomsttoken.

1. Skapa ett nytt skript med **namnet AADAuthentication** och ersätt koden med följande:

    ```cs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License. See LICENSE in the project root for license information.
    
    using Microsoft.Azure.RemoteRendering;
    using Microsoft.Identity.Client;
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using UnityEngine;
    
    public class AADAuthentication : BaseARRAuthentication
    {
        [SerializeField]
        private string activeDirectoryApplicationClientID;
        public string ActiveDirectoryApplicationClientID
        {
            get => activeDirectoryApplicationClientID.Trim();
            set => activeDirectoryApplicationClientID = value;
        }
    
        [SerializeField]
        private string azureTenantID;
        public string AzureTenantID
        {
            get => azureTenantID.Trim();
            set => azureTenantID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingDomain;
        public string AzureRemoteRenderingDomain
        {
            get => azureRemoteRenderingDomain.Trim();
            set => azureRemoteRenderingDomain = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountDomain;
        public string AzureRemoteRenderingAccountDomain
        {
            get => azureRemoteRenderingAccountDomain.Trim();
            set => azureRemoteRenderingAccountDomain = value;
        }    
    
        public override event Action<string> AuthenticationInstructions;
    
        string authority => "https://login.microsoftonline.com/" + AzureTenantID;
    
        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";
    
        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountDomain + "/mixedreality.signin" };
    
        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }
    
        public async override Task<SessionConfiguration> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);
    
                var AD_Token = result.AccessToken;
    
                return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
            }
            else
            {
                Debug.LogError("Error logging in");
            }
            return default;
        }
    
        private Task DeviceCodeReturned(DeviceCodeResult deviceCodeDetails)
        {
            //Since everything in this task can happen on a different thread, invoke responses on the main Unity thread
            ExecuteOnUnityThread.Enqueue(() =>
            {
                // Display instructions to the user for how to authenticate in the browser
                Debug.Log(deviceCodeDetails.Message);
                AuthenticationInstructions?.Invoke(deviceCodeDetails.Message);
            });
    
            return Task.FromResult(0);
        }
    
        public override async Task<AuthenticationResult> TryLogin()
        {
            var clientApplication = PublicClientApplicationBuilder.Create(ActiveDirectoryApplicationClientID).WithAuthority(authority).WithRedirectUri(redirect_uri).Build();
            AuthenticationResult result = null;
            try
            {
                var accounts = await clientApplication.GetAccountsAsync();
    
                if (accounts.Any())
                {
                    result = await clientApplication.AcquireTokenSilent(scopes, accounts.First()).ExecuteAsync();
    
                    return result;
                }
                else
                {
                    try
                    {
                        result = await clientApplication.AcquireTokenWithDeviceCode(scopes, DeviceCodeReturned).ExecuteAsync(CancellationToken.None);
                        return result;
                    }
                    catch (MsalUiRequiredException ex)
                    {
                        Debug.LogError("MsalUiRequiredException");
                        Debug.LogException(ex);
                    }
                    catch (MsalServiceException ex)
                    {
                        Debug.LogError("MsalServiceException");
                        Debug.LogException(ex);
                    }
                    catch (MsalClientException ex)
                    {
                        Debug.LogError("MsalClientException");
                        Debug.LogException(ex);
                        // Mitigation: Use interactive authentication
                    }
                    catch (Exception ex)
                    {
                        Debug.LogError("Exception");
                        Debug.LogException(ex);
                    }
                }
            }
            catch (Exception ex)
            {
                Debug.LogError("GetAccountsAsync");
                Debug.LogException(ex);
            }
    
            return null;
        }
    }
    ```

>[!NOTE]
> Den här koden är inte alls komplett och är inte redo för ett kommersiellt program. Till exempel vill du förmodligen lägga till möjligheten att logga ut också. Detta kan göras med hjälp av `Task RemoveAsync(IAccount account)` den metod som tillhandahålls av klientprogrammet. Den här koden är endast avsedd för självstudier. Implementeringen är specifik för ditt program.

Koden försöker först hämta token tyst med hjälp av **AquireTokenSilent**. Detta lyckas om användaren tidigare har autentiserat det här programmet. Om det inte lyckas går du vidare till en mer användar-inblandat strategi.

För den här koden använder vi [enhetskodflödet för att hämta](../../../../active-directory/develop/v2-oauth2-device-code.md) en åtkomsttoken. Med det här flödet kan användaren logga in på sitt Azure-konto på en dator eller mobil enhet och få den resulterande token skickad tillbaka till HoloLens-programmet.

Den viktigaste delen av den här klassen ur ett ARR-perspektiv är den här raden:

```cs
return await Task.FromResult(new SessionConfiguration(AzureRemoteRenderingAccountDomain, AzureRemoteRenderingDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Här skapar vi ett nytt **SessionConfiguration-objekt med** fjärrrenderingsdomänen, konto-ID:t, kontodomänen och åtkomsttoken. Denna token används sedan av ARR-tjänsten för att fråga, skapa och ansluta till fjärrrenderingssessioner så länge användaren har behörighet baserat på de rollbaserade behörigheter som konfigurerades tidigare.

Med den här ändringen ser det aktuella tillståndet för programmet och dess åtkomst till dina Azure-resurser ut så här:

![Ännu bättre säkerhet](./media/security-three.png)

Eftersom användarautentiseringsuppgifterna inte lagras på enheten (eller i det här fallet även anges på enheten), är deras exponeringsrisk mycket låg. Nu använder enheten en användarspecifik, tidsbegränsad åtkomsttoken för att få åtkomst till ARR, som använder åtkomstkontroll (IAM) för att komma åt Blob Storage. Dessa två steg har helt tagit bort "lösenorden" från källkoden och ökat säkerheten avsevärt. Detta är dock inte den mest tillgängliga säkerheten. Om du flyttar modell- och sessionshanteringen till en webbtjänst förbättras säkerheten ytterligare. Ytterligare säkerhetsöverväganden beskrivs i [kapitlet Kommersiell](../commercial-ready/commercial-ready.md) beredskap.

### <a name="testing-aad-auth"></a>Testa AAD-autentisering

När AAD Auth är aktivt i Unity-redigeraren måste du autentisera varje gång du startar programmet. På enheten sker autentiseringssteget första gången och krävs bara igen när token upphör att gälla eller har ogiltigförklarats.

1. Lägg till **komponenten AADAuthentication** i **RemoteRenderingCoordinator** GameObject.

    ![AAD-autentiseringskomponent](./media/azure-active-directory-auth-component.png)

1. Fyll i dina värden för Klient-ID och Klient-ID. Dessa värden finns på översiktssidan för din appregistrering:

    * **Klient-ID för Active Directory-program** *är det program-ID (klient) som* finns i din AAD-appregistrering (se bilden nedan).
    * **Azure-klientorganisations-ID** *är katalog-ID:t (klient)* som finns i din AAD-appregistrering ( se bilden nedan).
    * **Azure Remote Rendering Domän** är samma domän som du har använt i **RemoteRenderingCoordinators** Remote Rendering Domän.
    * **Azure Remote Rendering konto-ID** är samma **konto-ID** som du har använt **för RemoteRenderingCoordinator**.
    * **Azure Remote Rendering-kontodomän** är samma **kontodomän** som du har använt i **RemoteRenderingCoordinator**.

    ![Skärmbild som visar program-ID (klient) och katalog-ID (klient).](./media/app-overview-data.png)

1. Tryck på Spela upp i Unity-redigeraren och godkänn att du kör en session.
    Eftersom **AADAuthentication-komponenten** har en visningskontrollant, är den automatiskt ansluten för att visa en uppmaning efter den modala panelen för sessionsauktorisering.
1. Följ anvisningarna på panelen till höger om **AppMenu**.
    Du bör se något som liknar detta: ![ Bild som visar instruktionspanelen som visas till höger om AppMenu.](./media/device-flow-instructions.png)
    När du har angett den angivna koden på den sekundära enheten (eller webbläsaren på samma enhet) och loggat in med dina autentiseringsuppgifter returneras en åtkomsttoken till det begärande programmet, i det här fallet Unity-redigeraren.
1. Därefter bör allt i programmet fortsätta normalt. Kontrollera om det finns fel i Unity-konsolen om du inte går igenom stegen som förväntat.

## <a name="build-to-device"></a>Skapa till enhet

Om du skapar ett program med MSAL på enheten måste du inkludera en fil i projektets **mapp Tillgångar.** Detta hjälper kompilatorn att skapa programmet korrekt med hjälp avMicrosoft.Identity.Client.dll *som* ingår i **Självstudietillgångar**.

1. Lägg till en ny fil i **Tillgångar med** **namnetlink.xml**
1. Lägg till följande för i filen:

    ```xml
    <linker>
        <assembly fullname="Microsoft.Identity.Client" preserve="all"/>
        <assembly fullname="System.Runtime.Serialization" preserve="all"/>
        <assembly fullname="System.Core">
            <type fullname="System.Linq.Expressions.Interpreter.LightLambda" preserve="all" />
        </assembly>
    </linker>
    ```

1. Spara ändringarna

Följ stegen i [Snabbstart: Distribuera Unity-exempel till HoloLens – Skapa exempelprojektet](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)för att skapa till HoloLens.

## <a name="next-steps"></a>Nästa steg

Resten av den här självstudien innehåller konceptuella ämnen för att skapa ett produktionsklart program som använder Azure Remote Rendering.

> [!div class="nextstepaction"]
> [Nästa: Kommersiell beredskap](../commercial-ready/commercial-ready.md)