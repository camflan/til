# How to gain remote access to your mac after restart

When you have FileVault enabled on your mac, you will get locked out of Screen Sharing and/or ssh unless you use an "authenticated restart" which will tell MacOS to automatically unlock FileVault after coming up. Using this command, you will be required to provide your FileVault credentials in order for it to work.

    sudo fdesetup authrestart -delayminutes 0
