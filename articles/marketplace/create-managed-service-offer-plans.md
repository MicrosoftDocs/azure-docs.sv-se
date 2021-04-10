---
title: Skapa planer för ditt hanterade tjänst erbjudande på Azure Marketplace
description: Lär dig hur du skapar planer för ditt hanterade tjänst erbjudande på Azure Marketplace med Microsoft Partner Center.
author: Microsoft-BradleyWright
ms.author: brwrigh
ms.reviewer: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 12/23/2020
ms.openlocfilehash: 9b5526af03bdbefeb54633c49bbd43743555f60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383236"
---
# <a name="how-to-create-plans-for-your-managed-service-offer"></a>Skapa planer för ditt hanterade tjänst erbjudande

Hanterade tjänst erbjudanden som säljs via Microsofts kommersiella marknads plats måste ha minst en plan. Du kan skapa flera olika planer med olika alternativ i samma erbjudande. Dessa planer (kallas ibland SKU: er) kan variera beroende på version, monetarisering eller tjänst nivåer. Detaljerad vägledning om planer finns i [planer och priser för kommersiella Marketplace-erbjudanden](./plans-pricing.md).

## <a name="create-a-plan"></a>Skapa en plan

1. På fliken **plan översikt** i ditt erbjudande i Partner Center väljer du **+ Skapa ny plan**.
2. I dialog rutan som visas under **plan-ID** anger du ett unikt plan-ID. Använd upp till 50 gemena alfanumeriska tecken, bindestreck eller under streck. Du kan inte ändra plan-ID när du har valt **skapa**. Detta ID är synligt för dina kunder.
3. Ange ett unikt namn för den här planen i rutan **planerat namn** . Använd högst 50 tecken. Det här namnet visas för dina kunder.
4. Välj **Skapa**.

## <a name="define-the-plan-listing"></a>Definiera plan listan

På fliken **plan lista** definierar du plan namnet och beskrivningen som du vill att de ska visas på den kommersiella Marketplace.

1. I rutan **plan namn** visas det namn du angav tidigare för den här planen. Du kan ändra det när du vill. Det här namnet kommer att visas på den kommersiella marknads platsen som rubrik för ditt erbjudandes abonnemang.
2. I rutan **plan Sammanfattning** anger du en kort beskrivning av din plan, som kan användas i Sök resultaten för Marketplace.
3. I rutan **plan Beskrivning** förklarar du vad som gör den här planen unik och skiljer sig från andra planer i erbjudandet.
4. Välj **Spara utkast** innan du fortsätter till nästa flik.

## <a name="define-pricing-and-availability"></a>Definiera priser och tillgänglighet

Den enda pris modell som är tillgänglig för hanterade tjänst erbjudanden är **Bring Your Own License (BYOL)**. Det innebär att du fakturerar dina kunder direkt för kostnader som rör erbjudandet, och Microsoft debiterar inte några avgifter.

Du kan konfigurera varje plan så att den blir synlig för alla (offentlig) eller endast till en speciell publik (privat).

> [!NOTE]
> Privata planer stöds inte med prenumerationer som upprättats via en åter försäljare av ett CSP-program (Cloud Solution Provider).

> [!IMPORTANT]
> När en plan har publicerats som offentlig kan du inte ändra den till privat. Använd en privat plan för att kontrol lera vilka kunder som kan acceptera ditt erbjudande och delegera resurser. Med en offentlig plan kan du inte begränsa tillgänglighet till vissa kunder eller till och med ett visst antal kunder (även om du kan sluta sälja planen helt om du väljer att göra det). Du kan ta bort åtkomsten till en delegering efter att en kund har accepterat ett erbjudande endast om du har inkluderat en auktorisering med roll definitionen för tilldelning av hanterade tjänster för registrering av hanterade tjänster när du publicerade erbjudandet. Du kan också kontakta kunden och be dem att ta bort åtkomsten.

## <a name="make-your-plan-public"></a>Gör din plan offentlig

1. Under **plan synlighet** väljer du **offentlig**.
2. Välj **Spara utkast**. Om du vill gå tillbaka till fliken plan översikt väljer du **plan översikt** i det övre vänstra hörnet.
3. Om du vill skapa ett annat schema för det här erbjudandet väljer du **+ skapa en ny plan** på fliken **plan översikt** .

## <a name="make-your-plan-private"></a>Gör din plan privat

Du beviljar åtkomst till en privat plan med hjälp av Azures prenumerations-ID. Du kan lägga till högst 10 prenumerations-ID: n manuellt eller upp till 10 000 prenumerations-ID med hjälp av en. CSV-fil.

Så här lägger du till upp till 10 prenumerations-ID: n manuellt:

1. Under **plan synlighet** väljer du **privat**.
2. Ange ID för Azure-prenumerationen för den mål grupp som du vill bevilja åtkomst till.
3. Alternativt kan du ange en beskrivning av mål gruppen i rutan **Beskrivning** .
4. Om du vill lägga till ett annat ID väljer du **Lägg till ID (max 10)**.
5. När du är klar med att lägga till ID: n väljer du **Spara utkast**.

Om du vill lägga till upp till 10 000 prenumerations-ID: n med en. CSV-fil:

