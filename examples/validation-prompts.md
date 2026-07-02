# Validation Prompts

Use these prompts to check whether the skill generalizes across Power Platform topics. They are evaluation prompts, not canonical answers.

## Delegation and Formula Review

```text
I have a Canvas App connected to a SharePoint list with 30,000 requests. Review this Gallery.Items formula for correctness, delegation, and maintainability:

SortByColumns(
    Filter(
        Requests,
        Lower(Status.Value) = "open" &&
        txtSearch.Text in Lower(Title) &&
        Year(DueDate) = Year(Today()) &&
        AssignedTo.DisplayName = User().FullName
    ),
    "DueDate",
    SortOrder.Ascending
)

Rewrite it as safely as possible and explain what still must be verified.
```

Good answers should be cautious about delegation, distinguish SharePoint indexing from delegation support, preserve optional-search guards, and avoid treating the row-limit setting as a scalability fix.

## Patch and Data Shapes

```text
I need a Power Fx formula to create a new request. The data source might be SharePoint or Dataverse; I have Title, Status, Requester, Department, and DueDate fields. Show a Patch pattern, explain what changes for SharePoint choice/person/lookup fields versus Dataverse choice/lookup fields, and include error handling.
```

Good answers should ask or branch on the data source, avoid inventing field shapes, use `IfError()` for save failures, and explain that choice/person/lookup shapes must be verified.

## ALM and Power Automate

```text
We have a Canvas App that calls a Power Automate flow to send approvals. It works in dev but breaks after import into test. What should I check for ALM, connection references, environment variables, flow ownership, and user permissions?
```

Good answers should cover solutions, managed/unmanaged deployment, connection references, environment variables, flow ownership/connections, security roles/permissions, and testing with non-maker users.

## Responsive Screen Review

```text
Review a Canvas App screen that uses hard-coded X/Y positions, duplicated desktop/mobile screens, icon-only buttons, and a gallery next to an edit form. Suggest a responsive container-based layout and mention accessibility concerns.
```

Good answers should recommend containers, flexible sizing, meaningful control names, accessible labels, logical focus order, loading/empty/error states, and avoid implying access to the actual app.
