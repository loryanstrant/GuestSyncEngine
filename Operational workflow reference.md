## Operational workflow reference

### Creating & Deleting Guests

Whether a guest is created or deleted in the host tenant is determined based on its membership of the synchronisation group from the source tenant.

When creating a guest, the following details are captured and used from the source tenant:

-   User principal name
-   ID
-   Display name
-   Job title
-   Department
-   Company
-   Profile photo

The data for each guest is stored in a SharePoint list, along with:

-   User principal name of guest account in host tenant
-   ID of guest account in host tenant
-   Date of creation

The workflow for determining whether a user is created as a guest in the host tenant is outlined in the below image:

<img src="https://user-images.githubusercontent.com/51473494/214439354-599f78fb-ccde-40ff-957a-8b43777f0c90.png">


If a guest account is found to no longer be a member of the security group in the source tenant, the account will be deleted from the host tenant. Additionally, the record in the SharePoint list will be updated to reflect that it has been deleted from Azure Active Directory, and the date of deletion.

The workflow for determining whether a guest is deleted from the host tenant is similar to the creation (but with less steps) and is outlined below:

<img src="https://user-images.githubusercontent.com/51473494/214439455-3f733f03-e449-443b-8c87-f5d97f25718f.png">


### User / guest profile properties

As part of the synchronisation, the following user properties are copied from the source user account to the guest account, and kept up to date:

-   Display name
-   Job title
-   Department
-   Company

These are kept up to date as there may be changes in the source tenant such as a person changing surname due to marriage/divorce, change of role or department, renaming of roles or departments, etc.

The workflow for determining and updating user/guest profile properties is outlined in the below image:

<img src="https://user-images.githubusercontent.com/51473494/214439580-5b58d55e-9986-4d6b-8892-61b4daa37165.png">


### Profile photos

As part of the creation of a guest account, if the source user account has a profile photo it is recorded in the SharePoint list.

For all users that have a profile photo, the photo is copied as part of a separate workflow, as outlined in the image below:

![image](https://user-images.githubusercontent.com/51473494/214439676-5966b037-d906-4dc5-a841-c5b17ef9130e.png)


There are two things to note about this process:

-   As there is no simple way to identify whether a profile photo has changed, they are copied for all identified users regardless.
-   Photos are not copied as part of initial guest creation as there is a cool-down time of at least 5 minutes before a profile photo can be applied to an account. It was chosen to separate the process entirely, so as not to slow down a potentially large batch of users being created.

### Guest status tracking

To assist with internal visibility and reporting, a workflow runs on a daily basis to identify which users have redeemed their guest invitations – and are therefore able to access the host intranet.

This workflow is relatively straight-forward, in that it looks for the guest invitation status to change from “Invited” to “Accepted”, and is outlined in the below diagram:

![image](https://user-images.githubusercontent.com/51473494/214439747-2304cb56-bc1c-48da-9cd4-29a344269d25.png)


### Frequency of operations

All workflows are triggered to run on a daily basis by a master workflow.

This allows for workflow schedules and time allowances to be adjusted with more granularity and ease than to manage the schedule of every single workflow.

### Risks

There are two primary risks to the solution.

**Over 5,000 items**

As the service account running the workflow is using the Power Automate license benefit including an Office 365 licensing, there is a limit of 5,000 items per connector.

Where this may be a potential issue is:

-   Synchronising from a security group that has more than 5,000 members
-   Number of guests *in total* managed by the solution exceeds 5,000 members

If either of these becomes an eventuality, the risk can be mitigated by assigning a “Power Automate per user” license to the service account.

**App registration secret expiration**

The Azure AD app registration secret expiration date cannot be more than 24 months away from its creation.

It is important to be aware of the expiration date and to set a reminder to address this beforehand.

If the solution were to stop functioning as a result of secret expiration, an administrator would simply need to generate a new secret for the app registration and update the value in the Guest Sync Tenant list for the host organisation value.
