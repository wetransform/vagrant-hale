vagrant-hale
============

Ubuntu Desktop VM installing hale studio.

By default downloads hale studio 4.0.0 (see section *Configuration*)

Booting up
----------

```
vagrant up
```

Username: `vagrant`
Password: `vagrant`

When starting up the first time you may need to complete an Ubuntu setup wizard.


Starting hale studio
--------------------

The VM may launch before the hale studio installation is complete (the download may take a while), so make sure o wait until the provisioning is complete.

Right on the desktop you can find a link to start hale studio.

If you see a `hale.desktop` file instead of a **hale studio** link right-click on the `hale.desktop` file and select *Allow launching* from the context menu.


Upgrading
---------

When the installation script in the Vagrantfile has been updated or you changed any settings, run the provisioning again:

```
vagrant provision
```

Attention: This will delete the folder where hale studio was installed and replace it by a newly downloaded version.


Configuration
-------------

The file `vagrant.yml` can be used to configure the VM. See `vagrant.sample.yml` for an example. Following the different options are described in short:

* **cpus** - Number of virtual CPUs
* **cpucap** - The CPU execution cap
* **ram** - The system memory in MB
* **defaultShares** - If default shares should be mounted; currently this only applies for Windows, where `C:\Users` is available on `/c/Users` in the VM
* **shares** - List of custom mounted folders, each with the path on the **host** and the **vm**
* **haleDownload** - Use an alternate URL to download the hale studio Linux installation archive from
