---
title: Testa enhetens tekniska konfiguration, Microsofts kommersiella marknads plats
description: Lär dig mer om test enheter. Med test enheter kan nya kunder testa ditt erbjudande innan de genomför köpet.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 08/13/2019
author: trkeya
ms.author: trkeya
ms.openlocfilehash: 553eba3898a5ea42e5d478603e35e82c68abcab1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96462895"
---
# <a name="test-drive-technical-configuration"></a>Teknisk konfiguration för provkörning

Med alternativet testa enhet på Microsofts kommersiella marknads plats kan du konfigurera en praktisk guidad visning av produktens viktiga funktioner. Med en testen het kan nya kunder prova ditt erbjudande innan de genomför köpet. Mer information finns i [Vad är Test Drive?](what-is-test-drive.md)

Om du inte längre vill ange en testen het för ditt erbjudande går du tillbaka till sidan **erbjudande konfiguration** och avmarkerar kryss rutan **Aktivera test enhet** . Det finns inte någon test enhet tillgänglig för alla erbjudande typer.

## <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testen het

Detta är den enda alternativet test enhet för virtuell dator eller Azure App-erbjudanden och kräver en ganska detaljerad installation. Läs avsnitten nedan för [information om distributions prenumeration](#deployment-subscription-details) och [test av enhets listor](#test-drive-listings), och fortsätt sedan med det separata avsnittet för [Azure Resource Manager konfiguration av test enhet](azure-resource-manager-test-drive.md).

## <a name="hosted-test-drive"></a>Värdbaserad testen het

Microsoft kan ta bort komplexiteten för att konfigurera en testen het genom att vara värd för och underhålla tjänst etableringen och avetablering. Konfigurationen för den här typen av test enhet är samma oavsett om test enheten är riktad mot en Dynamics 365-kund engagemang eller Dynamics 365-drifts grupp.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. Varje samtidig användare förbrukar en Dynamics 365-licens medan test enheten är aktiv, så se till att du har tillräckligt med licenser för att stödja Max gränsen. Det rekommenderade värdet är 3-5.

- **Test enhetens varaktighet** (krävs) – ange antalet timmar som test enheten ska vara aktiv för varje kund. Efter den här perioden kommer sessionen att avslutas och använder inte längre någon av dina licenser. Vi rekommenderar ett värde på 2-24 timmar beroende på hur komplex ditt erbjudande är. Denna varaktighet får bara anges i hela timmar (till exempel "2 timmar" är giltig; "1,5 timmar" är inte). Användaren kan begära en ny session om de får slut på tid och vill komma åt test enheten igen.

- **Instans-URL** (obligatorisk) – URL: en där kunden kommer att påbörja sin test-enhet. Normalt är URL: en för din Dynamics 365-instans som kör appen med exempel data installerat (till exempel `https://testdrive.crm.dynamics.com` ).

- **URL för instans webb-API** (krävs) – Hämta URL: en för webb-API för din Dynamics 365-instans genom att logga in på ditt Microsoft 365-konto och navigera till **Inställningar**  >  **anpassning** av  >  **utvecklare resurser**  >  **instans webb-API (tjänstens rot-URL)**, kopiera webb adressen som finns här (till exempel `https://testdrive.crm.dynamics.com/api/data/v9.0` ).

- **Rollnamn** (obligatoriskt) – Ange namnet på den säkerhets roll som du har definierat i din anpassade Dynamics 365-testenhet, som kommer att tilldelas användaren under deras test enhet (till exempel Test-Drive-roll).

Om du behöver hjälp med att konfigurera din Dynamics 365-miljö för Test Drive och bevilja AppSource-behörighet för att etablera och avetablera användare av test enheter i din klient, följer du [dessa anvisningar](./test-drive-azure-subscription-setup.md).

Steg för steg-instruktioner om hur du registrerar och konfigurerar den värdbaserade test enheten finns på sidan [detaljerad konfiguration för den värdbaserade test enheten](./test-drive-hosted-detailed-config.md) .

## <a name="logic-app-test-drive"></a>Test enhet för Logic app

Den här typen av test enhet är inte Microsoft-värd. Använd den för att ansluta till ett erbjudande för Dynamics 365 eller en annan anpassad resurs som omfattar en mängd olika komplexa lösnings arkitekturer. Mer information om hur du konfigurerar Logi Kap par test enheter finns i [åtgärder](https://github.com/Microsoft/AppSource/blob/master/Setup-your-Azure-subscription-for-Dynamics365-Operations-Test-Drives.md) och [kund engagemang](https://github.com/Microsoft/AppSource/wiki/Setting-up-Test-Drives-for-Dynamics-365-app) på GitHub.

- **Region** (obligatorisk listruta för enskild markering) – för närvarande finns det 26 Azure-regioner som stöds där din test enhet kan göras tillgänglig. Resurserna för din Logic-app distribueras i den region som du väljer. Om din Logi Kap par innehåller anpassade resurser som lagras i en viss region, se till att regionen är vald här. Det bästa sättet är att helt distribuera din Logic app lokalt på din Azure-prenumeration i portalen och kontrol lera att den fungerar korrekt innan du gör det här valet.

- **Maximalt antal samtidiga test enheter** (krävs) – Ange det maximala antalet kunder som kan använda test enheten samtidigt. De här test enheterna har redan distribuerats, vilket gör det möjligt för kunder att komma åt dem direkt utan att vänta på en distribution.

- **Test enhetens varaktighet** (krävs) – ange hur lång tid test enheten ska vara aktiv, i antal timmar. Test enheten avslutas automatiskt när den här tids perioden är slut.

- **Namn på Azure-resurs grupp** (obligatoriskt) – Ange namnet på den [Azure-resurs grupp](../azure-resource-manager/management/overview.md#resource-groups)) där din Logic app-testenhet sparas.

- **Azure Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som tilldelar användaren till användaren. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

- **Avetablera Logic app-namn** (obligatoriskt) – Ange namnet på den Logic-app som avetablerar test enheten när kunden är färdig. Den här Logic-appen måste sparas i gruppen Azure-resurser ovan.

## <a name="power-bi-test-drive"></a>Power BI testen het

Produkter som vill demonstrera ett interaktivt Power BI visuellt objekt kan använda en inbäddad länk för att dela en anpassad instrument panel som test enhet, ingen ytterligare teknisk konfiguration krävs. Allt du behöver göra här är att ladda upp din inbäddade Power BI-URL.

Mer information om hur du konfigurerar Power BI appar finns i [Vad är Power BI appar?](/power-bi/service-template-apps-overview)

## <a name="deployment-subscription-details"></a>Information om distributions prenumeration

Om du vill att Microsoft ska kunna distribuera test enheten åt dig skapar du och tillhandahåller en separat, unik Azure-prenumeration (krävs inte för Power BI test enheter).

- **ID för Azure-prenumeration** (krävs för Azure Resource Manager-och logi Kap par) – ange prenumerations-ID för att bevilja åtkomst till dina Azure-Kontotjänster för rapportering och fakturering av resursanvändning. Vi rekommenderar att du [skapar en separat Azure-prenumeration](../cost-management-billing/manage/create-subscription.md) som ska användas för test enheter om du inte redan har en. Du hittar ditt prenumerations-ID för Azure genom att logga in på [Azure Portal](https://portal.azure.com/) och navigera till fliken **prenumerationer** på den vänstra menyn. Om du väljer fliken visas ditt prenumerations-ID (till exempel "a83645ac-1234-5AB6-6789-1h234g764ghty").

- **Azure AD-klient-ID** (krävs) – ange ditt Azure Active Directory (AD) [klient organisations-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Egenskaper** och letar efter **katalog-ID** -nummer som anges (till exempel 50c464d3-4930-494c-963c-1e951d15360e). Du kan också leta upp organisationens klient-ID med din domän namns adress på [https://www.whatismytenantid.com](https://www.whatismytenantid.com) .

- **Namn på Azure AD-klient** (krävs för dynamisk 365) – ange ditt Azure Active Directory (AD) namn. Du hittar det här namnet genom att logga in på [Azure Portal](https://portal.azure.com/)i det övre högra hörnet ditt klient namn visas under ditt konto namn.

- **ID för Azure AD-App** (krävs) – ange ditt Azure Active Directory (AD) [program-ID](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)). Om du vill hitta detta ID loggar du in på [Azure Portal](https://portal.azure.com/), väljer fliken Active Directory på den vänstra menyn, väljer **Appregistreringar** och letar sedan efter det **program-ID-** nummer som anges (till exempel `50c464d3-4930-494c-963c-1e951d15360e` ).

- **Azure AD-appens klient hemlighet** (krävs) – Ange din Azure AD-program [klient hemlighet](../active-directory/develop/howto-create-service-principal-portal.md#option-2-create-a-new-application-secret)). Du hittar det här värdet genom att logga in på [Azure Portal](https://portal.azure.com/). Välj fliken **Azure Active Directory** på den vänstra menyn, Välj **Appregistreringar** och välj sedan appen Test Drive. Välj sedan **certifikat och hemligheter**, Välj **ny klient hemlighet**, ange en beskrivning, Välj **aldrig** under **upphör ande** och välj **Lägg till**. Glöm inte att kopiera värdet. Du behöver inte gå bort från sidan innan du kopierar värdet.

## <a name="test-drive-listings"></a>Testa enhets listor

Alternativet **test enhets listor** på fliken **testenhet** i Partner Center visar de språk (och marknader) där test enheten är tillgänglig, för närvarande är engelska (USA) den enda tillgängliga platsen. Dessutom visar den här sidan status för den språkspecifika listan och datum/tid då den lades till. Du måste definiera information om test enheten (beskrivning, Användar handbok, videor osv.) för varje språk/marknad.

- **Beskrivning** (krävs): Beskriv test enheten, vad som visas, mål för användaren att experimentera med, vilka funktioner som ska utforskas och all relevant information som hjälper användaren att avgöra om ditt erbjudande ska erhållas. Upp till 3 000 tecken text kan anges i det här fältet.

- **Åtkomst information** (krävs för Azure Resource Manager-och logik test enheter): förklara vad en kund behöver veta för att få åtkomst till och använda den här test enheten. Gå igenom ett scenario för att använda ditt erbjudande och exakt vad kunden bör känna till för att få åtkomst till funktioner i hela test enheten. Upp till 10 000 tecken text kan anges i det här fältet.

- **Användar handbok** (krävs): en djupgående genom gång av test enhets upplevelsen. Användar handboken bör bestå av exakt vad du vill att kunden ska få från att använda test enheten och fungera som referens för alla frågor som de kan ha. Filen måste vara i PDF-format och ha namnet (högst 255 tecken) efter överföringen.

- **Videor: Lägg till videor** (valfritt): videor som finns på andra platser kan refereras till med en länk och miniatyr bild (533 x 324 bild punkter) så att en kund kan se en genom gång av information för att hjälpa dem att bättre förstå test enheten, inklusive hur du kan använda funktionerna i ditt erbjudande och förstå scenarier som framhäver deras fördelar.
  - **Namn** (obligatoriskt)
  - **URL** (YouTube eller Vimeo, krävs)
  - **Miniatyr** bild (533 x 324 bild punkter) – bilden måste vara i PNG-format.

Om du för närvarande skapar test enheten i Partner Center väljer du **Spara utkast** innan du fortsätter.

Steg för steg-instruktioner om hur du registrerar och konfigurerar den värdbaserade test enheten finns på sidan [detaljerad konfiguration för den värdbaserade test enheten](./test-drive-hosted-detailed-config.md) .

## <a name="additional-resources"></a>Ytterligare resurser

## <a name="next-steps"></a>Nästa steg

- [Metod tips för test enhet](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Översikt](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF, se till att blockering av popup-fönster är inaktiverat)
- [Uppdatera ett befintligt erbjudande i den kommersiella Marketplace](partner-center-portal/update-existing-offer.md)