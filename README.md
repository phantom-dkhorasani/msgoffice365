[comment]: # "Auto-generated SOAR connector documentation"
# MS Graph for Office 365

Publisher: Splunk  
Connector Version: 2\.4\.9  
Product Vendor: Microsoft  
Product Name: Office 365 \(MS Graph\)  
Product Version Supported (regex): "\.\*"  
Minimum Product Version: 5\.0\.0  

This app connects to Office 365 using the MS Graph API to support investigate and generic actions related to the email messages and calendar events

[comment]: # " File: README.md"
[comment]: # "  Copyright (c) 2017-2022 Splunk Inc."
[comment]: # ""
[comment]: # "Licensed under the Apache License, Version 2.0 (the 'License');"
[comment]: # "you may not use this file except in compliance with the License."
[comment]: # "You may obtain a copy of the License at"
[comment]: # ""
[comment]: # "    http://www.apache.org/licenses/LICENSE-2.0"
[comment]: # ""
[comment]: # "Unless required by applicable law or agreed to in writing, software distributed under"
[comment]: # "the License is distributed on an 'AS IS' BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,"
[comment]: # "either express or implied. See the License for the specific language governing permissions"
[comment]: # "and limitations under the License."
[comment]: # ""
## Authentication

This app requires registration of a Microsoft Graph Application. To do so, navigate to the URL
<https://portal.azure.com> in a browser and log in with the Microsoft account, then, click **App
registrations** .  
  
On the next page, select **New registration** and give your app a name.  
  
Once the app is created, follow the below three steps:

-   Under **Certificates & secrets** select **New client secret** . Note down this key somewhere
    secure, as it cannot be retrieved after closing the window.
-   Under **Overview** select **Add a redirect URI** . In the **Add Platform** window, select
    **Web** . The **Redirect URLs** field will be filled in the later steps.
