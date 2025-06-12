# SAF-3271: Safety Shield SSO Implementation Stories

This document contains the user stories for implementing Phase 1 of the Safety Shield SSO solution using Azure AD B2C. These stories are based on the epic SAF-3271 and the associated stories from Jira.

## Phase 1 Architecture Overview
Phase 1 uses Azure AD B2C purely for authentication. The flow is:
1. User authenticates with B2C (local B2C account or LDAP via B2C)
2. Client receives B2C token
3. Client immediately exchanges B2C token for standard Safety Shield token via API
4. All subsequent API calls use the Safety Shield token (existing behavior)
5. Existing Safety Shield authorization, session management, and permissions remain unchanged

## Implementation Priority
- **Phase 1A**: Local user authentication (easier implementation, foundational)
- **Phase 1B**: LDAP integration (more complex, builds on local user foundation)

## Story 1: Set up Azure AD B2C Application Registration (SAF-3293)

**As a** Safety Shield system engineer  
**I want to** set up an Azure AD B2C application registration for Safety Shield  
**So that** the Safety Shield application can securely interact with Azure AD B2C services

**Background:**
Create an application registration in the existing Azure AD B2C tenant that will represent the Safety Shield web UI. This involves setting up the appropriate redirect URIs, permissions, and application settings required for OAuth 2.0/OpenID Connect authentication. Use the Sandbox tenant for development, testing, and staging environments, and the Production tenant for the production environment.

**Technical Notes:**
- Use Microsoft Identity Platform v2.0 endpoints
- Configure Authorization Code Flow with PKCE for both web and mobile applications
- Avoid using Implicit Flow due to security considerations
- Enable ID tokens and access tokens
- Set appropriate token lifetimes according to security requirements

## Story 2: Create User Flow for Local Users (SAF-3294)

**As a** Safety Shield system engineer  
**I want to** set up B2C user flows for local Safety Shield users  
**So that** users with local accounts can authenticate using Azure AD B2C

**Background:**
Create and configure standard user flows in Azure AD B2C for sign-up and sign-in of local Safety Shield users (users previously stored only in the Safety Shield database). This will serve as the foundation for the SSO implementation before tackling the more complex LDAP integration.

**Technical Notes:**
- Use built-in B2C user flows rather than custom policies for this initial implementation
- Determine the minimal set of claims needed for Safety Shield operation
- Configure appropriate session behavior and token lifetimes

## Story 3: Implement API Integration for User Validation (SAF-3295)

**As a** Safety Shield developer  
**I want to** implement an API integration layer that validates B2C tokens and generates Safety Shield tokens  
**So that** the Safety Shield application can maintain existing authorization mechanisms while using B2C for authentication

**Background:**
Develop an integration between the Azure AD B2C authentication flow and the Safety Shield API that validates B2C tokens, maps users to existing Safety Shield accounts, and generates standard Safety Shield tokens. This allows B2C to handle authentication while preserving all existing Safety Shield authorization, session management, and API patterns.

**Technical Notes:**
- Implement JWT validation for B2C tokens according to best practices
- Create mapping between B2C user identifiers and Safety Shield user accounts
- Generate standard Safety Shield tokens using existing token generation mechanisms
- Handle edge cases such as B2C authenticated users without matching Safety Shield accounts
- Maintain existing API authorization patterns - only the initial token exchange changes
- Consider caching strategies for B2C token validation to minimize performance impact
## Story 4: Integrate OIDC Authentication Flow in Safety Shield Web UI (SAF-3296)

**As a** Safety Shield user  
**I want to** authenticate using my organization's credentials via SSO  
**So that** I can access Safety Shield without maintaining separate credentials

**Background:**
Modify the Safety Shield React web UI to integrate the OpenID Connect authentication flow with Azure AD B2C. After successful B2C authentication, the UI will exchange the B2C token for a standard Safety Shield token and use that for all subsequent API calls, maintaining existing application behavior.

**Technical Notes:**
- Use React-specific OIDC/OAuth libraries (e.g., MSAL React or react-oidc-context)
- Implement authentication context provider pattern for React component tree
- Implement PKCE for enhanced security in authorization code flow
- After receiving B2C token, immediately exchange it for Safety Shield token via API
- Store Safety Shield tokens using existing token storage mechanisms
- Handle token refresh using existing Safety Shield token refresh patterns
- Maintain existing session management and logout behavior

