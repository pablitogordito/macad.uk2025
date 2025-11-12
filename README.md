# Silicon Sandbox: Mastering Mac virtualisation for Jamf workflows - macad.uk2025

### [Read Blog Post that goes into more detail](https://www.motionbug.com/the-cookbook-baking-up-your-perfect-jamf-pro-test-vm/)

---

Resources from my talk at macad.uk named **"Silicon Sandbox: Mastering Mac virtualisation for Jamf workflows"**.

![macad.uk](img/logo.png)

## Overview

This repository contains Packer templates and resources for automating macOS virtualization for macadmins with Jamf Pro.

## Requirements

- macOS host with Apple Silicon
- [brew](https://brew.sh/)
- [Packer](https://www.packer.io/) 1.8.0 or later
- [Tart](https://github.com/cirruslabs/tart) 1.15.3 or later
- macOS Tahoe (macOS 26) IPSW file
  - [Optional - Mist](https://github.com/ninxsoft/Mist/releases)
- Jamf Pro (for MDM enrollment)

## Packer Templates

### apple-tart-tahoe.pkr.hcl

A Packer template for creating macOS Tahoe (macOS 26) virtual machines using [Tart](https://github.com/cirruslabs/tart) on Apple Silicon.

#### Features

- **Automated macOS Setup Assistant** - Fully automated first-boot configuration
- **MDM Enrollment** - Supports both profile-based or link-based Jamf enrollment
- **Configurable Features** - Toggle various system settings via variables
- **SSH Access** - Automatic SSH and screen sharing setup

#### Variables

##### VM Configuration

- `vm_name` - Name of the virtual machine (default: "this_is_the_base_image_name")
- `ipsw_url` - Path or URL to **macOS Tahoe IPSW** file

##### Account Configuration

- `account_userName` - macOS account username (default: "admin")
- `account_password` - macOS account password (default: "admin")

##### MDM Enrollment Configuration

- `enrollment_type` - Enrollment method: "profile" or "link" if you want a link to the enrollment page or you want the profile on the desktop after first boot. Combined now to one packer file. (default: "profile")
- `jamf_url` - Jamf Cloud URL (e.g., `https://instance.jamfcloud.com`)
- `mdm_invitation_id` - MDM enrollment invitation ID

##### Feature Toggles

- `enable_passwordless_sudo` - Enable passwordless sudo (default: "true")
- `enable_auto_login` - Enable automatic login (default: "true")
- `enable_safari_automation` - Enable Safari automation support (default: "true")
- `enable_screenlock_disable` - Disable screen lock (default: "true")
- `enable_spotlight_disable` - Disable Spotlight indexing (default: "true")
- `enable_clipboard_sharing` - Enable clipboard sharing via tart guest agent (default: "false")

#### Usage

**Important:** Do not edit the `apple-tart-tahoe.pkr.hcl` template file directly. Instead, create a separate variables file (`.pkrvars.hcl`) to customize your configuration.

1. **Install Tart**

   ```bash
   brew install cirruslabs/cli/tart
   ```

   **Note:** If you don't have Homebrew installed, visit [brew.sh](https://brew.sh/) to learn how to install it.

2. **Download macOS Tahoe IPSW**

   Use [Mist](https://github.com/ninxsoft/Mist/releases) to download the IPSW file. Mist is a Mac utility that automatically downloads macOS firmware and installers directly from Apple. Good tool to have installed as a macadmin. Anther option is to download the IPSW that you need from [Mr. Macintosh's IPSW list](https://mrmacintosh.com/apple-silicon-m1-full-macos-restore-ipsw-firmware-files-database/).

3. **Create a Variables File**

   Create a file named `my-config.pkrvars.hcl` in the `packer-templates` directory with your custom values:

   ```hcl
   # -------------------------
   # Packer Variables File
   # -------------------------
   # This file contains variable values for the apple-tart-tahoe.pkr.hcl template
   # Usage: packer build -var-file="my-config.pkrvars.hcl" apple-tart-tahoe.pkr.hcl

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

   **Note:** Variables files (`.pkrvars.hcl`) are gitignored by default to protect sensitive information like passwords and invitation IDs.

4. **Build the VM**

   Navigate to the packer-templates directory and run the build:

   ```bash
   cd packer-templates
   
   # Validate the template
   packer validate apple-tart-tahoe.pkr.hcl
   
   # Build with your variables file
   packer build -var-file="my-config.pkrvars.hcl" apple-tart-tahoe.pkr.hcl
   ```

#### Enrollment Options

##### Profile-based enrollment (`enrollment_type = "profile"`)

- Creates `mdm_enroll.mobileconfig` on the desktop
- User double-clicks to install the profile

##### Link-based enrollment (`enrollment_type = "link"`)

- Creates `Enroll_Your_Mac.webloc` on the desktop
- User double-clicks to open enrollment URL in browser then finishes the enrollment process

#### Additional Notes

##### NOTE: 🔊 Audio Disabled During Build

The template includes `run_extra_args = ["--no-audio"]` to disable audio output during VM creation. This prevents any unexpected sounds from the macOS Setup Assistant while the automated build is running.
