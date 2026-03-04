# AttachmentManager PCF Control

## Project Overview
This codebase is a **PowerApps Component Framework (PCF)** control named **AttachmentManager**. Its primary purpose is to allow users to browse documents stored in **SharePoint** and attach them directly to **Dynamics 365 Email** records.

## Key Architecture & Components

### 1. Core Lifecycle (`index.ts`)
- Acts as the bridge between the Dynamics 365 framework and the React UI.
- Handles initialization (`init`) and responds to data changes (`updateView`).
- Manages the process of fetching SharePoint document metadata and triggering the attachment creation.

### 2. User Interface (`AttachmentManagerApp.tsx`)
- Built using **React** and **Office UI Fabric (Fluent UI)**.
- Provides a "Attach Files" command bar button.
- Opens a modal dialog with a searchable `DetailsList` of files found in SharePoint related to the current record.

### 3. Data Modeling & Helpers
- **`Entity.ts`**: Contains the FetchXML used to query the `sharepointdocument` entity in Dynamics 365.
- **`PCFHelper.ts`**: Provides utilities for handling entity references and constructing URLs for the file retrieval service.
- **`ItemList.ts`**: Manages the data structure for the file list displayed in the UI.
- **`IconMapper.ts`**: Maps SharePoint file types to Fluent UI icons for a better visual experience.

### 4. External Integration (`http.ts` and the included `.zip` file)
- The control relies on a **Power Automate (Flow)** to actually read the file content from SharePoint. The included zip file contains the definition for this flow.
- When a user selects files to attach, the control calls this Flow's HTTP endpoint, receives the file content, and then uses the Dynamics 365 WebAPI to create `activitymimeattachment` records.

## Operational Workflow
1.  **Query**: On load, it identifies the "Regarding" object of the email and queries Dynamics 365 for related SharePoint documents.
2.  **Display**: It presents these documents in a list where users can multi-select files.
3.  **Transfer**: When "Attach" is clicked, it:
    *   Calls a Power Automate Flow to get the file content (Base64).
    *   Creates a new attachment record in Dynamics 365 for each selected file, linking it to the current Email.
