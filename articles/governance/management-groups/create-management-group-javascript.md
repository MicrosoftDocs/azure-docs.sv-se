---
title: 'Snabb start: skapa en hanterings grupp med Java Script'
description: I den här snabb starten använder du Java Script för att skapa en hanterings grupp för att organisera resurserna i en resurspool.
ms.date: 02/05/2021
ms.topic: quickstart
ms.custom: devx-track-js
ms.openlocfilehash: 99a2ed406d4837f6fc346e68b3b400003feed38c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100099157"
---
# <a name="quickstart-create-a-management-group-with-javascript"></a>Snabb start: skapa en hanterings grupp med Java Script

Hanterings grupper är behållare som hjälper dig att hantera åtkomst, principer och efterlevnad över flera prenumerationer. Skapa de här behållarna för att skapa en effektiv och effektiv hierarki som kan användas med [Azure policy](../policy/overview.md) och [Azure Role-baserade åtkomst kontroller](../../role-based-access-control/overview.md). Mer information om hanterings grupper finns i [ordna dina resurser med Azures hanterings grupper](overview.md).

Den första hanterings gruppen som skapas i katalogen kan ta upp till 15 minuter att slutföra. Det finns processer som körs första gången för att konfigurera hanterings grupps tjänsten i Azure för din katalog. Du får ett meddelande när processen är klar. Mer information finns i [den första installationen av hanterings grupper](./overview.md#initial-setup-of-management-groups).

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

- Innan du börjar kontrollerar du att minst version 12 av [Node.js](https://nodejs.org/) är installerad.

- Alla Azure AD-användare i klient organisationen kan skapa en hanterings grupp utan den Skriv behörighet för hanterings gruppen som tilldelats den användaren om du inte har aktiverat [skydd av hierarkin](./how-to/protect-resource-hierarchy.md#setting---require-authorization) . Den nya hanterings gruppen blir underordnad rot hanterings gruppen eller [standard hanterings gruppen](./how-to/protect-resource-hierarchy.md#setting---default-management-group) och skaparen tilldelas rollen "ägare". Hanterings grupp tjänsten tillåter den här möjligheten så att roll tilldelningar inte behövs på rotnivå. Inga användare har åtkomst till rot hanterings gruppen när den skapas. För att undvika att Hurdle för att hitta Azure AD global-administratörer ska börja använda hanterings grupper, tillåter vi att de första hanterings grupperna skapas på rotnivå.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="application-setup"></a>Programkonfiguration

Om du vill aktivera Java Script för att hantera hanterings grupper måste miljön konfigureras. Den här installationen fungerar där Java Script kan användas, inklusive [bash i Windows 10](/windows/wsl/install-win10).

1. Skapa ett nytt Node.js-projekt genom att köra följande kommando.

   ```bash
   npm init -y
   ```

1. Lägg till en referens till yargs-modulen.

   ```bash
   npm install yargs
   ```

1. Lägg till en referens till Azure-resursens diagram-modul.

   ```bash
   npm install @azure/arm-managementgroups
   ```

1. Lägg till en referens till Azure Authentication-biblioteket.

   ```bash
   npm install @azure/ms-rest-nodeauth
   ```

   > [!NOTE]
   > Kontrol lera att _package.jspå_ `@azure/arm-managementgroups` är version **1.1.0** eller högre och att den `@azure/ms-rest-nodeauth` är version **3.0.5** eller högre.

## <a name="create-the-management-group"></a>Skapa hanterings gruppen

1. Skapa en ny fil med namnet _index.js_ och ange följande kod.

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

   Se till att ersätta varje `<>` plats hållare med _hanterings gruppens ID_ och _eget namn för hanterings gruppen_.

   När skriptet försöker autentisera visas ett meddelande som liknar följande meddelande i terminalen:

   > Logga in genom att använda en webbläsare för att öppna sidan https://microsoft.com/devicelogin och ange koden FGB56WJUGK för att autentisera.

   När du har autentiserat i webbläsaren fortsätter skriptet att köras.

Resultatet av att skapa hanterings gruppen är utdata till-konsolen.

## <a name="clean-up-resources"></a>Rensa resurser

Kör följande kommando om du vill ta bort de installerade biblioteken från programmet.

```bash
npm uninstall @azure/arm-managementgroups @azure/ms-rest-nodeauth yargs
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en hanterings grupp för att organisera din resurspool. Hanterings gruppen kan innehålla prenumerationer eller andra hanterings grupper.

Om du vill veta mer om hanterings grupper och hur du hanterar din resurs-hierarki fortsätter du till:

> [!div class="nextstepaction"]
> [Hantera dina resurser med hanterings grupper](./manage.md)