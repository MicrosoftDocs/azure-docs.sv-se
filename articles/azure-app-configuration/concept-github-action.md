---
title: Synkronisera din GitHub-lagringsplats med app-konfigurationen
description: Använd GitHub-åtgärder för att automatiskt uppdatera din konfigurations instans när du uppdaterar din GitHub-lagringsplats.
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 05/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 588efd692119c9e2831e16c1ce26c2759898a1e5
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607372"
---
# <a name="sync-your-github-repository-to-app-configuration"></a>Synkronisera din GitHub-lagringsplats med app-konfigurationen

Team som vill fortsätta använda sina befintliga käll kontroll metoder kan använda GitHub-åtgärder för att automatiskt synkronisera sina GitHub-lagringsplatser med sitt konfigurations lager för appar. På så sätt kan du göra ändringar i config-filerna precis som vanligt, samtidigt som du får program konfigurations förmåner som: <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Centraliserad konfiguration utanför din kod <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Uppdatera konfigurationen utan att distribuera om hela appen <br>
&nbsp;&nbsp;&nbsp;&nbsp;• Integrering med tjänster som Azure App Service och funktioner. 

Ett [arbets flöde](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) för GitHub-åtgärder definierar en automatiserad process i en GitHub-lagringsplats. Åtgärden *Azure App Sync-konfiguration* utlöser uppdateringar till en konfigurations instans för appar när ändringar görs i käll lagrings platsen. Den använder en YAML-fil (. yml) som finns i `/.github/workflows/` sökvägen till lagrings platsen för att definiera stegen och parametrarna. Du kan utlösa konfigurations uppdateringar när du skickar, granska eller förgrena appars konfigurationsfiler precis som du gör med app-kod.

GitHub- [dokumentationen](https://docs.github.com/en/free-pro-team@latest/actions/learn-github-actions/introduction-to-github-actions) innehåller djupgående visning av GitHub-arbetsflöden och åtgärder. 

## <a name="enable-github-actions-in-your-repository"></a>Aktivera GitHub-åtgärder i din lagrings plats
Börja använda den här GitHub-åtgärden genom att gå till din lagrings plats och välja fliken **åtgärder** . Välj **nytt arbets flöde** och skapa sedan **ett arbets flöde själv**. Slutligen söker du efter "Azure App konfigurations synkronisering" på Marketplace.
> [!div class="mx-imgBorder"]
> ![Välj fliken åtgärd](media/find-github-action.png)

> [!div class="mx-imgBorder"]
> ![Välj synkronisering av app-konfiguration](media/app-configuration-sync-action.png)

## <a name="sync-configuration-files-after-a-push"></a>Synkronisera konfigurationsfiler efter en push
Den här åtgärden synkroniserar Azure App konfigurationsfiler när en ändring skickas till `appsettings.json` . När en utvecklare skickar en ändring till `appsettings.json` , uppdaterar synkroniseringen av app Configuration-instansen med de nya värdena.

I det första avsnittet av det här arbets flödet anges att åtgärden utlöses *på* en *push* som innehåller `appsettings.json` till *huvud* grenen. I det andra avsnittet visas jobben som körs när åtgärden har Aktiver ATS. Åtgärden kontrollerar de relevanta filerna och uppdaterar konfigurations instansen för appen med hjälp av anslutnings strängen som lagras som en hemlighet i lagrings platsen.  Mer information om hur du använder hemligheter i GitHub finns i [GitHub-artikeln](https://docs.github.com/en/free-pro-team@latest/actions/reference/encrypted-secrets) om hur du skapar och använder krypterade hemligheter.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your                        
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }} 
          separator: ':' 
