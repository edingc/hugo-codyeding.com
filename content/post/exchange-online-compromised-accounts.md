+++
Categories = ["Quick Notes"]
Description = "Identify and remedy compromised Exchange Online accounts."
Tags = ["Computer Security","PowerShell","Exchange","Office 365"]
date = "2016-08-29T08:34:21-06:00"
title = "Dealing with compromised accounts in Exchange Online"

+++

We have lately had a rash of compromised email accounts in our Office 365 Exchange Online infrastructure. It appears a well-crafted phishing email caught at least a small percentage of our 100,000-plus mailboxes.

The outbound SPAM protection in Office 365 and Exchange Online is very robust. Suspected SPAM messages are sent through a high-risk pool of IP addresses, and accounts are limited to 10,000 outbound messages per day before being blocked by the anti-SPAM intelligence. A support ticket must be filed with Microsoft to reactivate an account once it is blocked from sending outbound mail.

Normally, the anti-SPAM alerts received when an account hits the outbound message limit are sufficient for administrative notification. The most recent set of spammers, however, have been intelligently working underneath this notification system by sending less than 10,000 messages daily. The spammers instead cover their tracks by setting up email forwarding or an inbox rule to hide any bouncebacks from the slew of outbound junk.

Email forwarders victimized the most recent compromised accounts. These accounts came into the help desk with the same symptom of not receiving email messages. A look at the mailbox through Exchange Online PowerShell reveals the cause:

![PowerShell Prompt](/img/20160829-compromised-account.png "Compromised Account")

The spammer set the mailbox to forward all mail to an external address under their control, thereby hiding the nefarious activities.

To remove the forwarder, run the following in PowerShell:

    Set-Mailbox jsmith@domain.com -ForwardingSmtpAddress $Null

In some cases, spammers will instead setup an inbox rule to hide their activity. View all inbox rules for a mailbox through PowerShell by running the following:

    Get-InboxRule -Mailbox jsmith@domain.com
	
For a clean mailbox, this should return nothing or return valid inbox rules created by the customer.

It never hurts to educate your customers on never giving out login information through an email!
