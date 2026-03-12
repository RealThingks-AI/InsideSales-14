

## Campaign Module — Review & Improvements Plan

The Campaign module is already implemented with core CRUD, 7 database tables, sidebar routing, list page, detail panel with 6 tabs, and Convert to Deal dialog. After reviewing all components against the specification, here are the gaps and improvements needed.

### Issues & Missing Features

**1. Email Templates & Phone Scripts tabs missing from detail panel**
The database tables `campaign_email_templates` and `campaign_phone_scripts` exist, and types are defined, but there are no UI components or hooks to manage them. The Outreach tab only logs communications — it doesn't let users create/manage email templates or phone scripts per campaign.

**2. No hooks for email templates and phone scripts**
`useCampaigns.tsx` has hooks for campaigns, accounts, contacts, communications, and materials — but nothing for `campaign_email_templates` or `campaign_phone_scripts`.

**3. Campaign list missing key columns**
The spec calls for: Accounts Targeted, Contacts Targeted, Deals Created, Deals Won. Currently only shows: Name, Type, Status, Start/End Date, Region.

**4. No owner display or selection**
Campaign modal has no owner field. The `owner` column is UUID but there's no user picker. Campaign list doesn't show owner either.

**5. No delete confirmation**
Deleting a campaign from the list dropdown has no confirmation dialog.

**6. No bulk add for accounts**
Plan specifies bulk-adding accounts. Currently only single-add via popover.

**7. Settings integration missing**
No "Campaign Settings" section in Admin Settings.

**8. Analytics lacks visualizations**
Analytics tab shows stat cards and text summary. Plan calls for Recharts-based funnel/chart visualizations (Outreach Funnel, Response Rate, Deals Generated).

**9. Convert to Deal doesn't update campaign_account status**
When converting a contact to a deal, the code updates `campaign_contacts.stage` to "Qualified" but doesn't update the associated `campaign_accounts.status` to "Deal Created".

**10. Communication doesn't appear in contact/account activity history**
Plan states all campaign communications should appear in Account and Contact activity history. Currently they're siloed in the campaign.

### Implementation Plan

#### Phase A — Critical Fixes (implement now)

**File: `src/hooks/useCampaigns.tsx`**
- Add `useCampaignEmailTemplates(campaignId)` hook — CRUD for `campaign_email_templates`
- Add `useCampaignPhoneScripts(campaignId)` hook — CRUD for `campaign_phone_scripts`

**New file: `src/components/campaigns/CampaignEmailTemplatesTab.tsx`**
- Table listing templates (template_name, email_type, audience_segment, subject)
- Create/edit dialog with fields: template_name, subject, body, email_type, audience_segment
- Delete button per template

**New file: `src/components/campaigns/CampaignPhoneScriptsTab.tsx`**
- Table listing scripts (script_name, audience_segment)
- Create/edit dialog with fields: script_name, opening_script, key_talking_points, discovery_questions, objection_handling, audience_segment
- Delete button per script

**File: `src/components/campaigns/CampaignDetailPanel.tsx`**
- Add two new tabs: "Templates" and "Scripts" between Outreach and Materials
- Import and render the new tab components

**File: `src/components/campaigns/CampaignList.tsx`**
- Add columns: Accounts, Contacts, Deals Created
- These require aggregate counts — fetch via separate queries or add summary data to the campaigns list query

**File: `src/components/campaigns/ConvertToDealDialog.tsx`**
- After creating the deal, also update the associated `campaign_accounts` record status to "Deal Created"

**File: `src/components/campaigns/CampaignList.tsx`**
- Add delete confirmation using AlertDialog before calling `onDelete`

#### Phase B — Enhancements (implement after Phase A)

**File: `src/components/campaigns/CampaignAnalytics.tsx`**
- Add Recharts bar chart for outreach funnel (Targeted → Contacted → Responded → Qualified → Deal Created)
- Add pie chart for communication type breakdown

**File: `src/components/campaigns/CampaignModal.tsx`**
- Add owner field with user picker (fetch profiles, show dropdown)

**File: `src/components/campaigns/CampaignAccountsTab.tsx`**
- Add checkbox selection + "Add Selected" bulk-add capability

**Settings integration** — Add Campaign Settings sub-section in AdminSettingsPage for configuring campaign types, call outcomes, LinkedIn statuses (this is a larger effort, can be deferred).

### File Summary

| Action | File |
|--------|------|
| Create | `src/components/campaigns/CampaignEmailTemplatesTab.tsx` |
| Create | `src/components/campaigns/CampaignPhoneScriptsTab.tsx` |
| Modify | `src/hooks/useCampaigns.tsx` — add 2 hooks |
| Modify | `src/components/campaigns/CampaignDetailPanel.tsx` — add 2 tabs |
| Modify | `src/components/campaigns/CampaignList.tsx` — add columns + delete confirmation |
| Modify | `src/components/campaigns/ConvertToDealDialog.tsx` — update account status |
| Modify | `src/components/campaigns/CampaignAnalytics.tsx` — add Recharts charts |
| Modify | `src/components/campaigns/CampaignModal.tsx` — add owner picker |

No database changes needed — all tables already exist.

