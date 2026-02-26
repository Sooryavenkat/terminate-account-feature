# One Page PRD: Terminate Account (HECVAT Compliance)

**Description: What is it?**
A new feature that empowers authorized administrators to completely and permanently delete account-level data from the Securin platform. This includes the ability to delete Linked Accounts, Management Accounts, and Partner Accounts based on a strict permission and conditional matrix.

**Problem: What problem is this solving?**
Presently, Customer Admins on the Securin platform lack the ability to delete their account-level data. The HECVAT (Higher Education Community Vendor Assessment Toolkit) framework mandates that vendors clearly provide mechanisms to securely manage the data lifecycle, specifically the removal/deletion of data. Without this, the platform fails to meet this crucial compliance standard.

**Why: How do we know this is a real problem and worth solving?**
This is a strict compliance requirement under the HECVAT framework. Failing to provide data deletion capabilities can prevent the adoption or renewal of the platform by higher education institutions and other organizations that require adherence to stringent data lifecycle management policies.

**Success: How do we know if we’ve solved this problem?**
- A secure, reliable, and irreversible deletion mechanism is available via both the UI and the API.
- Users with the correct permissions (replicating the existing "Edit Account" permissions) can successfully delete Linked Accounts.
- Deletion appropriately cascades the removal of associated data (connector integrations, assets, exposures, tags, dashboards, Remops, Tasksuits, filters, and app access).
- The system correctly enforces prerequisites (e.g., a Management Account cannot be deleted until all its Linked Accounts are deleted first).

**Audience: Who are we building for?**
- **Securin Admins (L-1)**: Full oversight, able to delete Partner, Management, and Linked accounts.
- **Partner Account Admins**: Able to manage their customers by deleting Management and Linked accounts.
- **Customer Account Admins (L0)**: Able to delete their logically isolated Linked accounts from the centralized Management account interface.

**What: Roughly, what does this look like in the product?**
* **Access Points:** Account lists within a Linked Account and a Management Account.
* **UX/UI Workflow:**
  * Displays a destructive action button (similar UX to the existing user deletion tab).
  * Forces the user to reassign their "default account" if the one being deleted is currently marked as default.
  * Warns the user of irreversible consequences.
* **Deletion Rules Engine:**
  * **Linked Account (L1):** Can be deleted directly. It removes all operational data within the isolation boundary.
  * **Management Account (L0):** Can only be deleted *if* all associated Linked Accounts have already been deleted.
  * **Partner Account (L-1):** Can only be deleted by Securin Admins, and *only if* all associated Management Accounts (and their Linked Accounts) are already deleted. Partner Admins cannot delete their own Partner Account.
* **API Details:** Implements RESTful conventions by adding a `DELETE` method to the existing endpoint:
  * `DELETE /api/v1/accounts/{account-id}/sub-accounts/{id}`

**How: What is the experiment plan?**
1. **API Development:** Implement the `DELETE` method, ensuring proper authentication, authorization (matching Edit Account specs), and data teardown logic.
2. **Conditional Logic:** Build the validation engine that blocks Parent/Management account deletion if child accounts exist.
3. **Frontend Implementation:** Add deletion actions to the Account list screens.
4. **UX Flow:** Implement the forced "shift default account" pre-requisite UX when deleting a default account.
5. **Testing & QA:** Rigorous testing of the destructive action to ensure data isolation is respected (no data bleed) and that permissions precisely match the defined matrix.

**When: When does it ship and what are the milestones?**
*Milestones to be defined with the engineering team (API completion, UI completion, QA sign-off, Production release).*

---

### Open Questions / Notes
* **Management Accounts without Linked Accounts:** Can a management account exist without a linked account? If so, why? (The requirement notes that for a linked account, a management account is mandatory, but the reverse needs product clarification to ensure the validation logic behaves correctly).