## Story 5: Create Migration Tool for Local Users (SAF-3297)

**As a** Safety Shield administrator  
**I want to** migrate existing local user accounts to Azure AD B2C  
**So that** users can continue accessing the system with minimal disruption while using SSO authentication

**Background:**
Develop a migration tool or process to move existing Safety Shield local users to Azure AD B2C. Since the existing Safety Shield user records and permissions will remain unchanged, this migration focuses on creating corresponding B2C accounts and establishing the mapping between B2C identities and existing Safety Shield users.

**Technical Notes:**
- Use B2C Graph API or Microsoft Graph API for bulk user creation
- Create unique identifiers to link B2C accounts with existing Safety Shield user records
- Implement logging and reporting for the migration process
- Create users without passwords and force password reset on first login
- Maintain existing Safety Shield user permissions and roles unchanged

## Story 5B: Implement User Provisioning Integration (SAF-3324)

**As a** Safety Shield administrator  
**I want to** have Safety Shield user management automatically sync with Azure AD B2C  
**So that** I can maintain existing user management workflows while ensuring B2C stays synchronized

**Background:**
Integrate B2C user provisioning into existing Safety Shield user management workflows. When users are created, updated, or deactivated in Safety Shield, corresponding changes should be made in B2C automatically. Safety Shield user tables remain the source of truth.

**Technical Notes:**
- Use Microsoft Graph API for B2C user management operations
- Implement create, update, disable/enable, and delete operations
- Handle API failures gracefully with retry logic and error reporting
- Map Safety Shield user attributes to appropriate B2C user properties
- Consider eventual consistency - B2C operations may be asynchronous to SS operations
- Implement logging for all provisioning activities for audit and troubleshooting
- Handle edge cases like duplicate emails, invalid user data, B2C service outages

**Acceptance Criteria:**
- Creating a user in Safety Shield automatically creates corresponding B2C user
- Updating user email/status in Safety Shield syncs to B2C
- Disabling/enabling users in Safety Shield reflects in B2C account status
- Deleting users in Safety Shield properly handles B2C account cleanup
- Failed B2C operations are logged and can be retried
- Administrative interface shows B2C sync status for users
- Process gracefully handles B2C service outages without blocking SS operations

## Story 6: Integrate OIDC Authentication Flow in Safety Shield Mobile App (SAF-3298)

**As a** Safety Shield mobile app user  
**I want to** authenticate using my organization's credentials via SSO  
**So that** I can access the Safety Shield mobile app without maintaining separate credentials

**Background:**
Similar to the web UI integration, modify the Safety Shield mobile app to support OpenID Connect authentication flow with Azure AD B2C. After successful B2C authentication, the app will exchange the B2C token for a standard Safety Shield token and use that for all subsequent API calls, maintaining existing application behavior.

**Technical Notes:**
- Use platform-specific authentication libraries (e.g., MSAL for iOS/Android)
- After receiving B2C token, immediately exchange it for Safety Shield token via API
- Store Safety Shield tokens using existing secure token storage mechanisms
- Handle app backgrounding and token expiration using existing patterns
- Configure custom URL schemes for redirect URIs
- Maintain existing session management and logout behavior

## Story 10: Develop Documentation for Administrators (SAF-3302)

**As a** Safety Shield administrator  
**I want to** have comprehensive documentation on the SSO implementation  
**So that** I can configure, troubleshoot and manage the system effectively

**Background:**
Create detailed documentation for Safety Shield administrators covering the SSO implementation, including configuration steps, troubleshooting guides, and end-user instructions.

**Technical Notes:**
- Include screenshots and diagrams where appropriate
- Provide example configurations for common scenarios
- Document error messages and their meaning
- Include contact information for support

## Story 11: Implement Monitoring and Auditing (SAF-3303)

**As a** Security Administrator  
**I want to** have comprehensive monitoring and auditing in place for the SSO solution  
**So that** I can track usage, detect anomalies, and maintain compliance

**Background:**
Implement robust monitoring and auditing capabilities for the SSO implementation to track authentication events, failures, and administrative changes.

**Technical Notes:**
- Integrate with existing logging infrastructure
- Ensure PII is properly protected in logs
- Create dashboards for visualizing authentication metrics
- Set up alerts for suspicious activities
- Implement log rotation and retention policies to comply with regulatory requirements
- Track both successful and failed authentication attempts

## Quality Assurance Stories

