---
title: Typer av test enheter, Microsofts kommersiella marknads platser
description: Typer av test enheter i den kommersiella marknads platsen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 06/19/2020
ms.author: trkeya
author: trkeya
ms.openlocfilehash: 2addf415c39691b4e662f304522a418aa8a778c2
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730379"
---
# <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testen het

Använd den här typen om du har ett erbjudande på Azure Marketplace eller AppSource, men vill bygga en testen het med endast Azure-resurser. En Azure Resource Manager ARM-mall (ARM) är en kodad behållare för Azure-resurser som du utformar till att bäst representera din lösning. Test enheten tar den angivna ARM-mallen och distribuerar alla resurser som krävs till en resurs grupp. Det här är den enda alternativet test enhet för virtuella datorer eller Azure App-erbjudanden.

Om du inte känner till vad en ARM-mall är kan du läsa [Vad är Azure Resource Manager?](../azure-resource-manager/management/overview.md) och [förstå strukturen och SYNTAXen för ARM-mallar](../azure-resource-manager/templates/template-syntax.md) för att bättre förstå hur du skapar och testar dina egna mallar.

Information om en **värd** **app** -testenhet finns i [Vad är en test enhet?](what-is-test-drive.md)

## <a name="technical-configuration"></a>Teknisk konfiguration

En distributions mall innehåller alla Azure-resurser som utgör din lösning. Produkter som passar det här scenariot använder bara Azure-resurser. Ange följande egenskaper i Partner Center:

- **Regioner** (obligatoriskt) – för närvarande finns det 26 Azure-regioner som stöds där din test enhet kan göras tillgänglig. Vanligt vis vill du göra din test enhet tillgänglig i de regioner där du räknar med det största antalet kunder, så att de kan välja den region som är närmast för bästa prestanda. Du måste kontrol lera att din prenumeration har tillåtelse att distribuera alla resurser som behövs i varje region som du väljer.

- **Instanser** – Välj typ (frekvent eller kall) och antal tillgängliga instanser, vilket kommer att multipliceras med antalet regioner där ditt erbjudande är tillgängligt.

  - Frekvent **– den** här typen av instans distribueras och väntar på åtkomst per vald region. Kunder kan komma åt en test enhets *aktiva* instanser direkt i stället för att vänta på en distribution. Kompromissen är att dessa instanser alltid körs på din Azure-prenumeration, så de kommer att ådra sig en högre drift tid. Vi rekommenderar starkt att du har minst en *aktiv* instans, eftersom de flesta kunder inte vill vänta på fullständiga distributioner, vilket resulterar i en kombination av kund användning om ingen *aktiv* instans är tillgänglig.

  - **Kall** – den här typen av instans representerar det totala antalet instanser som eventuellt kan distribueras per region. Kalla instanser kräver att hela test enhetens Resource Manager-mall distribueras när en kund begär test enheten, så att *kalla* instanser är mycket långsammare att läsa in än *varma* instanser. Kompromissen är att du bara behöver betala under test enhetens varaktighet, den körs *inte* alltid på din Azure-prenumeration som en *aktiv* instans.

- **Testa enhet Azure Resource Manager mall** – Ladda upp zip-filen som innehåller din Azure Resource Manager-mall. Lär dig mer om hur du skapar en Azure Resource Manager-mall i snabb starts artikeln [skapa och distribuera Azure Resource Manager mallar med hjälp av Azure Portal](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md).

    > [!note]
    > För att publicera fungerar är det viktigt att verifiera formateringen av ARM-mallen. Två sätt att göra detta är (1) med hjälp av ett [online-API-verktyg](/rest/api/resources/deployments/validate) eller (2) med en [test distribution](../azure-resource-manager/templates/deploy-portal.md).

- **Test enhetens varaktighet** (krävs) – ange antalet timmar som test enheten ska vara aktiv. Test enheten avslutas automatiskt när den här tids perioden är slut. Använd endast heltal (till exempel "2" timmar är giltigt, "1,5" är inte).

## <a name="write-the-test-drive-template"></a>Skriv Test Drive-mallen

När du har utformat det önskade paketet med resurser skriver du och skapar ARM-mallen för test enheten. Eftersom test enheten kör distributioner i ett helt automatiserat läge, har Test Drive-mallar vissa begränsningar:

### <a name="parameters"></a>Parametrar

De flesta mallar har en uppsättning parametrar som definierar resurs namn, resurs storlekar (till exempel typer av lagrings konton eller storlekar på virtuella datorer), användar namn och lösen ord, DNS-namn och så vidare. När du distribuerar lösningar med hjälp av Azure Portal kan du fylla i alla dessa parametrar manuellt, välja tillgängliga DNS-namn eller lagrings konto namn och så vidare.

![Lista över parametrar i en Azure Resource Manager](media/test-drive/resource-manager-parameters.png)

