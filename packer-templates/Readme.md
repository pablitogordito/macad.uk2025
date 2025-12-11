## Usage reminder ￼

Create a variables file for this template before running builds. Packer looks for values in a .pkrvars.hcl file, which keeps sensitive data out of version control.

```
# -------------------------
# Packer Variables File
# -------------------------

This file contains variable values for the apple-tart-tahoe.pkr.hcl template
Usage: packer build -var-file="my-config.pkrvars.hcl" apple-tart-tahoe.pkr.hcl

# VM Configuration
vm_name  = "jamf-test-vm"
ipsw_url = "/path/to/your/macos-tahoe.ipsw"

# Account Configuration
account_userName = "admin"
account_password = "admin"

# MDM Enrollment Configuration
enrollment_type    = "profile"  # Options: "profile" or "link"
jamf_url           = "https://yourinstance.jamfcloud.com"
mdm_invitation_id  = "your-invitation-id-here"

# Feature Toggles
enable_passwordless_sudo   = "true"
enable_auto_login          = "true"
enable_safari_automation   = "true"
enable_screenlock_disable  = "true"
enable_spotlight_disable   = "true"
enable_clipboard_sharing   = "false"
```

Run the build with your vars file:￼

`packer build -var-file="my-config.pkrvars.hcl" apple-tart-tahoe.pkr.hcl`

Note: .pkrvars.hcl files are typically gitignored to protect credentials and invitation IDs.
