# azure-active-directory-powershell-command

## Install the AzureAD PowerShell Module (if not already installed):

Open PowerShell as an administrator and run the following command to install the AzureAD module:

````
Install-Module -Name AzureAD
````
## Connect to your Azure AD Tenant:

Run the following command to connect to your Azure AD tenant:

````
Connect-AzureAD
````

## Setup VS Code

#### Install Visual Studio Code (if not already installed):

If you haven't already installed Visual Studio Code, you can download and install it from the official website: Visual Studio Code.

#### Open VS Code:

Launch Visual Studio Code after installation.

#### Create or Open a PowerShell Script:

You can either create a new PowerShell script by clicking on `` "File" > "New File" and saving it with a .ps1 extension (e.g., myscript.ps1) ``. Alternatively, you can open an existing script.

#### Write or Paste Your PowerShell Script:

#### Write or paste your PowerShell script into the editor.


#### Run the Script:

To run your PowerShell script, open the integrated terminal in VS Code if it's not already open. You can do this by clicking ````  "View" > "Terminal" or by pressing Ctrl+Backtick (```) . ````

#### Navigate to the directory where your script is located using the cd command:

````
cd C:\Path\To\Your\Script\Folder
````

#### Then, execute your script by typing:

````
.\YourScriptName.ps1
````
#### Replace YourScriptName.ps1 with the actual name of your script.

#### View the Output:

The output of your script will appear in the terminal window within VS Code.

#### Save the Script (if you made changes):

If you made changes to the script, make sure to save it by clicking "File" > "Save" or pressing Ctrl+S.

## Retrieve Groups:

Once you're connected, you can use the Get-AzureADGroup cmdlet to retrieve groups from your Azure AD tenant. Here's how to use it:

````
# Retrieve all groups
$groups = Get-AzureADGroup

# Display group information
$groups | Select-Object DisplayName, ObjectId

````
The Get-AzureADGroup cmdlet retrieves all groups in your Azure AD tenant, and the Select-Object cmdlet is used to display selected properties like "DisplayName" and "ObjectId" (which uniquely identifies each group).

Remember that the Azure AD PowerShell module offers various cmdlets to manage Azure AD resources, so you can perform more advanced operations as needed.

## Retrieve a Specific Group's Details:

Use the Get-AzureADGroup cmdlet to retrieve the details of a specific group. You'll need to provide the group's ObjectId or DisplayName as a parameter. Here's how to do it:

````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Retrieve the group based on DisplayName
$group = Get-AzureADGroup -SearchString $groupName

# Display group information
$group | Select-Object DisplayName, ObjectId, Description, GroupTypes, SecurityEnabled

````
In this example, replace "YourGroupName" with the actual name of the group you want to retrieve. The Get-AzureADGroup cmdlet retrieves the group based on the provided search string (group name). The Select-Object cmdlet is used to display selected properties such as "DisplayName," "ObjectId," "Description," "GroupTypes," and "SecurityEnabled."

## Retrieve Specific Group's Members:

Use the Get-AzureADGroupMember cmdlet to retrieve the members of a specific group. Here's how to do it:
````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Retrieve the group based on DisplayName
$group = Get-AzureADGroup -SearchString $groupName

# Retrieve members of the group
$groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

# Display user details for each member
foreach ($member in $groupMembers) {
    $user = Get-AzureADUser -ObjectId $member.ObjectId
    $user | Select-Object DisplayName, UserPrincipalName, ObjectId
}

````

In this example, replace "YourGroupName" with the actual name of the group you want to retrieve member details for. The Get-AzureADGroup cmdlet retrieves the group based on the provided search string (group name). The Get-AzureADGroupMember cmdlet retrieves the members of the group based on the group's ObjectId. The Select-Object cmdlet is used to display selected properties such as "DisplayName," "UserPrincipalName," and "ObjectId" for each member.

## Retrieve Specific Group's Members:

Use the Get-AzureADGroupMember cmdlet to retrieve the members of a specific group. Here's how to do it:

````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

# Check if the group exists
if ($group) {
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

    # Display user details
    foreach ($member in $groupMembers) {
        if ($member.objectType -eq "User") {
            $user = Get-AzureADUser -ObjectId $member.ObjectId
            Write-Host "User DisplayName: $($user.DisplayName)"
            Write-Host "User Principal Name: $($user.UserPrincipalName)"
            # Add more user properties as needed
            Write-Host ""
        }
    }
} else {
    Write-Host "Group '$groupName' not found."
}

````
Replace "YourGroupName" with the actual name of the group you want to retrieve member details for. This script fetches the group by name, checks if it exists, and then retrieves and displays user details for each member of the group. You can customize which user properties you want to display by adding more lines in the foreach loop.

## Retrieve Specific Group's Members with Last Login Example1:

Use the Microsoft Graph API through the AzureADPreview module to retrieve the members of a specific group along with their last login information. Here's how to do it:

````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group's object ID
$groupId = (Get-AzureADGroup -SearchString $groupName).ObjectId

# Get group members with last login info
$groupMembers = Get-AzureADGroupMember -ObjectId $groupId | Where-Object { $_.ObjectType -eq "User" }

# Iterate through group members and get their last login info
foreach ($member in $groupMembers) {
    $user = Get-AzureADUser -ObjectId $member.ObjectId
    $user | Select-Object DisplayName, UserPrincipalName, LastSignInDateTime
}

````
In this example, replace "YourGroupName" with the actual name of the group you want to retrieve member details for. The Get-AzureADGroup cmdlet retrieves the group based on the provided search string (group name). The Get-AzureADGroupMember cmdlet retrieves the members of the group based on the group's ObjectId. The Select-Object cmdlet is used to display selected properties such as "DisplayName," "UserPrincipalName," and "LastSignInDateTime" (which provides the last login information) for each member.

