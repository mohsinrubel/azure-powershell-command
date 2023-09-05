Here is the step by step info to filter last login date for the users in specific group.

# Install the AzureADPreview Module
Install-Module -Name AzureADPreview

This line installs the AzureADPreview module, which is used to interact with Microsoft Azure Active Directory (Azure AD) in PowerShell.

# Import the Azure AD Module
Import-Module AzureADPreview

This line imports the AzureADPreview module so that its cmdlets (commands) are available for use in the script.

# Connect with AzureAD
Connect-AzureAD

This line establishes a connection to Azure AD, allowing you to perform actions and retrieve information from your Azure AD environment.


# Replace "YourGroupName" with the actual name of the group
$groupName = "XYZ"

This line assigns the group name "SCSI Users" to the variable $groupName. You should replace it with the actual name of the Azure AD group you want to query.


# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

This line retrieves the Azure AD group based on the display name stored in the $groupName variable.


# Check if the group exists
if ($group) {

This line starts an if statement to check whether the group was found. If it exists, the code within the if block will be executed.


 # Get group members
 $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

This line retrieves the members of the Azure AD group and stores them in the $groupMembers variable.


# Iterate through group members
$results = @()
foreach ($member in $groupMembers) {

These lines initialize an empty array $results and start a loop to iterate through the group members.


 if ($member.objectType -eq "User") {

This line checks whether the member is a user (as opposed to another type of object).


 $user = Get-AzureADUser -ObjectId $member.ObjectId

This line retrieves information about the user and stores it in the $user variable.


# Get the users last sign-in information

$signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreatedDateTime -Descending | Select-Object -First 1
$lastSignIn = $signInActivity.CreatedDateTime

These lines retrieve the last sign-in information for the user, including the timestamp of the last sign-in.


            $userInfo = [PSCustomObject]@{
                Name = $user.DisplayName
                Email = $user.Mail
                LastSignIn = $lastSignIn
            }
This block creates a custom PowerShell object $userInfo with properties for the user's display name, email, and last sign-in timestamp.

 # Add the user information to the results array
 $results += $userInfo
 
This line adds the user information to the $results array.

# Display user information
            Write-Host "User Name: $($user.DisplayName)"
            Write-Host "User Email: $($user.Mail)"
            Write-Host "Last Sign-In: $lastSignIn"
            Write-Host "#####"

These lines display the user's information in the console for debugging purposes.

        }
    }

This block concludes the loop that iterates through group members.

 # Save the CSV file with date
  $currentDateTime = Get-Date -Format "yyyyMMdd_HHmmss"
  $results | Export-Csv -Path ".\UserInformation_$currentDateTime.csv" -NoTypeInformation
  
These lines generate a unique timestamp, format it, and use it to save the user information to a CSV file with a name like "UserInformation_yyyymmdd_hhmmss.csv."

} else {
    Write-Host "Group '$groupName' not found."
}

This block handles the case where the specified group does not exist and displays a message in the console.


## Here is the complete code:

#Install the AzureADPreview Module
Install-Module -Name AzureADPreview

#Import the Azure AD Module
Import-Module AzureADPreview

#Connect with AzureAD
Connect-AzureAD

# Replace "YourGroupName" with the actual name of the group
$groupName = "XYZ"

# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

# Check if the group exists
if ($group) {
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

    # Iterate through group members
    $results = @()
    foreach ($member in $groupMembers) {
        if ($member.objectType -eq "User") {
            $user = Get-AzureADUser -ObjectId $member.ObjectId

            # Get the user's last sign-in information
            $signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreatedDateTime -Descending | Select-Object -First 1
            $lastSignIn = $signInActivity.CreatedDateTime
            $userInfo = [PSCustomObject]@{
                Name = $user.DisplayName
                Email = $user.Mail
                LastSignIn = $lastSignIn
            }

            # Add the user information to the results array
            $results += $userInfo
            # Display user information
            Write-Host "User Name: $($user.DisplayName)"
            Write-Host "User Email: $($user.Mail)"
            Write-Host "Last Sign-In: $lastSignIn"
            Write-Host "#####"
        }
    }

    # Save the csv file with date 
    $currentDateTime = Get-Date -Format "yyyyMMdd_HHmmss"
    $results | Export-Csv -Path ".\UserInformation_$currentDateTime.csv" -NoTypeInformation
    
} else {
    Write-Host "Group '$groupName' not found."
} 


