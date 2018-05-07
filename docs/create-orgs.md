Goals:  

1. Create Organizational Units GD-USA, CTO & KPIDashboard under master account.

2. Create the 6 specifit specific AWS accounts for KPIDashboard and map them under appropriate OU’s.


**Table of Contents:**
-   [Create Organizational Units in the master account](#create-organizational-units-in-the-master-account)
    -   [Get Organization](#get-organization)
    -   [Create Organizational Units (OUs)](#create-organizational-units-ous)
        -   [Create GD-USA OU](#create-security-ou)
        -   [Create CTO OU](#create-shared-services-ou)
        -   [Create KPIDashboard OU](#create-applications-ou)
-   [Create 6 required AWS accounts](#create-required-aws-accounts)
-   [Move 6 accounts under CTO Organizational Units](#move-accounts-under-corresponding-organizational-units)
    -   [Move 'KPIDashboard-Dev-Private' to 'KPIDashboard OU'.](#move-security-account-to-KPIDashboard-ou)
    -   [Move 'KPIDashboard-Dev' to 'KPIDashboard OU'.](#move-shared-services-account-to-shared-services-ou)
    
    Nice to have - need to define
    
-   [Configure CLI for Cross Account access through Assume Role (only if you are using CLI)](#configure-cli-for-cross-account-access-through-assume-role-only-if-you-are-using-cli)
-   [Expected Outcome](expected-outcome)


## CreateOrganizational Units in the master account

Login to your [AWS Management Console](https://us-east-1.console.aws.amazon.com/console/home?region=us-east-1) and navigate to [AWS Organizations](https://console.aws.amazon.com/organizations/home) console.

Create IAM-god role in Master Account

Get the ID of the master organization and save it in `ResourcesList.txt`

<code>
aws organizations list-roots --region us-east-1 --query 'Roots[0].Id'
</code>

```
r-abcd
```

### Create Organizational Units (OUs)

#### Create GD-USA OU
*   Create GD-USA Organizational Units (OU) and name it `GD-USA` [following the steps in documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#create_ou).

**Using CLI:**

*   Use the correct organization ID for parameter `--parent-id` in the below command, create organizational unit.

<code>aws organizations create-organizational-unit --region us-east-1 --name GD-USA --parent-id <b><i>r-abcd</i></b>
</code>


```json
{
    "OrganizationalUnit": {
        "Id": "ou-abcd-7example",
        "Arn": "arn:aws:organizations::123456789012:ou/o-got31bf9ah/ou-abcd-7example",
        "Name": "GD-USA"
    }
}
```

> Save the value of GD-USA OU Id (e.g. ou-abcd-7example) returned by the above command or from the UI in ResourcesList.txt file.

> Create Organizational Unit via API

https://docs.aws.amazon.com/organizations/latest/APIReference/API_CreateOrganizationalUnit.html


#### Create CTO OU
*   Create CTO Organizational Unit (OU) and name it `CTO` [following the steps in documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#create_ou).

**Using CLI:**

*   Use the correct organization ID for parameter `--parent-id` in the below command, create organizational unit.

<code>
aws organizations create-organizational-unit --region us-east-1 --name "CTO" --parent-id <b><i>r-abcd</i></b>
</code><br>


```json
{
    "OrganizationalUnit": {
        "Id": "ou-abcd-7example",
        "Arn": "arn:aws:organizations::123456789012:ou/o-got31bf9ah/ou-abcd-7example",
        "Name": "CTO"
    }
}
```

> Save the value of Shared Services OU Id (e.g. ou-abcd-7example) returned by the above command or from the UI in ResourcesList.txt file.


#### Create KPIDashboard OU
*   Create Applications Organizational Unit (OU) and name it `KPIDashboard` [following the steps in documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#create_ou).

**Using CLI:**

*   Use the correct organization ID for parameter `--parent-id` in the below command, create organizational unit.

<code>
aws organizations create-organizational-unit --region us-east-1 --name KPIDashboard --parent-id <b><i>r-abcd</i></b>
</code><br>


```json
{
    "OrganizationalUnit": {
        "Id": "ou-abcd-7example",
        "Arn": "arn:aws:organizations::123456789012:ou/o-got31bf9ah/ou-abcd-7example",
        "Name": "KPIDashboard"
    }
}
```

> Save the value of KPIDashboard OU Id (e.g. ou-abcd-7example) returned by the above command or from the UI in ResourcesList.txt file.


## Create required AWS accounts

> Each AWS account that you create requires an unique email address. For ease of use, most mail servers ignores the characters after a plus sign `+`. You shall add strings like `+lzsec` to your existing email address to get unique email address, still the mails will get delivered to the same mailbox as the original email.  
>   
> E.g. If your email address is `noreply@example.com`, you shall use `noreply+lzsec@example.com` while creating the account and it will deliver the emails to `noreply@example.com` mailbox.  
>   
> Check whether your mail server supports this capability by sending a test email. If it doesn’t support this capability then you need to create unique email address for each account that you are creating.

### Create KPIDashboard-Dev-Private Account
1.  Navigate to Accounts tab of AWS Organizations console.

2.  Click ‘Add Account’ followed by [‘Create Account’](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_create.html).

3.  Create a new security account by entering the following details.

    *   Full Name – Enter a name (e.g. Security Account)
    *   Email Address – Valid unique email address (e.g. noreply+lzsec@example.com)
    *   IAM role name – Admin IAM role which the appropriate user in the account can assume. Name it **IAM-god** for all the accounts you are creating under the Master account.
    *  <TAG or ?? > Every account that get’s created needs DL+SNS - escalation point for Billing, Alerts, critical escalation 

**Using CLI:**

Update the --email parameter to appropriate email address and run the command. Save the create request id in the 'ResourcesList.txt' file.

<code>aws organizations create-account --role-name PayerAccountAccessRole --iam-user-access-to-billing ALLOW --region us-east-1 --profile billing --account-name "Security Account" --email <b><i>noreply+lzsec@example.com</i></b>
</code>


```json
{
    "CreateAccountStatus": {
        "RequestedTimestamp": 1508943783.375,
        "State": "IN_PROGRESS",
        "Id": "car-77558640b99511e78c88511c44cd49c5",
        "AccountName": "Security Account"
    }
}
```

> Save the value of Create Account Request Id (e.g. car-77558640b99511e78c88511c44cd49c5) returned by the above command in ResourcesList.txt file to check the status if needed.

### Create Shared Services Account
1.  Click ‘Add Account’ followed by [‘Create Account’](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_create.html).

2.  Create a new Shared Services account by entering the following details.

    *   Full Name – Enter a name (e.g. Shared Services Account)
    *   Email Address – Valid unique email address (e.g. noreply+lzss@example.com)
    *   IAM role name – Admin IAM role which the appropriate user in Billing account can assume. Name it **PayerAccountAccessRole** for all the accounts you are creating under the Master account.

**Using CLI:**

Update the --email parameter to appropriate email address and run the command. Save the create request id in the 'ResourcesList.txt' file.


<code>aws organizations create-account --role-name PayerAccountAccessRole --iam-user-access-to-billing ALLOW --region us-east-1 --profile billing --account-name "Shared Services Account" --email <b><i>noreply+lzss@example.com</i></b>
</code>


```json
{
    "CreateAccountStatus": {
        "RequestedTimestamp": 1508943783.375,
        "State": "IN_PROGRESS",
        "Id": "car-77558640b99511e78c88511c44cd49c5",
        "AccountName": "Shared Services Account"
    }
}
```
> Save the value of Create Account Request Id (e.g. car-77558640b99511e78c88511c44cd49c5) returned by the above command in ResourcesList.txt file to check the status if needed.

### Create Application One Account
1.  Click ‘Add Account’ followed by [‘Create Account’](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_create.html).

2.  Create a new Application One account by entering the following details.

    *   Full Name – Enter a name (e.g. Application One Account)
    *   Email Address – Valid unique email address (e.g. noreply+lzapp1@example.com)
    *   IAM role name – Admin IAM role which the appropriate user in Billing account can assume. Name it **PayerAccountAccessRole** for all the accounts you are creating under the Master account.

**Using CLI:**

Update the --email parameter to appropriate email address and run the command. Save the create request id in the 'ResourcesList.txt' file.

<code>aws organizations create-account --role-name PayerAccountAccessRole --iam-user-access-to-billing ALLOW --region us-east-1 --profile billing --account-name "Application One Account" --email <b><i>noreply+lzapp1@example.com</i></b>
</code>


```json
{
    "CreateAccountStatus": {
        "RequestedTimestamp": 1508943783.375,
        "State": "IN_PROGRESS",
        "Id": "car-77558640b99511e78c88511c44cd49c5",
        "AccountName": "Shared Services Account"
    }
}
```
> Save the value of Create Account Request Id (e.g. car-77558640b99511e78c88511c44cd49c5) returned by the above command in ResourcesList.txt file to check the status if needed.

## Move accounts under corresponding Organizational Units

Navigate to 'Organize Accounts' tab in AWS Organizations console, which will display all the accounts under your organization.

**Using CLI**  
Get the 12 digit AWS account Ids of the 'Security', 'Shared Services' and 'Applications' accounts.

```
aws organizations list-accounts --region us-east-1 --profile billing --query 'Accounts[*].{Name:Name,Email:Email,AccountId:Id}' --output table
```

```
----------------------------------------------------------------------------
|                               ListAccounts                               |
+--------------+-------------------------------+---------------------------+
|   AccountId  |             Email             |           Name            |
+--------------+-------------------------------+---------------------------+
|  123456789012|  noreply+billing@example.com  |  ARC325 Team #            |
|  321098987654|  noreply+lzss@example.com     |  Shared Services Account  |
|  654321987098|  noreply+lzapp1@example.com   |  Application One Account  |
|  987654321098|  noreply+lzsec@example.com    |  Security Account         |
+--------------+-------------------------------+---------------------------+
```

If any of the accounts are missing, check the status of create account request using the following command by providing the correct creation request id for `--create-account-request-id` parameter and check the 'FailureReason' to fix it.

<code>aws organizations describe-create-account-status --region us-east-1 --profile billing --create-account-request-id <b><i>car-bb4f1750cdef11e78b08511c66cd64c5</i></b>
</code>


```json
{
    "CreateAccountStatus": {
        "AccountName": "Shared Services Account",
        "State": "FAILED",
        "RequestedTimestamp": 1511181518.779,
        "FailureReason": "EMAIL_ALREADY_EXISTS",
        "Id": "car-bb4f1750cdef11e78b08500c66cd64c5",
        "CompletedTimestamp": 1511181519.137
    }
}
```

### Move 'Security Account' to 'Security OU'.

Select the Security Account in the console and move it to Security OU as explained in [the documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#move_account_to_ou).

**Using CLI**

Provide the 12 digit account id of Security account for `--account-id` parameter, provide the ID of the organization (e.g. r-abcd) for `--source-parent-id` parameter and ID of the Security OU (e.g. ou-abcd-7example) for `--destination-parent-id`.

<code>aws organizations move-account --region us-east-1 --profile billing --source-parent-id <b><i>r-abcd</i></b> --destination-parent-id <b><i>ou-abcd-7example</i></b> --account-id <b><i>987654321098</i></b>
</code></br>


Check whether the account got moved successfully.

<code>
aws organizations list-accounts-for-parent --region us-east-1 --profile billing --query 'Accounts[&#42;].{Name:Name,Email:Email,Id:Id,Status:Status}' --output table --parent-id <b><i>ou-abcd-7example</i></b>
</code>

```
--------------------------------------------------------------------------------------
|                                ListAccountsForParent                               |
+------------------------------+---------------+---------------------------+---------+
|             Email            |      Id       |           Name            | Status  |
+------------------------------+---------------+---------------------------+---------+
|  noreply+lzsec@example.com   |  987654321098 |  Security Account         |  ACTIVE |
+------------------------------+---------------+---------------------------+---------+
```

### Move 'Shared Services Account' to 'Shared Services OU'.

Select the Shared Services Account in the console and move it to Shared Services OU as explained in [the documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#move_account_to_ou).

**Using CLI**

Provide the 12 digit account id of Shared Services account for `--account-id` parameter, provide the ID of the organization (e.g. r-abcd) for `--source-parent-id` parameter and ID of the Shared Services OU (e.g. ou-abcd-7example) for `--destination-parent-id`.

<code>aws organizations move-account --region us-east-1 --profile billing --source-parent-id <b><i>r-abcd</i></b> --destination-parent-id <b><i>ou-abcd-7example</i></b> --account-id <b><i>321098987654</i></b>
</code><br>

Check whether the account got moved successfully.


<code>
aws organizations list-accounts-for-parent --region us-east-1 --profile billing --query 'Accounts[&#42;].{Name:Name,Email:Email,Id:Id,Status:Status}' --output table --parent-id <b><i>ou-abcd-7example</i></b>
</code><br>

```
--------------------------------------------------------------------------------------
|                                ListAccountsForParent                               |
+------------------------------+---------------+---------------------------+---------+
|             Email            |      Id       |           Name            | Status  |
+------------------------------+---------------+---------------------------+---------+
|  noreply+lzss@example.com    |  321098987654 |  Shared Service Account   |  ACTIVE |
+------------------------------+---------------+---------------------------+---------+
```

### Move 'Application One Account' to 'Applications OU'.

Select the Application One Account in the console and move it to Applications OU as explained in [the documentation](http://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_ous.html#move_account_to_ou).


**Using CLI**

Provide the 12 digit account id of Application One account for `--account-id` parameter, provide the ID of the organization (e.g. r-abcd) for `--source-parent-id` parameter and ID of the Application One OU (e.g. ou-abcd-7example) for `--destination-parent-id`.

<code>aws organizations move-account --region us-east-1 --profile billing --source-parent-id <b><i>r-abcd</i></b> --destination-parent-id <b><i>ou-abcd-7example</i></b> --account-id <b><i>654321987098</i></b>
</code><br>

Check whether the account got moved successfully.


<code>
aws organizations list-accounts-for-parent --region us-east-1 --profile billing --query 'Accounts[&#42;].{Name:Name,Email:Email,Id:Id,Status:Status}' --output table --parent-id <b><i>ou-abcd-7example</i></b>
</code><br>

```
--------------------------------------------------------------------------------------
|                                ListAccountsForParent                               |
+------------------------------+---------------+---------------------------+---------+
|             Email            |      Id       |           Name            | Status  |
+------------------------------+---------------+---------------------------+---------+
|  noreply+lzapp1@example.com  |  654321987098 | Application One Account   |  ACTIVE |
+------------------------------+---------------+---------------------------+---------+
```

## Configure CLI for Cross Account access through Assume Role (only if you are using CLI)

Update the AWS CLI configuration file `~/.aws/config` in your workstation with the details of all the 3 accounts to perform [cross account assume role](http://docs.aws.amazon.com/cli/latest/userguide/cli-roles.html) using the ARN of the role (`PayerAccountAccessRole`) created in each account.

*Example:*

```
~/.aws/config
[profile billing]
region=us-east-1
output=json

[profile security]
role_arn = arn:aws:iam::987654321098:role/PayerAccountAccessRole
source_profile = billing
region=eu-west-1
output=json

[profile sharedserv]
role_arn = arn:aws:iam::321098987654:role/PayerAccountAccessRole
source_profile = billing
region=eu-west-1
output=json

[profile appone]
role_arn = arn:aws:iam::654321987098:role/PayerAccountAccessRole
source_profile = billing
region=eu-west-1
output=json
```

You shall use the above snippet and update the appropriate Account ID in the role_arn.

## Expected Outcome
*   Created an Organizations
*   Created 3 Organizational Units named `Security`, `Shared Services` and `Applications`
*   Created 3 Sub Accounts for `Security`, `Shared Services` and `Application One`
*   Moved the accounts under corresponding Organization Units.
*   Updated the AWS CLI config to enable Cross Account Access using role.

![create-orgs-image](../images/create-orgs.png)
