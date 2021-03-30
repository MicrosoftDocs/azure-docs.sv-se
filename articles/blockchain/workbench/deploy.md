---
title: Distribuera Azure blockchain Workbench Preview
description: Distribuera Azure blockchain Workbench Preview
ms.date: 07/16/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: references_regions
ms.openlocfilehash: b46a35b45a51d0cc76942c4ca142c4c7792a28b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "87077019"
---
# <a name="deploy-azure-blockchain-workbench-preview"></a>Distribuera Azure blockchain Workbench Preview

Azure blockchain Workbench Preview distribueras med en lösnings mall på Azure Marketplace. Mallen fören klar distributionen av komponenter som behövs för att skapa blockchain-program. När blockchain Workbench har distribuerats ger de till gång till klient program för att skapa och hantera användare och blockchain-program.

Mer information om komponenterna i blockchain Workbench finns i arkitekturen för [Azure blockchain Workbench](architecture.md).

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="prepare-for-deployment"></a>Förbereda för distribution

Med blockchain Workbench kan du distribuera en blockchain-redovisning tillsammans med en uppsättning relevanta Azure-tjänster som oftast används för att skapa ett blockchain-baserat program. Distribution av blockchain Workbench-resultat i följande Azure-tjänster som tillhandahålls i en resurs grupp i din Azure-prenumeration.

* App Service plan (standard)
* Application Insights
* Event Grid
* Azure Key Vault
* Service Bus
* SQL Database (Standard S0)
* Azure Storage konto (standard LRS)
* Skalnings uppsättning för virtuell dator med kapacitet 1
* Virtual Network resurs grupp (med Load Balancer, nätverks säkerhets grupp, offentlig IP-adress, Virtual Network)
* Azure blockchain-tjänsten. Om du använder en tidigare blockchain Workbench-distribution bör du överväga att distribuera om Azure blockchain Workbench för att använda Azure blockchain-tjänsten.

Följande är en exempel distribution som skapats i **myblockchain** -resurs gruppen.

![Exempel distribution](media/deploy/example-deployment.png)

Kostnaden för blockchain Workbench är en mängd kostnad för de underliggande Azure-tjänsterna. Pris information för Azure-tjänster kan beräknas med hjälp av [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/).

## <a name="prerequisites"></a>Förutsättningar

