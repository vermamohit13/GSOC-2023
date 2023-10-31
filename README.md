# GSOC-2023
GNOME Control Center: List and handle IPP print services for the New Architecture
Hello, I am Mohit Verma and I was selected as GSOC 2023 contributor in Open Printing @ The Linux Foundation. I have worked on compiling three GSoC's Project which were done on GNOME Settings Printer Panel earlier. Apart from that I have helped in designing modern Architecture of Printer's Panel. I am currently implementing the new method for handling of non driverless printer.

cups-pk-helper -
GNOME Settings - 

# Project Description
Modern printers usually are driverless IPP printers, and those get discovered fully automatically by CUPS; no CUPS queue needs to be explicitly created. Same for remote CUPS printers and also Printer Applications . They are all discovered as IPP print services. And listings of IPP print services have different requirements: One server can have more than one individual print service and these should get listed together. This could be a print queue and a fax out queue of the same multi-function printer, or two physical legacy printers supported by one Printer Application. Also the user interaction coupled to each listing is different. We do not need to configure PPD option settings, but instead, we need access to the IPP service's web administration interface and also to an IPP System Service configuration panel by a simple mouse click. Several parts of this were already coded in previous GSoCs, but we need to get everything smoothly integrated in the “Printers” part of the GNOME Control Center. I will work together with the upstream maintainer of the “Printers” module, Marek Kasik and also with the UI/UX design teams of GNOME and of Canonical.

# Compiling previous GSOC's project