```

## <a name="use-strict-sync"></a>Använd strikt synkronisering
Som standard aktiverar GitHub-åtgärden inte strikt läge, vilket innebär att synkroniseringen bara lägger till nyckel värden från konfigurations filen till appens konfigurations instans (inga nyckel/värde-par tas bort). Att aktivera strikt läge innebär att nyckel/värde-par som inte finns i konfigurations filen tas bort från program konfigurations instansen, så att den matchar konfigurations filen. Om du synkroniserar från flera källor eller använder Azure Key Vault med konfiguration av appar vill du använda olika prefix eller etiketter med strikt synkronisering för att undvika att ta bort konfigurations inställningar från andra filer (se exemplen nedan). 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: 'Label' 
          prefix: 'Prefix:' 
          strict: true 
```
## <a name="sync-multiple-files-in-one-action"></a>Synkronisera flera filer i en åtgärd 

Om konfigurationen finns i flera filer kan du använda mönstret nedan för att utlösa en synkronisering när någon av filerna ändras. Det här mönstret använder BLOB-biblioteket https://www.npmjs.com/package/glob . Observera att om namnet på konfigurations filen innehåller ett kommatecken kan du använda ett omvänt snedstreck för att undvika kommatecken. 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'appsettings2.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: '{appsettings.json,appsettings2.json}'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
```

## <a name="sync-by-prefix-or-label"></a>Synkronisera efter prefix eller etikett
Om du anger prefix eller etiketter i din synkroniseringsrelation synkroniseras bara den specifika uppsättningen. Detta är viktigt för att använda strikt synkronisering med flera filer. Beroende på hur konfigurationen har kon figurer ATS kan antingen ett prefix eller en etikett vara kopplad till varje fil och varje prefix eller etikett kan synkroniseras separat så att inget skrivs över. Vanligt vis används prefix för olika program eller tjänster och etiketter för olika miljöer. 

Synkronisera efter prefix: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          prefix: 'Prefix::'
```

Synkronisera efter etikett: 

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          label: 'Label'

```

## <a name="use-a-dynamic-label-on-sync"></a>Använd en dynamisk etikett vid synkronisering
Följande åtgärd infogar en dynamisk etikett vid varje synkronisering, vilket säkerställer att varje synkronisering kan identifieras unikt och tillåter att kod ändringar mappas till konfigurations ändringar.

I det första avsnittet av det här arbets flödet anges att åtgärden utlöses *på* en *push* som innehåller `appsettings.json` till *huvud* grenen. Det andra avsnittet kör ett jobb som skapar en unik etikett för konfigurations uppdateringen baserat på commit hash. Jobbet uppdaterar sedan appens konfigurations instans med de nya värdena och den unika etiketten för uppdateringen.

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # Creates a label based on the branch name and the first 8 characters          
      # of the commit hash 
      - id: determine_label 
        run: echo ::set-output name=LABEL::"${GITHUB_REF#refs/*/}/${GITHUB_SHA:0:8}" 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          label: ${{ steps.determine_label.outputs.LABEL }} 
```

## <a name="use-azure-key-vault-with-github-action"></a>Använd Azure Key Vault med GitHub-åtgärd
Utvecklare som använder Azure Key Vault med AppConfiguration bör använda två separata filer, vanligt vis en appsettings.jspå och en secretreferences.js. secretreferences.jspå kommer att innehålla URL: en till Key Vault-hemligheten.

{"hemlig hemlighet": "{ \" URI \" : \" https://myKeyVault.vault.azure.net/secrets/mySecret "} "}

GitHub-åtgärden kan sedan konfigureras för att göra en strikt synkronisering på appsettings.js, följt av en icke-strikt synkronisering på secretreferences.js. Följande exempel utlöser en synkronisering när någon av filerna uppdateras:

```json
on:
  push:
    branches:
      - 'main'
    paths:
      - 'appsettings.json'
      - 'secretreferences.json'

jobs:
  syncconfig:
    runs-on: ubuntu-latest
    steps:
      # checkout done so that files in the repo can be read by the sync
      - uses: actions/checkout@v1
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'appsettings.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          strict: true
      - uses: azure/appconfiguration-sync@v1
        with:
          configurationFile: 'secretreferences.json'
          format: 'json'
          # Replace <ConnectionString> with the name of the secret in your repository
          connectionString: ${{ secrets.<ConnectionString> }}
          separator: ':'
          contentType: 'application/vnd.microsoft.appconfig.keyvaultref+json;charset=utf-8'

```

