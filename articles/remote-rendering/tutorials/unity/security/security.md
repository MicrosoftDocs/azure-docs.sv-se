---
title: Skydda Azure Remote Rendering och modellagring
description: Härdning av ett program för fjärråter givning för att skydda innehåll
author: florianborn71
ms.author: flborn
ms.date: 06/15/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: d8a7bb620b7fcc9c878986d3575e22bb6f0f77bc
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724131"
---
# <a name="tutorial-securing-azure-remote-rendering-and-model-storage"></a>Självstudie: skydda Azure-fjärråter givning och modell lagring

I de här självstudierna får du lära dig att

> [!div class="checklist"]
>
> * Skydda Azure-Blob Storage som innehåller Azures modeller för fjärrrendering
> * Autentisera med Azure AD för att få åtkomst till din Azure Remote rendering-instans
> * Använd Azure-autentiseringsuppgifter för Azure-fjärrrendering-autentisering

## <a name="prerequisites"></a>Förutsättningar

* Den här självstudien bygger på [Självstudier: förfina material, belysning och effekter](..\materials-lighting-effects\materials-lighting-effects.md).

## <a name="why-additional-security-is-needed"></a>Varför ytterligare säkerhet krävs

Programmets aktuella tillstånd och dess åtkomst till dina Azure-resurser ser ut så här:

![Inledande säkerhet](./media/security-one.png)

Både "AccountID + AccountKey" och "URL + SAS-token" är både i princip lagring av användar namn och lösen ord tillsammans. Om t. ex. "AccountID + AccountKey" exponerades, skulle det vara enkelt för en angripare att använda dina ARR-resurser utan din tillåtelse till din kostnad.

## <a name="securing-your-content-in-azure-blob-storage"></a>Skydda ditt innehåll i Azure Blob Storage

