---
title: Azure ExpressRoute CrossConnnections API-utveckling och-integrering
description: Den här artikeln innehåller en detaljerad översikt över ExpressRoute-partner om resurs typen expressRouteCrossConnections.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: b80392231dba26a10141dcd8247b092e8171894c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96011990"
---
# <a name="expressroute-crossconnnections-api-development-and-integration"></a>ExpressRoute CrossConnnections API-utveckling och integrering

ExpressRoute partner Resource Manager API gör det möjligt för ExpressRoute-partner att hantera Layer-2-och Layer-3-konfigurationen av kund ExpressRoute-kretsar. ExpressRoute partner Resource Manager-API: et introducerar en ny resurs typ, **expressRouteCrossConnections**. Partner använder den här resursen för att hantera kund ExpressRoute-kretsar.

## <a name="workflow"></a>Arbetsflöde

ExpressRouteCrossConnections-resursen är en skugg resurs till ExpressRoute-kretsen. När en Azure-kund skapar en ExpressRoute-krets och väljer en specifik ExpressRoute-partner, skapar Microsoft en expressRouteCrossConnections-resurs i partnerns hanterings prenumeration för Azure-ExpressRoute. Då definierar Microsoft en resurs grupp för att skapa expressRouteCrossConnections-resursen i. Namngivnings standarden för resurs gruppen är **CrossConnection-* PeeringLocation * * *; där PeeringLocation = ExpressRoute-platsen. Om en kund till exempel skapar en ExpressRoute-krets i Denver skapas CrossConnection i partnerns Azure-prenumeration i följande resurs grupp: **CrossConnnection-Denver**.

ExpressRoute-partner hanterar Layer-2-och Layer-3-konfiguration genom att utfärda REST-åtgärder mot expressRouteCrossConnections-resursen.

## <a name="benefits"></a>Fördelar

Fördelar med att flytta till expressRouteCrossConnections-resursen:

* Alla framtida förbättringar av ExpressRoute-partner görs tillgängliga på ExpressRouteCrossConnection-resursen.

* Partner kan använda [Azure-rollbaserad åtkomst kontroll (Azure RBAC)](../role-based-access-control/overview.md) till expressRouteCrossConnection-resursen. Dessa kontroller kan definiera behörigheter för vilka användare som konton kan ändra expressRouteCrossConnection-resursen och lägga till/uppdatera/ta bort peering-konfigurationer.

* ExpressRouteCrossConnection-resursen exponerar API: er som kan vara till hjälp vid fel sökning av ExpressRoute-anslutningar. Detta inkluderar ARP-tabell, tabell Sammanfattning för BGP-vägar och information om BGP-routningstabell. Den här funktionen stöds inte av klassiska distributions-API: er.

* Partner kan också leta upp annonserade communities på Microsoft-peering med hjälp av *RouteFilter* -resursen.

## <a name="api-development-and-integration-steps"></a>API-utveckling och integrerings steg

För att utveckla mot partner-API: et använder ExpressRoute-partner en test-kund-och test partner installation. Test kund konfigurationen kommer att användas för att skapa ExpressRoute-kretsar i test-peering-platser som mappar till dummy-enheter och-portar. Installations programmets installations program används för att hantera de ExpressRoute-kretsar som skapats i test-peering-platsen.

### <a name="1-enlist-subscriptions"></a>1. registrera prenumerationer

Om du vill begära test partnern och testa kund konfigurationen registrerar du två Azure-prenumerationer enligt principen betala per användning till din ExpressRoute teknisk kontakt:
* **ExpressRoute_API_Dev_Provider_Sub:** Den här prenumerationen kommer att användas för att hantera ExpressRoute-kretsar som skapats i test-peering-platser på dummy-enheter och-portar.

* **ExpressRoute_API_Dev_Customer_Sub:** Den här prenumerationen kommer att användas för att skapa ExpressRoute-kretsar i test-peering-platser som mappar till dummy-enheter och-portar.

Test-peering-platserna: dummy-enheter och portar exponeras inte för produktions kunder som standard. För att kunna skapa ExpressRoute-kretsar som mappar till test installationen måste en prenumerations funktions flagga aktive ras.

### <a name="2-register-the-dev_provider-subscription-to-access-the-expressroutecrossconnections-api"></a>2. registrera Dev_Provider-prenumerationen för att få åtkomst till expressRouteCrossConnections-API: et

För att få åtkomst till expressRouteCrossConnections-API: et måste partner prenumerationen registreras i **Microsoft. Network Resource-providern**. Följ stegen i artikeln [Azure-resurs leverantörer och typer](../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) för att slutföra registrerings processen.

### <a name="3-set-up-authentication-for-azure-resource-manager-rest-api-calls"></a>3. Konfigurera autentisering för Azure Resource Manager REST API-anrop

De flesta Azure-tjänster kräver klient kod för att autentisera med Resource Manager, med giltiga autentiseringsuppgifter, innan tjänst-API: er anropas. Autentiseringen koordineras mellan de olika aktörerna av Azure AD och ger klienten en åtkomsttoken som bevis på autentisering.

Autentiseringsprocessen omfattar två huvud steg:

1. [Registrera klienten](/rest/api/azure/#register-your-client-application-with-azure-ad).
2. [Skapa åtkomstbegäran](/rest/api/azure/#create-the-request).

### <a name="4-provide-network-contributor-permission-to-the-client-application"></a>4. ge nätverks deltagare behörighet till klient programmet

När autentiseringen har kon figurer ATS måste du bevilja nätverks deltagare åtkomst till klient programmet under Dev_Provider_Sub. Om du vill bevilja behörighet loggar du in på [Azure Portal](https://ms.portal.azure.com/#home) och utför följande steg:

1. Gå till prenumerationer och välj Dev_Provider_Sub
2. Navigera till Access Control (IAM)
3. Lägg till roll tilldelning
4. Välj rollen nätverks deltagare
5. Tilldela åtkomst till Azure AD-användare,-grupp eller-tjänstens huvud namn
6. Välj ditt klient program
7. Spara ändringar

### <a name="5-develop"></a>5. utveckla

Utveckla mot [expressRouteCrossConnections-API: et](/rest/api/expressroute/expressroutecrossconnections).

## <a name="rest-api"></a>REST-API

Se [ExpressRoute CrossConnections REST API](/rest/api/expressroute/expressroutecrossconnections) for Rest API documentation.

## <a name="next-steps"></a>Nästa steg

Mer information om alla ExpressRoute REST API: er finns i [EXPRESSROUTE REST-API: er](/rest/api/expressroute/).