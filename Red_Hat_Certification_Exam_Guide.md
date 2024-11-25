
# Red Hat Certification Exam Guide

## Exam Methods
1. **Kiosk** - Testing Center.
2. **Remote** - Requirements:
   - Bootable USB drive.
   - Two cameras.
   - High-performance laptop.
   - Reliable internet connectivity.
   - Power backup.

---

## Candidate Requirements
- **Original ID Card**:
  - Must be pre-approved by Red Hat during exam scheduling.
  - Accepted IDs: 
    - Aadhar Card
    - PAN Card
    - Driving License
    - Passport  
  - **Note**: Laminated IDs are **not** accepted.

---

## Pre-Exam Steps
1. **Provide the Following Details**:
   - RHN ID.
   - Exam Code: `EX200`.
2. **Read Instructions Carefully**:
   - Duration: 10–15 minutes.
   - Includes:
     - Exam rules (do's and don'ts).
     - Node 1 and Node 2 IP addresses.
     - Credentials (e.g., usernames, passwords).

---

## Exam Details
- **Total Questions**: ~22
- **Passing Score**: 210
- **Duration**: 3 hours
- **Break Policy**: 10 minutes allowed after the first hour (included in the 3-hour total).

---

## Exam Environment
- **Allowed Commands**:
  - `copy` command is functional.
  - `tab` autocompletion is available.
  - `man` pages can be used but frequent usage may impact the score.
- **Question Handling**:
  - Mark questions as **Done** for review or revisit.

---

## Performing the Exam
1. **Access Virtual Machines (VMs)**:
   - Click on **Activities**.
   - Locate the four icons on the left side and select **VM Console**.
   - This will display Node 1 and Node 2.
   - Log in to both VMs as the **root user**.
   - Use the terminal provided in the exam to SSH into the VMs.

2. **Prerequisites for VM Connection**:
   - Root passwords (provided in instructions).
   - Ensure a working SSH connection.

---

## Tasks Overview
1. **Node 1**: 
   - Configure the network:
     - Set a **static IP** to establish connectivity.
2. **Node 2**:
   - Recover the **root password**:
     - Network configuration will already be in place.

---

## Additional Notes
- During practice, VM names may be `servera` and `serverb`. 
- In the exam, VM names could vary — adapt accordingly.
