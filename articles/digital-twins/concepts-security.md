---
title: Säkerhet för Azure Digital Twins-lösningar
titleSuffix: Azure Digital Twins
description: Lär dig mer om rekommenderade säkerhets metoder med Azure Digitals.
author: baanders
ms.author: baanders
ms.date: 3/18/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b589f98ee78d0709b2a74ba4e364cec0e486e968
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100547170"
---
# <a name="secure-azure-digital-twins"></a>Skydda digitala Azure-dubbla

För säkerhet ger Azure Digitals dubbla, exakt åtkomst kontroll över specifika data, resurser och åtgärder i distributionen. Det sker genom en detaljerad strategi för roll-och behörighets hantering som kallas **Azure rollbaserad åtkomst kontroll (Azure RBAC)**. Du kan läsa om de allmänna principerna för Azure RBAC [här](../role-based-access-control/overview.md).

Azure Digitals dubbla är också stöd för kryptering av data i vila.

## <a name="roles-and-permissions-with-azure-rbac"></a>Roller och behörigheter med Azure RBAC

Azure RBAC tillhandahålls till Azure Digitals sammanflätade via integrering med [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD).

Du kan använda Azure RBAC för att bevilja behörighet till ett *säkerhets objekt*, som kan vara en användare, en grupp eller ett program tjänst objekt. Säkerhets objekt autentiseras av Azure AD och tar emot en OAuth 2,0-token i retur. Denna token kan användas för att auktorisera en åtkomstbegäran till en Azure Digitals-instans.

### <a name="authentication-and-authorization"></a>Autentisering och auktorisering

Med Azure AD är Access en två stegs process. När ett säkerhets objekt (en användare, grupp eller ett program) försöker få åtkomst till Azures digitala dubbla, måste begäran *autentiseras* och *auktoriseras*. 

1. Först *autentiseras* säkerhets objektets identitet och en OAuth 2,0-token returneras.
2. Därefter skickas token som en del av en begäran till tjänsten Azure Digitals dubblare för att *ge* åtkomst till den angivna resursen.

Steget autentisering kräver att en program förfrågan innehåller en OAuth 2,0-åtkomsttoken vid körning. Om ett program körs i en Azure-entitet, till exempel en [Azure Functions](../azure-functions/functions-overview.md) app, kan den använda en **hanterad identitet** för att få åtkomst till resurserna. Läs mer om hanterade identiteter i nästa avsnitt.

Auktoriserings steget kräver att en Azure-roll tilldelas till säkerhets objekt. Rollerna som tilldelas ett säkerhets objekt bestämmer vilka behörigheter som huvud kontot ska ha. Azure Digitals dubbla ger Azure-roller som omfattar uppsättningar med behörigheter för Azure Digitals dubbla resurser. Dessa roller beskrivs längre fram i den här artikeln.

Mer information om roller och roll tilldelningar som stöds i Azure finns i [*förstå de olika rollerna*](../role-based-access-control/rbac-and-directory-admin-roles.md) i Azure RBAC-dokumentationen.

#### <a name="authentication-with-managed-identities"></a>Autentisering med hanterade identiteter

[Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md) är en funktion i Azure som gör att du kan skapa en säker identitet som är kopplad till distributionen där program koden körs. Du kan sedan associera identiteten med åtkomst kontroll roller för att bevilja anpassade behörigheter för åtkomst till specifika Azure-resurser som ditt program behöver.

Med hanterade identiteter hanterar Azure-plattformen den här körnings identiteten. Du behöver inte lagra och skydda åtkomst nycklar i din program kod eller konfiguration, antingen för själva identiteten eller för de resurser som du behöver komma åt. Ett digitalt Azure-klientprogram som körs inuti ett Azure App Service-program behöver inte hantera SAS-regler och nycklar eller andra åtkomsttoken. Klient programmet behöver bara slut punkts adressen för Azure Digitals-namnområdet. När appen ansluter binder Azure Digitals en hanterad entitets kontext till klienten. När den är kopplad till en hanterad identitet kan din Azure Digital-klient med dubbla Azure-klienter utföra alla behöriga åtgärder. Auktorisering beviljas sedan genom att associera en hanterad entitet med en Azure Digitals Azure-roll (beskrivs nedan).

