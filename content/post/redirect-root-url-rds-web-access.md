+++
Categories = ["Tutorials"]
Description = "By default, the Microsoft RDS Web Access root URL does not redirect to the RDS login screen. This can be changed by setting a redirect in IIS."
Tags = ["RDS"]
date = "2016-08-31T09:30:34-06:00"
title = "Redirecting the root RDS Web Access URL to the login screen"

+++

The root RDS Web Access URL (e.g. **https://rds.domain.com**) does not redirect to the RDS login screen by default in a Windows Server 2012R2 RDS farm setup. Thankfully, there is a simple change in Microsoft IIS to enable this functionality.

In the IIS Manager on each RDS Web Access server in your farm, select "Default Web Site" from the left pane, and select "HTTP Redirect" from the "Default Web Site Home" pane.

![RDS Web Access Redirect](/img/20160831-rds-redirect-1.png "RDS Web Access Redirect")

Check "Redirect requests to this destination:" and enter **https://rds.domain.com/RDWeb**. Check "Only redirect requests to content is this directory (not subdirectories)" and set the "Status code" dropdown to **Found (302)**.

![RDS Web Access Redirect](/img/20160831-rds-redirect-2.png "RDS Web Access Redirect")

The root URL **https://rds.domain.com** should now redirect to the RDS Web Access login page.