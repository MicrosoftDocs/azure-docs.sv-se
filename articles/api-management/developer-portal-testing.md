---
title: Testa utvecklarportalen med egen värd
titleSuffix: Azure API Management
description: Lär dig hur du ställer in enhetstester och tester från slutet till slut för din lokala API Management portalen.
author: dlepow
ms.author: apimpm
ms.date: 03/25/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: ab6a7aa8fc4f11c34126415379294ef1e48fa286
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741885"
---
# <a name="test-the-self-hosted-developer-portal"></a>Testa utvecklarportalen med egen värd

Den här artikeln förklarar hur du ställer in enhetstester och tester från slutet till slut [för din lokala portal.](developer-portal-self-host.md)

## <a name="unit-tests"></a>Enhetstester

Ett enhetstest är en metod för att validera små delar av funktionerna. Det görs isolerat från andra delar av programmet.

### <a name="example-scenario"></a>Exempelscenario

I det här scenariot testar du en kontroll för lösenordsinmatning. Den accepterar endast lösenord som innehåller minst:

- En bokstav

- Ett tal

- Ett specialtecken
 
Testet för att verifiera dessa krav ser ut så här:

```typescript
const passwordInput = new PasswordInput();

passwordInput.value = "";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "password";
expect(passwordInput.isValid).to.equal(false);

passwordInput.value = "p@ssw0rd";
expect(passwordInput.isValid.to.equal(true);
```
 
### <a name="project-structure"></a>Projektstruktur

Det är vanligt att ha ett enhetstest bredvid den komponent som den ska validera.

```console
component.ts
component.spec.ts
```

### <a name="mock-http-requests"></a>Fingera HTTP-begäranden

Det finns fall när du förväntar dig att en komponent ska göra HTTP-begäranden. Komponenten bör reagera korrekt på olika typer av svar. Om du vill simulera specifika HTTP-svar använder du `MockHttpClient` . Den implementerar gränssnittet `HttpClient` som används av många andra komponenter i projektet.

```typescript
const httpClient = new MockHttpClient();

httpClient.mock()
    .get("/users/jane")
    .reply(200, {
        firstName: "Jane",
        lastName: "Doe"
    });
```

## <a name="end-to-end-tests"></a>Tester från end-to-end

Ett test från end-to-end kör ett visst användarscenario och utför exakt de steg som du förväntar dig att användaren ska utföra. I en webbapp som Azure API Management utvecklarportalen bläddrar användaren igenom innehållet och väljer alternativ för att uppnå vissa resultat. 

