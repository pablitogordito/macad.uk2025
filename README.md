# Silicon Sandbox: Mastering Mac virtualisation for Jamf workflows - macad.uk2025

### [Read Blog Post](https://www.motionbug.com/the-cookbook-baking-up-your-perfect-jamf-pro-test-vm/)

---

![keynoteslide](img/slide.jpg)

Resources from my talk at macad.uk named **"Silicon Sandbox: Mastering Mac virtualisation for Jamf workflows"**.

![macad.uk](img/logo.png)

## Overview

This repository contains Packer templates and resources for automating macOS virtualization for macadmins with Jamf Pro / School.

## Packer Templates

### apple-tart-tahoe.pkr.hcl

A Packer template for creating macOS Tahoe (macOS 15) virtual machines using [Tart](https://github.com/cirruslabs/tart) on Apple Silicon.

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

1. **Install Tart**

   ```bash
   brew install cirruslabs/cli/tart
   ```

2. **Download macOS Tahoe IPSW**

   Use [Mist](https://github.com/ninxsoft/Mist/releases) to download the IPSW file. Mist is a Mac utility that automatically downloads macOS firmware and installers directly from Apple. Good tool to have installed as a macadmin. Anther option is to download the IPSW that you need from [Mr. Macintosh's IPSW list](https://mrmacintosh.com/apple-silicon-m1-full-macos-restore-ipsw-firmware-files-database/).

3. **Customize Variables**

   Create a variables file or use command-line flags:

   ```bash
   packer build \
     -var 'vm_name=jamf-test-vm' \
     -var 'ipsw_url=/path/to/tahoe.ipsw' \
     -var 'jamf_url=https://yourinstance.jamfcloud.com' \
     -var 'mdm_invitation_id=your-invitation-id' \
     -var 'enrollment_type=profile' \
     apple-tart-tahoe.pkr.hcl
   ```

4. **Build the VM**

   ```bash
   cd packer-templates
   packer validate apple-tart-tahoe.pkr.hcl
   packer build apple-tart-tahoe.pkr.hcl
   ```

#### Enrollment Options

##### Profile-based enrollment (`enrollment_type = "profile"`)

- Creates `mdm_enroll.mobileconfig` on the desktop
- User double-clicks to install the profile

##### Link-based enrollment (`enrollment_type = "link"`)

- Creates `Enroll_Your_Mac.webloc` on the desktop
- User double-clicks to open enrollment URL in browser then finishes the enrollment process

## Requirements

- macOS host with Apple Silicon
- [Packer](https://www.packer.io/) 1.8.0 or later
- [Tart](https://github.com/cirruslabs/tart) 1.15.3 or later
- [Mist - macOS Tahoe (macOS 26) IPSW file](https://github.com/ninxsoft/Mist/releases)
- Jamf Pro (for MDM enrollment)