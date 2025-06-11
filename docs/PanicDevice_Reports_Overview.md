# PanicDevice Report Helper Apps Overview

## Introduction

This document provides an overview of the three PanicDevice Report Helper Apps in the SafetyShieldHelperApps system. These applications monitor and report on the status of panic devices (wearables) deployed within organizations that have the "feature-wearables" capability enabled.

## Common Characteristics

All three PanicDevice report applications share several common characteristics:

1. **Implementation**: PHP scripts that are triggered by wrapper Bash scripts
2. **Data source**: API calls to retrieve data about panic devices, organizations, and users 
3. **Authentication**: Use access tokens for API authentication
4. **Output**: Email notifications with report content
5. **Filtering**: Process only organizations with the "feature-wearables" feature enabled
6. **Organization structure**: Handle parent-child organization hierarchies
7. **Security roles**: Send reports only to users with specific security roles

## 1. PanicDeviceLowBatteryReport

### Purpose
Monitors panic devices with low battery levels and sends email reports to designated users.

### Triggering
Scheduled execution via cron job with the report being triggered based on the INI configuration file.

### Key Features
- Retrieves all organizations with the "feature-wearables" feature enabled
- Obtains settings for battery level thresholds (low/critical) from organization configurations
- Checks battery levels for all allocated panic devices
- Identifies devices with battery levels below thresholds
- Formats report content with user information, device types, and battery levels
- Sends email reports to users with the "right-notif-pd-lowbat-report" security right
- Can be configured to skip sending blank reports

### Output
Plain text email with a listing of devices that have low batteries, including:
- User information (name, email)
- Device type
- Scan code
- Battery level label (low or critical)
- Battery percentage

## 2. PanicDeviceNeedsAttentionReport

### Purpose
Provides a more comprehensive report on panic devices that require attention, including low battery, lost contact, and firmware issues.

### Triggering
Scheduled execution via cron job with the report being triggered based on the INI configuration file.

### Key Features
- Checks for multiple issues:
  - Low battery levels
  - No recent contact/communication
  - Outdated firmware versions
- Creates XLSX spreadsheet reports instead of plain text
- Uses PHPExcel to generate formatted spreadsheets
- Uploads the report as an asset and provides a download link
- Sends reports based on organization hierarchies with more complex user-organization mappings
- Sorts data by battery percentage (ascending) for prioritization

### Output
Excel spreadsheet with multiple columns including:
- User information (name, email)
- Device status (assigned/inventory)
- Device type and scan code
- Battery level information
- Last contact time and duration
- Firmware version information
- Organization information

## 3. PanicDeviceNoContactReport

### Purpose
Focuses specifically on panic devices that have not communicated/reported within a configured timeframe.

### Triggering
Scheduled execution via cron job with the report being triggered based on the INI configuration file.

### Key Features
- Checks only for communication/contact issues
- Uses configuration for "warnAfterUnseenForSecs" to determine which devices to report
- Can be configured to only report on assigned devices (via warnAfterOnlyAssigned setting)
- Formats duration in a human-readable format (days, hours, minutes, seconds)
- Sorts by duration (descending) to highlight the most concerning cases first
- Sends email reports to users with the "right-notif-pd-nocomm-report" security right

### Output
Plain text email with a listing of devices that have not communicated recently, including:
- User information (name, email)
- Device type
- Scan code
- Last detected date/time
- Duration since last communication

## Technical Implementation

All three reports follow similar workflows:
1. Initialize and obtain an API access token
2. Get organizations with "feature-wearables" enabled
3. Get configuration settings from parent organizations (thresholds, warning levels)
4. Retrieve allocated panic devices for each organization
5. Check device statuses against configured thresholds
6. Build report content by organization
7. Identify users with appropriate rights to receive reports
8. Format and send reports via email

The `PanicDeviceNeedsAttentionReport` is the most comprehensive and uses the ReportEngine's `reportCommon.php` functionality to generate Excel spreadsheets.