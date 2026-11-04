Here is exactly how ServiceNow handles the reviewer's experience and how you can accurately capture the exact person who clicked "Approve" in your Flow.

### 1. How the Approver Reviews the Request

When your Flow hits the **Ask for Approval** step, ServiceNow automatically generates a new record in the standard **Approvals (`sysapproval_approver`)** table.

The approver does not need to navigate to your custom table to make a decision. They can review and approve it through standard channels:

* **Employee Center / Service Portal:** The request appears under their "My Approvals" widget.
* **Email:** ServiceNow can send an email with standard "Approve" and "Reject" mailto links, allowing them to approve directly from their inbox.
* **Backend UI:** They can view it under *Service Desk > My Approvals*.

### 2. How the System Captures and Stores the Decision

The `sysapproval_approver` table inherently captures the state (Approved/Rejected), the date, and the user who made the decision. However, your requirement specifically asks to store this on your *custom* table in the `approved_by` field.

While you could just map the employee's manager to that field, **best practice dictates you should find the actual person who clicked approve**. (For example, if the manager is on PTO, a delegated approver might have clicked it instead).

Here is how to update your Flow Designer logic to capture the *actual* approver:

**Add these steps inside your "If Request is Approved" block:**

1. **Action: Look Up Record**
* **Table:** Approval (`sysapproval_approver`)
* **Conditions:**
* `Approval for` **is** `[Trigger -> Record]`
* `State` **is** `Approved`


* *Why this matters:* This searches the system for the exact approval record tied to your custom ID card request that was just approved.


2. **Action: Update Record (Modifying step 4 from the previous guide)**
* **Record:** `[Trigger -> Record]`
* **Fields:**
* **Status:** `Approved / Processing`
* **Approved By:** `[Look Up Record -> Approver]` *(This pulls the sys_id of the actual person who did the approving, accounting for delegates).*





This satisfies the requirement to capture the decision on the record itself, while using enterprise-grade logic to handle real-world exceptions like delegated approvals.
