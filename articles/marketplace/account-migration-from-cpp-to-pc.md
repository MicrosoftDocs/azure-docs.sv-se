---
title: Migrera konton från Cloud Partner Portal till Microsofts kommersiella marknads platser
description: Lär dig hur du migrerar ditt konto från Cloud Partner Portal till Partner Center på Microsoft Commercial Marketplace för Azure
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: f43bdd0ec6654ba2fc8a04dbb62d2d658e06ce0f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108499"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Så här migrerar du ditt konto från Cloud Partner Portal till Partner Center

Om du har ett befintligt Cloud Partner Portal-konto (CPP) måste dina konto inställningar migreras till Partner Center.

## <a name="account-migration-process"></a>Process för konto migrering

Om du är en användare med ägar rollen i CPP för ett specifikt konto visas en gul banderoll på utgivarens profil sida. Du kan höra till något av följande två fall:

- Ditt konto har redan migrerats och du är redo att hantera dina konto inställningar i Partner Center.
- Ditt konto har inte migrerats till Partner Center och du måste vidta ytterligare åtgärder.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Ditt konto har migrerats till Partner Center

Nu hanterar du ditt konto i Partner Center. Ändringar som användar tillägg/borttagning kommer att synkroniseras tillbaka till CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Du har ännu inte migrerat ditt konto till Partner Center

Klicka på banderollen för att starta processen för migrering av konto. Du förväntas ange följande objekt:

