# Localization Pattern

This pattern uses a translation table and lookup formulas. It avoids unsupported custom function syntax. Power Fx does not let makers define arbitrary formula functions in the same way as many programming languages, so treat the helper as a named formula or repeated `With()` pattern depending on app capabilities.

## Translation Table

Use a local collection for small apps or a Dataverse/SharePoint table for managed translations.

Example collection:

```powerfx
ClearCollect(
    colTranslations,
    { Key: "app.title", Lang: "en", Text: "Service Requests" },
    { Key: "app.title", Lang: "fr", Text: "Demandes de service" },
    { Key: "button.save", Lang: "en", Text: "Save" },
    { Key: "button.save", Lang: "fr", Text: "Enregistrer" },
    { Key: "field.title", Lang: "en", Text: "Title" },
    { Key: "field.title", Lang: "fr", Text: "Titre" },
    { Key: "message.required", Lang: "en", Text: "{0} is required." },
    { Key: "message.required", Lang: "fr", Text: "{0} est obligatoire." }
)
```

Set current language:

```powerfx
Set(varLang, "en")
```

or:

```powerfx
Set(varLang, Left(Language(), 2))
```

## Basic Lookup with Fallback

Label `Text`:

```powerfx
With(
    { translationKey: "app.title" },
    Coalesce(
        LookUp(colTranslations, Key = translationKey && Lang = varLang, Text),
        LookUp(colTranslations, Key = translationKey && Lang = "en", Text),
        translationKey
    )
)
```

## Helper-Style Named Formula Concept

If named formulas are available, create a reusable pattern for common labels. Since Power Fx support varies by app feature set, do not assume unsupported custom function syntax.

Example named formula values:

```powerfx
nfAppTitle =
Coalesce(
    LookUp(colTranslations, Key = "app.title" && Lang = varLang, Text),
    LookUp(colTranslations, Key = "app.title" && Lang = "en", Text),
    "app.title"
)
```

Then use:

```powerfx
lblTitle.Text = nfAppTitle
```

## Placeholder Arguments

Use `Substitute()` for simple placeholders:

```powerfx
With(
    {
        template: Coalesce(
            LookUp(colTranslations, Key = "message.required" && Lang = varLang, Text),
            LookUp(colTranslations, Key = "message.required" && Lang = "en", Text),
            "{0} is required."
        ),
        fieldLabel: Coalesce(
            LookUp(colTranslations, Key = "field.title" && Lang = varLang, Text),
            LookUp(colTranslations, Key = "field.title" && Lang = "en", Text),
            "Title"
        )
    },
    Substitute(template, "{0}", fieldLabel)
)
```

French result:

```text
Titre est obligatoire.
```

Translate field names and message templates. Do not insert English field names into localized sentences unless that is an intentional product decision.

## Fallback Order

Use this order:

1. Current language text.
2. English text.
3. Translation key.

This prevents blank labels when a translation is missing.

## Practical Notes

- Keep translation keys stable.
- Do not concatenate long sentences from fragments.
- Translate field names, messages, and action labels.
- Consider Dataverse for managed multilingual content.
- Be careful with delegation if translations are stored in a large remote table.
