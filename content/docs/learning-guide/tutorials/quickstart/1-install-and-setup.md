---
linkTitle: "1. Install and Set Up"
title: "Step 1: Install O3DE and Create Your Project"
description: >
  Download and install O3DE, then use Project Manager to create and build
  your first project.
toc: true
weight: 100
---

In this step you'll install O3DE, launch **Project Manager**, and create and build a new project called **GemCollector**.

**Estimated time:** ~45 minutes (build time varies by CPU speed and internet connection)

## 1. Install O3DE

{{< tabs name="install-o3de" >}}

{{% tab name="Windows" %}}

1. Go to [o3de.org/download](https://o3de.org/download/#windows) and download the latest Windows installer.

1. Run the downloaded `.exe`. If **Windows Defender SmartScreen** shows a warning, choose **More info** and then **Run anyway** — this is expected for newly released software.

1. Accept the default install location (`C:\O3DE\<version>`) and choose **Install**. The installer downloads additional packages during this step; it can take a few minutes.

1. When the installer shows **Installation Successfully Completed**, choose **Launch** to open **Project Manager** for the first time, or close it and continue to the next section.

   The installer also creates two desktop shortcuts:
   - **O3DE Editor** — the main authoring tool
   - **O3DE Project Manager** — creates and manages projects

{{% /tab %}}

{{% tab name="Linux" %}}

1. Go to [o3de.org/download](https://o3de.org/download/#linux) and download the latest `.deb` package (Ubuntu 22.04 LTS recommended).

1. Install it with `apt`:

   ```shell
   sudo apt install ~/Downloads/<package-name>.deb
   ```

   Replace `<package-name>` with the file you downloaded (for example, `o3de_24.09.deb`). The install may take a few minutes.

   {{< known-issue link="https://bugs.launchpad.net/ubuntu/+source/synaptic/+bug/1522675" >}}
   You may see a `Permission denied` warning about `_apt`. This is a known `apt` bug and does not affect the install. The package installs correctly despite the warning.
   {{< /known-issue >}}

1. O3DE installs to `/opt/O3DE/<version>/`. Launch **Project Manager** from the terminal:

   ```shell
   /opt/O3DE/<version>/bin/Linux/profile/Default/o3de
   ```

   Replace `<version>` with the installed version (for example, `24.09`).

{{% /tab %}}

{{< /tabs >}}

For a detailed walkthrough with screenshots, refer to the full [Windows](/docs/welcome-guide/setup/installing-windows/) or [Linux](/docs/welcome-guide/setup/installing-linux/) installation guides.

---

## 2. Configure engine settings

The first time Project Manager opens, check the storage settings before creating a project.

1. Choose the **Engine** tab near the top of Project Manager.

1. Review the **Default Projects Folder** and **3rd Party Software Folder** paths. Both default to subdirectories of your home folder. Each location can use **10–20 GB**, so make sure the target drive has enough free space.

1. Change either path by clicking the folder icon next to it if you need to point them at a drive with more room.

1. Return to the **Projects** tab when you're done.

---

## 3. Create your project

1. Choose **Create a Project**. If you already have projects registered, open the **New Project** dropdown and choose **Create New Project**.

1. Fill in the project details:

   | Field | Value |
   |-------|-------|
   | **Project name** | `GemCollector` |
   | **Project location** | Leave as default, or choose any writable folder |
   | **Template** | **Default Project** (keep the default selection) |

   {{< note >}}
   The **Default Project** template includes all the Gems this tutorial needs: PhysX5 for physics, Script Canvas for visual scripting, and the Atom renderer for visuals. Leave the template and Gem list unchanged.
   {{< /note >}}

1. Choose **Create Project**. O3DE creates the project files and registers the project in your O3DE manifest.

---

## 4. Build the project

Building compiles the project code and links all the Gems you've enabled. You only need to do a full build once; after that, incremental builds are much faster.

1. In the project card, open the **Build Project** dropdown and choose **Build Now**. Confirm in the dialog that appears.

   {{< important >}}
   **The first build takes time.** O3DE downloads required third-party packages, then compiles the engine and project together. Expect **20–45 minutes** on a typical development machine, depending on your CPU and internet speed. This is a one-time cost.
   {{< /important >}}

   {{< tip >}}
   While the build runs, read [Key Concepts](/docs/welcome-guide/key-concepts/) to get familiar with entities, Gems, and the asset pipeline — concepts you'll use in every step that follows.
   {{< /tip >}}

1. When the build completes, the button in the project card changes to **Open Editor**.

---

## 5. Open the Editor

Choose **Open Editor** in the project card. O3DE Editor launches along with **Asset Processor** in the system tray.

{{< note >}}
**Wait for Asset Processor.** Asset Processor (the icon in your system tray / taskbar) scans and processes all assets in the background. You'll see a spinning indicator while it works. Let it reach **0 jobs remaining** before working with assets in the editor, otherwise some assets may not be available.
{{< /note >}}

---

## Checkpoint

You should now see O3DE Editor open to a default empty level. If so, you're ready to move on.

**Troubleshooting:**
- If the build failed, check the **Build Log** panel in Project Manager for error messages. Common causes are missing Visual Studio workloads (Windows) or missing Clang packages (Linux) — refer to [System Requirements](/docs/welcome-guide/requirements/).
- If the Editor opens but Asset Processor shows errors, let it finish processing before continuing.

---

**Next: [Step 2 — Create a Level →](../2-create-a-level)**