#### <a name="authorization-azure-roles-for-azure-digital-twins"></a>Auktorisering: Azure-roller för Azure Digitals dubbla

Azure tillhandahåller **två inbyggda Azure-roller** för att auktorisera åtkomst till [API: er](how-to-use-apis-sdks.md#overview-data-plane-apis)för Azure Digitals dubbla data plan. Du kan referera till rollerna antingen efter namn eller efter ID:

| Inbyggd roll | Description | ID | 
| --- | --- | --- |
| Azure Digitals sammanflätade data ägare | Ger fullständig åtkomst till resurser med Azure Digitals dubbla resurser | bcd981a7-7f74-457b-83e1-cceb9e632ffe |
| Azure Digitals sammanflätade data läsare | Ger skrivskyddad åtkomst till Azure Digitals dubbla resurser | d57506d4-4c8d-48b1-8587-93c323f6a5a3 |

Du kan tilldela roller på två sätt:
* via fönstret åtkomst kontroll (IAM) för Azure Digitals dubbla i Azure Portal (se [*lägga till eller ta bort Azure roll tilldelningar med hjälp av Azure Portal*](../role-based-access-control/role-assignments-portal.md))
* via CLI-kommandon för att lägga till eller ta bort en roll

Mer detaljerad information om hur du gör detta finns i självstudien om Azure Digitals dubbla steg [*: Anslut en lösning från slut punkt till slut punkt*](tutorial-end-to-end.md).

Mer information om hur inbyggda roller definieras finns i [*förstå roll definitioner*](../role-based-access-control/role-definitions.md) i Azure RBAC-dokumentationen. Information om hur du skapar anpassade Azure-roller finns i [*Azure-anpassade roller*](../role-based-access-control/custom-roles.md).

##### <a name="automating-roles"></a>Automatisera roller

När du refererar till roller i automatiserade scenarier rekommenderar vi att du refererar till dem med deras **ID** i stället för deras namn. Namnen kan ändras mellan versioner, men ID: n kommer inte att göra dem till en mer stabil referens i Automation.

> [!TIP]
> Om du assiging roller med en cmdlet, t. ex. `New-AzRoleAssignment` ([referens](/powershell/module/az.resources/new-azroleassignment)), kan du använda `-RoleDefinitionId` -parametern i stället för `-RoleDefinitionName` att skicka ett ID i stället för ett namn på rollen.

### <a name="permission-scopes"></a>Behörighetsomfattning

Innan du tilldelar en Azure-roll till ett säkerhets objekt bör du bestämma omfattningen av åtkomsten som säkerhets objekt ska ha. Bästa praxis är att bestämma att det är bäst att endast bevilja det snävaste möjliga omfånget.

I följande lista beskrivs de nivåer där du kan begränsa åtkomsten till Azure Digitals resurser.
* Modeller: åtgärderna för den här resursen dikterar kontroll över [modeller](concepts-models.md) som laddats upp i Azure Digitals.
* Fråga Digitals grafer: åtgärderna för den här resursen bestämmer möjlighet att köra [frågor](concepts-query-language.md) på digitala inne i Azure Digitals grafer.
* Digital, dubbel: åtgärder för den här resursen ger kontroll över CRUD-åtgärder på [digitala](concepts-twins-graph.md) byggen i det dubbla diagrammet.
* Digital, dubbel relation: åtgärderna för den här resursen definierar kontroll över CRUD-åtgärder på relationer mellan digitala [band](concepts-twins-graph.md) i den dubbla grafen.
* Händelse väg: åtgärder för den här resursen bestämmer behörigheter för att [dirigera händelser](concepts-route-events.md) från Azure Digitals till en slut punkts tjänst som [händelsehubben](../event-hubs/event-hubs-about.md), [Event Grid](../event-grid/overview.md)eller [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

### <a name="troubleshooting-permissions"></a>Fel söknings behörigheter

Om en användare försöker utföra en åtgärd som inte tillåts av deras roll, kan de få ett fel meddelande från begäran om läsning av tjänst `403 (Forbidden)` . Mer information och fel söknings steg finns i [*fel sökning: Azure Digitals-begäran misslyckades med status: 403 (förbjuden)*](troubleshoot-error-403.md).

## <a name="managed-identity-for-accessing-other-resources-preview"></a>Hanterad identitet för åtkomst till andra resurser (förhands granskning)

Genom att konfigurera en [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) - **hanterad identitet** för en Azure Digital-instans kan du enkelt komma åt andra Azure AD-skyddade resurser, till exempel [Azure Key Vault](../key-vault/general/overview.md). Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure AD finns i  [*hanterade identiteter för Azure-resurser*](../active-directory/managed-identities-azure-resources/overview.md). 

Azure stöder två typer av hanterade identiteter: systemtilldelade och tilldelade användare. Azure Digital-dubbla har för närvarande endast stöd för **systemtilldelade identiteter**. 

Du kan använda en systemtilldelad hanterad identitet för Azure Digital-instansen för att autentisera till en [anpassad definierad slut punkt](concepts-route-events.md#create-an-endpoint). Azure Digitals dubbla nätverk stöder systemtilldelad identitets baserad autentisering till slut punkter för [Event Hub](../event-hubs/event-hubs-about.md) och [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)   destinationer och till en [Azure Storage container](../storage/blobs/storage-blobs-introduction.md)   slut punkt för [händelser med obeställbara meddelanden](concepts-route-events.md#dead-letter-events). [Event Grid](../event-grid/overview.md)   slut punkter stöds för närvarande inte för hanterade identiteter.

Instruktioner för hur du aktiverar en Systemhanterad identitet för digitala Azure-användare och använder den för att dirigera händelser finns i [*så här aktiverar du en hanterad identitet för vägvals händelser (för hands version)*](how-to-enable-managed-identities.md).

## <a name="private-network-access-with-azure-private-link-preview"></a>Åtkomst till privat nätverk med Azure Private Link (för hands version)

[Azure Private Link](../private-link/private-link-overview.md) är en tjänst som ger dig åtkomst till Azure-resurser (t. ex. [azure Event Hubs](../event-hubs/event-hubs-about.md), [Azure Storage](../storage/common/storage-introduction.md)och [Azure Cosmos DB](../cosmos-db/introduction.md)) och Azure-värdbaserade kund-och partner tjänster via en privat slut punkt i din [Azure-Virtual Network (VNet)](../virtual-network/virtual-networks-overview.md). 

På samma sätt kan du använda privata slut punkter för Azure Digital-instansen för att tillåta klienter som finns i det virtuella nätverket att på ett säkert sätt komma åt instansen via privat länk. 

Den privata slut punkten använder en IP-adress från ditt Azure VNet-adressutrymme. Nätverks trafik mellan en klient i ditt privata nätverk och Azure Digital-instansen passerar över VNet och en privat länk i Microsoft stamnät nätverket, vilket eliminerar exponeringen för det offentliga Internet. Här är en visuell representation av systemet:

:::image type="content" source="media/concepts-security/private-link.png" alt-text="Ett diagram som visar ett nätverk för ett PowerGrid-företag som är ett skyddat VNET utan Internet/offentligt moln åtkomst, anslutning via privat länk till en digital Azure-instans med namnet CityOfTwins.":::

Genom att konfigurera en privat slut punkt för Azure Digital-instansen kan du skydda Azure Digitals-instansen och eliminera offentlig exponering, samt undvika data exfiltrering från ditt VNet.

Instruktioner för hur du konfigurerar en privat länk för digital Digitals i Azure finns i [*så här aktiverar du privat åtkomst med privat länk (för hands version)*](how-to-enable-private-link.md).

### <a name="design-considerations"></a>Designöverväganden 

När du arbetar med en privat länk för Azure Digitals, finns här några faktorer som du kanske vill tänka på:
* **Priser**: information om priser finns i  [priser för privata Azure-länkar](https://azure.microsoft.com/pricing/details/private-link). 
* **Regional tillgänglighet**: för Azure Digitals, finns den här funktionen tillgänglig i alla Azure-regioner där Azure Digitals, är tillgängligt. 
* **Maximalt antal privata slut punkter per Azure Digitals dubbla instanser**: 10

Information om gränserna för privat länk finns i dokumentationen för [Azure Private Link: begränsningar](../private-link/private-link-service-overview.md#limitations).

## <a name="service-tags"></a>Tjänsttaggar

En **service-tagg** representerar en grupp med IP-adressprefix från en specifik Azure-tjänst. Microsoft hanterar de adressprefix som omfattas av tjänst tag gen och uppdaterar automatiskt tjänst tag gen när adresser ändras, vilket minimerar komplexiteten vid frekventa uppdateringar av nätverks säkerhets regler. Mer information om service märken finns i  [*taggar för virtuella nätverk*](../virtual-network/service-tags-overview.md). 

Du kan använda service märken för att definiera nätverks åtkomst kontroller i [nätverks säkerhets grupper](../virtual-network/network-security-groups-overview.md#security-rules)   eller [Azure-brandvägg](../firewall/service-tags.md)genom att använda tjänst Taggar i stället för vissa IP-adresser när du skapar säkerhets regler. Genom att ange service tag-namnet (i det här fallet **AzureDigitalTwins**) i lämpligt *käll*   -eller *mål*   fält för en regel kan du tillåta eller neka trafiken för motsvarande tjänst. 

Nedan visas information om **AzureDigitalTwins** -tjänst tag gen.

| Tagg | Syfte | Kan använda inkommande eller utgående? | Kan regionala? | Kan använda med Azure-brandväggen? |
| --- | --- | --- | --- | --- |
| AzureDigitalTwins | Azure Digital Twins<br>OBS! den här taggen eller de IP-adresser som omfattas av den här taggen kan användas för att begränsa åtkomsten till slut punkter som kon figurer ATS för [händelse vägar](concepts-route-events.md). | Inkommande | Nej | Ja |

### <a name="using-service-tags-for-accessing-event-route-endpoints"></a>Använda service märken för att komma åt händelse vägens slut punkter 

Här följer stegen för att komma åt [händelse vägens](concepts-route-events.md) slut punkter med hjälp av service märken med Azure Digitals, dubbla.

1. Börja med att ladda ned denna JSON-filreferens som visar Azure IP-intervall och service märken: [*Azure IP-intervall och service märken*](https://www.microsoft.com/download/details.aspx?id=56519). 

2. Sök efter "AzureDigitalTwins"-IP-intervall i JSON-filen.  

3. Läs dokumentationen för den externa resurs som är ansluten till slut punkten (till exempel [Event Grid](../event-grid/overview.md), [händelsehubben](../event-hubs/event-hubs-about.md), [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)eller [Azure Storage](../storage/blobs/storage-blobs-overview.md) för meddelanden om [obeställbara meddelanden](concepts-route-events.md#dead-letter-events)) för att se hur du anger IP-filter för resursen.

4. Ange IP-filter för de externa resurserna med IP-intervallen från *steg 2*.  

5. Uppdatera IP-intervallen regelbundet efter behov. Intervallen kan ändras med tiden, så det är en bra idé att kontrol lera dem regelbundet och uppdatera dem när det behövs. Uppdaterings frekvensen kan variera, men det är en bra idé att kontrol lera dem en gång i veckan.

## <a name="encryption-of-data-at-rest"></a>Kryptering av data i vila

Azure Digitals-enheter tillhandahåller kryptering av data i vila och under överföring som de skrivs i våra data Center och dekrypterar dem åt dig när du har åtkomst till den. Krypteringen sker med hjälp av en Microsoft-hanterad krypterings nyckel.

## <a name="cross-origin-resource-sharing-cors"></a>Resursdelning för korsande ursprung (CORS)

Azure Digitals-korsningar stöder för närvarande inte **resurs delning mellan ursprung (CORS)**. Det innebär att om du anropar en REST API från en webbapp, ett [API Management (APIM)](../api-management/api-management-key-concepts.md) -gränssnitt eller en [Power Apps](/powerapps/powerapps-overview) -anslutning kan du se ett princip fel.

För att lösa det här felet kan du göra något av följande:
* Remsans CORS-rubrik `Access-Control-Allow-Origin` från meddelandet. Den här rubriken anger om svaret kan delas. 
* Du kan också skapa en CORS-proxy och göra Azures digitala dubblare REST API begäran via den. 

## <a name="next-steps"></a>Nästa steg

* Se de här koncepten i instruktionen [*så här: Konfigurera en instans och autentisering*](how-to-set-up-instance-portal.md).

* Se hur du interagerar med dessa begrepp från klient program kod i [*How-to: Write app authentication code*](how-to-authenticate-client.md).

* Läs mer om [Azure RBAC](../role-based-access-control/overview.md).