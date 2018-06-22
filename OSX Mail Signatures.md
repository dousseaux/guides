# HTML Mail Signatures MAC

Pedro Dousseau, January 2018

#### Create temporary signatures on app

Create a temporary signature for the account you want on the app, so we can update the file created with our HTML code.

#### Find signatures folder

The version may differ. Where is written V5 can change.

````
$ cd /Users/dousseau/Library/Mail/V5/MailData/Signatures
````

#### Locate the file and edit

The file have encoded names. Open them and locate the one that you want to edit. The files have the .mailsignature extension.

Once you located the file, edit it. You need to unlock, edit and lock again, as shown below:

```
chflags nouchg filename.mailsignature
nano filename.mailsignature
chflags uchg filename.mailsignature
```

#### My signature file names

- __pedro@dwsensors.com:__ 82FC28DD-6485-40F8-8EA4-9A1F0931DBAC
- __pedro@sigmasensors.com.br:__ CF81D91B-7905-444D-A37D-CDAA0ABB24A7
- __ti@sigmasensors.com.br:__ 9990DB04-6989-440F-8A8C-5007A7B943C2
- __pedro@duxdata.com.br:__ CEF411B4-BD53-4F39-BB83-9389288C8629