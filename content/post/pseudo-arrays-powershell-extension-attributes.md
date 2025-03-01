+++
Categories = ["Guides"]
Description = "Use PowerShell's .Split() function to make Active Directory extension attributes more powerful."
Tags = ["Active Directory","PowerShell","Exchange Online"]
date = "2016-05-10T15:30:21-06:00"
title = "Extension Attribute Pseudo-Arrays with PowerShell"
draft = "true"

+++

Many pieces of our environment key off custom data entered into Active Directory's extension attributes. So many pieces, in fact, we are close to running out of the 15 default extension attributes provided by AD.

I had the need to use several new identifiying attributes as part of our move of students to Office 365. These attributes help us to keep the dire

As part of a massive student email migration to Office 365 at work, we recently implemented a claims provider trust between Active Directory Federation Services and Shibboleth, our student portal identity provider. Students had previously enjoyed single sign-on between the student portal and Google Apps, and we wanted to keep a similar experience moving forward.

ADFS presents both claims provider options (Active Directory and Shibboleth) to the customer by default. A cookie is then set in the browser to remember the choice of provider. In our case, either provider would ultimately work, but we wanted to force all authentication to Shibboleth seamlessly. This is possible and documented on the [Shibboleth wiki](https://wiki.shibboleth.net/confluence/display/SHIB2/MicrosoftInterop).

We used our Citrix Netscaler to accomplish this through the following steps:

