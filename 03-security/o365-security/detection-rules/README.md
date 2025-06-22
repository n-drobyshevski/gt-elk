# O365 Security Detection Rules

## Overview

This directory contains detection rules specifically designed for monitoring Microsoft Office 365 environments. These rules focus on cloud-based collaboration tools, email security, and Office 365 administrative activities.

## Rule Categories

### Email Security
- Phishing detection rules
- Malicious attachment analysis
- Email exfiltration patterns
- Spam and abuse detection

### SharePoint Security
- Document access anomalies
- Permission elevation detection
- External sharing violations
- Data loss prevention

### Teams Security
- Unauthorized external access
- File sharing violations
- Meeting security breaches
- Guest user activities

### Administrative Security
- Admin privilege changes
- Configuration modifications
- Service disruptions
- Compliance violations

## File Organization

```
detection-rules/
├── email/            # Email security detection rules
├── sharepoint/       # SharePoint-specific rules
├── teams/            # Microsoft Teams security rules
├── admin/            # Administrative activity rules
└── compliance/       # O365 compliance rules
```

## Rule Development Guidelines

### O365-Specific Context
- Rules should leverage Office 365 audit logs
- Consider multi-tenant environment implications
- Account for legitimate business activities
- Focus on cloud-native security patterns

### Data Sources
- Office 365 Audit Logs
- Exchange Online Protection logs
- Azure Active Directory logs
- Microsoft Defender for Office 365
- SharePoint audit events
- Teams activity logs

### Thresholds and Tuning
- Rules should account for global user base
- Consider time zone differences for global orgs
- Account for legitimate admin activities
- Minimize false positives from automated processes

## Integration with Dashboards

These detection rules integrate with dashboards located in:
- `../dashboards/audit/` - O365 audit monitoring
- `../dashboards/email/` - Email security analysis
- `../dashboards/sharepoint/` - SharePoint activity monitoring
- `../dashboards/teams/` - Teams security oversight

## Related Documentation

- [Platform-Centric Detection Rules](../PLATFORM_CENTRIC_DETECTION_RULES.md)
- [O365 Security Overview](../README.md)
- [O365 Dashboards Documentation](../dashboards/O365_DASHBOARDS.md)