## Story 12: Test Local User Authentication End-to-End (SAF-3333)

**As a** Safety Shield Quality Engineer  
**I want to** comprehensively test local user authentication through the entire B2C → Safety Shield token exchange flow  
**So that** I can verify that local user authentication works correctly across all applications and scenarios

**Background:**
Execute comprehensive testing of the Phase 1A local user authentication implementation, including user creation, authentication flows, token exchange, and authorization validation. This testing ensures that the B2C integration maintains all existing Safety Shield functionality while providing the new authentication capabilities.

**Technical Notes:**
- Test user creation and B2C account provisioning integration
- Verify B2C token → Safety Shield token exchange functionality
- Test authentication flows in both web UI and mobile applications
- Validate that all existing authorization mechanisms work identically
- Test edge cases: expired tokens, invalid tokens, network failures
- Verify session management and logout behavior
- Test password reset and account recovery flows
- Validate audit logging for authentication events

**Acceptance Criteria:**
- Local users can successfully authenticate via B2C in web and mobile applications
- B2C token exchange produces valid Safety Shield tokens with correct user mapping
- All existing role-based features work identically (admin panels, permissions, data access)
- Authentication failures are handled gracefully with appropriate error messages
- Session management (timeout, logout, token refresh) works as expected
- User provisioning integration creates and manages B2C accounts correctly
- Audit logs capture all authentication events accurately
- Performance meets or exceeds existing authentication system benchmarks

## Story 13: Test LDAP User Authentication and Integration (SAF-3334)

**As a** Safety Shield Quality Engineer  
**I want to** thoroughly test LDAP user authentication through Azure AD B2C custom policies  
**So that** I can ensure customers can successfully authenticate using their existing directory services

**Background:**
Execute comprehensive testing of the Phase 1B LDAP integration implementation, including custom policy functionality, LDAP connectivity, user mapping, and authorization preservation. This testing validates that customers can seamlessly use their existing LDAP/AD infrastructure with Safety Shield.

**Technical Notes:**
- Test B2C custom policies for LDAP authentication
- Verify LDAP connectivity and secure communication (LDAPS)
- Test user mapping between LDAP attributes and Safety Shield user records
- Validate authentication with different LDAP configurations and schemas
- Test organization-level identity provider configuration
- Verify that Safety Shield authorization works correctly for LDAP users
- Test network failure scenarios and LDAP server unavailability
- Validate user attribute synchronization and updates

**Acceptance Criteria:**
- LDAP users can successfully authenticate through B2C custom policies
- LDAP authentication works with various directory configurations (AD, OpenLDAP, etc.)
- User mapping correctly links LDAP identities to existing Safety Shield accounts
- Organization-level IdP configuration properly routes users to LDAP authentication
- All existing Safety Shield permissions and roles work for LDAP-authenticated users
- Network failures and LDAP outages are handled gracefully
- Custom policies handle edge cases (locked accounts, expired passwords, etc.)
- Authentication performance is acceptable for production use
- LDAP attribute changes properly sync to Safety Shield user records

## Story 14: Test User Migration Processes and Data Integrity (SAF-3335)

**As a** Safety Shield Quality Engineer  
**I want to** validate all user migration processes ensure data integrity and seamless user experience  
**So that** existing users can transition to SSO authentication without data loss or access disruption

**Background:**
Execute comprehensive testing of both local user and LDAP user migration processes, focusing on data integrity, user experience continuity, and rollback capabilities. This testing ensures that existing Safety Shield deployments can safely migrate to the new SSO system.

**Technical Notes:**
- Test local user migration tool functionality and data mapping
- Verify LDAP user migration and identity linking
- Test dual-authentication support during migration periods
- Validate user data preservation (permissions, roles, preferences, history)
- Test migration rollback and recovery procedures
- Verify user provisioning integration works post-migration
- Test edge cases: duplicate users, orphaned accounts, partial failures
- Validate migration reporting and logging accuracy

**Acceptance Criteria:**
- Migration tools successfully transfer users without data loss
- User permissions, roles, and settings are preserved exactly
- Users can authenticate immediately after migration completion
- Dual-authentication works correctly during transition periods
- Migration failures are detected and can be safely retried
- Rollback procedures successfully restore pre-migration state
- Migration reports accurately reflect success/failure status
- No user access disruption occurs during migration windows
- User provisioning integration functions correctly post-migration
- All existing user workflows and features work identically after migration