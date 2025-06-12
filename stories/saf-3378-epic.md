# Phase 1.5: Safety Shield SSO - LDAP Integration
> Epic SAF-3378: LDAP integration for Safety Shield SSO using Azure AD B2C custom policies and organization-level identity provider configuration

## Business Context
Currently, Safety Shield authenticates users against a customer's AD/LDAP server through a direct LDAP-sync process. This approach requires maintenance of separate authentication mechanisms and doesn't provide the security benefits and user experience of modern SSO solutions. There is an opportunity to streamline authentication by implementing Azure AD B2C to handle authentication against customers' identity providers.

## Epic Goal
Implement Phase 1.5 of SSO integration by enabling Azure AD B2C to authenticate users against customer AD/LDAP servers using custom policies and organization-level identity provider configuration. This phase will focus on LDAP integration, building on the local user foundation established in Phase 1.

## User Impact
- Primary users: Safety Shield end users and administrators
- Current experience: Users authenticate directly against their organization's AD/LDAP through a custom implementation that requires synchronization and maintenance
- Desired experience: Users authenticate seamlessly through Azure AD B2C using their organizational credentials, with simplified administration and enhanced security features

## Scope
### Key Features & Capabilities
**Priority - LDAP Integration:**
1. Custom policies for customer AD/LDAP authentication
2. Organization-level identity provider configuration
3. Migration path for existing LDAP users
4. Develop documentation for administrators

### Out of Scope
- Integration with other identity providers beyond customer AD/LDAP servers
- Advanced SSO features (conditional access, MFA, etc.) which will be addressed in future phases
- Changes to existing Safety Shield authorization mechanisms (roles, permissions, etc.)
- Full bidirectional synchronization of user profile changes from B2C back to Safety Shield

## Success Metrics
- Metric 1: 100% of test users can successfully authenticate via Azure AD B2C with LDAP integration → Target: 100% success rate

## Dependencies & Constraints
- Systems impacted: Safety Shield web UI, mobile app, authentication services
- External dependencies: Azure AD B2C service, customer AD/LDAP servers
- Technical constraints: Must maintain compatibility with existing API token authorization
- Business constraints: Minimal disruption to existing users during transition

## Initial Story / Task Breakdown
**Phase 1.5 - LDAP Integration:**
1. Develop and test custom policies for AD/LDAP integration
2. Implement organization-level identity provider preference configuration
3. Create migration path for existing LDAP users
4. Develop documentation for administrators

## Risks & Assumptions
- Risk: Customer AD/LDAP configurations may vary significantly requiring additional customization
- Risk: Integration with mobile app may require additional security considerations
- Assumption: Azure AD B2C can be configured to authenticate against various AD/LDAP implementations
- Assumption: Current API authorization model can remain unchanged

## Additional Information
- Related epics: 
  - SAF-3271 (Phase 1)
  - SAF-3272 (Phase 2)
  - SAF-3273 (Phase 3)
- Helpful resources: Azure AD B2C documentation, OIDC standards
- Background documents: Current authentication flow documentation (pending)

## Metadata
- **Status**: New
- **Priority**: Priority 4
- **Reporter**: Brandon Hunt
- **Labels**: PI2025.3
- **Fix Version**: 6.1
- **Created**: 2025-06-12
- **Jira Epic**: SAF-3378