Om du vill replikera användarnavigering kan du använda hjälpbibliotek för webbläsarmanipulering [som Puppeteer](https://github.com/puppeteer/puppeteer). Med den kan du simulera användaråtgärder och automatisera förmodade scenarier. Puppeteer tar också automatiskt skärmbilder av sidor eller komponenter i vilket steg som helst av testet. Jämför dem senare med tidigare resultat för att fånga avvikelser och potentiella regressioner.

### <a name="example-scenario"></a>Exempelscenario

I det här scenariot måste du verifiera ett användarflöde för inloggning. Det här scenariot kräver följande steg:

1. Öppna webbläsaren och gå till inloggningssidan.

1. Ange e-postadressen.

1. Ange lösenordet.

1. Välj **Logga in.**

1. Kontrollera att användaren omdirigerades till startsidan.

1. Kontrollera att sidan innehåller **menyalternativet** Profil. Det är en av de möjliga indikatorerna som du har loggat in på.

Om du vill köra testet automatiskt skapar du ett skript med exakt samma steg:

```typescript
// 1. Open browser and navigate to the sign-in page.
const page = await browser.newPage();
await page.goto("https://contoso.com/signin");

// 2. Enter email.
await this.page.type("#email", "john.doe@contoso.com");

// 3. Enter password.
await this.page.type("#password", "p@s$w0rd");

// 4. Click Sign-in.
await this.page.click("#signin");

// 5. Verify that user got redirected to Home page.
expect(page.url()).to.equal("https://contoso.com");

// 6. Verify that the page includes the Profile menu item.
const profileMenuItem = await this.page.$("#profile");
expect(profileMenuItem).not.equals(null);
```

> [!NOTE]
> Strängar som "#email", "#password" och "#signin" är CSS-liknande väljare som identifierar HTML-element på sidan. Mer information finns i Specifikation för väljare [nivå 3](https://www.w3.org/TR/selectors-3/) W3C.

### <a name="ui-component-maps"></a>Gränssnittskomponentmappningar

Användarflöden går ofta igenom samma sidor eller komponenter. Ett bra exempel är huvudmenyn på webbplatsen som finns på varje sida. 

Skapa en gränssnittskomponentkarta för att undvika att konfigurera och uppdatera samma väljare för varje test. Du kan till exempel ersätta steg 2 till 6 i föregående exempel med bara två rader:

```typescript
const signInWidget = new SigninBasicWidget(page);
await signInWidget.signInWithBasic({ email: "...", password: "..." });
```

### <a name="test-configuration"></a>Testa konfigurationen

Vissa scenarier kan kräva förskapade data eller konfiguration. Du kan till exempel behöva automatisera användar inloggning med konton för sociala medier. Det är svårt att skapa dessa data snabbt eller enkelt.

För detta ändamål kan du lägga till en särskild konfigurationsfil i testscenariot. Testskripten kan hämta nödvändiga data från filen. Beroende på bygg- och testpipelinen kan testerna hämta hemligheterna från ett namngivet säkert arkiv.

Här är ett exempel på en `validate.config.json` som skulle lagras i `src` mappen för ditt projekt.

```json
{
    "environment": "validation",
    "urls": {
        "home": "https://contoso.com",
        "signin": "https://contoso.com/signin",
        "signup": "https://contoso.com/signup/"
    },
    "signin": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            },
            "aadB2C": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    },
    "signup": {
        "firstName": "John",
        "lastName": "Doe",
        "credentials": {
            "basic": {
                "email": "johndoe@contoso.com",
                "password": "< password >"
            }
        }
    }
}

```

### <a name="headless-vs-normal-tests"></a>Huvudlösa jämfört med normala tester

Moderna webbläsare som Chrome eller Microsoft Edge kan du köra automatisering i både huvudlöst och normalt läge. Webbläsaren fungerar utan ett grafiskt användargränssnitt i huvudlöst läge. Den utför fortfarande samma sida och Document Object Model (DOM). Webbläsarens användargränssnitt behövs vanligtvis inte i leveranspipelines. I så fall är det ett bra alternativ att köra tester i huvudlöst läge.

När du utvecklar ett testskript är det användbart att se exakt vad som händer i webbläsaren. Det är ett bra tillfälle att använda normalt läge.

Om du vill växla mellan lägena ändrar du `headless` alternativet i `constants.ts` filen. Den finns i `tests` mappen i projektet:

```typescript
export const LaunchOptions = {
    headless: false
};
```

Ett annat användbart alternativ är `slowMo` . Det pausar körningen av testet mellan varje åtgärd:

```typescript
export const LaunchOptions = {
    slowMo: 200 // milliseconds
};
```

## <a name="run-tests"></a>Köra tester

Det finns två inbyggda sätt att köra tester i det här projektet:

**npm-kommando**

```console
npm run test
```

**TestUtforskaren**

Test Explorer-tillägget för VS Code (till exempel [Mocha Test Explorer)](https://marketplace.visualstudio.com/items?itemName=hbenl.vscode-mocha-test-adapter)har ett praktiskt användargränssnitt och ett alternativ för att köra tester automatiskt vid varje ändring av källkoden:

:::image type="content" source="media/developer-portal-testing/visual-studio-code-test-explorer.png" alt-text="Skärmbild av testutforskaren Visual Studio kod":::

## <a name="next-steps"></a>Nästa steg

Läs mer om utvecklarportalen:

- [Översikt över Azure API Management-utvecklarportalen](api-management-howto-developer-portal.md)

- [Utvecklarportalen med egen värd](developer-portal-self-host.md)
