# Architecture Reference

The following information relates only to the host tenant, as that is where the entire solution resides.

## App registration

![Graphical user interface, text, application Description automatically generated](media/f3101546fda1b82c0effbcc674a2b9b9.png)

![Graphical user interface, text, application, email Description automatically generated](media/2714485a02c9e05d7a462c264f1723c1.png)

![Graphical user interface, text, application, email Description automatically generated](media/4f0a65e145c9f15ae15d4c54b22adaa0.png)

![Graphical user interface, text, application Description automatically generated](media/70f507f47d81d9c4ca481efaee082960.png)

## SharePoint permissions

In order for guests to access a specific site in SharePoint, the permissions must be configured similar to the below image.

![Graphical user interface, application Description automatically generated](media/7271501bfc7f75e357901c74bb567371.png)

## Operational data storage

Four lists are required in a SharePoint site, to store tenant settings used by workflows, users being synchronised, and statistics.

An existing SharePoint site may be used, however as the nature of the content being stored is potentially sensitive – it is important that access to the site is restricted.

A dedicated site is preferred, this should only be a *communication* site – not a *team* site, as it only needs to store four lists.

### List configuration

#### List 1: Guest Sync Tenants

This list stores the values used by the workflows for performing the synchronisation for each tenant.

The columns required for the list are:

| Name                                  | Purpose                                                                                                               | Type                | Required |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------|---------------------|----------|
| Organisation (renamed “Title” column) | Name of the organisation the tenant details refer to                                                                  | Single line of text | Y        |
| TenantID                              | ID of the Azure AD / M365 tenant                                                                                      | Single line of text | Y        |
| ApplicationID                         | ID of the application registration (also known as “Client ID”)                                                        | Single line of text | Y        |
| SecretValue                           | Value of the secret generated for the application                                                                     | Single line of text | Y        |
| SyncGroupID                           | ID of the security group in the subsidiary tenant which manages which users are to be synchronised to the host tenant | Single line of text | Y\*      |

\* The host tenant does not need to have the SyncGroupID field populated, as this functionality is not used in the parent tenant.

An example of how this list should appear:

![](media/e2bcc60d47719b775ea436e91d4ad574.png)

#### List 2: Guest Sync Status

This list stores the values used by the workflows for tracking various profile attributes, as well as dates of when guest states are changed – such as when they are created, redeemed (i.e. logged in for the first time), and deleted.

The columns required for the list are:

| Name                                 | Purpose                                                                                                                                                                                                                                                                                       | Type                | Required |
|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------|----------|
| ExternalUPN (renamed “Title” column) | User Principal Name (UPN) of the user from the subsidiary tenant                                                                                                                                                                                                                              | Single line of text | Y        |
| ExternalUserID                       | ID of the user object from the subsidiary tenant                                                                                                                                                                                                                                              | Single line of text |          |
| hostUPN                              | UPN of the guest in the host tenant                                                                                                                                                                                                                                                           | Single line of text |          |
| hostUserID                           | ID of the guest in the host tenant                                                                                                                                                                                                                                                            | Single line of text |          |
| DisplayName                          | Full name as identified in the subsidiary tenant                                                                                                                                                                                                                                              | Single line of text |          |
| ExternalCompany                      | Company name as identified in the subsidiary tenant                                                                                                                                                                                                                                           | Single line of text |          |
| ExternalDepartment                   | Department name as identified in the subsidiary tenant                                                                                                                                                                                                                                        | Single line of text |          |
| JobTitle                             | Job title as identified in the subsidiary tenant                                                                                                                                                                                                                                              | Single line of text |          |
| Status                               | Current status of the guest account: Pending Redemption = they have not redeemed their guest “invitation” yet Redeemed = they *have* redeemed their guest invitation Deleted = no longer within scope of the subsidiary synchronisation group, guest object has been deleted from host tenant | Single line of text |          |
| ProfilePhoto                         | Identifies where the guest has a profile photo in their user account within the subsidiary tenant NOTE: the default value should be *No*                                                                                                                                                      | Yes/No              |          |
| InvitedDate                          | The date the guest was added to the host tenant NOTE: the default value should be *(None)*                                                                                                                                                                                                    | Date and Time       |          |
| RedeemedDate                         | The date the guest redeemed their invitation to the host tenant NOTE: the default value should be *(None)*                                                                                                                                                                                    | Date and Time       |          |
| DeletedDate                          | The date the guest was deleted from the host tenant NOTE: the default value should be *(None)*                                                                                                                                                                                                | Date and Time       |          |
| SyncOrg                              | The organisation (tenant) that the guest comes from                                                                                                                                                                                                                                           | Single line of text |          |