-   Under **API Permissions** the following **Application Permissions** need to be added:
    -   Mail.Read (https://graph.microsoft.com/Mail.Read)

    -   Mail.ReadWrite (https://graph.microsoft.com/Mail.ReadWrite)

    -   User.Read.All (https://graph.microsoft.com/User.Read.All)

          

        -   For non-admin access, use User.Read (Delegated permission) instead
            (https://graph.microsoft.com/User.Read)

    -   Group.Read.All (https://graph.microsoft.com/Group.Read.All)- It is required only if you want
        to run the **list events** action for the group's calendar and for the **list groups**
        action

    -   Calendar.Read (https://graph.microsoft.com/Calendars.Read)- It is required only if you want
        to run the **list events** action for the user's calendar

    -   MailboxSettings.Read (https://graph.microsoft.com/MailboxSettings.Read)- It is required only
        if you want to run the **oof status** action

After making these changes, click **Add permissions** , then select **Grant admin consent for Test
Phantom** at the bottom of the screen.

## Phantom Graph Asset

When creating an asset for the **MS Graph for Office 365** app, place **Application ID** of the app
created during the app registration on the Azure Portal in the **Application ID** field and place
the client secret generated during the app registration process in the **Client Secret** field.
Then, after filling out the **Tenant** field, click **SAVE** . Both the Application/Client ID and
the Tenant ID can be found in the **Overview** tab on your app's Azure page.  
  
After saving, a new field will appear in the **Asset Settings** tab. Take the URL found in the
**POST incoming for MS Graph for Office 365 to this location** field and place it in the **Redirect
URLs** field mentioned in a previous step. To this URL, add **/result** . After doing so the URL
should look something like:  
  

    https://<phantom_host>/rest/handler/msgraphforoffice365_0a0a4087-10e8-4c96-9872-b740ff26d8bb/<asset_name>/result

  
Once again, click save at the bottom of the screen.  

## User Permissions

To complete the authorization process, this app needs permission to view assets, which is not
granted by default. First, under **asset settings** , check which user has listed under **Select a
user on behalf of which automated actions can be executed** . By default, the user will be
**automation** , but this user can be changed by clicking **EDIT** at the bottom of the window. To
give this user permission to view assets, follow these steps:

-   In the main drop-down menu, select **Administration** , then select the **User Management** ,
    and under that tab, select **Roles** . Finally, click **+ ROLE** .
-   In the **Add Role** wizard, give the role a name (e.g **Asset Viewer** ), and provide a
    description. Subsequently, under **Available Users** , add the user assigned to the asset viewed
    earlier. Then click the **Permissions** tab.
-   On the permission tab, under **Available Privileges** , give the role the **View Assets**
    privilege. Then click **SAVE** .

### Test connectivity

After setting up the asset and user, click the **TEST CONNECTIVITY** button. A window should pop up
and display a URL. Navigate to this URL in a separate browser tab. This new tab will redirect to a
Microsoft login page. Log in to a Microsoft account with administrator privileges to the desired
mailboxes. After logging in, review the requested permissions listed, then click **Accept** .
Finally, close that tab. The test connectivity window should show success.  
  
The app should now be ready to be used.  

### On-Poll

**Configuration:**  

-   email_address - Ingest from the provided email address.
-   folder - To fetch the emails from the given folder name (must be provided if running ingestion)
-   first_run_max_emails - Maximum containers to poll for the first scheduled polling (default -
    1000).
-   max_containers - Maximum containers to poll after the first scheduled poll completes (default -
    100).
-   extract_attachments - Extract all the attachments included in emails.
-   extract_urls - Extracts the URLs present in the emails.
-   extract_ips - Extracts the IP addresses present in the emails.
-   extract_domains - Extract the domain names present in the emails.
-   extract_hashes - Extract the hashes present in the emails (MD5).

## State file permissions

Please check the permissions for the state file as mentioned below.

#### State file path

-   For Non-NRI instance: /opt/phantom/local_data/app_states/\<appid>/\<asset_id>\_state.json
-   For NRI instance:
    /\<PHANTOM_HOME_DIRECTORY>/local_data/app_states/\<appid>/\<asset_id>\_state.json

#### State file permissions

-   File rights: rw-rw-r-- (664) (The phantom user should have read and write access for the state
    file)
-   File owner: Appropriate phantom user

### Note

-   An optional parameter **Admin Access Required** has been added to this app. In most cases, this
    should remain checked, as admin access is required for email use cases. If the desired
    integration is to integrate with only one user's calendar, you may consider unchecking this box.
    If unchecked, it allows a non-admin user to provide access to a specific account. This
    functionality will ONLY work with the **list events** functionality. If unchecked, the **Access
    scope** *must* be used. The default scope will work for listing calendar events. Additional
    information on scope can be found
    [here.](https://docs.microsoft.com/en-us/azure/active-directory/develop/v2-permissions-and-consent#openid-connect-scopes)
-   As per the Microsoft known issues for **Group.Read.All** permission (
    [here](https://docs.microsoft.com/en-us/graph/known-issues#groups) ), if you want to run the
    **list events** for fetching group's calendar events, you have to uncheck an optional parameter
    **Admin Access Required** and provide **Group.Read.All
    (https://graph.microsoft.com/Group.Read.All)** permission into the scope parameter in the asset
    configuration parameters. If an asset parameter **Admin Access Required** checked and configured
    the app with above mentioned all the application permissions (which includes **Group.Read.All**
    application permission), it throws an error like **Access is denied** while running **list
    events** action for fetching group's calendar events. Because of the known issue of
    **Group.Read.All** application permission, this permission required admin consent (on behalf of
    the user permission) to fetch the group's calendar events.
-   If the parameter **Admin Access Required** is unchecked, you have to provide a **scope**
    parameter in the asset configuration. All the actions will get executed according to the scopes
    provided in the **scope** config parameter. The actions will throw an appropriate error if the
    scope of the corresponding permission is not provided by the end-user.
-   If both the checkboxes **Admin Consent Already Provided** and **Admin Access Required** are kept
    as checked while running the test connectivity for the first time, then the test connectivity
    will pass but actions will fail. In order to use the **Admin Consent Already Provided**
    checkbox, first, you need to run the test connectivity with only **Admin Access Required** as
    checked and provide admin consent, and then while running the test connectivity for the second
    time the **Admin Consent Already Provided** should be checked if required. The **checkbox Admin
    Consent Already Provided** is used when running the test connectivity if admin consent is
    already provided.
-   There is an API limitation that will affect run_query action
    when providing Unicode values in the subject or in the body as parameters and if the
    result count exceeds 999, the action will fail.

  


### Configuration Variables
The below configuration variables are required for this Connector to operate.  These variables are specified when configuring a Office 365 \(MS Graph\) asset in SOAR.

VARIABLE | REQUIRED | TYPE | DESCRIPTION
-------- | -------- | ---- | -----------
**tenant** |  required  | string | Tenant ID \(e\.g\. 1e309abf\-db6c\-XXXX\-a1d2\-XXXXXXXXXXXX\)
**client\_id** |  required  | string | Application ID
**client\_secret** |  required  | password | Application Secret
**admin\_access** |  optional  | boolean | Admin Access Required
**admin\_consent** |  optional  | boolean | Admin Consent Already Provided
**scope** |  optional  | string | Access Scope \(for use with non\-admin access; space\-separated\)
**ph\_2** |  optional  | ph | 
**email\_address** |  optional  | string | User Email Mailbox \(On Poll\)
**folder** |  optional  | string | Mailbox folder to ingest \(On Poll\)
**first\_run\_max\_emails** |  optional  | numeric | Maximum Containers for scheduled polling first time
**max\_containers** |  optional  | numeric | Maximum Containers for scheduled polling
**extract\_attachments** |  optional  | boolean | Extract Attachments
**extract\_urls** |  optional  | boolean | Extract URLs
**extract\_ips** |  optional  | boolean | Extract IPs
**extract\_domains** |  optional  | boolean | Extract Domain Names
**extract\_hashes** |  optional  | boolean | Extract Hashes

### Supported Actions  
[test connectivity](#action-test-connectivity) - Use supplied credentials to generate a token with MS Graph  
[generate token](#action-generate-token) - Generate a token  
[oof check](#action-oof-check) - Get user's out of office status  
[list events](#action-list-events) - List events from user or group calendar  
[list users](#action-list-users) - Retrieve a list of users  
[list groups](#action-list-groups) - List all the groups in an organization, including but not limited to Office 365 groups  
[list folders](#action-list-folders) - Retrieve a list of mail folders  
[copy email](#action-copy-email) - Copy an email to a folder  
[move email](#action-move-email) - Move an email to a folder  
[delete email](#action-delete-email) - Delete an email  
[get email](#action-get-email) - Get an email from the server  
[get email properties](#action-get-email-properties) - Get non\-standard email properties from the server  
[run query](#action-run-query) - Search emails  
[create folder](#action-create-folder) - Create a new folder  
[get folder id](#action-get-folder-id) - Get the API ID of the folder  
[on poll](#action-on-poll) - Ingest emails from Office 365 using Graph API  

## action: 'test connectivity'
Use supplied credentials to generate a token with MS Graph

Type: **test**  
Read only: **True**

#### Action Parameters
No parameters are required for this action

#### Action Output
No Output  

## action: 'generate token'
Generate a token

Type: **generic**  
Read only: **False**

#### Action Parameters
No parameters are required for this action

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.data | string | 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'oof check'
Get user's out of office status

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**user\_id** |  required  | User ID/Principal name | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.user\_id | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.status | string | 
action\_result\.summary\.events\_matched | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list events'
List events from user or group calendar

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**user\_id** |  optional  | User ID/Principal name | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 
**group\_id** |  optional  | Group ID | string |  `msgoffice365 group id` 
**filter** |  optional  | OData query to filter/search for specific results | string | 
**limit** |  optional  | Maximum number of events to return | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.filter | string | 
action\_result\.parameter\.group\_id | string |  `msgoffice365 group id` 
action\_result\.parameter\.limit | numeric | 
action\_result\.parameter\.user\_id | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.attendee\_list | string | 
action\_result\.data\.\*\.attendees\.\*\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.attendees\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.attendees\.\*\.status\.response | string | 
action\_result\.data\.\*\.attendees\.\*\.status\.time | string | 
action\_result\.data\.\*\.attendees\.\*\.type | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.bodyPreview | string | 
action\_result\.data\.\*\.categories\.\*\.name | string | 
action\_result\.data\.\*\.changeKey | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.end\.dateTime | string | 
action\_result\.data\.\*\.end\.timeZone | string | 
action\_result\.data\.\*\.hasAttachments | boolean | 
action\_result\.data\.\*\.iCalUId | string | 
action\_result\.data\.\*\.id | string | 
action\_result\.data\.\*\.importance | string | 
action\_result\.data\.\*\.isAllDay | boolean | 
action\_result\.data\.\*\.isCancelled | boolean | 
action\_result\.data\.\*\.isOrganizer | boolean | 
action\_result\.data\.\*\.isReminderOn | boolean | 
action\_result\.data\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.location\.address\.city | string | 
action\_result\.data\.\*\.location\.address\.countryOrRegion | string | 
action\_result\.data\.\*\.location\.address\.postalCode | string | 
action\_result\.data\.\*\.location\.address\.state | string | 
action\_result\.data\.\*\.location\.address\.street | string | 
action\_result\.data\.\*\.location\.coordinates\.latitude | numeric | 
action\_result\.data\.\*\.location\.coordinates\.longitude | numeric | 
action\_result\.data\.\*\.location\.displayName | string | 
action\_result\.data\.\*\.location\.locationType | string | 
action\_result\.data\.\*\.location\.locationUri | string |  `url` 
action\_result\.data\.\*\.location\.uniqueId | string | 
action\_result\.data\.\*\.location\.uniqueIdType | string | 
action\_result\.data\.\*\.locations\.\*\.address\.city | string | 
action\_result\.data\.\*\.locations\.\*\.address\.countryOrRegion | string | 
action\_result\.data\.\*\.locations\.\*\.address\.postalCode | string | 
action\_result\.data\.\*\.locations\.\*\.address\.state | string | 
action\_result\.data\.\*\.locations\.\*\.address\.street | string | 
action\_result\.data\.\*\.locations\.\*\.coordinates\.latitude | numeric | 
action\_result\.data\.\*\.locations\.\*\.coordinates\.longitude | numeric | 
action\_result\.data\.\*\.locations\.\*\.displayName | string | 
action\_result\.data\.\*\.locations\.\*\.locationType | string | 
action\_result\.data\.\*\.locations\.\*\.locationUri | string |  `url` 
action\_result\.data\.\*\.locations\.\*\.uniqueId | string | 
action\_result\.data\.\*\.locations\.\*\.uniqueIdType | string | 
action\_result\.data\.\*\.onlineMeetingUrl | string |  `url` 
action\_result\.data\.\*\.organizer\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.organizer\.emailAddress\.name | string | 
action\_result\.data\.\*\.originalEndTimeZone | string | 
action\_result\.data\.\*\.originalStartTimeZone | string | 
action\_result\.data\.\*\.recurrence | string | 
action\_result\.data\.\*\.reminderMinutesBeforeStart | numeric | 
action\_result\.data\.\*\.responseRequested | boolean | 
action\_result\.data\.\*\.responseStatus\.response | string | 
action\_result\.data\.\*\.responseStatus\.time | string | 
action\_result\.data\.\*\.sensitivity | string | 
action\_result\.data\.\*\.seriesMasterId | string | 
action\_result\.data\.\*\.showAs | string | 
action\_result\.data\.\*\.start\.dateTime | string | 
action\_result\.data\.\*\.start\.timeZone | string | 
action\_result\.data\.\*\.subject | string | 
action\_result\.data\.\*\.type | string | 
action\_result\.data\.\*\.webLink | string |  `url` 
action\_result\.data\.locations\.\*\.displayName | string | 
action\_result\.summary\.events\_matched | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list users'
Retrieve a list of users

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**filter** |  optional  | Search for specific results | string | 
**limit** |  optional  | Maximum number of users to return | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.filter | string | 
action\_result\.parameter\.limit | numeric | 
action\_result\.data\.\*\.businessPhones | string | 
action\_result\.data\.\*\.displayName | string | 
action\_result\.data\.\*\.givenName | string | 
action\_result\.data\.\*\.id | string |  `msgoffice365 user id` 
action\_result\.data\.\*\.jobTitle | string | 
action\_result\.data\.\*\.mail | string |  `email` 
action\_result\.data\.\*\.mobilePhone | string | 
action\_result\.data\.\*\.officeLocation | string | 
action\_result\.data\.\*\.preferredLanguage | string | 
action\_result\.data\.\*\.surname | string | 
action\_result\.data\.\*\.userPrincipalName | string |  `msgoffice365 user principle name`  `email` 
action\_result\.summary\.total\_users\_returned | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list groups'
List all the groups in an organization, including but not limited to Office 365 groups

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**filter** |  optional  | Search for specific results | string | 
**limit** |  optional  | Maximum number of groups to return | numeric | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.filter | string | 
action\_result\.parameter\.limit | numeric | 
action\_result\.data\.\*\.classification | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.creationOptions | string | 
action\_result\.data\.\*\.deletedDateTime | string | 
action\_result\.data\.\*\.description | string | 
action\_result\.data\.\*\.displayName | string | 
action\_result\.data\.\*\.groupTypes | string | 
action\_result\.data\.\*\.id | string |  `msgoffice365 group id` 
action\_result\.data\.\*\.isAssignableToRole | string | 
action\_result\.data\.\*\.mail | string |  `email` 
action\_result\.data\.\*\.mailEnabled | boolean | 
action\_result\.data\.\*\.mailNickname | string | 
action\_result\.data\.\*\.onPremisesLastSyncDateTime | string | 
action\_result\.data\.\*\.onPremisesSecurityIdentifier | string | 
action\_result\.data\.\*\.onPremisesSyncEnabled | string | 
action\_result\.data\.\*\.preferredDataLocation | string | 
action\_result\.data\.\*\.proxyAddresses | string | 
action\_result\.data\.\*\.renewedDateTime | string | 
action\_result\.data\.\*\.resourceBehaviorOptions | string | 
action\_result\.data\.\*\.resourceProvisioningOptions | string | 
action\_result\.data\.\*\.securityEnabled | boolean | 
action\_result\.data\.\*\.visibility | string | 
action\_result\.summary\.total\_groups\_returned | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'list folders'
Retrieve a list of mail folders

Type: **investigate**  
Read only: **True**

If you want to list all the child folders \(includes all the sub\-levels\) of the specific parent folder, then, you have to provide the parent <b>folder\_id</b> parameter\. If you don't provide <b>folder\_id</b> it will list all the folders on Office 365 account \(includes all the sub\-level folders\)\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**user\_id** |  required  | User ID/Principal name | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 
**folder\_id** |  optional  | Parent mail folder ID | string |  `msgoffice365 folder id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.folder\_id | string |  `msgoffice365 folder id` 
action\_result\.parameter\.user\_id | string |  `msgoffice365 user id`  `msgoffice365 user principle name`  `email` 
action\_result\.data\.\*\.childFolderCount | numeric | 
action\_result\.data\.\*\.displayName | string | 
action\_result\.data\.\*\.id | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.totalItemCount | numeric | 
action\_result\.data\.\*\.unreadItemCount | numeric | 
action\_result\.summary\.total\_folders\_returned | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'copy email'
Copy an email to a folder

Type: **generic**  
Read only: **False**

The <b>get\_folder\_id</b> parameter should be enabled only when you have specified folder name/folder path in the <b>folder</b> parameter\. If you provide folder ID in the <b>folder</b> parameter and set <b>get\_folder\_id</b> parameter to true, it will throw an error of folder ID not found for given folder name \(because the action considers folder parameter value as folder name/folder path\)\. The <b>folder</b> parameter must be either a \(case sensitive\) well\-known name \[list here; https\://docs\.microsoft\.com/en\-us/graph/api/resources/mailfolder?view=graph\-rest\-1\.0\] or the internal o365 folder ID\. The action supports copying to a folder that is nested within another\. To copy in such a folder, specify the complete folder path using the <b>'/'</b> \(forward slash\) as the separator\.<br>e\.g\. to search in a folder named <i>phishing</i> which is nested within \(is a child of\) <i>Inbox</i>, set the value as <b>Inbox/phishing</b>\. If a folder name has a literal forward slash\('/'\) in the name escape it with a backslash\('\\'\) to differentiate\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**id** |  required  | Message ID to copy | string |  `msgoffice365 message id` 
**email\_address** |  required  | Source mailbox \(email\) | string |  `email` 
**folder** |  required  | Destination folder; this must be either a \(case\-sensitive\) well\-known name or the internal o365 folder ID | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
**get\_folder\_id** |  optional  | Assume the folder parameter contains a folder name/folder path, separated by '/' ; i\.e\. Inbox/dir1/dir2/dir3\. If this parameter is enabled, it retrieves the folder ID for the provided folder name/folder path automatically and replaces the parameter value | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
action\_result\.parameter\.get\_folder\_id | boolean | 
action\_result\.parameter\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.\@odata\.context | string |  `url` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.\@odata\.type | string | 
action\_result\.data\.\*\.bccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.bccRecipients\.name | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.bodyPreview | string | 
action\_result\.data\.\*\.categories | string | 
action\_result\.data\.\*\.ccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.ccRecipients\.name | string | 
action\_result\.data\.\*\.changeKey | string | 
action\_result\.data\.\*\.conversationId | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.from\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.from\.emailAddress\.name | string | 
action\_result\.data\.\*\.hasAttachments | boolean | 
action\_result\.data\.\*\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.importance | string | 
action\_result\.data\.\*\.inferenceClassification | string | 
action\_result\.data\.\*\.internetMessageId | string |  `msgoffice365 internet message id` 
action\_result\.data\.\*\.isDeliveryReceiptRequested | string | 
action\_result\.data\.\*\.isDraft | boolean | 
action\_result\.data\.\*\.isRead | boolean | 
action\_result\.data\.\*\.isReadReceiptRequested | boolean | 
action\_result\.data\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.receivedDateTime | string | 
action\_result\.data\.\*\.replyTo | string | 
action\_result\.data\.\*\.sender\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.sender\.emailAddress\.name | string | 
action\_result\.data\.\*\.sentDateTime | string | 
action\_result\.data\.\*\.subject | string |  `msgoffice365 subject` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.webLink | string |  `url` 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'move email'
Move an email to a folder

Type: **generic**  
Read only: **False**

The <b>get\_folder\_id</b> parameter should be enabled only when you have specified folder name/folder path in the <b>folder</b> parameter\. If you provide folder ID in the <b>folder</b> parameter and set <b>get\_folder\_id</b> parameter to true, it will throw an error of folder ID not found for given folder name \(because the action considers folder parameter value as folder name/folder path\)\. The <b>folder</b> parameter must be either a \(case sensitive\) well\-known name \[list here; https\://docs\.microsoft\.com/en\-us/graph/api/resources/mailfolder?view=graph\-rest\-1\.0\] or the internal o365 folder ID\. The action supports moving to a folder that is nested within another\. To copy in such a folder, specify the complete folder path using the <b>'/'</b> \(forward slash\) as the separator\.<br>e\.g\. to search in a folder named <i>phishing</i> which is nested within \(is a child of\) <i>Inbox</i>, set the value as <b>Inbox/phishing</b>\. If a folder name has a literal forward slash\('/'\) in the name escape it with a backslash\('\\'\) to differentiate\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**id** |  required  | Message ID to move | string |  `msgoffice365 message id` 
**email\_address** |  required  | Source mailbox \(email\) | string |  `email` 
**folder** |  required  | Destination folder; this must be either a \(case\-sensitive\) well\-known name or the internal o365 folder ID | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
**get\_folder\_id** |  optional  | Assume the folder parameter contains a folder name/folder path, separated by '/'\(forward slash\) ; i\.e\. Inbox/dir1/dir2/dir3\. If this parameter is enabled, it retrieves the folder ID for the provided folder name/folder path automatically and replaces the parameter value | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
action\_result\.parameter\.get\_folder\_id | boolean | 
action\_result\.parameter\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.\@odata\.context | string |  `url` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.\@odata\.type | string | 
action\_result\.data\.\*\.bccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.bccRecipients\.name | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.bodyPreview | string | 
action\_result\.data\.\*\.categories | string | 
action\_result\.data\.\*\.ccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.ccRecipients\.name | string | 
action\_result\.data\.\*\.changeKey | string | 
action\_result\.data\.\*\.conversationId | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.from\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.from\.emailAddress\.name | string | 
action\_result\.data\.\*\.hasAttachments | boolean | 
action\_result\.data\.\*\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.importance | string | 
action\_result\.data\.\*\.inferenceClassification | string | 
action\_result\.data\.\*\.internetMessageId | string |  `msgoffice365 internet message id` 
action\_result\.data\.\*\.isDeliveryReceiptRequested | string | 
action\_result\.data\.\*\.isDraft | boolean | 
action\_result\.data\.\*\.isRead | boolean | 
action\_result\.data\.\*\.isReadReceiptRequested | boolean | 
action\_result\.data\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.receivedDateTime | string | 
action\_result\.data\.\*\.replyTo | string | 
action\_result\.data\.\*\.sender\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.sender\.emailAddress\.name | string | 
action\_result\.data\.\*\.sentDateTime | string | 
action\_result\.data\.\*\.subject | string |  `msgoffice365 subject` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.webLink | string |  `url` 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'delete email'
Delete an email

Type: **contain**  
Read only: **False**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**id** |  required  | Message ID to delete | string |  `msgoffice365 message id` 
**email\_address** |  required  | Email address of the mailbox owner | string |  `email` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.id | string |  `msgoffice365 message id` 
action\_result\.data | string | 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get email'
Get an email from the server

Type: **investigate**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**id** |  required  | Message ID to get | string |  `msgoffice365 message id` 
**email\_address** |  required  | Email address of the mailbox owner | string |  `email` 
**download\_attachments** |  optional  | Download attachments to vault \(ingest only '\#microsoft\.graph\.fileAttachment' type of attachments\) | boolean | 
**extract\_headers** |  optional  | Extract email headers | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.download\_attachments | boolean | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.\@odata\.context | string |  `url` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.attachments\.\*\.\@odata\.type | string | 
action\_result\.data\.\*\.attachments\.\*\.attachmentType | string | 
action\_result\.data\.\*\.attachments\.\*\.itemType | string | 
action\_result\.data\.\*\.attachments\.\*\.contentId | string |  `email` 
action\_result\.data\.\*\.attachments\.\*\.contentLocation | string | 
action\_result\.data\.\*\.attachments\.\*\.contentType | string | 
action\_result\.data\.\*\.attachments\.\*\.id | string | 
action\_result\.data\.\*\.attachments\.\*\.isInline | boolean | 
action\_result\.data\.\*\.attachments\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.attachments\.\*\.name | string | 
action\_result\.data\.\*\.attachments\.\*\.size | numeric | 
action\_result\.data\.\*\.attachments\.\*\.vaultId | string |  `sha1` 
action\_result\.data\.\*\.bccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.bccRecipients\.name | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.bodyPreview | string | 
action\_result\.data\.\*\.categories | string | 
action\_result\.data\.\*\.ccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.ccRecipients\.name | string | 
action\_result\.data\.\*\.changeKey | string | 
action\_result\.data\.\*\.conversationId | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.from\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.from\.emailAddress\.name | string |  `email` 
action\_result\.data\.\*\.hasAttachments | boolean | 
action\_result\.data\.\*\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.importance | string | 
action\_result\.data\.\*\.inferenceClassification | string | 
action\_result\.data\.\*\.internetMessageId | string |  `msgoffice365 internet message id` 
action\_result\.data\.\*\.isDeliveryReceiptRequested | string | 
action\_result\.data\.\*\.isDraft | boolean | 
action\_result\.data\.\*\.isRead | boolean | 
action\_result\.data\.\*\.isReadReceiptRequested | boolean | 
action\_result\.data\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.receivedDateTime | string | 
action\_result\.data\.\*\.replyTo | string | 
action\_result\.data\.\*\.sender\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.sender\.emailAddress\.name | string |  `email` 
action\_result\.data\.\*\.sentDateTime | string | 
action\_result\.data\.\*\.subject | string |  `msgoffice365 subject` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.webLink | string |  `url` 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 
action\_result\.data\.\*\.flag\.flagStatus | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Return\-Path | string |  `email` 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationInterval | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Transport\-EndToEndLatency | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Received\-SPF | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Authentication\-Results | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Processed\-By\-BccFoldering | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Message\-ID | string | 
action\_result\.data\.\*\.internetMessageHeaders\.subject | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationStartTime | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Oob\-TLC\-OOBClassifiers | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-EOPTenantAttributedMessage | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-EOPAttributedMessage | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-AuthAs | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam\-Mailbox\-Delivery | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Google\-DKIM\-Signature | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationIntervalReason | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Date | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-AuthSource | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Received | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Transport\-CrossTenantHeadersStamped | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Received | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam\-Message\-Info | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Office365\-Filtering\-Correlation\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-Network\-Message\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-TrafficTypeDiagnostic | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-FromEntityHeader | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-Network\-Message\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-PublicTrafficType | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-OriginalArrivalTime | string | 
action\_result\.data\.\*\.internetMessageHeaders\.MIME\-Version | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-SCL | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Gm\-Message\-State | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-MessageDirectionality | string | 
action\_result\.data\.\*\.internetMessageHeaders\.DKIM\-Signature | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Forefront\-Antispam\-Report | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Content\-Type | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Google\-Smtp\-Source | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationStartTimeReason | string | 
action\_result\.data\.\*\.conversationIndex | string | 
action\_result\.parameter\.extract\_headers | boolean |   

## action: 'get email properties'
Get non\-standard email properties from the server

Type: **investigate**  
Read only: **True**

For a list of possible properties to retrieve, visit https\://docs\.microsoft\.com/en\-us/graph/api/message\-get?view=graph\-rest\-1\.0&tabs=http\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**id** |  required  | Message ID to get properties of | string |  `msgoffice365 message id` 
**email\_address** |  required  | Email address of the mailbox owner | string |  `email` 
**get\_headers** |  optional  | Get email headers | boolean | 
**get\_body** |  optional  | Get email body | boolean | 
**get\_unique\_body** |  optional  | Get unique email body | boolean | 
**get\_sender** |  optional  | Get email sender | boolean | 
**properties\_list** |  optional  | Other properties to get \(comma\-separated list\) | string | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.sender\.emailAddress\.name | string |  `email` 
action\_result\.data\.\*\.sender\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.\@odata\.context | string |  `url` 
action\_result\.data\.\*\.uniqueBody\.content | string | 
action\_result\.data\.\*\.uniqueBody\.contentType | string | 
action\_result\.data\.\*\.subject | string | 
action\_result\.data\.\*\.id | string | 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.message | string | 
action\_result\.parameter\.properties\_list | string | 
action\_result\.parameter\.get\_unique\_body | boolean | 
action\_result\.parameter\.get\_headers | boolean | 
action\_result\.parameter\.get\_body | boolean | 
action\_result\.parameter\.get\_sender | boolean | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.id | string |  `msgoffice365 message id` 
action\_result\.summary | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 
action\_result\.data\.\*\.internetMessageHeaders\.Return\-Path | string |  `email` 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationInterval | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Transport\-EndToEndLatency | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Received\-SPF | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Authentication\-Results | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Processed\-By\-BccFoldering | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Message\-ID | string | 
action\_result\.data\.\*\.internetMessageHeaders\.subject | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationStartTime | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Oob\-TLC\-OOBClassifiers | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-EOPTenantAttributedMessage | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-EOPAttributedMessage | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-AuthAs | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam\-Mailbox\-Delivery | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Google\-DKIM\-Signature | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationIntervalReason | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Date | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-AuthSource | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Received | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Transport\-CrossTenantHeadersStamped | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Received | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Microsoft\-Antispam\-Message\-Info | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Office365\-Filtering\-Correlation\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-Network\-Message\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-TrafficTypeDiagnostic | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-FromEntityHeader | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-Network\-Message\-Id | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-PublicTrafficType | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-CrossTenant\-OriginalArrivalTime | string | 
action\_result\.data\.\*\.internetMessageHeaders\.MIME\-Version | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-SCL | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Gm\-Message\-State | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-MessageDirectionality | string | 
action\_result\.data\.\*\.internetMessageHeaders\.DKIM\-Signature | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Forefront\-Antispam\-Report | string | 
action\_result\.data\.\*\.internetMessageHeaders\.Content\-Type | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-Google\-Smtp\-Source | string | 
action\_result\.data\.\*\.internetMessageHeaders\.X\-MS\-Exchange\-Organization\-ExpirationStartTimeReason | string | 
action\_result\.data\.\*\.receivedDateTime | string |   

## action: 'run query'
Search emails

Type: **investigate**  
Read only: **True**

If the <b>query</b> or <b>internet\_message\_id</b> parameters are included, the <b>subject</b>, <b>sender</b>, <b>body</b>, and <b>range</b> parameters will be ignored\. The <b>internet\_message\_id</b> parameter will take precedence over the <b>query</b> parameter\.<br><br>For information on formatting the <b>query</b> parameter, see https\://developer\.microsoft\.com/en\-us/graph/docs/concepts/query\_parameters\.<br><br>If the <b>limit</b> parameter is not included, the action will default to limiting to ten emails that match the rest of the query\. The <b>get\_folder\_id</b> parameter should be enabled only when you specified folder name/folder path in the folder parameter\. If you provide folder ID in the <b>folder</b> parameter and set <b>get\_folder\_id</b> parameter to true, it will throw an error of folder ID not found for given folder name \(because the action considers folder parameter value as folder name/folder path\)\. The <b>folder</b> parameter must be either a \(case sensitive\) well\-known name \[list here; https\://docs\.microsoft\.com/en\-us/graph/api/resources/mailfolder?view=graph\-rest\-1\.0\] or the internal o365 folder ID\. The action supports searching for a folder that is nested within another\. To copy in such a folder, specify the complete folder path using the <b>'/'</b> \(forward slash\) as the separator\.<br>e\.g\. to search in a folder named <i>phishing</i> which is nested within \(is a child of\) <i>Inbox</i>, set the value as <b>Inbox/phishing</b>\. If a folder name has a literal forward slash\('/'\) in the name escape it with a backslash\('\\'\) to differentiate\.<br>When the <b>search\_well\_known\_folders</b> parameter is set to true, action will ignore values provided in the <b>folder</b> and <b>get\_folder\_id</b> parameters and the user will get details from all 17 well\-known folders which are listed below\:<br><ul style="columns\: 2;\-webkit\-columns\: 2; \-moz\-columns\: 2"> <li>Archive</li> <li>Clutter</li> <li>Conflicts</li> <li>Conversation History</li> <li>Deleted Items</li> <li>Drafts</li> <li>Inbox</li> <li>Junk Email</li> <li>Local Failures</li> <li>Msg Folder Root</li> <li>Outbox</li> <li>Recoverable Items Deletions</li> <li>Scheduled</li> <li>Search Folders</li> <li>Sent Items</li> <li>Server Failures</li> <li>Sync Issues</li></ul><br>If the <b>limit</b> parameter is provided, the user will get the number of messages provided in the <b>limit</b> from every folder if present\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**email\_address** |  required  | User's email \(mailbox to search in\) | string |  `email` 
**folder** |  optional  | Destination folder; this must be either a \(case\-sensitive\) well\-known name or the internal o365 folder ID | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
**search\_well\_known\_folders** |  optional  | Checks all well known folders for messages, ignores folder name provided in parameter | boolean | 
**get\_folder\_id** |  optional  | Assume the folder parameter contains a folder name/folder path, separated by '/'\(forward slash\) ; i\.e\. Inbox/dir1/dir2/dir3\. If this parameter is enabled, it retrieves the folder ID for the provided folder name/folder path automatically and replaces the parameter value | boolean | 
**subject** |  optional  | Substring to search in subject | string |  `msgoffice365 subject` 
**body** |  optional  | Substring to search in body | string | 
**sender** |  optional  | Sender email address to match | string |  `email` 
**limit** |  optional  | Maximum emails to return | numeric | 
**query** |  optional  | MS Graph query string | string | 
**internet\_message\_id** |  optional  | Internet message ID | string |  `msgoffice365 internet message id` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.body | string | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path`  `msgoffice365 folder id` 
action\_result\.parameter\.get\_folder\_id | boolean | 
action\_result\.parameter\.internet\_message\_id | string |  `msgoffice365 internet message id` 
action\_result\.parameter\.limit | numeric | 
action\_result\.parameter\.query | string | 
action\_result\.parameter\.sender | string |  `email` 
action\_result\.parameter\.subject | string |  `msgoffice365 subject` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.bccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.bccRecipients\.name | string | 
action\_result\.data\.\*\.body\.content | string | 
action\_result\.data\.\*\.body\.contentType | string | 
action\_result\.data\.\*\.bodyPreview | string | 
action\_result\.data\.\*\.categories | string | 
action\_result\.data\.\*\.ccRecipients\.email | string |  `email` 
action\_result\.data\.\*\.ccRecipients\.name | string | 
action\_result\.data\.\*\.changeKey | string | 
action\_result\.data\.\*\.conversationId | string | 
action\_result\.data\.\*\.createdDateTime | string | 
action\_result\.data\.\*\.flag\.flagStatus | string | 
action\_result\.data\.\*\.from\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.from\.emailAddress\.name | string | 
action\_result\.data\.\*\.hasAttachments | boolean | 
action\_result\.data\.\*\.id | string |  `msgoffice365 message id` 
action\_result\.data\.\*\.importance | string | 
action\_result\.data\.\*\.inferenceClassification | string | 
action\_result\.data\.\*\.internetMessageId | string |  `msgoffice365 internet message id` 
action\_result\.data\.\*\.isDeliveryReceiptRequested | string | 
action\_result\.data\.\*\.isDraft | boolean | 
action\_result\.data\.\*\.isRead | boolean | 
action\_result\.data\.\*\.isReadReceiptRequested | boolean | 
action\_result\.data\.\*\.lastModifiedDateTime | string | 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.receivedDateTime | string | 
action\_result\.data\.\*\.replyTo | string | 
action\_result\.data\.\*\.sender\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.sender\.emailAddress\.name | string | 
action\_result\.data\.\*\.sentDateTime | string | 
action\_result\.data\.\*\.subject | string |  `msgoffice365 subject` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.address | string |  `email` 
action\_result\.data\.\*\.toRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.webLink | string |  `url` 
action\_result\.summary\.emails\_matched | numeric | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric | 
action\_result\.parameter\.search\_well\_known\_folders | boolean | 
action\_result\.data\.\*\.conversationIndex | string | 
action\_result\.data\.\*\.ccRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.ccRecipients\.\*\.emailAddress\.address | string | 
action\_result\.data\.\*\.replyTo\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.replyTo\.\*\.emailAddress\.address | string | 
action\_result\.data\.\*\.bccRecipients\.\*\.emailAddress\.name | string | 
action\_result\.data\.\*\.bccRecipients\.\*\.emailAddress\.address | string | 
action\_result\.data\.\*\.type | string | 
action\_result\.data\.\*\.isAllDay | boolean | 
action\_result\.data\.\*\.recurrence | string | 
action\_result\.data\.\*\.\@odata\.type | string | 
action\_result\.data\.\*\.endDateTime\.dateTime | string | 
action\_result\.data\.\*\.endDateTime\.timeZone | string | 
action\_result\.data\.\*\.isDelegated | boolean | 
action\_result\.data\.\*\.isOutOfDate | boolean | 
action\_result\.data\.\*\.startDateTime\.dateTime | string | 
action\_result\.data\.\*\.startDateTime\.timeZone | string | 
action\_result\.data\.\*\.previousLocation | string | 
action\_result\.data\.\*\.responseRequested | boolean | 
action\_result\.data\.\*\.meetingMessageType | string | 
action\_result\.data\.\*\.meetingRequestType | string | 
action\_result\.data\.\*\.previousEndDateTime\.dateTime | string | 
action\_result\.data\.\*\.previousEndDateTime\.timeZone | string | 
action\_result\.data\.\*\.allowNewTimeProposals | string | 
action\_result\.data\.\*\.previousStartDateTime\.dateTime | string | 
action\_result\.data\.\*\.previousStartDateTime\.timeZone | string | 
action\_result\.data\.\*\.previousEndDateTime | string | 
action\_result\.data\.\*\.previousStartDateTime | string |   

## action: 'create folder'
Create a new folder

Type: **generic**  
Read only: **False**

Create a new folder either in the mailbox root or inside an existing folder\. The action supports creating a folder that is nested within another\. To create in such a folder, specify the complete path using the <b>'/'</b> \(forward slash\) as the separator\.<br>e\.g\. to search in a folder named <i>phishing</i> which is nested within \(is a child of\) <i>Inbox</i>, set the value as <b>Inbox/phishing</b>\. If a folder name has a literal forward slash\('/'\) in the name escape it with a backslash\('\\'\) to differentiate\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**email\_address** |  required  | User's email \(mailbox to create folders\) | string |  `email` 
**folder** |  required  | Folder Name/Path\. Use '/'to separate folder elements; i\.e\. Inbox/dir1/dir2/dir3 | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 
**all\_subdirs** |  optional  | Make any missing directories in the path if they don't exist instead of failing | boolean | 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.all\_subdirs | boolean | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 
action\_result\.data\.\*\.\@odata\.etag | string | 
action\_result\.data\.\*\.childFolderCount | numeric | 
action\_result\.data\.\*\.displayName | string | 
action\_result\.data\.\*\.id | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.parentFolderId | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.totalItemCount | numeric | 
action\_result\.data\.\*\.unreadItemCount | numeric | 
action\_result\.summary | string | 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'get folder id'
Get the API ID of the folder

Type: **investigate**  
Read only: **True**

The action supports searching a folder that is nested within another\. To search in such a folder, specify the complete path using the <b>'/'</b> \(forward slash\) as the separator\.<br>e\.g\. to search in a folder named <i>phishing</i> which is nested within \(is a child of\) <i>Inbox</i>, set the value as <b>Inbox/phishing</b>\. If a folder name has a literal forward slash\('/'\) in the name escape it with a backslash\('\\'\) to differentiate\.

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**email\_address** |  required  | User's email \(mailbox\) | string |  `email` 
**folder** |  required  | Folder Name/Path\. Use '/' to separate folder elements; i\.e\. Inbox/dir1/dir2/dir3 | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 

#### Action Output
DATA PATH | TYPE | CONTAINS
--------- | ---- | --------
action\_result\.status | string | 
action\_result\.parameter\.email\_address | string |  `email` 
action\_result\.parameter\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 
action\_result\.data\.\*\.folder | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 
action\_result\.data\.\*\.folder\_id | string |  `msgoffice365 folder id` 
action\_result\.data\.\*\.path | string |  `msgoffice365 mail folder`  `msgoffice365 mail folder path` 
action\_result\.summary | string | 
action\_result\.summary\.folder\_id | string |  `msgoffice365 folder id` 
action\_result\.message | string | 
summary\.total\_objects | numeric | 
summary\.total\_objects\_successful | numeric |   

## action: 'on poll'
Ingest emails from Office 365 using Graph API

Type: **ingest**  
Read only: **True**

#### Action Parameters
PARAMETER | REQUIRED | DESCRIPTION | TYPE | CONTAINS
--------- | -------- | ----------- | ---- | --------
**start\_time** |  optional  | Parameter Ignored in this app | numeric | 
**end\_time** |  optional  | Parameter Ignored in this app | numeric | 
**container\_id** |  optional  | Parameter Ignored in this app | string | 
**container\_count** |  required  | Maximum number of emails to ingest | numeric | 
**artifact\_count** |  required  | Maximum number of artifact to ingest | numeric | 

#### Action Output
No Output