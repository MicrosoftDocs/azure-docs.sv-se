---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: f74d4cbc17e49345534a37e9e6612a36e19be295
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "73903686"
---
Men om du vill dela avbildningar utanför Azure-klienten, i skala, bör du skapa en app-registrering för att under lätta delningen.  Genom att använda en app-registrering kan du aktivera mer komplexa delnings scenarier, till exempel: 

* Hantera delade avbildningar när ett företag förvärvar ett annat och Azure-infrastrukturen sprids mellan olika klienter. 
* Azure-partner hanterar Azure-infrastruktur för kundernas räkning. Anpassning av avbildningar görs i partner innehavaren, men infrastruktur distributioner sker i kundens klient. 


## <a name="create-the-app-registration"></a>Skapa registrerings appen

Skapa en program registrering som ska användas av båda klienterna för att dela avbildnings Galleri resurserna.
1. Öppna [Appregistreringar (för hands version) i Azure Portal](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade/quickStartType//sourceType/).    
1. Välj **ny registrering** på menyn längst upp på sidan.
1. Skriv *myGalleryApp* i **namn**.
1. I **konto typer som stöds** väljer du **konton i valfri organisations katalog och personliga Microsoft-konton**.
1. I **omdirigerings-URI** skriver *https://www.microsoft.com* du och väljer sedan **Registrera**. När appens registrering har skapats öppnas översikts sidan.
1. På sidan Översikt kopierar du **programmet (klienten) ID** och sparar för användning senare.   
1. Välj **certifikat & hemligheter** och välj sedan **ny klient hemlighet**.
1. I **Beskrivning** skriver du *Shared Image Gallery Cross-Tenant app Secret*.
1. Lämna **förfaller**, lämna standardvärdet på **1 år** och välj sedan **Lägg till**.
1. Kopiera värdet för hemligheten och spara det på en säker plats. Du kan inte hämta det när du har lämnat sidan.


Ge appens registrerings behörighet att använda det delade avbildnings galleriet.
1. I Azure Portal väljer du det delade avbildnings galleri som du vill dela med en annan klient.
1. Välj **Välj åtkomst kontroll (IAM)** och under **Lägg till roll tilldelning** väljer du *Lägg till*. 
1. Välj **läsare** under **roll**.
1. Under **tilldela åtkomst till:**, lämna det här som **Azure AD-användare,-grupp eller tjänstens huvud namn**.
1. Under **Välj** skriver du *myGalleryApp* och väljer den när den visas i listan. När du är klar väljer du **Spara**.


## <a name="give-tenant-2-access"></a>Ge klient organisation 2 åtkomst

Ge klient organisationen 2 åtkomst till programmet genom att begära inloggning med hjälp av en webbläsare. Ersätt *\<Tenant2 ID>* med klient organisations-ID: t för den klient som du vill dela ditt avbildnings galleri med. Ersätt *\<Application (client) ID>* med program-ID: t för den app-registrering som du skapade. När du är klar med ersättningarna klistrar du in webb adressen i en webbläsare och följer inloggnings anvisningarna för att logga in på klient 2.

```
https://login.microsoftonline.com/<Tenant 2 ID>/oauth2/authorize?client_id=<Application (client) ID>&response_type=code&redirect_uri=https%3A%2F%2Fwww.microsoft.com%2F 
```

I [Azure Portal](https://portal.azure.com) loggar du in som klient 2 och ger appens registrerings åtkomst till den resurs grupp där du vill skapa den virtuella datorn.

1. Välj resurs gruppen och välj sedan **åtkomst kontroll (IAM)**. Under **Lägg till roll tilldelning** väljer du **Lägg till**. 
1. Under **roll** skriver du **Contributor**.
1. Under **tilldela åtkomst till:**, lämna det här som **Azure AD-användare,-grupp eller tjänstens huvud namn**.
1. Under **Välj** typ *myGalleryApp* väljer du den när den visas i listan. När du är klar väljer du **Spara**.

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.

