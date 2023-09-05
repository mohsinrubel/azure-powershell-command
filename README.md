Here is the step by step info to filter last login date for the users in specific group.

# Install the AzureADPreview Module
Install-Module -Name AzureADPreview

This line installs the AzureADPreview module, which is used to interact with Microsoft Azure Active Directory (Azure AD) in PowerShell.
powershell
Copy code
# Import the Azure AD Module
Import-Module AzureADPreview
This line imports the AzureADPreview module so that its cmdlets (commands) are available for use in the script.
powershell
Copy code
# Connect with AzureAD
Connect-AzureAD
This line establishes a connection to Azure AD, allowing you to perform actions and retrieve information from your Azure AD environment.
powershell
Copy code
# Replace "YourGroupName" with the actual name of the group
$groupName = "SCSI Users"
This line assigns the group name "SCSI Users" to the variable $groupName. You should replace it with the actual name of the Azure AD group you want to query.
powershell
Copy code
# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
This line retrieves the Azure AD group based on the display name stored in the $groupName variable.
powershell
Copy code
# Check if the group exists
if ($group) {
This line starts an if statement to check whether the group was found. If it exists, the code within the if block will be executed.
powershell
Copy code
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId
This line retrieves the members of the Azure AD group and stores them in the $groupMembers variable.
powershell
Copy code
    # Iterate through group members
    $results = @()
    foreach ($member in $groupMembers) {
These lines initialize an empty array $results and start a loop to iterate through the group members.
powershell
Copy code
        if ($member.objectType -eq "User") {
This line checks whether the member is a user (as opposed to another type of object).
powershell
Copy code
            $user = Get-AzureADUser -ObjectId $member.ObjectId
This line retrieves information about the user and stores it in the $user variable.
powershell
Copy code
            # Get the user's last sign-in information
            $signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreatedDateTime -Descending | Select-Object -First 1
            $lastSignIn = $signInActivity.CreatedDateTime
These lines retrieve the last sign-in information for the user, including the timestamp of the last sign-in.
powershell
Copy code
            $userInfo = [PSCustomObject]@{
                Name = $user.DisplayName
                Email = $user.Mail
                LastSignIn = $lastSignIn
            }
This block creates a custom PowerShell object $userInfo with properties for the user's display name, email, and last sign-in timestamp.
powershell
Copy code
            # Add the user information to the results array
            $results += $userInfo
This line adds the user information to the $results array.
powershell
Copy code
            # Display user information
            Write-Host "User Name: $($user.DisplayName)"
            Write-Host "User Email: $($user.Mail)"
            Write-Host "Last Sign-In: $lastSignIn"
            Write-Host "#####"
These lines display the user's information in the console for debugging purposes.
powershell
Copy code
        }
    }
This block concludes the loop that iterates through group members.
powershell
Copy code
    # Save the CSV file with date
    $currentDateTime = Get-Date -Format "yyyyMMdd_HHmmss"
    $results | Export-Csv -Path ".\UserInformation_$currentDateTime.csv" -NoTypeInformation
These lines generate a unique timestamp, format it, and use it to save the user information to a CSV file with a name like "UserInformation_yyyymmdd_hhmmss.csv."
powershell
Copy code
} else {
    Write-Host "Group '$groupName' not found."
}
This block handles the case where the specified group does not exist and displays a message in the console.
Confluence Document
Title: Retrieving Last Login Details for Azure AD Group Members
Introduction:
This document explains a PowerShell script to retrieve last login details for specific users within an Azure Active Directory (Azure AD) group. The script uses the AzureADPreview module to interact with Azure AD and exports the results to a CSV file.

Procedure:

Install the AzureADPreview Module:

Run the following command to install the AzureADPreview module:
powershell
Copy code
Install-Module -Name AzureADPreview
Import the Azure AD Module:

Import the AzureADPreview module with this command:
powershell
Copy code
Import-Module AzureADPreview
Connect to Azure AD:

Establish a connection to Azure AD using:
powershell
Copy code
Connect-AzureAD
Specify the Target Group:

Replace "SCSI Users" with the actual name of the Azure AD group you want to query.
powershell
Copy code
$groupName = "SCSI Users"
Retrieve the Group:

Obtain information about the Azure AD group with the specified display name:
powershell
Copy code
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"
Check Group Existence:

Check if the group exists:
powershell
Copy code
if ($group) {
Retrieve Group Members:

Get the members of the Azure AD group:
powershell
Copy code
$groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId
Iterate Through Members:

Iterate through the group members, checking if each is a user:
powershell
Copy code
foreach ($member in $groupMembers) {
Retrieve User Information:

Retrieve information about each user, including their last sign-in activity:
powershell
Copy code
$user = Get-AzureADUser -ObjectId $member.ObjectId
$signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreatedDateTime -Descending | Select-Object -First 1
$lastSignIn = $signInActivity.CreatedDateTime
Display User Information (Optional):

Display user information in the console (for debugging purposes):
powershell
Copy code
Write-Host "User Name: $($user.DisplayName)"
Write-Host "User Email: $($user.Mail)"
Write-Host "Last Sign-In: $lastSignIn"
Write-Host "#####"
Save Results to CSV:

Save the collected user information
* The script fetches the group by name, checks if it exists, and retrieves user details, including their email addresses and last sign-in information.
* User information is stored in a custom object and added to the $results array.
* The results are then exported to a CSV file named "UserInformation.csv" using Export-Csv.


