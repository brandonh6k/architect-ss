# Phase 1: Safety Shield SSO Implementation
> Epic SAF-3271: An implementation of Single Sign-On (SSO) capabilities for Safety Shield using Azure AD B2C to authenticate users via customer AD/LDAP servers

## Business Context
Currently, Safety Shield authenticates users against a customer's AD/LDAP server through a direct LDAP-sync process. This approach requires maintenance of separate authentication mechanisms and doesn't provide the security benefits and user experience of modern SSO solutions. There is an opportunity to streamline authentication by implementing Azure AD B2C to handle authentication against customers' identity providers.

## Epic Goal
Successfully implement Phase 1 of SSO integration using Azure AD B2C to authenticate users against customer AD/LDAP servers, eliminating the need for the current LDAP-sync process while maintaining all existing user authorization capabilities.

**Phase 1 Architecture**: B2C will serve as the authentication mechanism only. After successful B2C authentication, the Safety Shield API will validate the B2C token and generate a standard Safety Shield token. The remainder of the application will continue to use existing Safety Shield token mechanisms unchanged.

## User Impact
- Primary users: Safety Shield end users and administrators
- Current experience: Users authenticate directly against their organization's AD/LDAP through a custom implementation that requires synchronization and maintenance
- Desired experience: Users authenticate seamlessly through Azure AD B2C using their organizational credentials, with simplified administration and enhanced security features

## Scope
### Key Features & Capabilities
**Priority 1 - Local User Authentication:**
1. Azure AD B2C application registration for Safety Shield
2. User flows for local Safety Shield users (sign-up, sign-in, password reset, sign-out)
3. API integration layer for B2C token validation and SS token generation
4. OIDC integration with Safety Shield web UI
5. OIDC integration with Safety Shield mobile app
6. User provisioning integration (SS user management → B2C sync)
7. Migration tools for existing local users

**Priority 2 - LDAP Integration:**
8. Custom policies for customer AD/LDAP authentication
9. Organization-level identity provider configuration
10. Migration path for existing LDAP users

### Out of Scope
- Integration with other identity providers beyond customer AD/LDAP servers
- Advanced SSO features (conditional access, MFA, etc.) which will be addressed in future phases
- Changes to existing Safety Shield authorization mechanisms (roles, permissions, etc.)
- Full bidirectional synchronization of user profile changes from B2C back to Safety Shield

## Success Metrics
- Metric 1: 100% of test users can successfully authenticate via Azure AD B2C → Target: 100% success rate

## Dependencies & Constraints
- Systems impacted: Safety Shield web UI, mobile app, authentication services
- External dependencies: Azure AD B2C service, customer AD/LDAP servers
- Technical constraints: Must maintain compatibility with existing API token authorization
- Business constraints: Minimal disruption to existing users during transition

## Initial Story / Task Breakdown
**Phase 1A - Local User Foundation:**
1. Set up Azure AD B2C application registration for Safety Shield (B2C tenant already exists)
2. Create user flows for local Safety Shield users
3. Implement API integration layer for B2C token validation and SS token generation
4. Implement user provisioning integration (SS → B2C sync)
5. Integrate OIDC authentication flow in Safety Shield web UI
6. Create migration tools for existing local users
7. Integrate OIDC authentication flow in Safety Shield mobile app

**Phase 1B - LDAP Integration:**
8. Develop and test custom policies for AD/LDAP integration
9. Implement organization-level identity provider preference configuration
10. Create migration path for existing LDAP users
11. Develop documentation for administrators

## Risks & Assumptions
- Risk: Customer AD/LDAP configurations may vary significantly requiring additional customization
- Risk: Integration with mobile app may require additional security considerations
- Assumption: Azure AD B2C can be configured to authenticate against various AD/LDAP implementations
- Assumption: Current API authorization model can remain unchanged

## Additional Information
- Related epics: 
  - SAF-717 (Ability to authenticate users with Single Sign-On)
  - SAF-3272 (Phase 2)
  - SAF-3273 (Phase 3)
- Helpful resources: Azure AD B2C documentation, OIDC standards
- Background documents: Current authentication flow documentation (pending)

## Comments from Jira
- Note: Need to determine who will own the custom policy piece. Brandon to check with Ken Hobbs. (Anna Elder, 2025-05-19)

## Metadata
- **Status**: New
- **Priority**: Priority 4
- **Reporter**: Anna Elder
- **Labels**: Elaboration, PI2025.3
- **Fix Version**: 6.0
- **Created**: 2025-05-02
- **Updated**: 2025-05-19