#### List 3: Guest Sync Status Statistics

This list simply stores the status of how many guests are currently pending redemption, vs. those that have redeemed their invitation and signed into the tenant.

The columns required for the list are:

| Name                            | Purpose                                                                           | Type                | Required |
|---------------------------------|-----------------------------------------------------------------------------------|---------------------|----------|
| Status (renamed “Title” column) | Stores the two redemption statuses of guests: “Pending Redemption” and “Redeemed” | Single line of text | Y        |
| Count                           | Stores the numerical value of the number of guests in the two redemption statuses | Number              |          |

#### List 4: Guest Sync Organisation Statistics

Similar to the previous list, this list is purely for the storage of the number of guests per subsidiary organisation.

The columns required for the list are:

| Name                                  | Purpose                                                                                                           | Type                | Required |
|---------------------------------------|-------------------------------------------------------------------------------------------------------------------|---------------------|----------|
| Organisation (renamed “Title” column) | Stores the names of the organisations, as defined in the “Organisation” column from the *Guest Sync Tenants* list | Single line of text | Y        |
| Count                                 | Stores the numerical value of the number of guests from each organisation                                         | Number              |          |

### Guest statistics

To provide some basic statistics around guest redemption states as well as source organisations, the *Quick Chart* web part can be used on a SharePoint page to display visuals such as these:

![](media/724ac88ca71e304ca249da6dac569973.png)

| The Guest Redemption Status pie chart draws its data from the Guest Sync Status Statistics list.                                      | The Guests per Organisation pie chart draws its data from the Guest Sync Organisation Statistics list.                                |
|---------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------|
| ![Graphical user interface, text, application, email Description automatically generated](media/3a5aa14fe451d4bb7113f6ee3ae9eca2.png) | ![Graphical user interface, text, application, email Description automatically generated](media/f03a208a08d9351b0f31c18d2f42fa7b.png) |

## Workflows

There are six workflows for the entire solution:

![](media/36177eaeb1330f850d0d563b5df20702.png)

The details of each workflow are outlined below.

### Control all workflows

This is the master workflow, which triggers all of the other workflows to run.

It exists primarily to allow flexibility and ease of management of the scheduling.

The workflow runs every day, at 1, 3, 5, 7, and 9am. It does this so as to allow each workflow up to 2 hours to complete processing, before triggering the next one. This amount of time should only be required for initial creation of large amounts of guests, but otherwise leaves ample time in between.

![Graphical user interface, application Description automatically generated](media/ccc0b3b9eada60cee1f4583a62014e9b.png)

The two actions around UTC and AEST are simply to ensure that the time controls are using AEST times, instead of UTC (which is the time zone the workflow runs in, even though its schedule is based on AEST).

From there, the child workflows are triggered via HTTP calls and configured to run at the relevant hour intervals.

![Graphical user interface, application Description automatically generated](media/cd8a532bd592c83dcd4bcf06fbf788dd.png)

The method of HTTP calls and hour-based controls has been chosen to give a greater level of control if required, as well as the ability to run child workflows independently.

### Copy users to host tenant

The copy users workflow is used for both initial subsidiary onboarding, as well as ongoing addition of guests from subsidiary tenants.

It is triggered by the control workflow via HTTP and performs several steps.

![A picture containing timeline Description automatically generated](media/89c882dd665681cc01ffd223115ab68a.png)

The two “Get items” workflow steps below, show where the workflow gets the Azure AD app registration details for the host tenant (first action), as well getting the same information as well as the security group ID for any tenant in the list that *is not* host.

![](media/9b71254f1de721d312d306c51e72b60c.png)

From here, the same set of actions are performed against each subsidiary listed in the Guest Sync Tenants list:

![](media/9d4acedd143deb7475b7ffa5c9d36ff2.png)

![Graphical user interface, application, Teams Description automatically generated](media/65a1be9818185daedba6a61274eed50e.png)

![Graphical user interface, application Description automatically generated](media/6d5ba0ce822930ed1278c656a26e9e73.png)

The key activities of this workflow include:

