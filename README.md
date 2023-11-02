# GSOC-2023
GNOME Control Center: List and handle IPP print services for the New Architecture
Hello, I am Mohit Verma and I was selected as GSOC 2023 contributor in Open Printing @ The Linux Foundation. I have worked on compiling three GSoC's Project which were done on GNOME Settings Printer Panel, listing & handling of IPP print services. Apart from that I have helped in designing modern Architecture of Printer's Panel print serivces & expanding the cups-pk-helper DBus interface. I am currently implementing the new method for handling of non driverless printer in the Printer's Panel.

cups-pk-helper - https://gitlab.freedesktop.org/cups-pk-helper/cups-pk-helper/-/merge_requests/7

GNOME Settings - https://github.com/vermamohit13/gnome-control-center/tree/presentation

# Project Description
Modern printers usually are driverless IPP printers, and those get discovered fully automatically by CUPS; no CUPS queue needs to be explicitly created. Same for remote CUPS printers and also Printer Applications . They are all discovered as IPP print services. And listings of IPP print services have different requirements: One server can have more than one individual print service and these should get listed together. This could be a print queue and a fax out queue of the same multi-function printer, or two physical legacy printers supported by one Printer Application. Also the user interaction coupled to each listing is different. We do not need to configure PPD option settings, but instead, we need access to the IPP service's web administration interface and also to an IPP System Service configuration panel by a simple mouse click. Several parts of this were already coded in previous GSoCs, but we need to get everything smoothly integrated in the “Printers” part of the GNOME Control Center. I will work together with the upstream maintainer of the “Printers” module, Marek Kasik and also with the UI/UX design teams of GNOME and of Canonical.

# Compiling previous GSOC's project

## Lakshay's Panel
In GSoC's 2020 Edition Lakshay Bandlish worked on creating a GTK-based tool to administer IPP Multi-Function devices using IPP System Service. He has created the tool to list ipps system services available only. His work has demonstrated the browsing of ipps system services and resolving those services. However, the administrative task for the system services and the queues installed on them wasn't there. This was a crucial feature which was missing. Along with that, the browsing of services happens directly by calling avahi. This has added libavahi as a dependency. This added dependency is not accepted by GNOME's Maintainer. Apart from this the code written for UI cannot be in anyway integrated with GCC.

## Divyasheel's Panel
In GSoC's 2021 Edition Divyasheel Kumar worked on the project GUI for listing and managing available IPP Print/Scan services. His work resulted in a new button in GNOME Settings Printer's Panel which can browse for MDNS-SD service type _ipps._tcp & _ipp._tcp along with a button to access the web interface. However, The work done by Divyasheel was outdated soon due to issues such as libavahi as a dependency, several design aspects. The work done was quite useful & helped in creating the UI as per our needs.

## Shivam's Panel
In GSoC's 2022 Edition Shivam worked on a GTK-based tool to setup IPP devices using IPP System Service. This project's motive was to build a printer setup system with a GTK-based GUI that can be embedded in the GNOME Control Center to bring all printer management control in one place. The worked done by Shivam was only a tree view of discovered ipp services. So, not much work was done.

## My Approach
With several discussion with Marek Kasik from GNOME, few things were clear to me that, almost everything which was done earlier was not as per the expectation of him.

### Asynchronous discovery of services using DBUS calls
I started out with writing everything from scratch. I first wrote the task of discovery of IPP services using DBUS calls to avahi. Writing DBUS calls were a new thing for me. It took me a lot of time to understand how they work. These calls then had to be async in nature so that the GUI doesn't suffer. Again, writing async jobs were a new thing for me. All of this need to be done in GCC whose printer's panel in it's own is a very big module. This took me a quite a lot of time to implement. Almost an entire month went just in this. With the discovery of services with DBUS calls we have successfully removed libavahi as a dependency. Along with this the piece of code was async in nature which was new in comparison of previous project.

### Changes in GUI
For GUI of the new Printer Setup Tool, I & Till conversed a lot with Elio Qoshi of Canonical as the GNOMies were too busy with some of the projects. We have discussed in detail, what we wanted and how the things should be. As per our needs, he provided us with a UI which can suffice our needs. To see the conversation - https://github.com/vermamohit13/GSOC_2022_Summary/issues/1.



