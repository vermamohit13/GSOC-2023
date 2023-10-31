# GSOC-2023
GNOME Control Center: List and handle IPP print services for the New Architecture
Hello, I am Mohit Verma and I was selected as GSOC 2023 contributor in Open Printing @ The Linux Foundation. I have worked on compiling three GSoC's Project which were done on GNOME Settings Printer Panel, listing & handling of IPP print services. Apart from that I have helped in designing modern Architecture of Printer's Panel print serivces & expanding the cups-pk-helper DBus interface. I am currently implementing the new method for handling of non driverless printer in the Printer's Panel.

cups-pk-helper - https://gitlab.freedesktop.org/cups-pk-helper/cups-pk-helper/-/merge_requests/7
GNOME Settings - 

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

With this approach I have updated the Main Window of Printer's panel. It is now able to list Printer Application and all the queues setup within in it. Apart from this, each entry has a link to it's web interface for any tasks. Also, the entries are grouped together as described above. Amongst all, the services are discovered asynchronously and displayed in the existing UI with minimal changes. This has taken a lot of my time since the implementation required me to go through entire printer's panel code to understand how it exactly work.
