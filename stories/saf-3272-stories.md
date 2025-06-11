# SAF-3272: Safety Shield SSO Phase 2 Implementation Stories

This document elaborates on the user stories for implementing Phase 2 of the Safety Shield SSO solution, adding support for social identity providers and migrating to direct JWT authentication. These stories align with the epic described in [saf-3272.md](saf-3272.md).

## Architecture Overview

**Phase 2 Approach**: Building on Phase 1's foundation, Phase 2 adds social provider authentication (Google, Office 365) and migrates from B2C token → Safety Shield token exchange to direct B2C JWT validation. All authorization (roles, permissions, custom organization settings) remains in Safety Shield - B2C only handles authentication.

**Key Principles**:
- Social provider accounts must be linked to pre-provisioned Safety Shield users
- No self-registration - admin-driven provisioning model continues
- Authorization logic stays entirely within Safety Shield
- Direct JWT authentication improves performance by eliminating token exchange

## Social Provider Authentication (First Priority)

## Story 1 (SAF-3325): Implement Google and Office 365 Social Provider User Flows in Azure AD B2C

**As a** Safety Shield system engineer  
**I want to** configure user flows and custom policies in Azure AD B2C for Google and Office 365 social providers  
**So that** users can authenticate using their existing social accounts

**Background:**
Expand Azure AD B2C configuration to support Google and Office 365 as social identity providers. This includes setting up and testing user flows, configuring claims, and ensuring seamless integration with Safety Shield. Note: Users must still be pre-provisioned by administrators - social providers only handle authentication.

**Technical Notes:**
- Use Azure AD B2C custom policies for social IdPs
- Configure and test redirect URIs for each social provider
- Ensure claims mapping is consistent across providers
- Validate token issuance and claims for all social IdPs
- Link social provider accounts to existing pre-provisioned Safety Shield users
- **Authorization**: All role/permission validation continues to happen in Safety Shield backend

**Acceptance Criteria:**
- Users can authenticate with Google or Office 365 accounts
- Social accounts are properly linked to pre-provisioned Safety Shield users
- Claims are mapped and available to Safety Shield backend
- User flows are tested for all supported social providers
- **Safety Shield authorization remains unchanged** - roles and permissions work identically

## Story 2 (SAF-3326): Add Social Provider Account Linking Capabilities

**As a** Safety Shield user  
**I want to** link my pre-provisioned Safety Shield account with my social provider account  
**So that** I can authenticate using my preferred social identity

**Background:**
Allow pre-provisioned Safety Shield users to link their accounts with social providers. This maintains the admin-driven provisioning model while enabling social authentication.

**Technical Notes:**
- Create account linking workflow in Safety Shield UI
- Store social provider mappings in the database
- Handle account linking verification and security
- Support unlinking and re-linking social accounts
- **Critical**: Maintain user-role associations during social account linking

**Acceptance Criteria:**
- Pre-provisioned users can link their accounts to Google or Office 365
- Account linking is secure and properly verified
- Users can unlink and re-link social accounts
- Admins can view social provider linkages for users
- **User roles and permissions are preserved** during account linking process

## Story 3 (SAF-3327): Enable Admin Configuration of Organization-Level Social Provider Options

**As a** Safety Shield administrator  
**I want to** configure which social provider options are available for my organization  
**So that** I can control authentication methods according to organizational policies

**Background:**
Provide admin UI and backend support for configuring which social providers (Google, Office 365) are available for users in an organization. This allows organizations to control authentication methods while maintaining user choice within approved options.

**Technical Notes:**
- Add admin interface for managing organization-level social provider settings
- Implement configuration storage and validation
- Update authentication flows to respect organization settings
- Ensure changes are auditable

**Acceptance Criteria:**
- Admins can enable/disable social providers for their organization
- Users only see approved social provider options during authentication
- Configuration changes are effective immediately
- All changes are logged for audit purposes

## JWT Migration (Second Priority)

## Story 4 (SAF-3328): Migrate API Modules from Token Exchange to Direct B2C JWT Validation

**As a** Safety Shield developer  
**I want to** update API modules to validate B2C JWTs directly instead of using token exchange  
**So that** authentication is more efficient and standards-based

**Background:**
Refactor backend API modules to accept and validate JWTs issued by Azure AD B2C directly, eliminating the B2C token → Safety Shield token exchange layer implemented in Phase 1. This reduces complexity and improves performance.

**Technical Notes:**
- Implement direct B2C JWT validation using best practices
- Map B2C claims to Safety Shield user model  
- Remove token exchange endpoints and logic
- Ensure backward compatibility during migration
- **Authorization**: Extract user identity from JWT claims, then apply Safety Shield role/permission logic
- Maintain existing Safety Shield authorization mechanisms unchanged