Test enheten fungerar dock automatiskt, utan mänsklig interaktion, så den stöder bara en begränsad uppsättning parameter kategorier. Om en parameter i ARM-mallen för test enheten inte finns i någon av de kategorier som stöds, måste du ersätta den med en variabel eller ett konstant värde.

Du kan använda valfritt giltigt namn för parametrarna. test enheten känner igen parameter kategori genom att använda ett värde för metadata-typ. Ange metadata-typ för varje mallparameter, annars kommer din mall inte att klara verifiering:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username"
    }
  },
  ...
}
```

> [!NOTE]
> Alla parametrar är valfria, så om du inte vill använda några behöver du inte göra det.

### <a name="accepted-parameter-metadata-types"></a>Godkända typer av metadata för parameter

| Typ av metadata   | Parameter typ  | Beskrivning     | Exempel värde    |
|---|---|---|---|
| **BaseUri**     | sträng          | Distributions paketets bas-URI| `https://<..>.blob.core.windows.net/<..>` |
| **användar**    | sträng          | Nytt slumpmässigt användar namn.| admin68876      |
| **lösenord**    | säker sträng    | Nytt slumpmässigt lösen ord | LP! ACS- \^ 2kh     |
| **sessions-ID**   | sträng          | Unikt ID för test driven session-ID (GUID)    | b8c8693e-5673-449c-badd-257a405a6dee |

#### <a name="baseuri"></a>BaseUri

Test enheten initierar den här parametern med en **bas-URI** för distributions paketet så att du kan använda den här parametern för att skapa en URI för alla filer som ingår i paketet.

```JSON
"parameters": {
  ...
  "baseuri": {
    "type": "string",
    "metadata": {
      "type": "baseuri",
      "description": "Base Uri of the deployment package."
    }
  },
  ...
}
```

Använd den här parametern inuti mallen för att skapa en URI för en fil från distributions paketet för test enheten. I följande exempel visas hur du skapar en URI för den länkade mallen:

```JSON
"templateLink": {
  "uri": "[concat(parameters('baseuri'),'templates/solution.json')]",
  "contentVersion": "1.0.0.0"
}
```

#### <a name="username"></a>användarnamn

Test enheten initierar den här parametern med ett nytt slumpmässigt användar namn:

```JSON
"parameters": {
  ...
  "username": {
    "type": "string",
    "metadata": {
      "type": "username",
      "description": "Solution admin name."
    }
  },
  ...
}
```

Exempel värde: `admin68876`

Du kan använda antingen slumpmässiga eller konstanta användar namn för din lösning.

#### <a name="password"></a>password

Test enheten initierar den här parametern med ett nytt slumpmässigt lösen ord:

```JSON
"parameters": {
  ...
  "password": {
    "type": "securestring",
    "metadata": {
      "type": "password",
      "description": "Solution admin password."
    }
  },
  ...
}
```

Exempel värde:  `Lp!ACS^2kh`

Du kan använda antingen slumpmässiga eller fasta lösen ord för din lösning.

#### <a name="session-id"></a>sessions-ID

Test enheten initierar den här parametern med ett unikt GUID som representerar Test Drive sessions-ID:

```JSON
"parameters": {
  ...
  "sessionid": {
    "type": "string",
    "metadata": {
      "type": "sessionid",
      "description": "Unique test drive session id."
    }
  },
  ...
}
```

Exempel värde: `b8c8693e-5673-449c-badd-257a405a6dee`

Du kan använda den här parametern för att unikt identifiera testen hets sessionen, om det behövs.

### <a name="unique-names"></a>Unika namn

Vissa Azure-resurser, t. ex. lagrings konton eller DNS-namn, kräver globalt unika namn. Det innebär att varje gång test enheten distribuerar ARM-mallen skapas en ny resurs grupp med ett unikt namn för alla dess resurser. Därför måste du använda funktionen [uniquestring](../azure-resource-manager/templates/template-functions.md) som kombineras med dina variabel namn i resurs grupps-ID: n för att generera slumpmässiga unika värden:

```JSON
"variables": {
  ...
  "domainNameLabel": "[concat('contosovm',uniquestring(resourceGroup().id))]",
  "storageAccountName": "[concat('contosodisk',uniquestring(resourceGroup().id))]",
  ...
}
```

Se till att du sammanfogar parameter-/variabel strängarna ( `contosovm` ) med en unik sträng utmatning ( `resourceGroup().id` ), eftersom detta garanterar unikheten och tillförlitligheten för varje variabel.

De flesta resurs namn får till exempel inte börja med en siffra, men en unik sträng funktion kan returnera en sträng som börjar med en siffra. Det innebär att om du använder rå unika sträng utdata kommer distributionen inte att kunna utföras.

