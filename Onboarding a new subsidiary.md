# Onboarding a new subsidiary

# Onboarding a new subsidiary

## Subsidiary tenant requirements

To simplify the implementation effort and level of complexity, only two things are required in the subsidiary tenant:

-   A security group to manage which users are synchronised across to host as guests
-   An app registration to enable the workflows to extract data from Azure Active Directory

### Security group

The security group within the subsidiary tenant is used by the workflows to identify which users should be created as guests within the host tenant.

This is done to reduce the potential clutter of having service accounts and other resources from the subsidiary’s Azure Active Directory tenant, created in the host tenant.

It is recommended that this group be configured with the membership type as *Assigned* to provide a greater level of control. If *Dynamic* is chosen, then the rules should be validated to ensure only valid users are added.

<img src="https://user-images.githubusercontent.com/51473494/214440259-8e451a14-b516-4b7f-a613-0f7053a09461.png">

<img src="https://user-images.githubusercontent.com/51473494/214440438-eae1c6e1-473a-4fb2-917b-e42d391cfdaf.png">


### App registration

The app registration within the subsidiary tenant provides the host workflows with access to identify users that must be synchronised to the host tenant, updated, or deleted. Additionally, it enables the copying of profile information and photo.

The following images outlines the process for configuring the app registration in Azure AD.

**Initial creation**

Upon creation of a new app registration, specify a suitable display name that will makes sense and is logical.

For supported account types, select “Accounts in this organizational directory only”.

For Redirect URI, select “Web” from the dropdown, and enter the following URL: *https://global.consent.azure-apim.net/redirect*

<img src="https://user-images.githubusercontent.com/51473494/214440540-ac3b36b2-9a06-4d2f-8fa5-17f00ca39f53.png">


**Client secret**

Navigate to *Certificates & secrets* menu and press the “New client secret” button.

Give the secret a description and specify a suitable duration for it to expire.

<img src="https://user-images.githubusercontent.com/51473494/214440623-fac5de1e-30e7-4441-835f-43f84190b25d.png">


Ensure you record only the secret value, as this is required later.

<img src="https://user-images.githubusercontent.com/51473494/214440770-04089c5e-e8bf-4eee-98a1-7ea60e494b92.png">


**App permissions**

Navigate to the *API permissions* menu and press the “Add permission” button.

![image](https://user-images.githubusercontent.com/51473494/214441766-139fa9a3-ea02-4b27-9344-48d32a2acecf.png)


Select *Microsoft Graph*.

![image](https://user-images.githubusercontent.com/51473494/214440861-a507dec7-3b34-4f8f-8ef6-8127dff4b441.png)


Select *Application permissions*.

![image](https://user-images.githubusercontent.com/51473494/214440880-b802e86e-d3ee-4c19-af0d-33d5306bf61d.png)

Search for “Directory.Read.All” and select the checkbox.

![image](https://user-images.githubusercontent.com/51473494/214440903-8be563c5-18a6-4ca3-b28b-2616cae53e81.png)


Next, search for “User.Read.All”, select the checkbox, and press the “Add permissions” button.

![image](https://user-images.githubusercontent.com/51473494/214440928-e39e9e41-0a5e-478e-a737-93e8549baff6.png)


Your API permissions screen should look like the below image.

<img src="https://user-images.githubusercontent.com/51473494/214441033-fd51d0f4-84d9-49eb-9fdc-b42254027633.png">


Press the “Grant admin consent for \<your organisation name\>” button and select *Yes* when prompted.

![image](https://user-images.githubusercontent.com/51473494/214441073-13eaa073-d41d-4572-bc3e-48f114c9c1e6.png)


Your permission screen should now look like the image below.

<img src="https://user-images.githubusercontent.com/51473494/214441182-3b0fad31-4f26-4130-9bb6-a6cc9e9a12fb.png">


You will need to provide the secret value recorded earlier, as well as the application (client) ID and directory (tenant) ID from the *Overview* screen, as in the image below.

<img src="https://user-images.githubusercontent.com/51473494/214441317-c1b44cce-b3ca-48d3-94a1-5f4b55d16861.png">


## host Tenant Requirements

There are several components required within the host tenant for this solution to work:

-   Security group(s) to store the guests who require access to the intranet
-   App registration to perform guest operations such as creation, updating, and deletion
-   Adjustment to intranet site permissions
-   SharePoint site with lists for storage of guest sync information

### Security group

The security group within the host tenant is used to manage access to the intranet.

host can choose to have a single group per subsidiary organisation, or a single group for all of them.

To simplify management membership for the group within the host tenant, a dynamic rule can be configured that automatically adds guests who are from the subsidiary tenant(s).

![image](https://user-images.githubusercontent.com/51473494/214441352-e3029408-0b12-4dbf-bd10-16ddc11a8052.png)

The structure of the dynamic rule contains two main components:

-   The domain name of the UPN of the guest from their home tenant (the subsidiary)
-   The user type is of “guest”

An example of such a rule can be found below:

<img src="https://user-images.githubusercontent.com/51473494/214441447-23051d27-746e-4f68-ab9d-f294358e0131.png">


### Addition of subsidiary tenant to sync engine

All that is required to add a tenant to the Guest Sync Engine is to record the security group ID and app registration details from the subsidiary tenant in the Guest Sync Tenants list.

<img width="326" alt="image" src="https://user-images.githubusercontent.com/51473494/214441606-7ae55f34-d39f-441c-ab3b-ca1bbb1fc1a8.png">


Once added, the users from the tenant will be picked up in the next run of the engine workflows.

### Granting SharePoint site access

In order to grant the newly created guests access to the SharePoint site, all that is required is to add the security group created earlier in the host Tenant Requirements section as a Site Visitor.

![image](https://user-images.githubusercontent.com/51473494/214441639-02d8f88f-3d6c-4921-9672-b454808aba1a.png)

It is however a pre-requisite that the SharePoint site (and tenant itself) be allowed to admit existing guests.