-   Retrieve a list of group members from the subsidiary tenant
-   Compare the list against the guests listed for the subsidiary in the Guest Sync Status list
-   Create guests for any users that are not in the list but are in the subsidiary tenant group
-   Capture the created guest account details in the list
-   Populate the guest account with profile details from the subsidiary tenant user account
-   Identify and record whether the account has a profile photo

A 1-second cooldown exists for each guest created, as sometimes Power Automate works faster than Azure AD does in creating the guest – which can result in an error in the workflow. The cooldown assists by allowing Azure AD to catch up, before obtaining the UPN of the created guest account.

Additionally, profile photos cannot be applied to Azure AD user/guest accounts for at least 5 minutes after creation. Putting a delay to cater for this in the workflow could considerably slow it down when a large volume of guests is involved – so instead a step is taken to record the existence of a profile photo for the next workflow to pick up.

### Copy/update profile photos

The workflow to copy and/or update profile photos works by simply looking at the Guest Sync Status list to determine which guests have profile photos in their home tenants, and then copies them across.

It performs this regardless of whether a photo has already been copied, as there is no way to know if the photo has changed without using image analytics.

The initial part of the workflow is the same as the trigger and first two actions in the “Copy users to host tenant” workflow and has been excluded from the below screenshot.

![Graphical user interface, text, application Description automatically generated](media/16cd0f710ab3d500ce7ba9113fa44a71.png)

### Delete missing users from host tenant

A common problem for many organisations is having stale guests in their Azure AD. This workflow aims to assist that by cleaning up guests that no longer exist in the subsidiary tenant as users.

The initial part of the workflow is the same as the trigger and first two actions in the “Copy users to host tenant” workflow and has been excluded from the below screenshots.

![Graphical user interface, application Description automatically generated](media/5cc61163d0e1d4668e21c748db91482c.png)

![Graphical user interface, application Description automatically generated](media/81a39ba1371e0d963ddaa11b646db9e5.png)

The key activities of this workflow include:

-   Retrieve a list of group members from the subsidiary tenant
-   Compare the list against the guests listed for the subsidiary in the Guest Sync Status list
-   Delete guests for any users that *are* in the list but *are* *not* in the subsidiary tenant group
-   Update the guest details in the list to reflect their state as deleted, and date it was identified that they were deleted

### Update guest redemption state & statistics

To track whether guests have redeemed their “invitation” or are still pending, as well as to provide data that is used for the graphs on the front page of the SharePoint site, this workflow performs both of those functions. It is divided into two parallel streams, as seen below:

![Graphical user interface, application, Teams Description automatically generated](media/a974a32adc05bd8cae8b3eeae7ce7a29.png)

#### Redemption Status & Statistics

The part of the workflow that checks for redemption status is expanded below:

![Graphical user interface, application Description automatically generated](media/3ab204ba1cdb8f341aad1ad644d929f4.png)

The key activities of this part of the workflow include:

-   Check the status in Azure AD of all guests that have a status of “Pending Redemption”
-   Update the guest record for any that have redeemed their invitation, as well as recording the date it occurred
-   After performing this, get the count of all guests in both “Pending Redemption” and “Redeemed” states, and update the count in the Guest Sync Status Stats list

#### Organisation Statistics

The part of the workflow that calculates the number of guests per subsidiary organisation is expanded below:

![Graphical user interface, application Description automatically generated](media/e49ef3989898871dc10de44241f8a036.png)

The key activities of this part of the workflow include:

-   Delete existing records
-   Retrieve every organisation that needs to be counted
-   For each organisation, count the number of users and create an item in the Guest Sync Org Stats list

### Update user properties of host guests

As users may have their profile properties (i.e. name, job title, department, company) updated in the subsidiary tenant, this workflow helps keep them updated in the host tenant.

The initial part of the workflow is the same as the trigger and first two actions in the “Copy users to host tenant” workflow and has been excluded from the below screenshots.

![Graphical user interface, application, Teams Description automatically generated](media/9a4a2d76d7f56718bcf504460bb965b4.png)

![Graphical user interface, application Description automatically generated](media/8e0667480a997114541b987552b2bc6e.png)

The key activities of this workflow include:

-   Retrieve the group members from the subsidiary tenant, which includes their profile details
-   Store the data into an array for faster comparison
-   For each guest, compare whether the profile fields match against the record in the Guest Sync Status list
-   If any field has changed, update all fields with the current information