Du hittar mer information om namngivnings regler och begränsningar för resurs namn i [den här artikeln](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="deployment-location"></a>Distributions plats

Du kan göra en test enhet tillgänglig i olika Azure-regioner. Idén är att tillåta att en användare väljer den region som ligger närmast, för att ge Beast användar upplevelse.

När test enheten skapar en instans av labbet skapar den alltid en resurs grupp i den region som väljs av en användare och kör sedan distributions mal len i den här grupp kontexten. Därför bör din mall välja distributions platsen från resurs gruppen:

```JSON
"variables": {
  ...
  "location": "[resourceGroup().location]",
  ...
}
```

Och använder sedan den här platsen för varje resurs för en speciell labb instans:

```JSON
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/publicIPAddresses",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/virtualNetworks",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Network/networkInterfaces",
    "location": "[variables('location')]",
    ...
  },
  {
    "type": "Microsoft.Compute/virtualMachines",
    "location": "[variables('location')]",
    ...
  }
]
```

Se till att prenumerationen kan distribuera alla resurser som du vill ha i varje region som du väljer. Se också till att dina virtuella dator avbildningar är tillgängliga i alla regioner som du ska aktivera, annars fungerar inte distributions mal len för vissa regioner.

### <a name="outputs"></a>Utdata

Normalt med Resource Manager-mallar kan du distribuera utan att producera några utdata. Detta beror på att du vet alla värden som du använder för att fylla i mallparametrar och att du alltid kan inspektera egenskaper för alla resurser manuellt.

För test av Resource Manager-mallar är det dock viktigt att du återgår till att testa enheten all information, vilket krävs för att få åtkomst till labbet (webbplats-URI: er, namn på virtuella värddatorer, användar namn och lösen ord). Se till att alla dina utmatnings namn är läsbara eftersom dessa variabler presenteras för kunden.

Det finns inga begränsningar relaterade till mallens utdata. Test enheten konverterar alla utdatakolumner till strängar, så om du skickar ett objekt till utdata visas en JSON-sträng i en användare.

Exempel:

```JSON
"outputs": {
  "Host Name": {
    "type": "string",
    "value": "[reference(variables('pubIpId')).dnsSettings.fqdn]"
  },
  "User Name": {
    "type": "string",
    "value": "[parameters('adminName')]"
  },
  "Password": {
    "type": "string",
    "value": "[parameters('adminPassword')]"
  }
}
```

### <a name="subscription-limits"></a>Prenumerations begränsningar

Glöm inte om prenumerations-och tjänst begränsningar. Om du till exempel vill distribuera upp till 10 4 virtuella datorer måste du se till att den prenumeration du använder för ditt labb gör att du kan använda 40 kärnor. Mer information om begränsningar för Azure-prenumeration och-tjänster finns i [begränsningar, kvoter och begränsningar för Azure-prenumerationer och tjänster](../azure-resource-manager/management/azure-subscription-service-limits.md). När flera test enheter kan vidtas samtidigt kontrollerar du att din prenumeration kan hantera antalet kärnor multiplicerat med det totala antalet samtidiga test enheter som kan tas.

### <a name="what-to-upload"></a>Vad som ska laddas upp

Test Drive ARM-mallen överförs som en zip-fil som kan innehålla olika distributions artefakter, men måste ha en fil med namnet `main-template.json` . Det här är mallen för ARM-distribution som används av Testkör för att instansiera ett labb. Om du har ytterligare resurser utöver den här filen kan du referera till dem som externa resurser i mallen eller inkludera dem i zip-filen.

Under publicerings certifieringen utvärderar test enheten ditt distributions paket och placerar innehållet i en intern test enhets BLOB-behållare. Behållar strukturen visar strukturen för ditt distributions paket:

| package.zip                       | BLOB-behållare för test enhet         |
|---|---|
| `main-template.json`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/main-template.json`  |
| `templates/solution.json`           | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/templates/solution.json` |
| `scripts/warmup.ps1`                | `https:\//\<\...\>.blob.core.windows.net/\<\...\>/scripts/warmup.ps1`  |
|||

Vi anropar en URI för den här bas-URI: n för BLOB container. Eftersom varje revidering av labbet har sin egen BLOB-behållare, har varje revidering av ditt labb en egen bas-URI. Test enheten kan skicka en bas-URI för det zippade distributions paketet till din mall via mallparametrar.

### <a name="transform-template-examples-for-test-drive"></a>Exempel på omvandling av mallar för test enhet

Processen för att aktivera en arkitektur av resurser till en Resource Manager-mall för test enheter kan vara avskräckande. Mer hjälp finns i följande exempel på hur du bäst omformar de aktuella distributions mallarna på [Vad är en test enhet?](what-is-test-drive.md#transforming-examples).

## <a name="test-drive-deployment-subscription-details"></a>Information om distributions prenumeration för test enhet

Det sista avsnittet att slutföra är att kunna distribuera test enheterna automatiskt genom att ansluta din Azure-prenumeration och Azure Active Directory (AD).

![Information om distributions prenumeration för test enhet](media/test-drive/deployment-subscription-details.png)

1. Hämta ett **ID för Azure-prenumeration**. Detta ger åtkomst till Azure-tjänster och Azure Portal. Prenumerationen är den plats där resursanvändningen rapporteras och tjänsterna faktureras. Om du inte redan har en separat Azure-prenumeration för test enheter kan du göra en. Du kan hitta ID: n för Azure-prenumerationer (till exempel `1a83645ac-1234-5ab6-6789-1h234g764ghty1` ) genom att logga in på Azure Portal och välja **prenumerationer** på menyn till vänster-navigerings.

   ![Azure-prenumerationer](media/test-drive/azure-subscriptions.png)

2. Hämta ett **klient-ID för Azure AD**. Om du redan har ett klient-ID tillgängligt kan du hitta det i **Azure Active Directory**  >  **Egenskaper**  >  **katalog-ID**:

   ![Azure Active Directory egenskaper](media/test-drive/azure-active-directory-properties.png)

   Om du inte har ett klient-ID skapar du ett nytt i Azure Active Directory. Hjälp med att konfigurera en klient finns i [snabb start: Konfigurera en klient](../active-directory/develop/quickstart-create-new-tenant.md).

3. **Azure AD App-ID** – skapa och registrera ett nytt program. Vi kommer att använda det här programmet för att utföra åtgärder på din test enhets instans.

   1. Navigera till katalogen som skapats eller redan är befintlig och välj Azure Active Directory i filter fönstret.
   2. Sök **Appregistreringar** och välj **Lägg till**.
   3. Ange ett program namn.
   4. Välj **typ** av **webbapp/API**.
   5. Ange ett värde i URL: en för inloggning, det här fältet används inte.
   6. Välj **Skapa**.
   7. När programmet har skapats väljer du **Egenskaper**  >  **Ange programmet som flera innehavare** och sedan **Spara**.

4. Välj **Spara**.

5. Kopiera program-ID: t för den här registrerade appen och klistra in det i fältet test enhet.

   ![Information om Azure AD-programid](media/test-drive/azure-ad-application-id-detail.png)

6. Eftersom vi använder programmet för att distribuera till prenumerationen måste vi lägga till programmet som deltagare i prenumerationen:

   1. Välj den typ av **prenumeration** som du använder för test enheten.
   1. Välj **Åtkomstkontroll (IAM)** .
   1. Välj fliken **roll tilldelningar** och **Lägg sedan till roll tilldelning**.

      ![Lägg till en ny Access Control huvud konto](media/test-drive/access-control-principal.jpg)

   1. Ange **roll** och **tilldela åtkomst till** som visas. I **Välj** -fältet anger du namnet på Azure AD-programmet. Välj det program som du vill tilldela **deltagar** rollen till.

      ![Lägg till behörigheterna](media/test-drive/access-control-permissions.jpg)

   1. Välj **Spara**.

7. Generera en nyckel för **Azure AD App** -autentisering. Under **nycklar** lägger du till en **nyckel Beskrivning**, ställer in varaktigheten så att den **aldrig upphör att gälla** (en förfallen nyckel bryter din test enhet i produktion) och väljer sedan **Spara**. Kopiera och klistra in det här värdet i fältet för test enheten som krävs.

![Visar nycklar för Azure AD-programmet](media/test-drive/azure-ad-app-keys.png)

## <a name="republish"></a>Publicera om

Nu när alla test enhets fält är slutförda **publicerar** du om erbjudandet. När test enheten har klarat certifieringen testar du kund upplevelsen i för hands versionen av ditt erbjudande:

1. Starta en testen het i användar gränssnittet.
1. Öppna din Azure-prenumeration i Azure Portal.
1. Kontrol lera att test enheten är korrekt distribuerad.

   ![Azure Portal](media/test-drive/azure-portal.png)

Ta inte bort några test enhets instanser som tillhandahålls för dina kunder. Test Drive-tjänsten rensar automatiskt dessa resurs grupper efter att en kund är klar.

När du är van vid ditt för hands versions erbjudande är det dags att **gå live**! Det finns en slutgiltig gransknings process för att kontrol lera hela slut punkt till slut punkt. Om vi avvisar erbjudandet kommer vi att e-posta teknisk kontakt för ditt erbjudande som förklarar vad som behöver åtgärdas.

## <a name="next-steps"></a>Nästa steg

- Om du följer anvisningarna för att skapa ditt erbjudande i Partner Center, använder du back-pilen för att återgå till det avsnittet.
- Vill du veta mer om andra typer av test enheter på [Vad är en test enhet?](what-is-test-drive.md).