# Controlling Access to VPC Networks

> 📅 **Date:** 2026-02-24 | ✍️ **Author:** Hugo Souza | 🏷️ **Version:** 1.0.0

[![Status](https://img.shields.io/badge/Status-Active-success?style=flat-square)](#)

## 🎯 Objective

In this hands-on lab, you create two nginx web servers and control external HTTP access to the web servers using tagged firewall rules. Then you explore IAM roles and service accounts.

Specifically, you will learn how to:

- Create an nginx web server.
- Create tagged firewall rules.
- Create a service account with IAM roles.
- Explore permissions for the Network Admin and Security Admin roles.

## 📋 Prerequisites

- [ ] A Google Cloud project with billing enabled.
- [ ] Basic understanding of Google Compute Engine and VPC networks.

---

## 🏗️ Architecture

> _(Insert the diagram representing the deployed topology or architecture below)_

---

## 🚀 Step by Step

### 1. Create the web servers

Create two web servers (blue and green) in the default VPC network. Then install nginx on the web servers and modify the welcome page to distinguish the servers.

#### Create the blue server

Create the blue server with a network tag.

1. In the Cloud Console, on the Navigation menu, click **Compute Engine > VM instances**.
2. Click **Create Instance**.
3. Specify the following, and leave the remaining settings as their defaults:
   - **Name**: `blue`
   - **Region**: `us-west1`
   - **Zone**: `us-west1-c`
4. Click **Networking**.
5. For **Network tags**, type `web-server`.
   > **Note:** Network tags are used by networks to identify which VM instances are subject to certain firewall rules and network routes.
6. Click **Create**.

#### Create the green server

Create the green server without a network tag.

1. Click **Create instance**.
2. Specify the following, and leave the remaining settings as their defaults:
   - **Name**: `green`
   - **Region**: `us-west1`
   - **Zone**: `us-west1-c`
3. Click **Create**.

#### Install nginx and customize the welcome page

Install nginx on both VM instances and modify the welcome page to distinguish the servers.

1. For `blue`, click **SSH** to launch a terminal and connect.
2. Run the following command to install nginx:
   ```bash
   sudo apt-get install nginx-light -y
   ```
3. Open the welcome page in the nano editor:
   ```bash
   sudo nano /var/www/html/index.nginx-debian.html
   ```
4. Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the blue server!</h1>`.
5. Press `CTRL+O`, `ENTER`, `CTRL+X`.
6. Verify the change:
   ```bash
   cat /var/www/html/index.nginx-debian.html
   ```
7. Close the SSH terminal to blue:
   ```bash
   exit
   ```

**Repeat the same steps for the green server:**

1. For `green`, click **SSH** to launch a terminal and connect.
2. Install nginx:
   ```bash
   sudo apt-get install nginx-light -y
   ```
3. Open the welcome page in the nano editor:
   ```bash
   sudo nano /var/www/html/index.nginx-debian.html
   ```
4. Replace the `<h1>Welcome to nginx!</h1>` line with `<h1>Welcome to the green server!</h1>`.
5. Press `CTRL+O`, `ENTER`, `CTRL+X`.
6. Verify the change.
7. Close the SSH terminal to green:
   ```bash
   exit
   ```

---

### 2. Create the firewall rule

Create the tagged firewall rule and test HTTP connectivity.

#### Create the tagged firewall rule

Create a firewall rule that applies to VM instances with the `web-server` network tag.

1. In the Cloud Console, on the Navigation menu, click **VPC network > Firewall**.
2. Notice the `default-allow-internal` firewall rule (allows traffic on all protocols/ports within the default network).
3. Click **Create Firewall Rule**.
4. Specify the following, and leave the remaining settings as their defaults:
   - **Name**: `allow-http-web-server`
   - **Network**: `default`
   - **Targets**: `Specified target tags`
   - **Target tags**: `web-server`
   - **Source filter**: `IPv4 Ranges`
   - **Source IPv4 ranges**: `0.0.0.0/0`
   - **Protocols and ports**: Specified protocols and ports, check `tcp` and specify port `80`.
5. Click **Create**.

#### Create a test-vm

Create a `test-vm` instance using the gcloud command line.

1. In the Cloud Shell, run the following command to create a test-vm instance:
   ```bash
   gcloud compute instances create test-vm --machine-type=e2-medium --subnet=default --zone=us-west1-c
   ```

#### Test HTTP connectivity

From `test-vm` test the internal and external IP addresses of blue and green.

1. In the Cloud Console, on the Navigation menu, click **Compute Engine > VM instances**. Note the internal and external IP addresses of blue and green.
2. For `test-vm`, click **SSH** to launch a terminal and connect.
3. Test HTTP connectivity to blue's internal IP:
   ```bash
   curl <blue_internal_ip>
   ```
   _You should see the "Welcome to the blue server!" header._
4. Test HTTP connectivity to green's internal IP:
   ```bash
   curl <green_internal_ip>
   ```
   _You should see the "Welcome to the green server!" header._
5. Test HTTP connectivity to blue's external IP:
   ```bash
   curl <blue_external_ip>
   ```
   _You should see the "Welcome to the blue server!" header._
6. Test HTTP connectivity to green's external IP:
   ```bash
   curl <green_external_ip>
   ```
   _This should not work, as expected._
7. Press `CTRL+C` to stop the HTTP request.

---

### 3. Explore the Network and Security Admin roles

Cloud IAM lets you authorize who can take action on specific resources.

- **Network Admin:** Permissions to create, modify, and delete networking resources, _except_ for firewall rules and SSL certificates.
- **Security Admin:** Permissions to create, modify, and delete firewall rules and SSL certificates.

#### Verify current permissions

Currently, `test-vm` uses the Compute Engine default service account. Try to list or delete the available firewall rules from `test-vm`.

1. Return to the SSH terminal of the `test-vm` instance.
2. Try to list the available firewall rules:
   ```bash
   gcloud compute firewall-rules list
   ```
   _(This should fail with Insufficient Permission)_
3. Try to delete the `allow-http-web-server` firewall rule:
   ```bash
   gcloud compute firewall-rules delete allow-http-web-server
   ```
   _(This should fail with Insufficient Permission)_

#### Create a service account

Create a service account and apply the Network Admin role.

1. In the Cloud Console, on the Navigation menu, click **IAM & admin > Service accounts**.
2. Click **Create service account**.
3. Set the Service account name to `Network-admin`.
4. Click **Create and Continue**.
5. For **Select a role**, select **Compute Engine > Compute Network Admin**.
6. Click **Continue**, then **Done**.

#### Authorize test-vm

Authorize `test-vm` to use the `Network-admin` service account.

1. On the Navigation menu, click **Compute Engine > VM instances**.
2. Click on the name of the `test-vm` instance to open the details page.
3. Click **Stop** and confirm to Stop the instance.
4. Once stopped, click **Edit**.
5. Select `Network-admin` for **Service account**.
6. For **Access Scopes**, select **Set access for each API**, and in the Compute Engine dropdown, choose the **Read/Write** option.
7. Click **Save** and wait for the instance to update.
8. Click **Start** and confirm. Wait for `test-vm` to change to a running state.
9. Click **SSH** for the `test-vm` instance.

#### Verify permissions

1. Try to list the available firewall rules:
   ```bash
   gcloud compute firewall-rules list
   ```
   _(This should work!)_
2. Try to delete the `allow-http-web-server` firewall rule:
   ```bash
   gcloud compute firewall-rules delete allow-http-web-server
   ```
   _(This should fail, as Network Admin lacks permissions to modify/delete firewall rules)_

#### Update service account and verify permissions

Update the `Network-admin` service account by providing it with the Security Admin role.

1. In the Cloud Console, on the Navigation menu, click **IAM & admin > IAM**.
2. Find the `Network-admin` account and click on the pencil icon to edit.
3. Change **Role** to **Compute Engine > Compute Security Admin**.
4. Click **Save**.
5. Return to the SSH terminal of the `test-vm` instance.
6. Try to list the available firewall rules:
   ```bash
   gcloud compute firewall-rules list
   ```
   _(This should work!)_
7. Try to delete the `allow-http-web-server` firewall rule:
   ```bash
   gcloud compute firewall-rules delete allow-http-web-server
   ```
   _(This time, it should work and delete the rule)_

#### Verify the deletion of the firewall rule

Verify that you can no longer HTTP access the external IP of the blue server.

1. Return to the SSH terminal of the `test-vm` instance.
2. Test HTTP connectivity to blue's external IP:
   ```bash
   curl <blue_external_ip>
   ```
   _(This should not work anymore!)_
3. Press `CTRL+C` to stop the HTTP request.

---

## 4. Review

In this lab, you created two nginx web servers and controlled external HTTP access using a tagged firewall rule. Then you created a service account with first the Network Admin role and then the Security Admin role to explore the different permissions of these roles.

> 💡 **Best Practice:** If your company has a security team managing firewalls and SSL certificates, and a networking team managing the rest of the networking resources, grant the security team the Security Admin role and the networking team the Network Admin role.

---

## 📚 Official References

- [🔗 Google Cloud VPC Documentation](https://cloud.google.com/vpc/docs)
- [🔗 IAM Roles for Compute Engine](https://cloud.google.com/compute/docs/access/iam)
- [🔗 Google Skills: Controlling Access to VPC Networks (Source Lab)](https://www.skills.google/paths/14/course_templates/35/labs/316804)

[⬅️ Back to GCP Index](../README.md)
