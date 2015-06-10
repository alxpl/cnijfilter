cnijfilter
==========

Modifications for Canon's Linux driver **v.3.80-1** so that it can be installed in the latest versions of Fedora with minimal fuss.
According to Canon, this driver supports MP230, MG2200, E510, MG3200, MG4200, iP7200, MG5400 and MG6300 series devices.

I have corrected quite a number of errors and warnings during build/compilation. Checking with rpmlint, it is now down to 1 error and 8 warnings for the common package and 7 errors, 6 warnings for the printer-specific package. The rpms provided by Canon have 6 errors, 15 warnings and 177 errors, 51 warnings, respectively. Oh and they fail to install because of missing dependencies... Most of the changes in the patch file, particularly those concerning ipp and libpng, came from various, online sources and I adapted them to this version of the driver. Kudos to everyone that cleared the path for me. As for the changes in the spec file, they are the result of many hours of RTFM. I'm still learning, so feel free to drop me a line if you spot any shortcomings.

###Disclaimer:
Due to licensing restrictions, I can't provide rpm or even srpm packages, so a bit of manual labor is involved.

Do not think that I do this for a living, or that I have the time to tackle other drivers, or other versions of Canon's driver. This has been a sort of an exercise for me, plus an online note to self. If someone happens to find this useful, then I'll be happy. 

I have tested my packages on F20, F21 and F22, both x86_64 and i686. I have no idea if these modifications might work with previous or later versions of the driver. I can not guarantee that my patches will work for you, even if you are on the same distro version and architecture. I can not even guarantee that they won't murder you in your sleep.
___
##How to install:

1. You need to have autoconf, automake, gcc-c++ and rpmbuild installed. All other dependencies will be pulled in automatically.

2. If you do not have that already, create a folder named **rpmbuild** in your home folder. In it, create two subfolders, one called **SOURCES** and the other one **SPECS**. If your user is called john, there should be a /home/john/rpmbuild/SOURCES/ and a /home/john/rpmbuild/SPECS/ (you could also have rpmdev-setuptree from the rpmdevtools package do that for you).

3. Download the source tarball from [Canon](http://www.canon-europe.com/support/consumer_products/ "Canon Europe - Consumer Products Support") and place it in the SOURCES subfolder. Do the same for the patch (cnijfilter-3.80-1.libraries.patch). Now download the spec file (cnijfilter-common.spec) and put it in SPECS.

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

*_Note:_ Don't just guess the name for the ppd file, look in /usr/share/cups/model/.* 
