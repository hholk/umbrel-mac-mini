# Multipass on macOS: Installation, Changing Image Storage, and Launching Ubuntu with Custom Configuration

This tutorial will guide you through the process of installing Multipass on macOS, changing the default image storage location to an external drive, and launching an Ubuntu instance with custom CPU, memory, and disk configurations.

## Table of Contents

1. [Installing Multipass with Homebrew](#installing-multipass-with-homebrew)
2. [Changing Image Storage Location to an External Drive](#changing-image-storage-location-to-an-external-drive)
3. [Launching Ubuntu Instance with Custom Configuration](#launching-ubuntu-instance-with-custom-configuration)

## Installing Multipass with Homebrew

1. Install Homebrew: If you don't have Homebrew installed on your Mac, follow the instructions on the [Homebrew website](https://brew.sh/) to install it.

2. Install Multipass: Run the following command to install Multipass using Homebrew:

```
brew install --cask multipass
```

## Changing Image Storage Location to an External Drive

1. Find the mount point of your external drive by running:

```
diskutil list
```

Identify your external drive and its mount point, which will look like `/dev/diskXsY` (e.g., `/dev/disk4s2`).

2. Create a folder on your external drive to store the Multipass images:

```
mkdir /Volumes/MyExternalDrive/multipass-images
```

Replace `/Volumes/MyExternalDrive` with the actual mount point of your external drive.

3. Stop the Multipass service:

```
sudo launchctl stop /Library/LaunchDaemons/com.canonical.multipassd.plist
```

4. Edit the `/Library/LaunchDaemons/com.canonical.multipassd.plist` file using your preferred text editor, such as nano or vi:

```
sudo nano /Library/LaunchDaemons/com.canonical.multipassd.plist
```

5. Add the `MULTIPASS_STORAGE` key and value in the `EnvironmentVariables` dictionary, replacing `/Volumes/MyExternalDrive/multipass-images` with the desired storage location on your external drive:

```xml
<key>MULTIPASS_STORAGE</key>
<string>/Volumes/MyExternalDrive/multipass-images</string>
```

The modified `EnvironmentVariables` section should look like this:

```xml
<key>EnvironmentVariables</key>
<dict>
  <key>PATH</key>
  <string>/Library/Application Support/com.canonical.multipass/bin:/usr/bin:/bin:/usr/sbin:/sbin</string>
  <key>MULTIPASS_STORAGE</key>
  <string>/Volumes/MyExternalDrive/multipass-images</string>
</dict>
```

Save and close the file.

6. Start the Multipass service:

```
sudo launchctl start /Library/LaunchDaemons/com.canonical.multipassd.plist
```

The Multipass image storage location is now changed to the new location on your external drive.

## Launching Ubuntu Instance with Custom Configuration

1. Launch a new Ubuntu instance named "primary" with 8 CPU cores, 3.5 TB of disk space, and 8 GB of RAM:

```
multipass launch --name primary --cpus 8 --disk 3500G --mem 8G
```

2. Verify the configuration by running the following command:

```
multipass info primary
```

You should see that the "primary" instance has 8 CPUs, 8 GB of memory, and 3.5 TB of disk space.

The simplest way to automatically start the "primary" VM when your Mac mini boots up is to create a launch agent plist file. Follow these steps:

1. Open your favorite text editor and create a new file called `com.mymac.multipass.primary.plist`. Replace "mymac" with your desired identifier, but ensure it remains unique.

2. Copy and paste the following XML content into the file:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
  <key>Label</key>
  <string>com.mymac.multipass.primary</string>
  <key>ProgramArguments</key>
  <array>
    <string>/usr/local/bin/multipass</string>
    <string>start</string>
    <string>primary</string>
  </array>
  <key>RunAtLoad</key>
  <true/>
</dict>
</plist>
```

3. Save the file.

4. Copy the saved `com.mymac.multipass.primary.plist` file to the `~/Library/LaunchAgents/` directory. You can use the following command in the terminal:

```
cp /path/to/com.mymac.multipass.primary.plist ~/Library/LaunchAgents/
```

Replace `/path/to/` with the actual path to the `com.mymac.multipass.primary.plist` file.

5. Load the launch agent plist:

```
launchctl load ~/Library/LaunchAgents/com.mymac.multipass.primary.plist
```

Now, every time your Mac mini boots up, the "primary" VM will automatically start.

To unload the launch agent and disable the automatic start of the "primary" VM, you can run:

```
launchctl unload ~/Library/LaunchAgents/com.mymac.multipass.primary.plist
```


## Installing Umbrel using a One-liner Script

1. Install Umbrel by running the following command:

```
sudo curl -L https://umbrel.sh | bash
```

This one-liner will download and execute the Umbrel installation script. The script will automatically install and configure all necessary components for Umbrel.

2. Once the installation is complete, you can access the Umbrel web interface by visiting the displayed local IP address or domain name in your web browser.

That's it! You've successfully installed Umbrel on your "primary" instance.