## Retrieve Specific Group's Members with Last Login Example2:
````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group members
$groupMembers = Get-ADGroupMember -Identity $groupName

# Initialize an array to store the results
$results = @()

# Iterate through group members and get their LastLogonDate
foreach ($member in $groupMembers) {
    if ($member.objectClass -eq "user") {
        $user = Get-ADUser -Identity $member -Properties LastLogonDate

        # Check if the LastLogonDate property is not null
        if ($user.LastLogonDate) {
            $result = [PSCustomObject]@{
                DisplayName = $user.DisplayName
                UserPrincipalName = $user.UserPrincipalName
                LastLogonDate = $user.LastLogonDate
            }

            $results += $result
        }
    }
}


# Display the results
$results | Select-Object DisplayName, UserPrincipalName, LastLogonDate
````

### In this script:

* Replace "YourGroupName" with the actual name of the group you want to retrieve member details for.

* The Get-ADGroupMember cmdlet retrieves the members of the specified group.

* We initialize an array called $results to store the user details with LastLogonDate.

* We iterate through the group members, and for each member, we check if they are of the object class "user."

* For user objects, we use Get-ADUser to fetch their LastLogonDate property.

* We check if the LastLogonDate property is not null (i.e., if it has a value), and if so, we create a custom object and add it to the $results array.

* Finally, we display the results containing the DisplayName, UserPrincipalName, and LastLogonDate properties for users who are members of the specified group.


## Retrieve Specific Group's Members and Their User Information:

Use the Get-AzureADGroupMember cmdlet to retrieve the members of a specific group. Then, for each user, fetch their user information and last login information. Here's how to do it:

````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

# Check if the group exists
if ($group) {
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

    # Iterate through group members
    foreach ($member in $groupMembers) {
        if ($member.objectType -eq "User") {
            $user = Get-AzureADUser -ObjectId $member.ObjectId

            # Get the user's last login information
            $signInActivity = Get-AzureADAuditSignInLogs -Filter "UserId eq '$($user.ObjectId)'" | Sort-Object -Property CreationTime -Descending | Select-Object -First 1

            $lastLogin = $signInActivity.CreationTime
            
            # Display user information
            Write-Host "User DisplayName: $($user.DisplayName)"
            Write-Host "User Email: $($user.Mail)"
            Write-Host "Last Login: $lastLogin"
            Write-Host ""
        }
    }
} else {
    Write-Host "Group '$groupName' not found."
}

````

Replace "YourGroupName" with the actual name of the group you want to retrieve member details for. This script fetches the group by name, checks if it exists, and then retrieves and displays user details including their email addresses and last login information. Make sure you have the necessary permissions to read group and user information in your Azure AD tenant.


## Retrieve Specific Group's Members and Their User Information:

Use the Get-AzureADGroupMember cmdlet to retrieve the members of a specific group. Then, for each user, fetch their user information and last sign-in information. Here's how to do it:

````
# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

# Check if the group exists
if ($group) {
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

    # Iterate through group members
    foreach ($member in $groupMembers) {
        if ($member.objectType -eq "User") {
            $user = Get-AzureADUser -ObjectId $member.ObjectId

            # Get the user's last sign-in information
            $signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreationTime -Descending | Select-Object -First 1

            $lastSignIn = $signInActivity.CreationTime

            # Display user information
            Write-Host "User Name: $($user.DisplayName)"
            Write-Host "User Email: $($user.Mail)"
            Write-Host "Last Sign-In: $lastSignIn"
            Write-Host ""
        }
    }
} else {
    Write-Host "Group '$groupName' not found."
}

````

Replace "YourGroupName" with the actual name of the group you want to retrieve member details for. This script fetches the group by name, checks if it exists, and then retrieves and displays user details, including their email addresses and last sign-in information. Ensure that you have the necessary permissions to read group and user information in your Azure AD tenant.

## Add specific group user info in csv
````
# Install and import the AzureAD module if not already done
Install-Module AzureAD
Import-Module AzureAD

# Connect to your Azure AD tenant
Connect-AzureAD

# Replace "YourGroupName" with the actual name of the group
$groupName = "YourGroupName"

# Get the group
$group = Get-AzureADGroup -Filter "DisplayName eq '$groupName'"

# Check if the group exists
if ($group) {
    # Get group members
    $groupMembers = Get-AzureADGroupMember -ObjectId $group.ObjectId

    # Initialize an array to store results
    $results = @()

    # Iterate through group members
    foreach ($member in $groupMembers) {
        if ($member.objectType -eq "User") {
            $user = Get-AzureADUser -ObjectId $member.ObjectId

            # Get the user's last sign-in information
            $signInActivity = Get-AzureADAuditSignInLogs -Filter "UserPrincipalName eq '$($user.UserPrincipalName)'" | Sort-Object -Property CreationTime -Descending | Select-Object -First 1

            $lastSignIn = $signInActivity.CreationTime

            # Create a custom object with user information
            $userInfo = [PSCustomObject]@{
                Name = $user.DisplayName
                Email = $user.Mail
                LastSignIn = $lastSignIn
            }

            # Add the user information to the results array
            $results += $userInfo
        }
    }

    # Output the results to a text file
    $results | Export-Csv -Path "UserInformation.csv" -NoTypeInformation
    Write-Host "User information exported to UserInformation.csv"
} else {
    Write-Host "Group '$groupName' not found."
}
````

### In this script:

* Replace "YourGroupName" with the actual name of the group you want to retrieve member details for.
* The script fetches the group by name, checks if it exists, and retrieves user details, including their email addresses and last sign-in information.
* User information is stored in a custom object and added to the $results array.
* The results are then exported to a CSV file named "UserInformation.csv" using Export-Csv.


