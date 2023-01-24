# Architecture Reference

The following information relates only to the host tenant, as that is where the entire solution resides.

## App registration
![image](https://user-images.githubusercontent.com/51473494/214435819-5bf44d9d-8585-48d1-a2cc-1f6c9061fe16.png)
![image](https://user-images.githubusercontent.com/51473494/214435836-d3afe4cc-ba8d-450d-992e-501a8bbb35a4.png)
<img width="325" alt="image" src="https://user-images.githubusercontent.com/51473494/214435960-d5b5c953-1815-48ca-8ac1-4ba081b87e34.png">
![image](https://user-images.githubusercontent.com/51473494/214435981-4c13a43b-0f2d-4c9f-947d-e28cf9be1bca.png)


## SharePoint permissions

In order for guests to access a specific site in SharePoint, the permissions must be configured similar to the below image.

![image](https://user-images.githubusercontent.com/51473494/214436020-4f635255-f2be-481e-86c3-aa029fac6bca.png)


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

![image](https://user-images.githubusercontent.com/51473494/214436059-53815788-d30c-493f-8af8-79f1f4ce9b67.png)


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

![image](https://user-images.githubusercontent.com/51473494/214436142-03f334ea-d756-43ff-8c41-a436c3e01d16.png)


The Guest Redemption Status pie chart draws its data from the Guest Sync Status Statistics list.                                |
![image](https://user-images.githubusercontent.com/51473494/214436183-cf3fea72-b84a-4188-aa47-227296ada961.png)

The Guests per Organisation pie chart draws its data from the Guest Sync Organisation Statistics list.
![image](https://user-images.githubusercontent.com/51473494/214436212-1926353d-5221-4004-bcb0-a511732c07d7.png)

## Workflows

There are six workflows for the entire solution:

![image](https://user-images.githubusercontent.com/51473494/214436236-fe53cfb8-7f31-4984-b79e-720bc543f06d.png)


The details of each workflow are outlined below.

### Control all workflows

This is the master workflow, which triggers all of the other workflows to run.

It exists primarily to allow flexibility and ease of management of the scheduling.

The workflow runs every day, at 1, 3, 5, 7, and 9am. It does this so as to allow each workflow up to 2 hours to complete processing, before triggering the next one. This amount of time should only be required for initial creation of large amounts of guests, but otherwise leaves ample time in between.

![image](https://user-images.githubusercontent.com/51473494/214436278-5d57b09a-446e-47d0-b7b0-372c74a1f746.png)


The two actions around UTC and AEST are simply to ensure that the time controls are using AEST times, instead of UTC (which is the time zone the workflow runs in, even though its schedule is based on AEST).

From there, the child workflows are triggered via HTTP calls and configured to run at the relevant hour intervals.

![image](https://user-images.githubusercontent.com/51473494/214436307-4a66bb8c-8001-464d-82ae-6a913c1fee2f.png)


The method of HTTP calls and hour-based controls has been chosen to give a greater level of control if required, as well as the ability to run child workflows independently.

### Copy users to host tenant

The copy users workflow is used for both initial subsidiary onboarding, as well as ongoing addition of guests from subsidiary tenants.

It is triggered by the control workflow via HTTP and performs several steps.

![image](https://user-images.githubusercontent.com/51473494/214436358-e06efeb7-ef40-41c3-b012-a34ac076fcaf.png)


The two “Get items” workflow steps below, show where the workflow gets the Azure AD app registration details for the host tenant (first action), as well getting the same information as well as the security group ID for any tenant in the list that *is not* host.

![image](https://user-images.githubusercontent.com/51473494/214436381-65fb1593-6492-4088-a56f-b0b1b34b4747.png)

From here, the same set of actions are performed against each subsidiary listed in the Guest Sync Tenants list:

![image](https://user-images.githubusercontent.com/51473494/214436431-b0b1d036-f5ee-4b40-bf27-7d09ab302bb3.png)

![image](https://user-images.githubusercontent.com/51473494/214436457-b6d73637-d3f5-429b-aa70-9ba82c99f294.png)

![image](https://user-images.githubusercontent.com/51473494/214436483-2e5f6ed7-cb39-475c-98fc-8c94df13c2df.png)

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

![image](https://user-images.githubusercontent.com/51473494/214436506-27089444-5220-4c5d-b6ec-991b8160344c.png)

### Delete missing users from host tenant

A common problem for many organisations is having stale guests in their Azure AD. This workflow aims to assist that by cleaning up guests that no longer exist in the subsidiary tenant as users.

The initial part of the workflow is the same as the trigger and first two actions in the “Copy users to host tenant” workflow and has been excluded from the below screenshots.

![image](https://user-images.githubusercontent.com/51473494/214436541-32349662-1279-4b44-9dfe-7974ec1646fe.png)

![image](https://user-images.githubusercontent.com/51473494/214436566-6d8bfede-7f66-4b2f-b866-df186e23718e.png)

The key activities of this workflow include:

-   Retrieve a list of group members from the subsidiary tenant
-   Compare the list against the guests listed for the subsidiary in the Guest Sync Status list
-   Delete guests for any users that *are* in the list but *are* *not* in the subsidiary tenant group
-   Update the guest details in the list to reflect their state as deleted, and date it was identified that they were deleted

### Update guest redemption state & statistics

To track whether guests have redeemed their “invitation” or are still pending, as well as to provide data that is used for the graphs on the front page of the SharePoint site, this workflow performs both of those functions. It is divided into two parallel streams, as seen below:

![image](https://user-images.githubusercontent.com/51473494/214436608-ba2af10d-82cc-4767-ad11-c3565390a344.png)

#### Redemption Status & Statistics

The part of the workflow that checks for redemption status is expanded below:

![image](https://user-images.githubusercontent.com/51473494/214436639-0edc0345-2d9b-4c4a-b3b6-c21908fa70b0.png)

The key activities of this part of the workflow include:

-   Check the status in Azure AD of all guests that have a status of “Pending Redemption”
-   Update the guest record for any that have redeemed their invitation, as well as recording the date it occurred
-   After performing this, get the count of all guests in both “Pending Redemption” and “Redeemed” states, and update the count in the Guest Sync Status Stats list

#### Organisation Statistics

The part of the workflow that calculates the number of guests per subsidiary organisation is expanded below:

![image](https://user-images.githubusercontent.com/51473494/214436663-41f8019f-d296-4b8f-8739-2ae43f5d92d2.png)

The key activities of this part of the workflow include:

-   Delete existing records
-   Retrieve every organisation that needs to be counted
-   For each organisation, count the number of users and create an item in the Guest Sync Org Stats list

### Update user properties of host guests

As users may have their profile properties (i.e. name, job title, department, company) updated in the subsidiary tenant, this workflow helps keep them updated in the host tenant.

The initial part of the workflow is the same as the trigger and first two actions in the “Copy users to host tenant” workflow and has been excluded from the below screenshots.

![image](https://user-images.githubusercontent.com/51473494/214436716-22629212-3b08-447a-8e1d-1d6ae67b3ea3.png)

![image](https://user-images.githubusercontent.com/51473494/214436738-4ce27daf-264c-45b4-a66a-89383e9ce561.png)

The key activities of this workflow include:

-   Retrieve the group members from the subsidiary tenant, which includes their profile details
-   Store the data into an array for faster comparison
-   For each guest, compare whether the profile fields match against the record in the Guest Sync Status list
-   If any field has changed, update all fields with the current information
