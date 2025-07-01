# NECTAR Virtual Machine Setup Guide

This guide walks you through setting up and using a NECTAR VM, including SSH access and volume mounting.    

NECTAR (National eResearch Collaboration Tools and Resources) is a cloud computing platform funded by the Australian government to support research and innovation. It provides free or subsidized access to virtual machines, storage, and other cloud resources for eligible researchers across Australia.

---

##  Step 1: Request a Project and Volume Allocation

Request your project here:  
[NECTAR Project Requests Guide](https://support.ehelp.edu.au/support/solutions/articles/6000068044-project-trials-project-requests-and-managing-an-allocation)

- Select the **correct funding area** (e.g., your institution).
- **VERY IMPORTANT** Don't forget to **add a volume request**.
- Projects without volumes won't have storage for persistent data.

The evaluation process takes a few weeks, and the NECTAR team will notify you. Once you are granted a project, please continue to the following steps.

---

## Step 2: Create an SSH Group Ingress Rule

Set up a security group to allow SSH access:  
[Create Security Group Rule](https://tutorials.rc.nectar.org.au/sec-groups-101/03-create)

- **Protocol**: TCP  
- **Port range**: 22  
- **CIDR**: `0.0.0.0/0` (open to all IPs; restrict for more security)  
- Name your group something like `ssh-access`.

---

## Step 3: Launch Your First Instance (VM)

Follow this guide:  
[Launch Instance Guide](https://tutorials.rc.nectar.org.au/launching-virtual-machines/02-launch-dialog)

Go to "Compute" → "Instances" → "Launch Instance"
- Add Details information
- **Select an OS - Source** like NECTAR Ubuntu 22.04 LTS (Jammy) amd64.
- **Choose a flavour** (e.g. `m2.medium`, `m3.large`) based on your needs and resources
- Assign the `ssh-access` group created in step 2.
- **Generate your SSH key** (download the private key to your local machine).
- Wait until the instance is in a **"Running"** state and has a **Floating IP**.
- "Launch instance" with the bottom yellow button after applying all the changes.

---

## Step 4: Attach a Volume

- Navigate to **Volumes** in the dashboard.
- Create or choose an existing volume.
- Attach to your instance:
  - `Actions` → `Manage Attachments` → Select your instance.
  - Example device path: `/dev/vdb`.

---

## Step 5: SSH Into Your Instance

Fix permissions for the SSH key, from step 3:
```bash
chmod 600 your_key
```

Use your terminal to connect:

```bash
ssh -i /path/to/your_key ubuntu@<Floating-IP>
```

Replace:
- `/path/to/your_key` with your key's path.
- `<Floating-IP>` with your VM’s IP address.
- `ubuntu` with your default OS username if different.

---

## Step 6: Mount and Format the Volume

Reference:  
🔗 [Format and Mount a Volume](https://tutorials.rc.nectar.org.au/volume-storage/04-format-mount)

In the VM instance: 

1. Check the volume:
    ```bash
    lsblk
    ```

2. Format it (only if new):
    ```bash
    sudo mkfs.ext4 /dev/vdb
    ```

3. Create a mount point:
    ```bash
    sudo mkdir /mnt/storage
    ```

4. Mount the volume:
    ```bash
    sudo mount /dev/vdb /mnt/storage
    ```

5. Persist mount on reboot:
    ```bash
    echo "/dev/vdb /mnt/storage ext4 defaults 0 0" | sudo tee -a /etc/fstab
    ```

---

## Summary Checklist

| Step | Action |
|------|--------|
| 1 | Request project + volume |
| 2 | Create `ssh-access` group |
| 3 | Launch instance with OS + flavour |
| 4 | Add security group & SSH key |
| 5 | Attach volume |
| 6 | SSH into instance |
| 7 | Format & mount volume |

---

## License

This project is licensed under the [CC BY 4.0 License](https://creativecommons.org/licenses/by/4.0/).
