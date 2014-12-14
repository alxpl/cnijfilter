cnijfilter
==========

Patches for Canon's Linux driver v.3.80-1 so that it can be installed in the latest versions of Fedora with minimal fuss.
According to Canon, this driver supports MP230, MG2200, E510, MG3200, MG4200, iP7200, MG5400 and MG6300 series devices.
Due to licensing restrictions, I can't provide rpm or even source.rpm packages, so a bit of manual labor is involved.
___
##How to install:

1. You need to have autoconf, automake, gcc-c++ and rpmbuild installed. All other dependencies will be pulled in automatically.

2. If you do not have that already, create a folder named **rpmbuild** in your home folder. In it, create two subfolders, one called **SOURCES** and the other one **SPECS**. If your user is called john, there should be a /home/john/rpmbuild/SOURCES/ and a /home/john/rpmbuild/SPECS/.

3. Download the source tarball from [Canon](http://www.canon.co.uk/Support/Consumer_Products/products/printers/InkJet/PIXMA_iP_series/PIXMA_iP7250.aspx?type=download&language=&os=Linux) and place it in the SOURCES subfolder. Do the same for the patch (cnijfilter-3.80-1.f20.patch). Now download the spec file (cnijfilter-common.spec) and put it in SPECS.

4. As root, install dependencies:

  `yum-builddep rpmbuild/SPECS/cnijfilter-common.spec`
  
  (or with dnf: *dnf builddep rpmbuild/SPECS/cnijfilter-common.spec*)
4. As your regular user, build the rpms from the specfile:

  `rpmbuild -bb rpmbuild/SPECS/cnijfilter-common.spec --define="MODEL [Model Name]" --define="MODEL_NUM [Model ID]" --with build_common_package`

  Where model name and ID may be one of the following (for v3.80-1):

      [Printer Model Name]        [Printer Model ID]
         mp230                       401
         mg2200                      402
         e510                        403
         mg3200                      404
         mg4200                      405
         ip7200                      406
         mg5400                      407
         mg6300                      408
  If immediately after invoking rpmbuild it complains about a missing source or patch, you must have misplaced something. Check you paths and spelling.

6. When it's done, you will get two rpms plus a debuginfo one (we don't want the debuginfo) in rpmbuild/RPMS/{arch}: **cnijfilter-common-3.80-1.{dist}.{arch}.rpm** and **cnijfilter-{printer model}-3.80-1.{dist}.{arch}.rpm**. Install them both in this order (ideally via yum or dnf) and you're good to go.

If your printer is connected over the network, you can run

`cnijnetprn --search auto`

which after a while will give you something like this:

`networkcnijnet:/00-1A-2B-3C-4D-5E “Canon iP7200 series” “Canon-iP7200-series_00-1A-2B-3C-44-5F″`

The first part *networkcnijnet:/00-1A-2B-3C-4D-5E* is the device uri. You can use that when you set up your printer in your DE's printer configuration tool, or you can simply run (as root):

`lpadmin -p canonip7200 -m canonip7200.ppd -v cnijnet:/00-1A-2B-3C-44-5F -E`

*_Note:_ Don't just guess the name for the ppd file, look in /usr/share/cups/model/* 
