---
layout: post
title: "Fix SAP Collaboration Window 500 Error"
date: 2024-07-15 08:00:00 -0500
categories: SAP
tags: SAP
image:
 path: /assets/img/headers/SAPLogo.png
---

# Error encountered when installing SAP Collaboration Window for ByDesign.


1. Close Collaboration Window

 
2. Enter the following registry keys

```bash
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319" /v SchUseStrongCrypto /t REG_DWORD /d 1 /f
```

```bash
reg add "HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319" /v SchUseStrongCrypto /t REG_DWORD /d 1 /f
```

3. Restart Collaboration Window