Azure blockchain Workbench kräver konfiguration och program registrering i Azure AD. Du kan välja att göra Azure AD- [konfigurationerna manuellt](#azure-ad-configuration) innan du distribuerar eller köra en skript post distribution. Om du omdistribuerar blockchain Workbench, se [Azure AD-konfiguration](#azure-ad-configuration) för att verifiera din Azure AD-konfiguration.

> [!IMPORTANT]
> Workbench behöver inte distribueras i samma klient organisation som den som du använder för att registrera ett Azure AD-program. Workbench måste distribueras i en klient där du har tillräcklig behörighet för att distribuera resurser. Mer information om Azure AD-klienter finns i [så här hämtar du en Active Directory-klient](../../active-directory/develop/quickstart-create-new-tenant.md) och [integrerar program med Azure Active Directory](../../active-directory/develop/quickstart-register-app.md).

## <a name="deploy-blockchain-workbench"></a>Distribuera blockchain Workbench

När de nödvändiga stegen har slutförts är du redo att distribuera blockchain Workbench. Följande avsnitt beskriver hur du distribuerar ramverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klient där du vill distribuera Azure blockchain Workbench.
1. Klicka på **Skapa en resurs** längst upp till vänster i Azure-portalen.
1. Välj **blockchain**  >  **Azure blockchain Workbench (för hands version)**.

    ![Skapa Azure blockchain Workbench](media/deploy/blockchain-workbench-settings-basic.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Resource prefix | Kort unik identifierare för din distribution. Det här värdet används som bas för namngivning av resurser. |
    | Användar namn för virtuell dator | Användar namnet används som administratör för alla virtuella datorer (VM). |
    | Autentiseringstyp | Välj om du vill använda ett lösen ord eller en nyckel för att ansluta till virtuella datorer. |
    | Lösenord | Lösen ordet används för att ansluta till virtuella datorer. |
    | SSH | Använd en offentlig RSA-nyckel i det enradiga formatet som börjar med **SSH-RSA** eller Använd multi-line PEM-formatet. Du kan generera SSH-nycklar med hjälp av `ssh-keygen` Linux och OS X, eller genom att använda PuTTYGen i Windows. Mer information om SSH-nycklar finns i [så här använder du SSH-nycklar med Windows på Azure](../../virtual-machines/linux/ssh-from-windows.md). |
    | Databas-och blockchain-lösenord | Ange det lösen ord som ska användas för åtkomst till databasen som skapats som en del av distributionen. Lösen ordet måste uppfylla tre av följande fyra krav: längden måste vara mellan 12 & 72 tecken, 1 gemen bokstav, 1 versal bokstav, 1 siffra och 1 specialtecken som inte är nummer tecken (#), procent (%), komma (,), stjärna (*), citat tecken ( \` ), dubbelt citat tecken ("), enkla citat tecken (), bindestreck (-) och semicolumn (;) |
    | Distributions region | Ange var du vill distribuera blockchain Workbench-resurser. För bästa tillgänglighet ska detta överensstämma med inställningen **region** plats. Alla regioner är inte tillgängliga under för hands versionen. Funktioner kanske inte är tillgängliga i vissa regioner. Azure blockchain Data Manager finns i följande Azure-regioner: USA, östra och Västeuropa.|
    | Prenumeration | Ange den Azure-prenumeration som du vill använda för din distribution. |
    | Resursgrupper | Skapa en ny resurs grupp genom att välja **Skapa ny** och ange ett unikt resurs grupp namn. |
    | Location | Ange den region som du vill distribuera ramverket för. |

1. Klicka på **OK** för att slutföra inställningen konfiguration av grundläggande konfiguration.

1. I **Avancerade inställningar** väljer du om du vill skapa ett nytt blockchain-nätverk eller använda ett befintligt blockchain-nätverk (proof-of-Authority).

    **Skapa ny**:

    Med alternativet för att *Skapa nytt* distribueras redovisningen för Azure blockchain service-kvorum med standard bas-SKU: n.

    ![Avancerade inställningar för nytt blockchain-nätverk](media/deploy/advanced-blockchain-settings-new.png)

    | Inställning | Beskrivning  |
    |---------|--------------|
    | Pris nivå för Azure blockchain service | Välj **standard** nivån **för Azure-** blockchain som används för blockchain Workbench |
    | Azure Active Directory-inställningar | Välj **Lägg till senare**.</br>Obs: om du väljer att [förkonfigurera Azure AD](#azure-ad-configuration) eller omdistribueras väljer du att *lägga till nu*. |
    | Val av virtuell dator | Välj önskade lagrings prestanda och VM-storlek för ditt blockchain-nätverk. Välj en mindre VM-storlek, till exempel *standard ds1 v2* , om du har en prenumeration med låg tjänste gräns som Azures kostnads fri nivå. |

    För **Använd befintlig**:

    Med alternativet *Använd befintlig* kan du ange ett Ethereum-nätverk (POA proof-of-Authority) blockchain. Slut punkter har följande krav.

   * Slut punkten måste vara ett PoA-blockchain (Ethereum proof-of-Authority).
   * Slut punkten måste vara offentligt tillgänglig över nätverket.
   * PoA blockchain-nätverket ska konfigureras att ha gas pris inställt på noll.

     > [!NOTE]
     > Blockchain Workbench-konton finansieras inte. Om det krävs penning medel går det inte att utföra transaktionerna.

     ![Avancerade inställningar för befintligt blockchain-nätverk](media/deploy/advanced-blockchain-settings-existing.png)

     | Inställning | Beskrivning  |
     |---------|--------------|
     | Ethereum RPC-slutpunkt | Ange RPC-slutpunkten för ett befintligt PoA blockchain-nätverk. Slut punkten börjar med https://eller http://och slutar med ett port nummer. Till exempel `http<s>://<network-url>:<port>` |
     | Azure Active Directory-inställningar | Välj **Lägg till senare**.</br>Obs: om du väljer att [förkonfigurera Azure AD](#azure-ad-configuration) eller omdistribueras väljer du att *lägga till nu*. |
     | Val av virtuell dator | Välj önskade lagrings prestanda och VM-storlek för ditt blockchain-nätverk. Välj en mindre VM-storlek, till exempel *standard ds1 v2* , om du har en prenumeration med låg tjänste gräns som Azures kostnads fri nivå. |

1. Välj **Granska + skapa** för att slutföra avancerade inställningar.

1. Granska sammanfattningen för att kontrol lera att parametrarna är korrekta.

    ![Sammanfattning](media/deploy/blockchain-workbench-summary.png)

1. Välj **skapa** för att godkänna villkoren och distribuera ditt Azure blockchain Workbench.

Distributionen kan ta upp till 90 minuter. Du kan använda Azure Portal för att övervaka förloppet. I den nyligen skapade resurs gruppen väljer du **distributioner > översikt** för att se status för de distribuerade artefakterna.

> [!IMPORTANT]
> Efter distributionen måste du slutföra Active Directory inställningar. Om du väljer **Lägg till senare** måste du köra [konfigurations skriptet för Azure AD](#azure-ad-configuration-script).  Om du väljer **Lägg till nu** måste du [Konfigurera svars-URL: en](#configuring-the-reply-url).

## <a name="blockchain-workbench-web-url"></a>Webb-URL för blockchain Workbench

När distributionen av blockchain Workbench har slutförts innehåller en ny resurs grupp dina blockchain Workbench-resurser. Blockchain Workbench-tjänster nås via en webb-URL. Följande steg visar hur du hämtar webb adressen för det distribuerade ramverket.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. I det vänstra navigerings fönstret väljer du **resurs grupper**.
1. Välj det resurs grupps namn som du angav när du distribuerade blockchain Workbench.
1. Välj kolumn rubriken **typ** för att sortera listan alfabetiskt efter typ.
1. Det finns två resurser av typen **App Service**. Välj resurs av typen **App Service** *utan* "-API"-suffixet.

    ![App Service-lista](media/deploy/resource-group-list.png)

1. Kopiera **URL** -värdet i App Service **Översikt** som representerar webb adressen till din distribuerade blockchain Workbench.

    ![Viktig information för App Service](media/deploy/app-service.png)

Information om hur du associerar ett anpassat domän namn med blockchain Workbench finns [i Konfigurera ett anpassat domän namn för en webbapp i Azure App Service att använda Traffic Manager](../../app-service/configure-domain-traffic-manager.md).

## <a name="azure-ad-configuration-script"></a>Konfigurations skript för Azure AD

Azure AD måste konfigureras för att slutföra din blockchain Workbench-distribution. Du använder ett PowerShell-skript för att utföra konfigurationen.

1. I en webbläsare navigerar du till [webb adressen för blockchain Workbench](#blockchain-workbench-web-url).
1. Du kommer att få anvisningar om hur du konfigurerar Azure AD med hjälp av Cloud Shell. Kopiera kommandot och starta Cloud Shell.

    ![Starta AAD-skript](media/deploy/launch-aad-script.png)

1. Välj den Azure AD-klient där du distribuerade blockchain Workbench.
1. I Cloud Shell PowerShell-miljö klistrar du in och kör kommandot.
1. När du uppmanas till det anger du den Azure AD-klient som du vill använda för blockchain Workbench. Detta är klient organisationen som innehåller användare för blockchain Workbench.

    > [!IMPORTANT]
    > Den autentiserade användaren måste ha behörighet att skapa program registreringar i Azure AD och bevilja delegerade program behörigheter i klienten. Du kan behöva be en administratör av klienten att köra konfigurations skriptet för Azure AD eller skapa en ny klient.

    ![Ange Azure AD-klient](media/deploy/choose-tenant.png)

1. Du uppmanas att autentisera till Azure AD-klienten med hjälp av en webbläsare. Öppna webb adressen i en webbläsare, ange koden och autentisera.

    ![Autentisera med kod](media/deploy/authenticate.png)

1. Skriptet utdata visar flera status meddelanden. Du **får ett status** meddelande om att klienten har kon figurer ATS.
1. Navigera till blockchain Workbench-URL: en. Du uppmanas att bevilja Läs behörighet till katalogen. Detta gör att blockchain Workbench-webbappen kan komma åt användarna i klienten. Om du är klient organisations administratör kan du välja att godkänna hela organisationen. Det här alternativet godkänner godkännande för alla användare i klient organisationen. Annars uppmanas varje användare att ange medgivande vid första användningen av blockchain Workbench-webbprogrammet.
1. Välj **Godkänn** till medgivande.

     ![Medgivande för att läsa användar profiler](media/deploy/graph-permission-consent.png)

1. Efter godkännandet kan du använda blockchain Workbench-webbappen.

Du har slutfört din Azure blockchain Workbench-distribution. I [Nästa steg](#next-steps) får du förslag på hur du kommer igång med din distribution.

## <a name="azure-ad-configuration"></a>Azure AD-konfiguration

Om du väljer att konfigurera eller verifiera Azure AD-inställningar manuellt före distributionen slutför du alla steg i det här avsnittet. Om du vill konfigurera Azure AD-inställningar automatiskt använder du [konfigurations skriptet för Azure AD](#azure-ad-configuration-script) när du har distribuerat blockchain Workbench.

### <a name="blockchain-workbench-api-app-registration"></a>Blockchain Workbench API app-registrering

Blockchain Workbench-distribution kräver registrering av ett Azure AD-program. Du behöver en Azure Active Directory-klient (Azure AD) för att registrera appen. Du kan använda en befintlig klient eller skapa en ny klient. Om du använder en befintlig Azure AD-klient måste du ha tillräcklig behörighet för att registrera program, bevilja Graph API behörigheter och tillåta gäst åtkomst i en Azure AD-klient. Om du inte har tillräckliga behörigheter i en befintlig Azure AD-klient skapar du en ny klient.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj ditt konto i det övre högra hörnet och växla till önskad Azure AD-klient. Klienten bör vara prenumerations administratörens klient organisation för den prenumeration där Azure blockchain Workbench distribueras och du har tillräcklig behörighet för att registrera program.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Appregistreringar**  >  **ny registrering**.

    ![Appregistrering](media/deploy/app-registration.png)

1. Ange ett visnings **namn** och välj **konton endast i den här organisations katalogen**.

    ![Skapa en appregistrering](media/deploy/app-registration-create.png)

1. Välj **Registrera** för att registrera Azure AD-programmet.

### <a name="modify-manifest"></a>Ändra manifest

Därefter måste du ändra manifestet för att använda program roller i Azure AD för att ange blockchain Workbench-administratörer.  Mer information om program manifest finns i [Azure Active Directory Application manifest](../../active-directory/develop/reference-app-manifest.md).

1. Ett GUID krävs för manifestet. Du kan generera en GUID med PowerShell-kommandot `[guid]::NewGuid()` eller `New-GUID` cmdleten. Ett annat alternativ är att använda en webbplats för GUID-Generator.
1. Välj **manifest** i avsnittet **Hantera** för det program som du har registrerat.
1. Sedan uppdaterar du **appRoles** -avsnittet i manifestet. Ersätt `"appRoles": []` med den angivna JSON-filen. Se till att ersätta värdet för `id` fältet med det GUID som du skapade.
    ![Redigera manifest](media/deploy/edit-manifest.png)

    ``` json
    "appRoles": [
         {
           "allowedMemberTypes": [
             "User",
             "Application"
           ],
           "displayName": "Administrator",
           "id": "<A unique GUID>",
           "isEnabled": true,
           "description": "Blockchain Workbench administrator role allows creation of applications, user to role assignments, etc.",
           "value": "Administrator"
         }
       ],
    ```

    > [!IMPORTANT]
    > Värde **administratören** behövs för att identifiera blockchain Workbench-administratörer.

1. I manifestet ändrar du även värdet för **Oauth2AllowImplicitFlow** till **True**.

    ``` json
    "oauth2AllowImplicitFlow": true,
    ```

1. Välj **Spara** för att spara manifest ändringarna.

### <a name="add-graph-api-required-permissions"></a>Lägg till Graph API nödvändiga behörigheter

API-programmet måste begära behörighet från användaren för att få åtkomst till katalogen. Ange följande nödvändiga behörighet för API-programmet:

1. Välj **API-behörigheter** i *blockchain API* -appens registrering. Som standard läggs Graph API **User. Read** -behörighet till.
1. Workbench-programmet kräver Läs behörighet till användarnas grundläggande profil information. I *konfigurerade behörigheter* väljer du **Lägg till en behörighet**. I **Microsoft API: er** väljer du **Microsoft Graph**.
1. Eftersom Workbench-programmet använder de autentiserade användarnas autentiseringsuppgifter väljer du **delegerade behörigheter**.
1. Välj **User. ReadBasic. all** behörighet i kategorin *användare* .

    ![Azure AD App Registration-konfiguration som visar hur du lägger till Microsoft Graph User. ReadBasic. all delegerad behörighet](media/deploy/add-graph-user-permission.png)

    Välj **Lägg till behörigheter**.

1. I *konfigurerade behörigheter* väljer du **bevilja administratörs medgivande** för domänen och väljer sedan **Ja** för verifierings frågan.

   ![Bevilja behörigheter](media/deploy/client-app-grant-permissions.png)

   Genom att bevilja behörighet kan blockchain Workbench komma åt användare i katalogen. Läs behörighet krävs för att söka efter och lägga till medlemmar i blockchain Workbench.

### <a name="get-application-id"></a>Hämta program-ID

Program-ID och klient information krävs för distribution. Samla in och lagra informationen som ska användas under distributionen.

1. Välj **Översikt** för det program som du har registrerat.
1. Kopiera och lagra **programmets ID-** värde för senare användning under distributionen.

    ![Egenskaper för API-app](media/deploy/app-properties.png)

    | Inställning att lagra  | Använd i distribution |
    |------------------|-------------------|
    | Program-ID (klient) | Azure Active Directory installations > program-ID |

### <a name="get-tenant-domain-name"></a>Hämta klient domän namn

Samla in och lagra Active Directory klient domän namnet där programmen registreras.

I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Egna domännamn**. Kopiera och lagra domän namnet.

![Domännamn](media/deploy/domain-name.png)

### <a name="guest-user-settings"></a>Gäst användar inställningar

Om du har gäst användare i Azure AD-klienten följer du de ytterligare stegen för att se till att blockchain Workbench användar tilldelning och hantering fungerar korrekt.

1. Växla till Azure AD-klienten och välj **Azure Active Directory > användar inställningar > hantera inställningar för externt samarbete**.
1. Ange **gäst användar behörigheter är begränsade** till **Nej**.
    ![Inställningar för externt samarbete](media/deploy/user-collaboration-settings.png)

## <a name="configuring-the-reply-url"></a>Konfigurera svars-URL

När Azure blockchain Workbench har distribuerats måste du konfigurera den Azure Active Directory (Azure AD)-klient program **svars-URL: en** för den distribuerade blockchain Workbench-webbadressen.

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Kontrol lera att du är i klient organisationen där du registrerade Azure AD-klientprogrammet.
1. I det vänstra navigeringsfönstret väljer du **Azure Active Directory**-tjänsten. Välj **Appregistreringar**.
1. Välj det Azure AD-klientcertifikat som du registrerade i avsnittet krav.
1. Välj **Autentisering**.
1. Ange huvud webb adress till den Azure blockchain Workbench-distribution som du hämtade i avsnittet [blockchain Workbench webb-URL](#blockchain-workbench-web-url) . Svars-URL: en har prefixet `https://` . Till exempel `https://myblockchain2-7v75.azurewebsites.net`

    ![URL för autentiserings svar](media/deploy/configure-reply-url.png)

1. I avsnittet **Avancerad inställning** kontrollerar du **åtkomsttoken** och **ID-token**.

    ![Avancerade inställningar för autentisering](media/deploy/authentication-advanced-settings.png)

1. Välj **Spara** för att uppdatera klient registreringen.

## <a name="remove-a-deployment"></a>Ta bort en distribution

När en distribution inte längre behövs kan du ta bort en distribution genom att ta bort resurs gruppen blockchain Workbench.

1. I Azure Portal navigerar du till **resurs grupp** i det vänstra navigerings fönstret och väljer den resurs grupp som du vill ta bort.
1. Välj **Ta bort resursgrupp**. Verifiera borttagning genom att ange resurs gruppens namn och välj **ta bort**.

    ![Ta bort resursgrupp](media/deploy/delete-resource-group.png)

## <a name="next-steps"></a>Nästa steg

I den här instruktions artikeln distribuerade du Azure blockchain Workbench. Om du vill lära dig hur du skapar ett blockchain-program fortsätter du till nästa instruktions artikel.

> [!div class="nextstepaction"]
> [Skapa ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)