**Acceptance Criteria:**
- API modules accept and validate B2C JWTs directly
- Token exchange layer is removed
- Claims are mapped and used for **authentication only** - authorization stays in Safety Shield
- Performance improvement is measurable
- **All existing role/permission functionality works identically**

## Story 5 (SAF-3329): Remove B2C → Safety Shield Token Exchange Infrastructure

**As a** Safety Shield developer  
**I want to** eliminate all B2C token exchange logic from Phase 1  
**So that** the system uses direct JWT authentication without the exchange layer

**Background:**
Identify and remove all code related to the B2C → Safety Shield token exchange mechanism implemented in Phase 1. This includes exchange endpoints, token generation, and related validation logic.

**Technical Notes:**
- Audit codebase for token exchange logic
- Remove exchange endpoints and related infrastructure
- Update API documentation
- **Preserve all Safety Shield authorization logic** - only remove authentication token exchange

**Acceptance Criteria:**
- B2C token exchange infrastructure is completely removed
- All authentication uses direct B2C JWTs
- Documentation reflects new direct JWT approach
- Migration from exchange to direct JWT is seamless
- **Safety Shield authorization (roles, permissions, custom org settings) remains fully functional**

## Story 6 (SAF-3330): Update Web UI to Use B2C JWTs Directly

**As a** Safety Shield web user  
**I want to** authenticate using B2C JWTs directly without token exchange  
**So that** I have faster and more efficient authentication

**Background:**
Update the Safety Shield React web UI to use B2C JWTs directly instead of exchanging them for Safety Shield tokens. This eliminates the token exchange step and improves performance.

**Technical Notes:**
- Remove token exchange calls from web UI
- Update authentication context to use B2C JWTs directly
- Modify API calls to send B2C JWTs in authorization headers
- Handle JWT expiration and refresh using B2C mechanisms
- **Ensure role-based UI elements continue to work** with new authentication approach

**Acceptance Criteria:**
- Web UI uses B2C JWTs directly for API calls
- Token exchange logic is removed from web UI
- Authentication performance is improved
- JWT refresh mechanism works properly
- **All role-based features and permissions work identically** (admin panels, user restrictions, etc.)

## Story 7 (SAF-3331): Update Mobile App to Use B2C JWTs Directly and Support Social Providers

**As a** Safety Shield mobile app user  
**I want to** authenticate using social providers with direct JWT authentication  
**So that** I have a seamless and efficient SSO experience on mobile

**Background:**
Update the mobile app to support authentication via Google and Office 365 social providers, and use B2C JWTs directly instead of the token exchange mechanism from Phase 1.

**Technical Notes:**
- Add social provider authentication flows to mobile app
- Remove token exchange calls from mobile app
- Use secure token storage for B2C JWTs on device
- Support deep linking/redirects for social providers
- Handle JWT refresh and expiration using B2C mechanisms
- **Ensure mobile app respects all Safety Shield user roles and permissions**

**Acceptance Criteria:**
- Mobile app supports Google and Office 365 authentication
- App uses B2C JWTs directly for API calls
- Social provider account linking works on mobile
- JWTs are securely managed on device
- **All role-based mobile features work identically** (feature access, data visibility, etc.)

## Story 8 (SAF-3332): Documentation and Migration Support

**As a** Safety Shield administrator  
**I want to** have clear documentation and migration tools for Phase 2  
**So that** I can support the transition from token exchange to direct JWT authentication and enable social providers

**Background:**
Provide comprehensive documentation and migration scripts/tools to help admins transition from the Phase 1 token exchange model to direct JWT authentication while adding social provider support.

**Technical Notes:**
- Write admin guides for social provider configuration
- Document JWT migration process and considerations
- Develop migration scripts for transitioning from token exchange
- Provide troubleshooting and rollback procedures
- Include performance comparison documentation
- **Document authorization approach**: Emphasize that Safety Shield handles all authorization

**Acceptance Criteria:**
- Documentation covers social provider setup and JWT migration
- Migration tools are available and tested
- Support resources help admins manage the transition
- Performance improvements are documented
- **Authorization documentation clearly explains** that roles/permissions remain in Safety Shield

## Risks & Supporting Tasks

- Risk: Integration with multiple IdPs may introduce unforeseen complexity
- Risk: Removing legacy token logic could impact existing integrations
- Risk: **Authorization logic disruption** - Ensure Safety Shield role/permission system remains intact during JWT migration
- Supporting Task: Audit and test all authentication flows for edge cases and regressions
- Supporting Task: Update monitoring and logging for new authentication events
- Supporting Task: **Validate authorization continuity** - Test all role-based features with new JWT authentication