1. E-postadress till arbetet: <br> <br> I de flesta fall loggar du in med den e-postadress som du använder för att logga in på CPP. I vissa fall måste en annan e-postadress användas:

    * Microsoft-konto: om kontot CPP är ett Microsoft-konto anger du ett giltigt arbets-e-postmeddelande som är associerat med den klient som Microsoft Partner Network (MPN) ID är registrerat för. Mer information finns i [Registrera dig för Microsoft Partner Network-programmet](#sign-up-for-microsoft-partner-network-program).

    * Klient matchnings fel: om din e-postadress inte tillhör den klient som är associerad med Microsoft Partner Network-ID som finns på ditt CPP-konto visas ett fel meddelande. Om du vill flytta förbi det här felet anger du en e-postadress som är kopplad till klienten. Ett fel meddelande anger namnet på klienten.

2. Registrera dig för Microsoft Partner Network program

    Om ditt CPP-konto inte har ett Microsoft Partner Network-ID eller har ett ogiltigt ID, måste du registrera dig för det Microsoft Partner Network programmet som en del av aktiverings processen.

## <a name="publishers-moving-from-cpp"></a>Utgivare som flyttar från CPP

Om ditt konto har migrerats från CPP behöver du inte skapa ett nytt Partner Center-konto. Du bör ha fått en anpassad länk till ditt nya partner Center-konto i e-postmeddelandet och i ett informations meddelande när du har loggat in på ditt befintliga CPP-konto.

När du har aktiverat ditt nya partner Center-konto genom att gå till den här anpassade länken kan du gå tillbaka till ditt konto genom att gå till [instrument panelen för kommersiellt marknads platser](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partner Center.

Publicerings avtalet och företags profil informationen kommer att migreras till ditt nya partner Center-konto, tillsammans med tidigare konfigurerade profil information för konto utbetalning, användar konton och behörigheter samt aktiva erbjudanden som är associerade med ditt CPP-konto.

När din konto information har flyttats från CPP till Partner Center använder du inte längre CPP för att göra konto uppdateringar eller hantera användare, behörigheter och fakturering. Under en begränsad tid uppdateras eventuella konto uppdateringar som du gör i Partner Center automatiskt i ditt skrivskyddade CPP-konto tills CPP-portalen slutligen är föråldrad.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registrera dig för Microsoft Partner Network program

Företag som vill partner med Microsoft måste gå med i Microsoft Partner Network (MPN) och hämta ett MPN-ID. Om du redan är medlem i Microsoft Partner Network och har ett MPN-ID bör du hålla informationen praktisk när du behöver den under konto aktiverings processen.  

Om du inte är medlem i Microsoft Partner Network kan du [ansluta hit](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) för att få ett MPN-ID. Anteckna ditt MPN-ID eftersom du måste ange det under konto aktiverings processen.

Mer information om Microsoft Partner Network finns i [delta i Microsoft Partner Network](https://partner.microsoft.com/membership) på partner webbplatsen. Mer information om ISV-förmåner i Microsoft Partner Network finns i ISV- [instansresursen](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Flytta Dynamics 365-och PowerApps-erbjudanden till Partner Center

För att effektivisera konto-och erbjudande hantering för Dynamics 365-kund engagemang, PowerApps och Dynamics 365-åtgärder har erbjudanden flyttats till [partner Center](https://partner.microsoft.com/). Flytten säkerställer att samma innehåll är tillgängligt för både offentliga och säljande kataloger.

Om 365 du vill ha specifik information om vad som måste göras den **15 oktober 2019** för kund engagemang, PowerApps och Dynamics 365-åtgärder, följer du anvisningarna nedan.

> [!NOTE]
> Detta gäller inte för Dynamics 365 Business Central-erbjudanden.  

1. Om ditt MPN-medlemskaps konto ursprungligen skapades i partner Membership Center (PMC) loggar du in på [partner Center](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) för att bekräfta att ditt konto har migrerats. Om du ser en profil skärm med ditt MPN-ID är du redo att fortsätta. Annars måste du starta migreringen av kontot genom att följa instruktionerna i [partner medlemskaps centret](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Om du behöver hjälp kan du besöka [supporten](https://partner.microsoft.com/support?issueid=100-0077).
2. Gå till [sidan för extern Marketplace-översikt i Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview). Om du ser "kommersiell marknads plats" i det vänstra navigerings fönstret är du registrerad och bör fortsätta till nästa steg. Om inte, [Registrera dig på den kommersiella marknads platsen](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) nu.
3. Bekräfta att dina erbjudanden är i AppSource genom att [söka efter dina erbjudanden](https://appsource.microsoft.com/). Om dina erbjudanden redan finns i AppSource kan du fortsätta till nästa steg. För ett erbjudande som inte finns i AppSource skapar du ett nytt erbjudande för [dynamics 365 kund engagemang](.\partner-center-portal\create-new-customer-engagement-offer.md) eller ett [nytt erbjudande för Dynamics 365-åtgärder](.\partner-center-portal\create-new-operations-offer.md).
4. På [sidan avtal](https://partner.microsoft.com/dashboard/account/agreements)i partner centret ser du till att du har granskat och accepterat **Business Applications ISV-tillägget**.
5. Se till att fakturerings informationen är klar i [konto inställningarna](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)för partner Center.
6. Skicka in varje nytt och befintligt erbjudande för certifiering och publicering, även om dina erbjudanden tidigare har certifierats.
    * Slutför informations skärmarna, inklusive att tillhandahålla appen för certifiering, samt marknadsförings information. Välj **Skicka** (övre högra hörnet på skärmen) senast den **15 oktober 2019**. De här stegen måste utföras för att undvika att erbjudandet påverkas.
    * Om det är berättigat kan du begära att delta på Premium nivån under den här processen.
    * Certifiering eller omcertifiering kräver att appen har stöd för den senaste versionen av vår Business Applications-plattform.
    * När din app har godkänts får du ett e-postmeddelande som du kan gå tillbaka till erbjudandet och välja "Go Live" för att aktivera erbjudandet i drift på Microsoft AppSource.

## <a name="additional-resources"></a>Ytterligare resurser

Få hjälp från experter och kollegor i forum och upptäck Bloggar, webb seminarier, videor, händelser och mycket mer på [Microsoft Dynamics CRM](https://community.dynamics.com/crm?wa=wsignin1.0).

Om du behöver hjälp med att publicera, certifiera eller hantera dina Marketplace-erbjudanden kan du [skicka in ett support ärende](https://aka.ms/MarketplacePublisherSupport).

## <a name="next-step"></a>Nästa steg

- [Hantera ditt kommersiella Marketplace-konto i Partner Center](./manage-account.md)
