---
layout: post
title: "Android SDK setup"
date: 2012-09-25 11:47
comments: true
categories: [Android]
---

{% codeblock setup-sdk.sh %}
wget http://dl.google.com/android/android-sdk_r20.0.3-macosx.zip
tar xvzf android-sdk_r20.0.3-macosx.zip
cd android-sdk_r20.0.3-macosx.zip
./tools/android update sdk --no-ui --filter `./tools/android list sdk | grep 'SDK Platform Android 4.0.3, API 15, revision 3' | cut -c 4-4`
./tools/android update sdk --no-ui --filter `./tools/android list sdk | grep 'Android SDK Platform-tools, revision 11' | cut -c 4-4`
./tools/android update sdk --no-ui --filter `./tools/android list sdk | grep 'Android SDK Tools, revision 19' | cut -c 4-4`
./tools/android update sdk --no-ui --filter `./tools/android list sdk | grep 'ARM EABI v7a System Image, Android API 15, revision 2' | cut -c 3-4`
./tools/android update sdk --no-ui --filter `./tools/android list sdk | grep 'Google APIs, Android API 15, revision 2' | cut -c 3-4`
echo 'export PATH=$PATH:`pwd`/platform-tools:`pwd`/tools' >> ~/.zshrc
echo 'export ANDROID_HOME=`pwd`' >> ~/.zshrc
. ~/.zshrc
{% endcodeblock %}