# okta-aws-connect-connector
## Introduction
Amazon Connect is a cloud-based contact center solution that provides businesses with a set of tools to interact with your customers. Amazon Connect supports SAML 2.0-based authentication to  simplify the process of managing user access to applications and resources, and the employees don't need to remember multiple usernames and passwords, making it easier for them to access the resources they need. Okta is a popular identity and access management solution that many customers may choose inside their organizations and use it to integrate with Amazon Connect. AWS has blog and workshop to guide you how to setup your Amazon Connect instance with OKTA step by step.

However, currently it doesn't support automatic user provisioning from Okta to Amazon Connect. That means even when you add users into your applications on Okta, and you still needs to manually create those users on Amazon Connect or upload the user list through a Amazon Connect [csv template](https://docs.aws.amazon.com/connect/latest/adminguide/user-management.html#add-users-in-bulk).

## Implementation
To sync the users created in Okta, we need to leverage [Okta Event hooks](https://help.okta.com/en-us/Content/Topics/automation-hooks/event-hooks-main.htm) to automatically provision users to Amazon Connect with mapping routing profile and security profiles to give the proper permissions to your agents.

This sample use Amazon API Gateway and AWS Lambda to retrieve the event hooks from Okta and implements the [initial event hook verification](initial event hook verification) when the first time you setup event hook on your Okta org and the logic to create the users on Amazon Connect.

## Prerequisite
* [Configure Single Sign-On for Amazon Connect Using Okta](https://aws.amazon.com/blogs/contact-center/configure-single-sign-on-for-amazon-connect-using-okta/)
* Setup [Okta event hooks](https://help.okta.com/en-us/Content/Topics/automation-hooks/event-hooks-main.htm)
* Subscribe specific [Okta events](https://developer.okta.com/docs/reference/api/event-types/)
  * **application.user_membership.add**
  * **group.user_membership.add**

## Configs
Change configs in the **config.ini** to fit your Amazon Connect and Okta settings

```[OKTA]
APP_NAME = AWS Account Federation  # Your Amazon Connect Application name in Okta
GROUP_NAME = amazon_connect # The group ties to your Amazon Connect Application
APP_MEMBERSHIP_ADD_EVENT = application.user_membership.add # User provisioned to app event
GROUP_MEMBERSHIP_ADD_EVENT = group.user_membership.add # Add user to group membership event

[Connect]
SECURITY_PROFILE_IDS = 12345678-1234-2345-abd8-0aa7f5b46852, 65345678-1234-2345-abd8-0aa7f5b46852, 98345678-1234-2345-abd8-0aa7f5b46852 # Bind security profile the provisioning agents
ROUTING_PROFILE_ID = 87654321-69fb-43b6-a5e6-f8666ac189cb # Bind routing profile to provisioning agents
INSTANCE_ID = abcdefgh-0122-4131-adc2-a0ebe5a2b2a7 # Your Amazon Connect Instance ID
```