Azure fjärrrendering kan komma åt innehållet i Azure-Blob Storage på ett säkert sätt med rätt konfiguration. Se [anvisningar: länka lagrings konton](../../../how-tos/create-an-account.md#link-storage-accounts) för att konfigurera din Azure Remote rendering-instans med Blob Storage-konton.

När du använder en länkad blob-lagring, använder du något annorlunda metoder för att läsa in modeller:

```csharp
var loadModelParams = new LoadModelFromSASParams(modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelFromSASAsync(loadModelParams);
```

Ovanstående rader använder `FromSAS` versionen av params och session åtgärden. De måste konverteras till icke-SAS-versioner:

```csharp
var loadModelParams = new LoadModelParams(storageAccountPath, blobContainerName, modelPath, modelEntity);
var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
```

Nu ska vi ändra **RemoteRenderingCoordinator** för att läsa in en anpassad modell från ett länkat Blob Storage-konto.

1. Om du inte redan har gjort det, slutför du [instruktionen för att länka lagrings konton](../../../how-tos/create-an-account.md#link-storage-accounts) så att du får åtkomst till din Blob Storage-instans för arr-instansen.
1. Lägg till följande modifierade **LoadModel** -Metod i **RemoteRenderingCoordinator** precis under den aktuella **LoadModel** -metoden:

    ```csharp
    /// <summary>
    /// Loads a model from blob storage that has been linked to the ARR instance
    /// </summary>
    /// <param name="storageAccountName">The storage account name, this contains the blob containers </param>
    /// <param name="blobContainerName">The blob container name, i.e. arroutput</param>
    /// <param name="modelPath">The relative path inside the container to the model, i.e. test/MyCustomModel.arrAsset</param>
    /// <param name="parent">The parent Transform for this remote entity</param>
    /// <param name="progress">A call back method that accepts a float progress value [0->1]</param>
    /// <returns></returns>
    public async Task<Entity> LoadModel(string storageAccountName, string blobContainerName, string modelPath, Transform parent = null, ProgressHandler progress = null)
    {
        //Create a root object to parent a loaded model to
        var modelEntity = ARRSessionService.CurrentActiveSession.Actions.CreateEntity();

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
        var loadModelParams = new LoadModelParams($"{storageAccountName}.blob.core.windows.net", blobContainerName, modelPath, modelEntity);
        var loadModelAsync = ARRSessionService.CurrentActiveSession.Actions.LoadModelAsync(loadModelParams);
        if (progress != null)
            loadModelAsync.ProgressUpdated += progress;
        var result = await loadModelAsync.AsTask();
        return modelEntity;
    }
    ```

    Den här koden är identisk med den ursprungliga `LoadModel` metoden, men vi har ERSATT SAS-versionen av metoden anrop med icke-SAS-versioner.

    Ytterligare indata `storageAccountName` och `blobContainerName` har också lagts till i argumenten. Vi kallar den här nya **LoadModel** -metoden från en annan metod som liknar den allra första **LoadTestModel** -metoden som vi skapade i den första självstudien.

1. Lägg till följande metod i **RemoteRenderingCoordinator** precis efter **LoadTestModel**

    ```csharp
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

    Den här koden lägger till tre ytterligare String-variabler i **RemoteRenderingCoordinator** -komponenten.
    ![Skärm bild som visar lagrings kontots namn, BLOB container namn och modell Sök väg för RemoteRenderingCoordinator-komponenten.](./media/storage-account-linked-model.png)

1. Lägg till dina värden i **RemoteRenderingCoordinator** -komponenten. När du har följt [snabb starten för modell konverteringen](../../../quickstarts/convert-model.md)ska värdena vara:

    * **Lagrings konto namn**: ditt lagrings konto namn, det globalt unika namn som du väljer för ditt lagrings konto. I snabb starten är det *arrtutorialstorage*. ditt värde är annorlunda.
    * **Namn på BLOB-behållare**: arroutput, Blob Storage container
    * **Modell Sök väg**: kombinationen av "outputFolderPath" och "outputAssetFileName" som definierats i *arrconfig.jsi* filen. I snabb starten är detta "outputFolderPath": "konverterad/robot", "outputAssetFileName": robot. arrAsset ". Vilket skulle resultera i en modell Sök väg med värdet "konverterad/robot/robot. arrAsset", ditt värde är annorlunda.

    >[!TIP]
    > Om du [kör **Conversion.ps1**](../../../quickstarts/convert-model.md#run-the-conversion) skriptet, utan argumentet "-UseContainerSas", kommer skriptet att mata ut alla ovanstående värden för din i stället för SAS-token. ![Länkad modell](./media/converted-output.png)
1. För tiden tar du bort eller inaktiverar GameObject- **TestModel** för att göra plats för din anpassade modell att läsa in.
1. Spela upp scenen och Anslut till en fjärran sluten session.
1. Högerklicka på din **RemoteRenderingCoordinator** och välj **Läs in länkad anpassad modell**.
    ![Läs in länkad modell](./media/load-linked-model.png)

De här stegen har ökat säkerheten för programmet genom att ta bort SAS-token från det lokala programmet.

Nu ser det aktuella läget för programmet och dess åtkomst till dina Azure-resurser ut så här:

![Bättre säkerhet](./media/security-two.png)

Vi har ett "lösen ord", AccountKey, för att ta bort från det lokala programmet. Detta kan göras med hjälp av Azure Active Directory (AAD)-autentisering.

## <a name="azure-active-directory-azure-ad-authentication"></a>Azure Active Directory (Azure AD)-autentisering

Med AAD-autentisering kan du avgöra vilka individer eller grupper som använder ARR på ett mer kontrollerat sätt. ARR har inbyggt stöd för att acceptera [åtkomsttoken](../../../../active-directory/develop/access-tokens.md) i stället för att använda en konto nyckel. Du kan tänka på åtkomst-token som en tidsbegränsad, användarspecifik nyckel, som bara låser upp vissa delar av den specifika resurs som den begärde för.

**RemoteRenderingCoordinator** -skriptet har ett ombud med namnet **ARRCredentialGetter**, som innehåller en metod som returnerar ett **AzureFrontendAccountInfo** -objekt, som används för att konfigurera fjärrhantering av sessionen. Vi kan tilldela en annan metod till **ARRCredentialGetter**, så att vi kan använda ett Azure Sign in-flöde, vilket genererar ett **AzureFrontendAccountInfo** -objekt som innehåller en Azure-åtkomsttoken. Denna åtkomsttoken är speciell för användaren som loggar in.

1. Följ anvisningarna för [att: Konfigurera autentisering-autentisering för distribuerade program](../../../how-tos/authentication.md#authentication-for-deployed-applications), i synnerhet du följer anvisningarna i dokumentationen för Azures ankare i Azure [AD User Authentication](../../../../spatial-anchors/concepts/authentication.md?tabs=csharp#azure-ad-user-authentication). Det innebär att registrera ett nytt Azure Active Directory program och konfigurera åtkomst till din ARR-instans.
1. När du har konfigurerat det nya AAD-programmet kontrollerar du att AAD-programmet ser ut som följande bilder:

    **AAD-program – > autentisering** ![ App-autentisering](./media/app-authentication-public.png)

    **AAD-program – > API-behörigheter** ![ App-API: er](./media/request-api-permissions-step-five.png)

1. När du har konfigurerat ditt konto för fjärrrendering kontrollerar du att konfigurationen ser ut som på följande bild:

    **AAR-> AccessControl (IAM)** ![ ARR-roll](./media/azure-remote-rendering-role-assignment-complete.png)

    >[!NOTE]
    > En *ägar* roll är inte tillräcklig för att hantera sessioner via klient programmet. För varje användare som du vill ge möjlighet att hantera sessioner måste du tillhandahålla rollen **Remote rendering-klient**. För varje användare som du vill hantera sessioner och konvertera modeller måste du ange rollen som **administratör för Fjärrrendering**.

Med Azures sida av saker på plats måste vi nu ändra hur koden ansluter till AAR-tjänsten. Vi gör det genom att implementera en instans av **BaseARRAuthentication**, vilket kommer att returnera ett nytt **AzureFrontendAccountInfo** -objekt. I det här fallet kommer konto informationen att konfigureras med Azure-åtkomsttoken.

1. Skapa ett nytt skript med namnet **AADAuthentication** och ersätt dess kod med följande:

    ```csharp
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
        private string accountDomain;
        public string AccountDomain
        {
            get => accountDomain.Trim();
            set => accountDomain = value;
        }

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
        private string azureRemoteRenderingAccountID;
        public string AzureRemoteRenderingAccountID
        {
            get => azureRemoteRenderingAccountID.Trim();
            set => azureRemoteRenderingAccountID = value;
        }
    
        [SerializeField]
        private string azureRemoteRenderingAccountAuthenticationDomain;
        public string AzureRemoteRenderingAccountAuthenticationDomain
        {
            get => azureRemoteRenderingAccountAuthenticationDomain.Trim();
            set => azureRemoteRenderingAccountAuthenticationDomain = value;
        }

        public override event Action<string> AuthenticationInstructions;

        string authority => "https://login.microsoftonline.com/" + AzureTenantID;

        string redirect_uri = "https://login.microsoftonline.com/common/oauth2/nativeclient";

        string[] scopes => new string[] { "https://sts." + AzureRemoteRenderingAccountAuthenticationDomain + "/mixedreality.signin" };

        public void OnEnable()
        {
            RemoteRenderingCoordinator.ARRCredentialGetter = GetAARCredentials;
            this.gameObject.AddComponent<ExecuteOnUnityThread>();
        }

        public async override Task<AzureFrontendAccountInfo> GetAARCredentials()
        {
            var result = await TryLogin();
            if (result != null)
            {
                Debug.Log("Account signin successful " + result.Account.Username);

                var AD_Token = result.AccessToken;

                return await Task.FromResult(new AzureFrontendAccountInfo(AzureRemoteRenderingAccountAuthenticationDomain, AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
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
> Den här koden är inte helt slutförd och är inte klar för ett kommersiellt program. Till exempel är det minst sannolikt att du vill lägga till möjligheten att logga ut. Detta kan göras med hjälp av den `Task RemoveAsync(IAccount account)` metod som tillhandahålls av klient programmet. Den här koden är endast avsedd för självstudier, din implementering är specifik för ditt program.

Koden försöker först hämta token i tyst läge med hjälp av **AquireTokenSilent**. Detta kommer att lyckas om användaren tidigare har autentiserat det här programmet. Om det inte lyckas kan du gå vidare till en mer engagerad strategi.

För den här koden använder vi [enhets kod flödet](../../../../active-directory/develop/v2-oauth2-device-code.md) för att få en åtkomsttoken. Det här flödet gör att användaren kan logga in på sina Azure-konton på en dator eller en mobil enhet och låta den resulterande token skickas tillbaka till HoloLens-programmet.

Den viktigaste delen av den här klassen från ett ARR-perspektiv är den här raden:

```csharp
return await Task.FromResult(new AzureFrontendAccountInfo(AccountDomain, AzureRemoteRenderingAccountID, "", AD_Token, ""));
```

Här skapar vi ett nytt **AzureFrontendAccountInfo** -objekt med hjälp av konto domänen, konto-ID, konto verifierings domän och åtkomsttoken. Denna token används sedan av ARR-tjänsten för att fråga, skapa och ansluta sessioner för fjärrrendering så länge som användaren är auktoriserad baserat på de rollbaserade behörigheter som kon figurer ATS tidigare.

Med den här ändringen ser det aktuella läget för programmet och dess åtkomst till dina Azure-resurser att se ut så här:

![Ännu bättre säkerhet](./media/security-three.png)

Eftersom autentiseringsuppgifterna för användaren inte lagras på enheten (eller i det här fallet även på enheten) är deras exponerings risk mycket låg. Nu använder enheten en användarspecifik, tidsbegränsad åtkomsttoken för att få åtkomst till ARR, som använder åtkomst kontroll (IAM) för att få åtkomst till Blob Storage. Dessa två steg har helt tagit bort "lösen orden" från käll koden och ökad säkerhet avsevärt. Detta är dock inte den mest tillgängliga säkerheten, och om du flyttar modell-och sessionshantering till en webb tjänst förbättras säkerheten ytterligare. Ytterligare säkerhets aspekter beskrivs i kapitlet om [kommersiellt beredskap](../commercial-ready/commercial-ready.md) .

### <a name="testing-aad-auth"></a>Testa AAD-autentisering

När AAD-autentisering är aktiv i Unity-redigeraren måste du autentisera varje gång du startar programmet. På enheten sker autentiseringen första gången och krävs bara igen när token går ut eller är ogiltig.

1. Lägg till **AADAuthentication** -komponenten i **RemoteRenderingCoordinator** -GameObject.

    ![AAD auth-komponent](./media/azure-active-directory-auth-component.png)

1. Fyll i värdena för klient-ID och klient-ID. Du hittar dessa värden på sidan Översikt för din app Registration:

    * **Konto domänen** är samma domän som du har använt i **RemoteRenderingCoordinator** konto domän.
    * **Active Directory programmets klient-ID** är *program-ID: t (Client)* som finns i din AAD-app-registrering (se bilden nedan).
    * **Azure-klient-ID** är *katalog-ID: t* som finns i registreringen av AAD-appen (se bilden nedan).
    * **Konto-ID för Azure Remote rendering** är samma **konto-ID** som du har använt för **RemoteRenderingCoordinator**.
    * **Kontots autentiserings domän** är samma **konto för autentisering av konto** som du har använt i **RemoteRenderingCoordinator**.

    ![Skärm bild som visar program-ID: t och katalog (klient)-ID: t.](./media/app-overview-data.png)

1. Tryck på Play i Unity-redigeraren och medgivande till att köra en session.
    Eftersom **AADAuthentication** -komponenten har en Visa kontrollant, kopplas den automatiskt för att visa en prompt efter den modala panel som har behörighet för sessionen.
1. Följ anvisningarna som finns i panelen till höger om **AppMenu**.
    Du bör se något som liknar detta: ![ illustration som visar instruktions panelen som visas till höger om AppMenu.](./media/device-flow-instructions.png)
    När du har angett den angivna koden på den sekundära enheten (eller webbläsaren på samma enhet) och logga in med dina autentiseringsuppgifter, returneras en åtkomsttoken till det begär ande programmet, i det här fallet Unit-redigeraren.
1. Efter den här punkten bör allt i programmet fortsätta normalt. Kontrol lera om det finns fel i enhets konsolen om du inte går igenom stegen som förväntat.

## <a name="build-to-device"></a>Bygg till enhet

Om du skapar ett program med MSAL to Device måste du inkludera en fil i projektets mapp **till gångar** . Detta hjälper kompileraren att bygga programmet korrekt med *Microsoft.Identity.Client.dll* som ingår i **självstudiernas till gångar**.

1. Lägg till en ny fil i **till gångar** med namnet **link.xml**
1. Lägg till följande för filen:

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

Följ anvisningarna i [snabb start: Distribuera Unity-exempel till HoloLens – Bygg exempelprojektet](../../../quickstarts/deploy-to-hololens.md#build-the-sample-project)för att bygga till HoloLens.

## <a name="next-steps"></a>Nästa steg

Resten av den här själv studie kursen innehåller konceptuella ämnen för att skapa ett produktions klart program som använder Azure-fjärrrendering.

> [!div class="nextstepaction"]
> [Nästa: kommersiell beredskap](../commercial-ready/commercial-ready.md)