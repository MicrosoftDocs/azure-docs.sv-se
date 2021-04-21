---
title: Migrera konton från Cloud Partner Portal till Microsofts kommersiella marknadsplats
description: Lär dig hur du migrerar ditt konto från Cloud Partner Portal till Partnercenter på Microsofts kommersiella marknadsplats för Azure
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: parthpandyaMSFT
ms.author: parthp
ms.date: 09/23/2019
ms.openlocfilehash: 189966c468fe5a39fbd44f7961e9512b7b054882
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811807"
---
# <a name="how-to-migrate-your-account-from-cloud-partner-portal-to-partner-center"></a>Så här migrerar du ditt konto Cloud Partner Portal till Partnercenter

Om du har ett Cloud Partner Portal-konto (CPP) måste dina kontoinställningar migreras till Partnercenter.

## <a name="account-migration-process"></a>Kontomigreringsprocess

Om du är en användare med rollen Ägare i CPP för ett visst konto visas en gul banderoll på sidan Utgivarprofil. Du kan tillhöra något av följande två fall:

- Ditt konto har redan migrerats och du är redo att hantera dina kontoinställningar i Partnercenter.
- Ditt konto har inte migrerats till Partnercenter och du måste vidta ytterligare åtgärder.

### <a name="your-account-has-been-migrated-to-partner-center"></a>Ditt konto har migrerats till Partnercenter

Nu hanterar du ditt konto i Partnercenter. Ändringar som tillägg/borttagning av användare synkroniseras tillbaka till CPP.

### <a name="you-have-not-yet-migrated-your-account-to-partner-center"></a>Du har ännu inte migrerat ditt konto till Partnercenter

Klicka på banderollen för att starta kontomigreringsprocessen. Du förväntas ange följande objekt:

