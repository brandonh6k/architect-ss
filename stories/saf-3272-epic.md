# Phase 2: Safety Shield SSO Implementation cont.
> Expand SSO to support multiple identity providers and modernize token handling for Safety Shield

## Business Context
Phase 1 enabled SSO via Azure AD B2C for Safety Shield local users and customer AD/LDAP integration, using a B2C token → Safety Shield token exchange mechanism. However, many organizations require support for additional social identity providers (Google, Office 365). The current token exchange mechanism, while functional, adds unnecessary complexity and latency. This phase addresses these needs by adding social provider support and migrating to direct JWT authentication while maintaining Safety Shield's existing role/permission system for authorization.

## Epic Goal
Enable users to authenticate using social identity providers (Google, Office 365) for Safety Shield authentication, and migrate from the B2C token exchange pattern to direct JWT-based authentication. **Critical**: All authorization (roles, permissions, custom organization settings) remains entirely within Safety Shield - B2C and social providers handle authentication only.

## User Impact
- Primary users: Safety Shield end users and administrators
- Current experience: Users authenticate via Safety Shield local users or their organization's AD/LDAP through B2C with token exchange; limited to organizational identity providers
- Desired experience: Users can authenticate using social providers (Google, Office 365) and benefit from direct JWT-based authentication without token exchange overhead

## Scope
### Key Features & Capabilities
1. Add user flows/custom policies in Azure AD B2C for Google and Office 365 social providers
2. Allow users to link their Safety Shield accounts with social identity providers
3. Enable Safety Shield administrators to configure social provider options at the organization level
4. Migrate API modules from B2C token validation + SS token exchange to direct B2C JWT validation
5. Update web and mobile apps to use B2C JWTs directly instead of exchanging for SS tokens
6. Remove the B2C → Safety Shield token exchange layer
7. Incorporate social provider authentication options in mobile and web applications
8. **Maintain existing Safety Shield authorization system** - roles, permissions, custom org settings stay in Safety Shield

### Out of Scope
- Self-registration or B2C local accounts (Safety Shield requires admin provisioning)
- Support for social providers beyond Google and Office 365
- Changes to user provisioning/management workflows (remains admin-driven)
- **Changes to Safety Shield authorization mechanisms** (roles, permissions, custom org settings)
- Advanced SSO features (conditional access, MFA beyond B2C defaults)

## Success Metrics
- Metric 1: 100% of test users can authenticate using social providers → Target: 100% success rate
- Metric 2: B2C token exchange layer fully removed from codebase → Target: 0 token exchange code
- Metric 3: Direct JWT validation performance improvement → Target: 20% reduction in authentication latency

## Dependencies & Constraints
- Systems impacted: Safety Shield web UI, mobile app, authentication services, API modules
- External dependencies: Azure AD B2C, Google, Office 365
- Technical constraints: Must maintain compatibility with existing user/authorization model
- Business constraints: Minimal disruption to users during migration

## Initial Story / Task Breakdown
1. Implement Google and Office 365 social provider user flows in Azure AD B2C
2. Add social provider account linking capabilities in Safety Shield
3. Enable admin configuration of organization-level social provider options
4. Migrate API modules from token exchange to direct B2C JWT validation
5. Update web UI to use B2C JWTs directly instead of token exchange
6. Update mobile app to use B2C JWTs directly instead of token exchange
7. Remove B2C → Safety Shield token exchange infrastructure
8. Documentation and migration support

## Risks & Assumptions
- Risk: Social provider integrations may introduce unforeseen complexity or rate limiting
- Risk: Removing token exchange layer could impact existing integrations during migration
- Risk: B2C JWT size may be larger than current SS tokens, affecting performance
- Risk: **Authorization disruption** - JWT migration must not break existing role/permission functionality
- Assumption: Azure AD B2C supports required Google and Office 365 integrations
- Assumption: Direct JWT validation provides adequate security and performance
- Assumption: User linking between SS accounts and social providers can be managed effectively
- Assumption: **Safety Shield authorization logic can seamlessly work with JWT claims** instead of SS token claims

## Additional Information
- Related epics: SAF-717 (SSO initiative), SAF-3271 (Phase 1), SAF-3273 (Phase 3)
- Helpful resources: Azure AD B2C documentation, OAuth2/JWT standards
- Background documents: Current authentication flow documentation, Phase 1 implementation notes