## <a name="use-max-depth-to-limit-github-action"></a>Använd maximalt djup för att begränsa GitHub-åtgärden
Standard beteendet för kapslade JSON-attribut är att förenkla hela objektet.  JSON nedan definierar detta nyckel/värde-par:

| Tangent | Värde |
| --- | --- |
| Objekt: inre: InnerKey | InnerValue |

```json
{ "Object": 
    { "Inner":
        {
        "InnerKey": "InnerValue"
        }
    }
}
```

Om det kapslade objektet är avsett att vara det värde som skickas till konfigurations instansen kan du använda *djup* -värdet för att stoppa förenklingen vid lämpligt djup. 

```json
on: 
  push: 
    branches: 
      - 'main' 
    paths: 
      - 'appsettings.json' 
 
jobs: 
  syncconfig: 
    runs-on: ubuntu-latest 
    steps: 
      # checkout done so that files in the repo can be read by the sync 
      - uses: actions/checkout@v1 
      - uses: azure/appconfiguration-sync@v1 
        with: 
          configurationFile: 'appsettings.json' 
          format: 'json' 
          # Replace <ConnectionString> with the name of the secret in your 
          # repository 
          connectionString: ${{ secrets.<ConnectionString> }}  
          separator: ':' 
          depth: 2 
```

Med ett djup på 2 returnerar exemplet ovan följande nyckel/värde-par:

| Tangent | Värde |
| --- | --- |
| Objekt: inre | {"InnerKey":"InnerValue"} |

## <a name="understand-action-inputs"></a>Förstå åtgärds inmatningar
Indataparametrar anger data som används av åtgärden under körning.  Följande tabell innehåller indataparametrar som har godkänts av app Configuration Sync och de förväntade värdena för var och en.  Mer information om åtgärds inmatningar för GitHub-åtgärder finns i GitHub- [dokumentationen](https://docs.github.com/en/free-pro-team@latest/actions/creating-actions/metadata-syntax-for-github-actions#inputs).

> [!Note]
> Indatamängds-ID: n är Skift läges okänsliga.


| Inmatat namn | Obligatoriskt? | Värde |
|----|----|----|
| configurationFile | Yes | Relativ sökväg till konfigurations filen i lagrings platsen.  BLOB mönster stöds och kan innehålla flera filer. |
| format | Yes | Fil format för konfigurations filen.  Giltiga format är: JSON, YAML, Properties. |
| Begär | Yes | Anslutnings sträng för app Configuration-instansen. Anslutnings strängen ska lagras som en hemlighet i GitHub-lagringsplatsen och endast det hemliga namnet ska användas i arbets flödet. |
| brytning | Yes | Avgränsning som används vid förenkling av konfigurations filen till nyckel/värde-par.  Giltiga värden är:. , ; : - _ __ / |
| protokollprefixet | No | Prefix som ska läggas till i början av nycklar. |
| etikett | No | Etikett som används vid inställning av nyckel/värde-par. Om inget anges används en null-etikett. |
| begränsade | No | Ett booleskt värde som anger om strikt läge är aktiverat. Standardvärdet är false. |
| djuplodande | No | Högsta djup för att förenkla konfigurations filen.  Djupet måste vara ett positivt tal.  Standardvärdet har inget max djup. |
| tags | No | Anger taggen som angetts för nyckel/värde-par.  Det förväntade formatet är en stringified form av ett JSON-objekt av följande form: {[propertyName: sträng]: String;} Varje egenskaps namn-värdet blir en tagg. |

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om GitHub-åtgärden för app Configuration-synkronisering och hur den kan användas för att automatisera uppdateringar av din konfigurations instans för appar. Fortsätt till nästa [artikel](./concept-app-configuration-event.md)om du vill lära dig hur Azure App-konfigurationen reagerar på ändringar i nyckel/värde-par.