1. E-postadress till arbetet: <br> <br> I de flesta fall loggar du in med den e-postadress som du använder för att logga in på CPP. I vissa fall måste en annan e-postadress användas:

    * Microsoft-konto: Om CPP-kontot är ett Microsoft-konto anger du ett giltigt e-postmeddelande för arbetet som är associerat med den klientorganisation som MPN-ID:t (Microsoft Partner Network) har registrerats för. Mer information finns i [Registrera dig för Microsoft Partner Network Program](#sign-up-for-microsoft-partner-network-program).

    * Matchningsfel för klientorganisation: Om din e-postadress till arbetet inte tillhör den klientorganisation som är associerad med det Microsoft Partner Network-ID som finns på ditt CPP-konto visas ett fel. Om du vill gå förbi det här felet anger du en e-postadress som är associerad med klienten. Ett felmeddelande anger namnet på klientorganisationen.

2. Registrera dig för Microsoft Partner Network program

    Om ditt CPP-konto inte har något Microsoft Partner Network-ID eller har ett som är ogiltigt måste du registrera dig för Microsoft Partner Network-programmet som en del av aktiveringsprocessen.

## <a name="publishers-moving-from-cpp"></a>Utgivare som flyttar från CPP

Om ditt konto har migrerats från CPP behöver du inte skapa ett nytt Partnercenter-konto. Du bör ha fått en anpassad länk till ditt nya Partnercenter-konto via e-post och i ett banderollmeddelande när du har loggat in på ditt befintliga CPP-konto.

När du har aktiverat ditt nya Partnercenter-konto genom att besöka den här anpassade länken kan du gå tillbaka till ditt konto genom att besöka instrumentpanelen för den kommersiella [marknadsplatsen](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i Partnercenter.

Publiceringsavtalet och företagets profilinformation migreras till ditt nya Partnercenter-konto, tillsammans med eventuell tidigare konfigurerad kontoutbetalningsprofilinformation, användarkonton och behörigheter samt aktiva erbjudanden som är associerade med ditt CPP-konto.

När din kontoinformation har flyttats från CPP till Partnercenter använder du inte längre CPP för att göra kontouppdateringar eller hantera användare, behörigheter och fakturering. Under en begränsad tid uppdateras alla kontouppdateringar som du gör i Partnercenter automatiskt i ditt skrivskyddade CPP-konto tills CPP-portalen så småningom blir inaktuell.

## <a name="sign-up-for-microsoft-partner-network-program"></a>Registrera dig för Microsoft Partner Network program

Företag som vill samarbeta med Microsoft måste gå med i Microsoft Partner Network (MPN) och få ett MPN-ID. Om du redan är medlem i Microsoft Partner Network och har ett MPN-ID bör du ha informationen till hands eftersom du behöver den under kontoaktiveringsprocessen.  

Om du inte är medlem i Microsoft Partner Network kan du gå med här [för att få](https://signup.microsoft.com/signup?sku=StoreForBusinessIW&origin=partnerdashboard&culture=en-us&ru=https://partner.microsoft.com/dashboard/account/v3/xpu/onboard?ru=/dashboard/account/v3/enrollment/companyprofile/basicpartnernetwork/new) ett MPN-ID. Anteckna ditt MPN-ID eftersom du måste ange det under kontoaktiveringsprocessen.

Mer information om Microsoft Partner Network finns i [Delta i Microsoft Partner Network](https://partner.microsoft.com/membership) på partnerwebbplatsen. Mer information om ISV-fördelar i Microsoft Partner Network finns i [ISV Resource Hub](https://partner.microsoft.com/isv-resource-hub).  

## <a name="move-dynamics-365-and-powerapps-offers-to-partner-center"></a>Flytta erbjudanden för Dynamics 365 och PowerApps till Partnercenter

För att effektivisera konto- och erbjudandehantering för Dynamics 365 Customer Engagement, PowerApps och Dynamics 365 Operations har erbjudanden flyttats [till Partnercenter.](https://partner.microsoft.com/) Flytten säkerställer att samma innehåll är tillgängligt för både offentliga kataloger och säljarkataloger.

Om du vill ha specifik information om vad som behöver göras **den 15 oktober 2019** för dina Erbjudanden för Dynamics 365 Customer Engagement, PowerApps och Dynamics 365 Operations följer du anvisningarna nedan.

> [!NOTE]
> Detta gäller inte för Erbjudanden för Dynamics 365 Business Central.  

1. Om ditt MPN-medlemskapskonto ursprungligen skapades i Partner Membership Center (PMC) loggar du in på [Partnercenter](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) för att bekräfta att ditt konto har migrerats. Om du ser en profilskärm med ditt MPN-ID är du redo att fortsätta. Om inte måste du starta kontomigrering genom att följa anvisningarna i [Partner Membership Center](https://partners.microsoft.com/partnerprogram/Welcome.aspx). Om du behöver hjälp kan du gå till [supporten.](https://partner.microsoft.com/support?issueid=100-0077)
2. Gå till [översiktssidan för Den kommersiella marknadsplatsen i Partnercenter.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Om du ser "Kommersiell marknadsplats" i det vänstra navigeringsfönstret är du registrerad och bör fortsätta till nästa steg. Om inte, [registrera dig på den kommersiella marknadsplatsen](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) nu.
3. Bekräfta att dina erbjudanden finns i AppSource genom [att söka efter dina erbjudanden.](https://appsource.microsoft.com/) Om dina erbjudanden redan finns i AppSource fortsätter du till nästa steg. Information om erbjudanden som inte finns i AppSource finns i Planera ett erbjudande för [Dynamics 365 Customer Engagement](dynamics-365-customer-engage-offer-setup.md) eller Skapa ett Erbjudande för Dynamics [365 Operations.](.\partner-center-portal\create-new-operations-offer.md)
4. På sidan med [Partnercenter-avtal](https://partner.microsoft.com/dashboard/account/agreements)kontrollerar du att du har granskat och godkänt **Business Applications ISV-tillägget**.
5. Kontrollera att [faktureringsinformationen är slutförd](https://partner.microsoft.com/dashboard/account/v3/accountsettings/billingprofile)i Kontoinställningar i Partnercenter.
6. Skicka varje nytt och befintligt erbjudande för certifiering och publicering, även om dina erbjudanden tidigare har certifierats.
    * Fyll i informationsskärmarna, inklusive att tillhandahålla din app för certifiering, samt marknadsföringsinformation. Välj **Skicka** (övre högra hörnet på skärmen) senast den **15 oktober 2019.** De här stegen måste utföras för att undvika att erbjudandets tillgänglighet påverkas.
    * Om du är berättigad kan du begära att delta på premiumnivån under den här processen.
    * Certifiering eller omcertifiering kräver att din app stöder den senaste versionen av vår Business Applications Platform.
    * När din app har godkänts får du ett e-postmeddelande för att återgå till erbjudandet och välja "gå live" för att aktivera erbjudandet så att det kan Microsoft AppSource.

## <a name="additional-resources"></a>Ytterligare resurser

Få hjälp från experter och kollegor i forum och upptäck bloggar, webbseminor, videor, evenemang med mera på [Microsoft Dynamics CRM.](https://community.dynamics.com/crm?wa=wsignin1.0)

Om du behöver hjälp med att publicera, certifiera eller hantera dina Marketplace-erbjudanden kan [du skicka en supportbiljett.](https://aka.ms/MarketplacePublisherSupport)

## <a name="next-step"></a>Nästa steg

- [Hantera ditt commercial marketplace-konto i Partnercenter](./manage-account.md)
