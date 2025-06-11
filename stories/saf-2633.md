# Third-Party BLE Beacon Support Automation
> Streamline and automate the process of registering customer BLE beacons with SafetyShield using pre-allocated CoRe Serials

## Business Context
Currently, the process of integrating customer BLE beacons (such as Cisco APs) requires back-and-forth coordination between VOS and Intrado for serial allocation. To improve efficiency, VOS will pre-allocate blocks of CoRe Serials that can be used during the beacon registration process. This eliminates the need for real-time coordination during customer deployments.

## Epic Goal
Implement a two-part workflow where VOS pre-allocates CoRe Serials for bulk beacon registration, and create an automated system for registering customer BLE beacons using these pre-allocated serials. This will significantly reduce implementation time and eliminate the need for back-and-forth communication during the registration process.

## User Impact
- Primary users: SRE team (beacon provisioning) and Implementation team (customer onboarding)
- Current experience: Manual process requiring CSV file exchanges and real-time coordination with VOS
- Desired experience: Automated system using pre-allocated serials for immediate beacon registration

## Scope
### Key Features & Capabilities
- Bulk CoRe Serial pre-allocation system
- Storage and management of pre-allocated serials
- Automated import system for customer BLE MAC addresses
- API endpoints for beacon registration and management
- Validation system for iBeacon payload configuration
- Documentation and guides for customer AP configuration

### Out of Scope
- Changes to VOS firmware or existing beacon detection logic
- Support for non-iBeacon formats
- Custom beacon registration processes outside the standard flow
- Direct integration with specific vendor AP management systems

## Success Metrics
- Metric 1: Time to register beacons: Currently undefined → Target < 1 day from MAC address receipt to completion
- Metric 2: Registration success rate: Currently undefined → Target 95% or better first-time success rate

## Dependencies & Constraints
- Systems impacted: SafetyShield API, VOS Bulk Allocation Service
- External dependencies: 
  - VOS ability to pre-allocate blocks of CoRe Serials
  - Customer ability to configure APs with specific iBeacon advertisement using the existing VOS UUID
- Technical constraints:
  - Must maintain compatibility with current VOS firmware
  - Must use existing iBeacon format / UUID for beacon detection
  - Must support iBeacon payload requirements (Tx Power: 0 to +4dBm, Advertisement interval: 1s preferred)
- Business constraints:
  - Must support existing customer beacon deployment processes
  - VOS Systems remains source of truth for CoRe Serial allocation

## Initial Story Breakdown
1. Implement VOS Bulk Serial Pre-allocation System
   - Design and implement serial block allocation process
   - Create storage system for pre-allocated serials
2. Design and implement automated beacon import system
   - MAC address validation
   - Serial assignment from pre-allocated pool
3. Create API endpoints for beacon registration
4. [Optional] Implement validation system for customer-supplied beacon configurations
   - UUID format validation
   - iBeacon payload parameter validation
5. Create customer-facing documentation for AP configuration
   - iBeacon configuration guides
   - Troubleshooting guides
6. Develop testing and validation procedures
   - End-to-end testing plan
   - Customer acceptance criteria

## Risks & Assumptions
- Assumes VOS can implement bulk serial pre-allocation system
- Risk of exhausting pre-allocated serial pool during high-volume deployments
- Assumes customers can configure their APs to match required iBeacon format
- Risk of incomplete or incorrect MAC address information from customers
- Assumes current VOS firmware limitations remain unchanged

---
Size Estimate: L
Priority: High
