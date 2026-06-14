---
name: framer-cms-extraction
description: Framer CMS access through the official Server API, with legacy search-index extraction as a fallback
---

# Framer CMS Extraction

Use the official Framer Server API first. The older "searchIndex from Network tab" method is now a legacy fallback for cases where the user only has access to a published site and does not have project credentials.

## Preferred Method: Server API

Framer now supports project-level Server API access through the `framer-api` package. This is the preferred path for agents, scripts, migration tools, CMS audits, and batch exports.

### Requirements

- Framer project URL, usually copied from the browser URL bar in Framer.
  - Example: `https://framer.com/projects/Sites--aabbccddeeff`
- API key created from the project's Site Settings.
- A local Node.js script or automation runtime.
- `framer-api` installed in the script project.

### Setup

```bash
npm i framer-api
```

Store secrets outside source code:

```bash
export FRAMER_API_KEY="..."
export FRAMER_PROJECT_URL="https://framer.com/projects/<id>"
```

Never paste API keys into a Framer Code Component or any client-side bundle. Server API keys are project-bound credentials and should live in `.env`, a password manager, CI secrets, or another server-side secret store.

### Read CMS Collections

```ts
import { connect } from "framer-api"

const projectUrl = process.env.FRAMER_PROJECT_URL
const apiKey = process.env.FRAMER_API_KEY

if (!projectUrl || !apiKey) {
    throw new Error("Missing FRAMER_PROJECT_URL or FRAMER_API_KEY")
}

const framer = await connect(projectUrl, apiKey)

try {
    const collections = await framer.getCollections()

    for (const collection of collections) {
        const collectionId = (collection as any).id ?? "unknown"
        const fields = await collection.getFields()
        const items = await collection.getItems()

        console.log({
            id: collectionId,
            readonly: collection.readonly,
            managedBy: collection.managedBy,
            fields,
            items,
        })
    }
} finally {
    await framer.disconnect()
}
```

### Export CMS Data

```ts
import { writeFile } from "node:fs/promises"
import { connect } from "framer-api"

const framer = await connect(
    process.env.FRAMER_PROJECT_URL!,
    process.env.FRAMER_API_KEY!
)

try {
    const output: Record<string, unknown> = {}
    const collections = await framer.getCollections()

    for (const collection of collections) {
        const collectionId = (collection as any).id ?? "unknown"
        const collectionName = (collection as any).name || collectionId

        output[collectionName] = {
            id: collectionId,
            readonly: collection.readonly,
            managedBy: collection.managedBy,
            fields: await collection.getFields(),
            items: await collection.getItems(),
        }
    }

    await writeFile("framer-cms-export.json", JSON.stringify(output, null, 2))
} finally {
    await framer.disconnect()
}
```

## When to Use Plugin CMS APIs

Inside a Framer plugin, use the CMS APIs directly. The API surface includes collection discovery, active collection access, fields, items, item ordering, and field ordering.

Common operations:

- `getActiveCollection()`
- `getCollections()`
- `getManagedCollections()`
- `collection.getFields()`
- `collection.getItems()`
- `collection.addFields()`
- `collection.addItems()`
- `collection.removeFields()`
- `collection.removeItems()`

Use a plugin when the workflow needs to run inside the Framer editor. Use the Server API when the workflow needs to run from scripts, agents, CI, webhooks, or scheduled jobs.

## Legacy Fallback: Published Search Index

Use this only when all of the following are true:

- The user does not have an API key.
- The user does not have project edit access.
- The task is inspection/export from a public published site.
- The result can tolerate broken data if Framer changes the search index format.

### Steps

1. Open the published Framer site.
2. Trigger the Search component.
3. Open browser Developer Tools.
4. Go to the Network tab.
5. Look for a request related to `searchIndex`.
6. Open the response and save the JSON.

### Legacy Risks

- The URL and response shape are not a public contract.
- The data may be incomplete compared to CMS item data available through the API.
- The method can break without warning.
- It may not match the project's private draft state.

## Decision Rule

- Have project URL and API key: use Server API.
- Building an editor plugin: use Plugin CMS APIs.
- Only have a public site: use search-index extraction as a brittle fallback.

## References

- Framer Server API Quick Start: https://www.framer.com/developers/server-api-quick-start
- Framer API Reference: https://www.framer.com/developers/reference
- Framer Server API Examples: https://github.com/framer/server-api-examples
