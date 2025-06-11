# Automated Beacon Import System Design
> Technical design document for the automated beacon import system using pre-allocated CoRe Serials

## System Overview
The Automated Beacon Import System will handle the registration of third-party BLE beacons (such as Cisco APs) using pre-allocated CoRe Serials from VOS Systems. This system replaces the manual back-and-forth process with an automated workflow.

## Architecture Components

### 1. Pre-allocated Serial Pool
- **Storage**: Database tables for managing pre-allocated serials
- **Components**:
  - Serial Pool Manager
  - Allocation Status Tracker
  - Low Pool Alert System

### 2. Beacon Import API
- **Endpoint**: `/api/v1/beacons/import`
- **Method**: POST
- **Content-Type**: multipart/form-data
- **Input Format**: CSV file with required columns:
  - MAC Address (required)
  - Description (required)
  - Location Name (optional)
  - Floor/Level (optional)
  - Building (optional)
  - Installation Date (optional)

### 3. Validation Service
- **MAC Address Validation**:
  - Format verification
  - Duplicate detection
  - Existing registration check
- **Location Data Validation**:
  - Required field presence
  - Format verification
  - Geolocation validation (TBD)

### 4. Serial Allocation Service
- **Functionality**:
  - Retrieve available serial from pre-allocated pool
  - Map serial to Major/Minor values for iBeacon
  - Track serial assignments
  - Handle pool depletion scenarios

### 5. Beacon Registration Service
- **Functions**:
  - Create beacon records
  - Generate iBeacon configuration
  - Track registration status
  - Handle partial failures

## Data Model

### PreAllocatedSerials
```sql
CREATE TABLE PreAllocatedSerials (
    serial_id VARCHAR(16) PRIMARY KEY,
    allocation_date TIMESTAMP,
    status ENUM('available', 'assigned', 'reserved'),
    assigned_date TIMESTAMP NULL,
    assigned_to_mac VARCHAR(17) NULL
);
```

### CustomerBeacons
```sql
CREATE TABLE CustomerBeacons (
    beacon_id UUID PRIMARY KEY,
    mac_address VARCHAR(17) UNIQUE,
    core_serial VARCHAR(16),
    description TEXT,
    location_name VARCHAR(255),
    floor_level VARCHAR(50),
    building VARCHAR(255),
    installation_date DATE,
    created_at TIMESTAMP,
    updated_at TIMESTAMP,
    FOREIGN KEY (core_serial) REFERENCES PreAllocatedSerials(serial_id)
);
```

### BeaconRegistrationStatus
```sql
CREATE TABLE BeaconRegistrationStatus (
    beacon_id UUID PRIMARY KEY,
    status ENUM('pending', 'configured', 'active', 'error'),
    last_check_date TIMESTAMP,
    error_message TEXT NULL,
    FOREIGN KEY (beacon_id) REFERENCES CustomerBeacons(beacon_id)
);
```

## iBeacon Configuration

### Standard Configuration
- **UUID**: 10770633-0304-2018-1087-410874108741 (Location beacon type)
- **Major/Minor**: Derived from CoRe Serial
  - Example for CoRe Serial "7A710001":
    - Major (hex): 0x7A71 (decimal: 31345)
    - Minor (hex): 0x0001 (decimal: 1)
- **Advertisement Parameters**:
  - Tx Power: 0 to +4dBm
  - Advertisement interval: 1s preferred

## API Contract

### Import Beacons
```typescript
POST /api/v1/beacons/import
Content-Type: multipart/form-data

Request:
{
    file: File (CSV),
    customerId: string,
    projectId?: string
}

Response:
{
    status: 'success' | 'partial' | 'error',
    totalRecords: number,
    successfulImports: number,
    failedImports: number,
    errors: Array<{
        row: number,
        message: string,
        macAddress?: string
    }>,
    importId: string // For status checking
}
```

### Check Import Status
```typescript
GET /api/v1/beacons/import/{importId}

Response:
{
    status: 'processing' | 'completed' | 'failed',
    progress: number, // 0-100
    completedRecords: number,
    totalRecords: number,
    errors: Array<{
        row: number,
        message: string,
        macAddress?: string
    }>
}
```

## Error Handling

### Validation Errors
- Invalid MAC address format
- Duplicate MAC addresses
- Missing required fields
- Invalid location data

### System Errors
- Pre-allocated serial pool depletion
- Database connectivity issues
- File processing errors
- Third-party system integration failures

## Monitoring and Alerts

### Metrics to Track
- Serial pool utilization
- Import success/failure rates
- Processing times
- Error rates by type

### Alert Conditions
- Serial pool below 20% available
- Import failure rate exceeds 10%
- Processing time exceeds 5 minutes
- System errors

## Security Considerations

### Authentication & Authorization
- API endpoint protected by OAuth 2.0
- Role-based access control for imports
- Customer isolation

### Data Protection
- MAC address encryption at rest
- Audit logging of all operations
- Data retention policies

## TODO Items
- [ ] Define geolocation validation requirements
- [ ] Specify batch size limits for imports
- [ ] Define retry policies for failed operations
- [ ] Determine data retention period
- [ ] Define SLAs for import processing
- [ ] Specify monitoring dashboard requirements