### IAM Security Tools
- **IAM Credentials Report (account-level)**
    - a report that lists all your account's users and the status of their various credentials

- **IAM Access Advisor (user-level)**
    - Access advisor shows the service permissions granted to a user and when those services were last accessed.
    - You can use this information to revise your policies.

### IAM Access Analyzer
- Find out which resources are shared externally
    - S3 Buckets
    - IAM Roles
    - KMS Keys
    - Lambda Functions and Layers
    - SQS queues
    - Secrets Manager Secrets
- Define **Zone of Trust** = AWS Account or AWS Organization
- Access outside zone of trusts => findings

### What’s Identity Federation?
- Federation lets users outside of AWS to assume temporary role for accessing AWS resources.
- These users assume identity provided access role.
- **Federation assumes a form of 3rd party authentication**
    - LDAP
    - Microsoft Active Directory (~= SAML)
    - Single Sign On
    - Open ID
    - Cognito
- Using federation, you don’t need to create IAM users (user management is outside of AWS)

### SAML Federation 
**For Enterprises**
- To integrate Active Directory / ADFS with AWS (or any SAML 2.0)
- Provides access to AWS Console or CLI (through temporary creds)
- No need to create an IAM user for each of your employees

### Custom Identity Broker Application 
**For Enterprises**
- Use only if identity provider is not compatible with SAML 2.0
- **The identity broker must determine the appropriate IAM policy**

https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_common-scenarios_federated-users.html

### AWS Cognito - Federated Identity Pools
**For Public Applications**
- **Goal**:
    - Provide direct access to AWS Resources from the Client Side
- **How**:
    - Log in to federated identity provider – or remain anonymous
    - Get temporary AWS credentials back from the Federated Identity Pool
    - These credentials come with a pre-defined IAM policy stating their permissions
- Example:
    - provide (temporary) access to write to S3 bucket using Facebook Login
- **Note**:
    - Web Identity Federation is an alternative to using Cognito but AWS recommends against it

### AWS STS – Security Token Service
- **Allows to grant limited and temporary access to AWS resources.**
- Token is valid for up to one hour (must be refreshed)
- **AssumeRole**
    - Within your own account: for enhanced security
    - Cross Account Access: assume role in target account to perform actions there
- **AssumeRoleWithSAML**
    - return credentials for users logged with SAML
- **AssumeRoleWithWebIdentity**
    - return creds for users logged with an IdP (Facebook Login, Google Login, OIDC compatible…)
    - AWS recommends against using this, and using **Cognito** instead
- **GetSessionToken**
    - for MFA, from a user or AWS account root user

### Using STS to Assume a Role
- Define an IAM Role within your account or cross-account
- Define which principals can access this IAM Role
- Use AWS STS (Security Token Service) to retrieve credentials and impersonate the IAM Role you have access to **(AssumeRole API)**
- Temporary credentials can be valid between 15 minutes to 1 hour

### Cross-Account Access with STS

### Cognito User Pools (CUP) – User Features
- **Create a serverless database of user for your web & mobile apps**
- Simple login: Username (or email) / password combination
- Password reset
- Email & Phone Number Verification
- Multi-factor authentication (MFA)
- Federated Identities: users from Facebook, Google, SAML…
- Feature: block users if their credentials are compromised elsewhere
- Login sends back a JSON Web Token (JWT)

### Cognito User Pools (CUP) – Diagram

### Cognito User Pools (CUP) - Integrations
- CUP integrates with **API Gateway** and **Application Load Balancer**

### Cognito Identity Pools (Federated Identities)
- **Get identities for “users” so they obtain temporary AWS credentials**
- Your identity pool (e.g identity source) can include:
    - Public Providers (Login with Amazon, Facebook, Google, Apple)
    - Users in an Amazon Cognito user pool
    - OpenID Connect Providers & SAML Identity Providers
    - Developer Authenticated Identities (custom login server)
    - Cognito Identity Pools allow for **unauthenticated (guest) access**
- **Users can then access AWS services directly or through API Gateway**
    - The IAM policies applied to the credentials are defined in Cognito
    - They can be customized based on the user_id for fine grained control

### Cognito Identity Pools – Diagram

### Cognito Identity Pools – Diagram with CUP

### Cognito Identity Pools – IAM Roles
- Default IAM roles for authenticated and guest users
- Define rules to choose the role for each user based on the user’s ID
- You can partition your users’ access using **policy variables**

- IAM credentials are obtained by Cognito Identity Pools through STS
- The roles must have a “trust” policy of Cognito Identity Pools

### Cognito Identity Pools – Guest User example

### Cognito Identity Pools – Policy variable on S3

### Cognito Identity Pools – DynamoDB

### Cognito User Pools vs Identity Pools
- **Cognito User Pools (for authentication = identity verification)**
    - Database of users for your web and mobile application
    - Allows to federate logins through Public Social, OIDC, SAML…
    - Can customize the hosted UI for authentication (including the logo)
    - Has triggers with AWS Lambda during the authentication flow
    - Adapt the sign-in experience to different risk levels (MFA, adaptive authentication, etc…)
- **Cognito Identity Pools (for authorization = access control)**
    - Obtain AWS credentials for your users
    - Users can login through Public Social, OIDC, SAML & Cognito User Pools
    - Users can be unauthenticated (guests)
    - Users are mapped to IAM roles & policies, can leverage policy variables
- **CUP + CIP = authentication + authorization**

### Cognito Identity Pools – Diagram with CUP