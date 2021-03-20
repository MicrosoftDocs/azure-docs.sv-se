---
title: Exportera etablerings konfigurationen och återställa till ett känt fungerande tillstånd för haveri beredskap
description: Lär dig hur du exporterar etablerings konfigurationen och återställer till ett känt fungerande tillstånd för haveri beredskap.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256381"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Anvisningar: exportera etablerings konfigurationen och återställa till ett känt fungerande tillstånd

I den här artikeln får du lära dig att:

- Exportera och importera etablerings konfigurationen från Azure Portal
- Exportera och importera etablerings konfigurationen med hjälp av Microsoft Graph API

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Exportera och importera etablerings konfigurationen från Azure Portal

### <a name="export-your-provisioning-configuration"></a>Exportera etablerings konfigurationen

Så här exporterar du konfigurationen:

1. Välj **Azure Active Directory** på den vänstra navigerings panelen i [Azure Portal](https://portal.azure.com/).
1. I fönstret **Azure Active Directory** väljer du **företags program** och väljer ditt program.
1. I det vänstra navigerings fönstret väljer du **etablering**. På sidan etablerings konfiguration klickar du på **mappningar för attribut**, sedan på **Visa avancerade alternativ** och slutligen på **granska schemat**. Det tar dig till schema redigeraren.
1. Klicka på Hämta i kommando fältet längst upp på sidan för att ladda ned schemat.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Haveri beredskap – återställning till ett känt fungerande tillstånd

Genom att exportera och spara konfigurationen kan du återställa till en tidigare version av din konfiguration. Vi rekommenderar att du exporterar etablerings konfigurationen och sparar den för senare användning när du gör en ändring i attributets mappningar eller omfångs filter. Allt du behöver göra är att öppna JSON-filen som du laddade ned i stegen ovan, kopiera hela innehållet i JSON-filen, ersätta hela innehållet i JSON-nyttolasten i schema redigeraren och sedan Spara. Om det finns en aktiv etablerings cykel slutförs den och nästa cykel kommer att använda det uppdaterade schemat. Nästa cykel kommer också att vara en första cykel som utvärderar varje användare och grupp baserat på den nya konfigurationen. Tänk på följande när du återställer till en tidigare konfiguration:

- Användarna kommer att utvärderas igen för att avgöra om de ska vara i omfånget. Om omfångs filtren har ändrat att en användare inte är inom omfånget kommer de att inaktive ras. Detta är det önskade beteendet i de flesta fall, men det finns tillfällen då du kanske vill förhindra detta och kan använda funktionen [hoppa över borttagning av omfattning](./skip-out-of-scope-deletions.md) . 
- Om du ändrar etablerings konfigurationen startas tjänsten om och en [första cykel](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental)utlöses.

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>Exportera och importera etablerings konfigurationen med hjälp av Microsoft Graph API

Du kan använda Microsoft Graph-API: t och Microsoft Graph Explorer för att exportera mappningar och schemat för användar etablerings attribut till en JSON-fil och importera tillbaka dem till Azure AD. Du kan också använda stegen som beskrivs här för att skapa en säkerhets kopia av etablerings konfigurationen.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>Steg 1: Hämta etablerings App Serviceens huvud-ID (objekt-ID)

1. Starta [Azure Portal](https://portal.azure.com)och navigera till avsnittet Egenskaper i ditt etablerings program. Om du till exempel vill exportera en *arbets dag till AD User Provisioning* -programmappning navigerar du till avsnittet Egenskaper i appen.
1. I avsnittet Egenskaper i din etablerings app kopierar du det GUID-värde som är associerat med fältet *objekt-ID* . Det här värdet kallas även **ServicePrincipalId** för din app och används i Microsoft Graph Explorer-åtgärder.

   ![Workday App Service huvud-ID](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Steg 2: Logga in på Microsoft Graph Explorer

1. Starta [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)
1. Klicka på knappen "logga in med Microsoft" och logga in med Azure AD global admin-eller app admin-autentiseringsuppgifter.

    ![Microsoft Graph inloggning](./media/export-import-provisioning-configuration/wd_export_02.png)

1. Vid lyckad inloggning visas användar konto informationen i den vänstra rutan.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>Steg 3: Hämta etablerings jobb-ID: t för etablerings appen

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] med **servicePrincipalId** som extraherats från [steg 1](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id).

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Du får ett svar på det sätt som visas nedan. Kopiera "ID-attributet" som finns i svaret. Det här värdet är **ProvisioningJobId** och kommer att användas för att hämta underliggande metadata för schemat.

   [![Jobb-ID för etablering](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>Steg 4: Ladda ned etablerings schema

Kör följande GET-fråga i Microsoft Graph Explorer och Ersätt [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtades i föregående steg.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopiera JSON-objektet från svaret och spara det i en fil för att skapa en säkerhets kopia av schemat.

### <a name="step-5-import-the-provisioning-schema"></a>Steg 5: importera etablerings schema

> [!CAUTION]
> Utför bara det här steget om du behöver ändra schemat för konfigurationen som inte kan ändras med hjälp av Azure Portal eller om du behöver återställa konfigurationen från en tidigare säkerhetskopierad fil med ett giltigt och fungerande schema.

I Microsoft Graph Explorer konfigurerar du följande skicka fråga, ersätter [servicePrincipalId] och [ProvisioningJobId] med ServicePrincipalId och ProvisioningJobId som hämtades i föregående steg.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

På fliken "begär ande text" kopierar du innehållet i JSON-schemafilen.

   [![Begärandetext](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

På fliken "begär ande rubriker" lägger du till attributet Content-Type Head med värdet "Application/JSON"

   [![Begärandehuvuden](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Välj **Kör fråga** för att importera det nya schemat.