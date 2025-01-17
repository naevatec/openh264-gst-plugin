OpenH264 plugin for GStreamer
=============================

After dropping support for our old forks of GStreamer 1.8, and moving to the official GStreamer packages provided by Ubuntu, we still need to build some GStreamer plugins that are *not* included by default in the official Ubuntu packages, such as libusrsctp and OpenH264.

In the case of OpenH264, this royalty-free implementation of the H.264 standard is needed to ensure legally safe encoding of H.264 video for users of Kurento, without the need to pay for MPEG-LA licensing costs (for more information see https://www.openh264.org/).

The OpenH264 library is used by Firefox and other open-source software, and Kurento is no exception. OpenH264 itself doesn't contain needed code to integrate with GStreamer as a plugin; instead, a GStreamer plugin is automatically built as part of 'gst-plugins-bad' if the OpenH264 library is found to be installed in the system **at build time**.

This means that the official 'gst-plugins-bad' package from Ubuntu should have been built already with OpenH264 plugin support, but that is not the case. To solve this, we need to download the source code of 'gst-plugins-bad' and re-build after making sure that OpenH264 is installed in the system.

If no OpenH264 is installed, the configure script from 'gst-plugins-bad' prints this info:

    configure: *** checking feature: openh264 library ***
    configure: *** for plug-ins: openh264 ***
    checking for OPENH264... no
    configure: Package openh264 was not found in the pkg-config search path.
    Perhaps you should add the directory containing `openh264.pc'
    to the PKG_CONFIG_PATH environment variable
    Package 'openh264', required by 'world', not found
    configure: *** These plugins will not be built: openh264
    [...]
    configure: *** Plug-ins with dependencies that will NOT be built:
        [...]
        openh264

With OpenH264 installed, the configure output changes to this:

    configure: *** checking feature: openh264 library ***
    configure: *** for plug-ins: openh264 ***
    checking for OPENH264... yes
    configure: *** These plugins will be built: openh264
    [...]
    configure: *** Plug-ins with dependencies that will be built:
        [...]
        openh264

and this file gets generated:

    debian/tmp/usr/lib/x86_64-linux-gnu/gstreamer-1.0/libgstopenh264.so



Build method
------------

In order to build the OpenH264 GStreamer plugin, this project contains a './configure' file that will download and build 'gst-plugins-bad' while ensuring that OpenH264 is already installed in the system.

The usual Debian packaging methods are followed, by defining a 'debian/rules' file which simply calls `dh`, part of the Debhelper tooling.

`dh` will in turn call `dh_auto_configure`, which calls our own `./configure`. Next, it will call `dh_auto_build` which in this case does nothing because all the grunt work is done already by the configure step. Finally, the 'debian/*.install' file will tell `dh_auto_install` where to find the GStreamer plugin file that got built, and package it into a .deb file.



Licensing and distribution
--------------------------

Copyright 2019 Kurento

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
