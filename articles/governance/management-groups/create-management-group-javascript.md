---
title: 'Snabbstart: Skapa en hanteringsgrupp med JavaScript'
description: I den här snabbstarten använder du JavaScript för att skapa en hanteringsgrupp för att organisera dina resurser i en resurshierarki.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom:
- devx-track-js
- mode-api
ms.openlocfilehash: 553133f30bd16543444356e8cda3e70458247b5c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536006"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Snabbstart: Skapa en hanteringsgrupp med JavaScript

Hanteringsgrupper är containrar som hjälper dig att hantera åtkomst, policyer och efterlevnad i flera prenumerationer. Skapa dessa containrar för att skapa en effektiv hierarki som kan användas [med Azure Policy](../policy/overview.md) och [Rollbaserade åtkomstkontroller i Azure.](../../role-based-access-control/overview.md) Mer information om hanteringsgrupper finns i [Organisera dina resurser med Azure-hanteringsgrupper.](overview.md)

Den första hanteringsgruppen som skapats i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera tjänsten för hanteringsgrupper i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i inledande [konfiguration av hanteringsgrupper.](./overview.md#initial-setup-of-management-groups)

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Innan du börjar kontrollerar du att minst version 12 av [Node.js](https://nodejs.org/) har installerats.

- Alla Azure AD-användare i klientorganisationen kan skapa en hanteringsgrupp utan den skrivbehörighet för hanteringsgruppen som tilldelats användaren om [hierarkiskydd](./how-to/protect-resource-hierarchy.md#setting---require-authorization) inte är aktiverat. Den här nya hanteringsgruppen blir underordnad rothanteringsgruppen eller standardhanteringsgruppen och skaparen får rolltilldelningen "Ägare". [](./how-to/protect-resource-hierarchy.md#setting---default-management-group) Med hanteringsgrupptjänsten kan du göra det så att rolltilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rothanteringsgruppen när den skapas. För att undvika att hitta de globala Azure AD-administratörerna för att börja använda hanteringsgrupper kan vi skapa de första hanteringsgrupperna på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Programkonfiguration

Om du vill aktivera JavaScript för att hantera hanteringsgrupper måste miljön konfigureras. Den här konfigurationen fungerar överallt där JavaScript kan användas, inklusive [bash på Windows 10](/windows/wsl/install-win10).

1. Konfigurera ett nytt Node.js projekt genom att köra följande kommando.

   ```bash
   npm init -y
   ```

1. Lägg till en referens till yargs-modulen.

   ```bash
   npm install yargs
   ```

1. Lägg till en referens till Azure Resource Graph modulen.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Lägg till en referens till Azure-autentiseringsbiblioteket.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Kontrollera att _package.jspå_ är `@azure/arm-managementgroups` version **1.1.0** eller senare och `@azure/ms-rest-nodeauth` är version **3.0.5** eller senare.

## <a name="create-the-management-group"></a>Skapa hanteringsgruppen

1. Skapa en ny fil _medindex.js_ och ange följande kod.

   ```javascript
   const argv = require("yargs").argv;
   const authenticator = require("@azure/ms-rest-nodeauth");
   const managementGroups = require("@azure/arm-managementgroups");

   if (argv.groupID && argv.displayName) {
       const createMG = async () => {
          const credentials = await authenticator.interactiveLogin();
          const client = new managementGroups.ManagementGroupsAPI(credentials);
          const result = await client.managementGroups.createOrUpdate(
             groupId: argv.groupID,
             {
                 displayName: argv.displayName
             }
          );
          console.log(result);
       };

       createMG();
   }
   ```

1. Ange följande kommando i terminalen:

   ```bash
   node index.js --groupID "<NEW_MG_GROUP_ID>" --displayName "<NEW_MG_FRIENDLY_NAME>"
   ```

   Se till att ersätta varje `<>` tokenplatshållare med ditt _hanteringsgrupps-ID_ respektive _eget namn_ för hanteringsgruppen.

   När skriptet försöker autentisera visas ett meddelande som liknar följande meddelande i terminalen:

   > Logga in genom att använda en webbläsare för att öppna sidan och ange koden https://microsoft.com/devicelogin FGB56WJUGK för att autentisera.

   När du har autentiserat i webbläsaren fortsätter skriptet att köras.

Resultatet av att skapa hanteringsgruppen är utdata till konsolen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort de installerade biblioteken från programmet kör du följande kommando.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten skapade du en hanteringsgrupp för att organisera resurshierarkin. Hanteringsgruppen kan innehålla prenumerationer eller andra hanteringsgrupper.

Om du vill veta mer om hanteringsgrupper och hur du hanterar din resurshierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanteringsgrupper](./manage.md)
