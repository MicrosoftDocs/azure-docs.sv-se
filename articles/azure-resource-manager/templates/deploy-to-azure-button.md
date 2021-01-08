---
title: Distribuera till Azure (knapp)
description: Använd knappen för att distribuera Azure Resource Manager mallar från en GitHub-lagringsplats.
ms.topic: conceptual
ms.date: 11/10/2020
ms.openlocfilehash: abe59f377474540e9209691df8b1d1a7b806c26d
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028751"
---
# <a name="use-a-deployment-button-to-deploy-templates-from-github-repository"></a>Använd en distributions knapp för att distribuera mallar från GitHub-lagringsplatsen

Den här artikeln beskriver hur du använder knappen **distribuera till Azure** för att distribuera mallar från en GitHub-lagringsplats. Du kan lägga till knappen direkt i _Readme.MD_ -filen i din GitHub-lagringsplats. Du kan också lägga till knappen till en webb sida som refererar till lagrings platsen.

Distributions omfånget bestäms av schemat för mallen. Mer information finns i:

- [resurs grupper](deploy-to-resource-group.md)
- [prenumerationer](deploy-to-subscription.md)
- [hanterings grupper](deploy-to-management-group.md)
- [klienter](deploy-to-tenant.md)

## <a name="use-common-image"></a>Använd gemensam avbildning

Om du vill lägga till knappen på din webb sida eller lagrings plats använder du följande bild:

```markdown
![Deploy to Azure](https://aka.ms/deploytoazurebutton)
```

```html
<img src="https://aka.ms/deploytoazurebutton"/>
```

Bilden visas som:

![Distribuera till Azure (knapp)](https://aka.ms/deploytoazurebutton)

## <a name="create-url-for-deploying-template"></a>Skapa URL för distribution av mall

Om du vill skapa en URL för mallen börjar du med den råa URL: en till mallen i din lagrings platsen. Om du vill se den råa URL: en väljer du **RAW**.

:::image type="content" source="./media/deploy-to-azure-button/select-raw.png" alt-text="Välj RAW":::

URL: en har följande format:

```html
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

Konvertera sedan URL: en till ett URL-kodat värde. Du kan använda en online-kodare eller köra ett kommando. I följande PowerShell-exempel visas hur du kodar ett värde i URL-adressen.

```powershell
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
[uri]::EscapeDataString($url)
```

Exempel-URL: en har följande värde när URL: en är kodad.

```html
https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Varje länk börjar med samma bas-URL:

```html
https://portal.azure.com/#create/Microsoft.Template/uri/
```

Lägg till länken URL-kodad mall i slutet av bas-URL: en.

```html
https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json
```

Du har en fullständig URL för länken.

Normalt är du värd för mallen i en offentlig lagrings platsen. Om du använder en privat lagrings platsen måste du inkludera en token för att få åtkomst till mallens rå innehåll. Den token som genereras av GitHub är endast giltig för en kort tid. Du behöver uppdatera länken ofta.

Om du använder [git med Azure databaser](/azure/devops/repos/git/) i stället för en GitHub-lagrings platsen kan du fortfarande använda knappen **distribuera till Azure** . Kontrol lera att din lagrings platsen är offentlig. Använd [åtgärden objekt](/rest/api/azure/devops/git/items/get) för att hämta mallen. Din begäran ska ha följande format:

```http
https://dev.azure.com/{organization-name}/{project-name}/_apis/git/repositories/{repository-name}/items?scopePath={url-encoded-path}&api-version=6.0
```

Koda denna URL för begäran.

## <a name="create-deploy-to-azure-button"></a>Knappen Skapa distribuera till Azure

Lägg slutligen till länken och bilden tillsammans.

Om du vill lägga till knappen med markdown i _Readme.MD_ -filen på GitHub-lagringsplatsen eller på en webb sida använder du:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)
```

För HTML använder du:

```html
<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json" target="_blank">
  <img src="https://aka.ms/deploytoazurebutton"/>
</a>
```

För git med Azure lagrings platsen har knappen formatet:

```markdown
[![Deploy to Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fdev.azure.com%2Forgname%2Fprojectname%2F_apis%2Fgit%2Frepositories%2Freponame%2Fitems%3FscopePath%3D%2freponame%2fazuredeploy.json%26api-version%3D6.0)
```

## <a name="deploy-the-template"></a>Distribuera mallen

Om du vill testa den fullständiga lösningen väljer du följande knapp:

[![Distribuera till Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-storage-account-create%2Fazuredeploy.json)

Portalen visar ett fönster som gör att du enkelt kan ange parameter värden. Parametrarna fylls i automatiskt med standardvärdena från mallen.

![Använda portalen för att distribuera](./media/deploy-to-azure-button/portal.png)

## <a name="next-steps"></a>Nästa steg

- Mer information om mallar finns i [förstå strukturen och syntaxen för ARM-mallar](template-syntax.md).
