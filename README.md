# Workflow / Security

References:
- [Workflows and the Workbox](https://doc.sitecore.net/sitecore_experience_platform/82/content_authoring/managing_items/workflows/workflows_and_the_workbox)
- [SCORE Component Datasource Workflow](https://brainjocks.atlassian.net/wiki/spaces/SDD/pages/114525478/Component+Datasource+Workflow)

## Considerations

- Are any workflow steps required for any sort of compliance (e.g., legal, or company policy)?
- Iterate to find/fix bottlenecks in workflow. Start simple and grow as needed.
- Workflow will slow down assembly, but is necessary for maintenance.
- Only a single workflow can be applied per item. The workflow state, however, changes between languages.

## Page Structure

- Pages contain a green `content` folder, which contains the different parts of a given page.  This folder contains the page's local `data sources`

![location page](/img/location-page.png)

- Pages inherit enterprise & score 'Features' that give them certain functionality
  - Navigation
  - SEO
  - Location Meta Data
- Pages contain `components`, also known as Sitecore Renderings.
- Components have corresponding Datasource items, which represent different sections of a page

![location page](/img/location-page2.png)

- When editing data (e.g., change text, swap an image) on a page, you are really editing the corresponding datasource.
- Example activities that result in editing the page item:
  - `moving` components around
  - `personalizing` components
  - `style selections` or any other component properties (rendering parameters) being changed on any component
  - editing `metadata` such as SEO Keywords
  - changing the `location address`

## Workflow:

- Workflows are composed of `states` and `transitions`
- Sitecore's workflow system is very similar to other workflow systems.  An item will move from state to state as user's perform transitions.
- When an item has workflow applied, it will always have a workflow state assigned.  Example states:
  - Draft
  - Pending Approval
  - Live
- To move an item from state to state, user's perform transitions.  Example transitions:
  - Submit for Approval
  - Reject Changes
  - Approve Changes
- As transitions occur, business logic can be executed.  For example:
  - submitting an item for approval can trigger an email to be sent to the corresponding approver.
  - approving changes can trigger an automatic publish of the item.
  - etc

![basic workflow](/img/basic-workflow.png)

- When an item transitions to draft mode, a new version is created.  While this version is not in the Live workflow state, it is not publishable.

## Security
- Security permissions should be granted to user `roles`.  You can assign multiple `roles` to a `user`.
- Security roles can control `actions` and `visibility`
- Example `actions`:
  - edit an item
  - create an item
  - rename an item
  - execute a script
  - _execute a particular workflow transition_
- Example `visibility`:
  - Aerotek content author can see Aerotek settings, but cannot edit them.
  - Aerotek content author cannot see EASi content.
  - ACS Developer can see all OpCo sites, but cannot edit content.

## General Recommendations & Considerations

- Split the security model into two categories: content editing and content approval.
- For content editing, create roles that grant access to the content tree.  For example:
  - `OpCo Simple User` can edit datasources, but cannot move components. Could potentially insert branch templates for certain content areas such as Insights or News.
  - `OpCo Content Author` can edit datasources and move components. Can edit all pages and manage non-tile snippets
  - `OpCo Power User` can edit all content for the OpCo, including settings.  Allows user to execute utility scripts but not access script terminal.
- For workflow, create roles that allow transitions to occur.  For example:
  - All users are allowed to transition items to `draft` mode if they are able to edit the item.
  - All users are allowed to `Submit for Approval`, and cannot directly publish changes.
  - `OpCo Workflow Approver` can approve or reject any item in the `Pending Approval` state.
- If you keep the model split, you can create interesting user accounts based on business need:
  - Bob is an `OpCo Power User`
  - Sally is an `OpCo Content Author` and an `OpCo Workflow Approver`
  - Sam is an `OpCo Simple User` for 3 seperate OpCo's.
- When an item is in the final workflow state, it becomes publishable.  If the item is in any other workflow state, it cannot be published to the web database.
- You can `simplify editing` for non-advanced users by `removing their ability to move and insert components`.  These users would be 100% reliant on branch templates, but can be an effective way to manage the creation of new pages such as insights, news, and events.
- When approving changes in experience editor, you can enable SCORE's `approve with datasources` helper: https://brainjocks.atlassian.net/wiki/spaces/SDD/pages/114525507/Workflow+Approval+Configuration
- Workbox is not very intuitive and difficult to navigate.  May be worthwhile to create powershell utilities for approvers.
  - Example: right click sub-tree in content editor, and select a script for "Approve Items"