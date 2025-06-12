# SAF-3378: Safety Shield SSO LDAP Integration Stories

This document contains the user stories for implementing Phase 1.5 (LDAP integration) of the Safety Shield SSO solution using Azure AD B2C. These stories are based on the epic SAF-3378 and the associated stories from Jira.

## Story 1: Develop Custom Policies for AD/LDAP Integration (SAF-3299)

**As a** Safety Shield system engineer  
**I want to** develop custom policies that allow authentication against customer AD/LDAP servers  
**So that** customers can use their existing identity directories with Safety Shield

**Background:**
Create custom policies (trust framework policies) in Azure AD B2C that enable authentication against customer AD/LDAP servers. This will involve developing XML policy files that establish trust relationships with customer identity providers.

**Technical Notes:**
- Use Azure AD B2C Identity Experience Framework (IEF)
- Implement secure connection to LDAP servers (LDAPS)
- Design for minimal attribute collection needed for Safety Shield user identification
- Support for different LDAP schemas and configurations

## Story 2: Create Migration Path for LDAP Users (SAF-3300)

**As a** Safety Shield administrator  
**I want to** have a clear migration path for existing LDAP users to the new SSO system  
**So that** user transition is smooth and minimizes disruption

**Background:**
Develop a strategy and tooling for migrating existing LDAP-synced users to the new authentication system. This should include identifying matching users between the existing system and the customer's directory, handling edge cases, and providing tools for administrators to manage the transition.

**Technical Notes:**
- Create database scripts for user mapping
- Implement dual-authentication support during transition
- Provide logging and reporting on migration status
- Test migration process thoroughly with sample data

## Story 3: Implement Organization-level Identity Provider Configuration (SAF-3301)

**As a** Safety Shield administrator  
**I want to** configure the preferred identity provider at the organization level  
**So that** users from different organizations can use their appropriate authentication method

**Background:**
Add functionality in the Safety Shield administration interface to allow administrators to configure which identity provider should be used for their organization. This setting will control which authentication method is presented to users from that organization.

**Technical Notes:**
- Update database schema to store identity provider preference
- Create API endpoints to manage identity provider configuration
- Implement proper validation and error handling
- Design React components for the administration interface
- Use React form libraries (e.g., Formik, React Hook Form) for configuration forms
- Implement state management for configuration settings (Redux, Context API, etc.)
- Document the configuration process for administrators

## Story 4: Test LDAP User Authentication and Integration (QA) (SAF-3334)

**As a** Safety Shield Quality Engineer  
**I want to** thoroughly test LDAP user authentication through Azure AD B2C custom policies  
**So that** I can ensure customers can successfully authenticate using their existing directory services

**Background:**
Execute comprehensive testing of the Phase 1.5 LDAP integration implementation, including custom policy functionality, LDAP connectivity, user mapping, and authorization preservation. This testing validates that customers can seamlessly use their existing LDAP/AD infrastructure with Safety Shield.

**Technical Notes:**
- Test B2C custom policies for LDAP authentication
- Verify LDAP connectivity and secure communication (LDAPS)
- Test user mapping between LDAP attributes and Safety Shield user records
- Validate authentication with different LDAP configurations and schemas
- Test organization-level identity provider configuration
- Verify that Safety Shield authorization works correctly for LDAP users
- Test network failure scenarios and LDAP server unavailability
- Validate user attribute synchronization and updates
