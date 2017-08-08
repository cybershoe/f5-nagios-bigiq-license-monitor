# f5-nagios-bigiq-license-monitor
Nagios plugin to monitor time remaining on a BIG-IQ evaluation license

This plugin works almost identically to the BIG-IP version, with the exception of the addition of an optional "-l" parameter. Because BIG-IQ/IWF allows you to select an authentication provider, you'll need a loginReference to use a service account stored in AD. You can get your loginReference by first getting an authentication token as a local administrative user, then cURLing the REST endpoint at https://<Management IP>/mgmt/cm/system/authn/providers/ldap/. It will look something like "https://localhost/mgmt/cm/system/authn/providers/ldap/1a8c2902-d940-4bca-a4b6-c403d62eceed/login".

## Command definition:
```
# 'check_bigiq-license' command definition
define command{
        command_name    check_bigiq-license
        command_line    $USER1$/check_bigiq-license.sh -H $HOSTADDRESS$ $ARG1$
        }
```

## Service definition:
```
# Monitor license expiry via iControl
 
define service{
        use                    generic-service ; Inherit values from a template
        hostgroup_name          bigiq-hostgroup
        service_description    license
        check_command          check_bigiq-license!-u service_account -p "password_here" -l "loginReference here"
        normal_check_interval  1440
        notification_interval  1440
        retry_check_interval    60
        }
```

## Script usage:
```
Usage:
 
 
check_bigiq-license.sh <options>
 
 
Options:
 
 
-H <host>
Mandatory. Specifies the hostname or IP address to check.
 
 
-u <usermame>
Mandatory. Specifies the iControl user account username.
 
 
-p <password>
Mandatory. Specifies the iControl user account password.
 
 
-l <loginReference>
Optional. Specifies the loginReference for non-local user authentication
in URI format.
 
 
-w <warn_threshold>
Optional. Specifies the warning threshold in days. If not specified, the
value of DEFAULT_WARN_THRESHOLD is used instead.
 
 
-c <crit_threshold>
Optional. Specifies the critical threshold in days. If not specified, the
value of DEFAULT_CRIT_THRESHOLD is used instead.
```
