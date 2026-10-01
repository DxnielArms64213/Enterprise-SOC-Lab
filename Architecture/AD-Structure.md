# Active Directory Structure

## Overview

Active Directory provides centralised identity and management for the Windows systems within the Enterprise SOC Homelab.

The environment uses organisational units, domain users and security groups to create a basic enterprise-style directory structure.

## Active Directory Structure

```text
YOUR-DOMAIN
│
├── Lab-Users
│   └── labuser
│
├── Lab-Computers
│
├── Lab-Servers
│
└── Lab-Groups
    └── SOC-Analysts
        └── labuser
```

## Organisational Units

### Lab-Users

Contains user accounts belonging to the lab environment.

Current account:

```text
labuser
```

Separating users into their own OU provides a dedicated location for managing domain users.

### Lab-Computers

Contains domain-joined endpoint computers.

The future Windows Client will be placed inside this OU.

The security baseline Group Policy is also linked to this OU.

### Lab-Servers

Provides a dedicated organisational unit for server objects as the environment grows.

### Lab-Groups

Contains security groups used to manage permissions and access.

Current security group:

```text
SOC-Analysts
```

## Domain User

The domain user:

```text
labuser
```

was created and placed inside:

```text
Lab-Users
```

This provides a realistic user identity that can later generate authentication and account-management activity.

## Security Group

The following Global Security group was created:

```text
SOC-Analysts
```

The `labuser` account was added as a member.

Using security groups allows access and permissions to be assigned according to a user's role rather than configuring every account individually.

## Why This Structure Was Chosen

The structure separates the main types of Active Directory objects used within the lab:

* Users
* Computers
* Servers
* Security groups

This makes the environment easier to manage and provides a foundation for future Group Policy configuration.

It also creates a more realistic enterprise-style Active Directory environment.

## Future Expansion

As the SOC lab develops, additional users, computers and groups can be added to the existing structure.

The Windows Client will eventually be placed into:

```text
Lab-Computers
```

This will allow it to receive the security baseline Group Policy configured for the lab.