1. Under **plan synlighet** väljer du **privat**.
2. Välj länken **Exportera mål grupp (CSV)** . Då hämtas en. CSV-fil.
3. Öppna. CSV-fil. I kolumnen **ID** anger du de ID: n för Azure-prenumerationen som du vill bevilja åtkomst till.
4. I kolumnen **Beskrivning**   har du möjlighet att lägga till en beskrivning för varje post.
5. I kolumnen **typ**   lägger du till **SubscriptionId**   till varje rad som har ett ID.
6. Spara filen som en. CSV-fil.
7. I Partner Center väljer du länken **Importera mål grupp (CSV)** .
8. I ****   dialog rutan Bekräfta väljer du **Ja** och laddar upp. CSV-fil.
9. Välj **Spara utkast**.

## <a name="technical-configuration"></a>Teknisk konfiguration

Det här avsnittet skapar ett manifest med auktoriseringsinformation för att hantera kund resurser. Den här informationen krävs för att aktivera [Azure-delegerad resurshantering](../lighthouse/concepts/azure-delegated-resource-management.md).

Granska [klienter, roller och användare i Azure Lighthouse-scenarier](../lighthouse/concepts/tenants-users-roles.md#best-practices-for-defining-users-and-roles) för att förstå vilka roller som stöds och de bästa metoderna för att definiera dina auktoriseringar.

> [!NOTE]
> Användare och roller i dina auktoriseringsposter gäller alla kunder som aktiverar planen. Om du vill begränsa åtkomsten till en specifik kund måste du publicera en privat plan för exklusiv användning.

### <a name="manifest"></a>Manifest

1. Under **manifest** anger du en **version** för manifestet. Använd formatet n. n. n (till exempel 1.2.5).
2. Ange ditt **klient-ID**. Detta är ett GUID som är kopplat till organisationens ID för Azure Active Directory (Azure AD). det vill säga den hanterings klient som du kommer att få åtkomst till dina kunders resurser. Om du inte har det här är praktiskt kan du hitta det genom att hovra över ditt konto namn längst upp till höger i Azure Portal eller genom att välja **Växla katalog**.

Om du publicerar en ny version av erbjudandet och behöver skapa ett uppdaterat manifest väljer du **+ nytt manifest**. Se till att öka versions numret från den tidigare manifest versionen.

### <a name="authorizations"></a>Auktoriseringar

Auktoriseringarna definierar de entiteter i din hanterings klient som har åtkomst till resurser och prenumerationer för kunder som köper planen. Var och en av dessa entiteter tilldelas en inbyggd roll som ger vissa åtkomst nivåer.

Du kan skapa upp till 20 auktoriseringar för varje plan.

> [!TIP]
> I de flesta fall vill du tilldela roller till en Azure AD-användargrupp eller tjänstens huvud namn i stället för till en serie med enskilda användar konton. På så sätt kan du lägga till eller ta bort åtkomst för enskilda användare utan att behöva uppdatera och publicera om planen när dina åtkomst krav ändras. När du tilldelar roller till Azure AD-grupper [ska grupp typen vara säkerhet och inte Office 365](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Ytterligare rekommendationer finns i [klienter, roller och användare i Azure Lighthouse-scenarier](../lighthouse/concepts/tenants-users-roles.md).

För varje auktorisering måste du ange följande information. Du kan sedan välja **+ Lägg till auktorisering** så många gånger som behövs för att lägga till fler användare och roll definitioner.

* **AAD-objekt-ID**: Azure AD-identifieraren för en användare, användar grupp eller ett program som ska beviljas vissa behörigheter (enligt definitionen i roll definitionen) till dina kunders resurser.
* **AAD-objektets visnings namn**: ett eget namn som hjälper kunden att förstå syftet med den här auktoriseringen. Kunden ser det här namnet när resurser delegeras.
* **Roll definition**: Välj en av de tillgängliga inbyggda Azure AD-rollerna i listan. Den här rollen avgör vilka behörigheter användaren i fältet **ägar-ID** kommer att ha på dina kunders resurser. Beskrivningar av dessa roller finns [inbyggda roller](../role-based-access-control/built-in-roles.md) och [roll stöd för Azure-Lighthouse](../lighthouse/concepts/tenants-users-roles.md#role-support-for-azure-lighthouse).

> [!NOTE]
> När så är tillämpligt nya inbyggda roller läggs de till i Azure. de blir tillgängliga här. Det kan uppstå en fördröjning innan de visas.

* **Tilldelnings bara roller**: det här alternativet visas bara om du har valt administratör för användar åtkomst i **roll definitionen** för den här auktoriseringen. I så fall måste du lägga till en eller flera tilldelnings bara roller här. Användaren i fältet **Azure AD-objekt-ID** kommer att kunna tilldela dessa roller till hanterade identiteter, vilket krävs för att [distribuera principer som kan åtgärdas](../lighthouse/how-to/deploy-policy-remediation.md). Inga andra behörigheter som vanligt vis är kopplade till rollen administratör för användar åtkomst gäller för den här användaren.

> [!TIP]
> För att se till att du kan [ta bort åtkomsten till en delegering](../lighthouse/how-to/remove-delegation.md) , måste du ta med en **auktorisering** med **roll definitionen** inställd på [ta bort roll tilldelning för hanterade tjänster](../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role). Om den här rollen inte är tilldelad kan delegerade resurser bara tas bort av en användare i kundens klient organisation.

När du har slutfört alla avsnitt för din plan kan du välja **+ Skapa ny plan** för att skapa ytterligare planer. När du är klar väljer du **Spara utkast** innan du fortsätter.

## <a name="next-steps"></a>Nästa steg

* [Granska och publicera](review-publish-offer.md)