## Main Window of Printer's Panel
A substantial amount of work was also needed in the Main Window of Printer's Panel. We wanted to update as per the New Architecture as well.The main panel will be able to list two types of entries:
1. DNS-SD (print/fax-out) service
2. Classic CUPS queue
For (1) the user actions will be to access the web interface and to open the IPP System Service panel. "Printer Options" and "Printer Details" do not make sense any more here.
For (2) the same user actions as before will get offered ("Printer Options", "Printer Details"). Also only manually created CUPS queue should be displayed here, no temporary CUPS queues.
Also the Printer Application entry and its queues should be grouped. For network printers and IPP-over-USB printers which are discovered we should also show a printer entry in the main view, which also has only the web interface in its menu. Grouping is here that entries from the same device (UUID, if not available hostname/IP/port is same) and manually created CUPS queues for such a device grouped with the entry of the device.

With this approach I have updated the Main Window of Printer's panel. It is now able to list entries for Printer Application and all the queues setup within in it. Apart from this, each entry has a link to it's web interface for any tasks. Also, the entries are grouped together as described above. Amongst all, the services are discovered asynchronously and displayed in the existing UI with minimal changes. This has taken a lot of my time since the implementation required me to go through entire printer's panel code to understand how it exactly works.

Here is a screencast of the New Main Panel-


# Addition of Non Driverless Printer in GCC
Modern printers usually are driverless IPP printers, and those get discovered and set up fully automatically with CUPS, no Printer Application is required for them, so it is easy for users to get up and running with them.

Printers which do not do driverless IPP are either legacy printers, the many older printers which got developed before driverless IPP printing existed, and specialty printers. These need Printer Applications. As there will be several different Printer Applications and each one supporting another set of printers it is not trivial for the user to discover available non-IPP-driverless printers and find out which is the Printer Application to use and whether it is already installed.

I have already been working on this segment of GCC since the previous Edition of GSoC and it required the revamp of New Printer Dialog of Printer's Panel which searches for non-IPP-driverless printers, local (USB) and network devices. With a lot of discussion with Marek (See here[https://gitlab.gnome.org/GNOME/gnome-control-center/-/issues/1878]) and trying out several approaches, we have finally been able to freeze our approach. The addition of non-driverless printers with classic drivers is already implemented in the existing code, we will re-use this code, but we will proceed as follows for the whole thing:
1. The "Add Printer" dialog lists all discovered USB and network printers (this we had already before, code for it must exist).
2. The user selects a printer from the list.
3. We try to auto-assign a driver, we start trying to assign a Printer Application, if there is one which supports the printer, we trigger the setup of a queue for this printer by sending an appropriate IPP request to the Printer Application.
4. If we do not find an installed Printer Application supporting the printer, we try to find a classic driver and in case of success we create a classic CUPS queue with this driver. For all this we have already the code. We skip this step if our CUPS is version 3.x or newer or if it is the Snap.
5. If we are still without success, we need to let the user choose manually, offering the manufacturer/model list, with all entries from both the installed Printer Applications and installed classic drivers. We also offer a button then to poll OpenPrinting for suitable Printer Applications and then try to install with cups-pk-helper.

I am currently working on this approach since it was decided just a month ago. However, several things are completed as of it now and only a few weeks of work is required.

## Expansion of DBus Interface of cups-pk-helper
In the step 3 of the above approach we need a way for the GCC to get the available Printer Applications in the system. For this, I have to again discover services of type _ipps-system._tcp and make it available to GCC. Since, a lot of tasks were deligated to cups-pk-helper from GCC, I followed the same approach. I first wrote the synchronous discovery of Printer Application and then made it available via DBus calls. This also took me a bit of time to implement since I had to learn how to make new DBus interface for this call. Apart from this, I have also added an async method in GCC which calls for availing the service created by me in cups-pk-helper. Again, I also learned how to make async calls in C using glib.

## Addition of new IPP Calls in GCC  
PAPPL has made available IPP operations to request for discovered devices and available drivers from Printer Application as server. With this, we can now query a Printer App whether it contains a driver for it or not. Also, we can query for all available drivers available with a Printer Application. We can also request for the addition of a new device with a Printer Application with IPP calls. All of these calls have been implemented in GCC by me. 

# Remaining Work
THe only part which is remaining is the creation of GUI for Step 5 so we can show all the available drivers as described. Then, we have to refine the entire GCC with further suggestion from Marek & Till.
