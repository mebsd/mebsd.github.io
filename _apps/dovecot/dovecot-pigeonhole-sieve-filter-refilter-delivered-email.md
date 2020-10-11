---
id: 1065
title: 'sieve-filter delivered email'
date: 2012-10-17T16:47:52+01:00
author: Jake
layout: default
guid: http://mebsd.com/?p=1065
parent: Dovecot
grand_parent: apps
permalink: /configure-freebsd-servers/dovecot-pigeonhole-sieve-filter-refilter-delivered-email.html
tags:
  - dovecot
  - freebsd server
  - sieve
---
## Dovecot: Pigeonhole sieve-filter refilter delivered email

After adjusting your sieve rules or adding them for the first time on an existing mailbox you will probably want to *refilter* any existing delivered mail so they are moved to the correct IMAP folders. The dovecot wiki suggests to redeliver the mail to yourself to run it though the new filters, the method is outlined here <a href="http://wiki2.dovecot.org/HowTo/RefilterMail" target="_blank">http://wiki2.dovecot.org/HowTo/RefilterMail</a>. There is another way of doing the same thing which is far less messy, using an inbuilt tool, **sieve-filter**.

### Introducing sieve-filter

As of dovecot **pigeonhole v0.3** a new script, **sieve-filter**, has been added to do just this. You simply point it at your sieve script and a source mailbox and it will do the rest. Before we look into how to use this script, I am first going to reiterate a section of the man page.

#### From sieve-filter(1) man page

```
CAUTION
       Although this is a very useful tool, it can also  be  very  destructive
       when  used  improperly. A small bug in your Sieve script in combination
       with the wrong command line options could cause it to discard the wrong
       e-mails. And, even if the source-mailbox is opened in read-only mode to
       prevent such mishaps, it can still litter other mailboxes with spurious
       copies  of  your  e-mails if your Sieve script decides to do so. There-
       fore, users are advised to read this manual carefully and  to  use  the
       simulation mode first to check what the script will do. And, of course:

       MAKING A BACKUP IS IMPERATIVE FOR ANY IMPORTANT MAIL!

```

So please be careful you could really mess up you mailbox. I suggest you setup a test mailbox with some basic sieve rules, send some mail to it and test it without putting your real mailbox at risk.

### Setting up the test environment

First things first, SSH to you mail server, check you have **pigeonhole v0.3** or newer installed and that the script **sieve-filter** is available. If you are new to dovecot sieve you can find in the FreeBSD ports tree at **mail/dovecot2-pigeonhole**. You will, of course, need to be using dovecot v2 or newer also, the port can be found at **mail/dovecot2**.

The mail server in these examples is using **dovecot-2.1.10** and **dovecot-pigeonhole-0.3.3**. Sieve is configured to store the rules in a text file in the mailbox root, `Maildir/sieve/rules.sieve`. If you are new to configuring sieve rules I recommend installing <a href="http://roundcube.net/" target="_blank">roundcube webmail</a> and enable the sieve plugin, this makes managing your rules very easy.

### Create a test mailbox

For these examples we will be using a mailbox called **sieve@example.com** with a username of **sieve**. The mail directory will be located in `/var/mail/example.com/sieve/Maildir`. An IMAP directory has been created called **`sieve-test`** and a simple sieve rule has been added to match a subject of **`move to folder sieve-test`** and move the email to this IMAP folder.

```sh
require ["fileinto"];
# rule:[sieve-test]
if header :contains "Subject" "move to folder sieve-test"
{
	fileinto "INBOX.sieve-test";
	stop;
}

```

Now the mailbox is configured we will send the following test emails to it.

#### Email #1

```
Subject: land in inbox
Body: Test email to land in Inbox.
```

#### Email #2

```
Subject: move to folder sieve-test
Body: Test email to land in sieve-test folder.
```

#### Email #3

```
Subject: this will be refiltered
Body: Test email to be refiltered post delivery.
```

#### Email #4

```
Subject: this is spam
Body: Test email spam to be deleted.
```

After these test emails have been delivered we can see **email #1** has landed in the inbox, **email #2** matched the sieve rule and was moved the folder **`sieve-test`**. **Email #3 and #4** have landed in inbox as expected, no filter rule currently exists.

### Adding a new sieve rule

To test refiltering delivered mail we will add a new sieve rule. At this point we will only add the rule to match **email #3** with subject *'this will be refiltered'*, we will cover removing the test spam email later on. This is what our sieve rules file now looks like.

```sh
require ["fileinto"];
# rule:[sieve-test]
if header :contains "Subject" "move to folder sieve-test"
{
	fileinto "INBOX.sieve-test";
	stop;
}
# rule:[refilter]
if header :contains "Subject" "this will be refiltered"
{
	fileinto "INBOX.sieve-test";
	stop;
}

```

### Running sieve-filter for the first time

Now that we have something to refilter in our mailbox we can run **sieve-filter** against it. By default **sieve-filter** runs in read only mode, for obvious safety reasons. We will not change this default behaviour to begin with, as we want to make sure it's going to do what we expect.

First we'll look at the options we need to give to **sieve-filter** and break down what they do.

#### sieve-filter usage output

```
# sieve-filter
Usage: sieve-filter [-c <config-file>] [-C] [-D] [-e] [-m <default-mailbox>]
                    [-P <plugin>] [-q <output-mailbox>] [-Q <mail-command>]
                    [-s <script-file>] [-u <user>] [-v] [-W] [-x <extensions>]
                    <script-file> <source-mailbox> [<discard-action>]
```

#### Option: -u

```
-u user
              Run the Sieve script for the given user.
```

This is option specifies which dovecot user's mailbox you are refiltering, this must match the login name of the IMAP account.

#### Option: -C

```
-C     Force compilation. By default, the compiled binary is stored  on
              disk.  When  this  binary  is found during the next execution of
              sieve-filter and its modification time is more recent  than  the
              script  file,  it  is used and the script is not compiled again.
              This option forces the script to be compiled, thus ignoring  any
              present  binary.  Refer  to sievec(1) for more information about
              Sieve compilation.
```

When **sieve-filter** is runs it uses a complied version of the sieve rule set, this is done for speed. To ensure the sieve rule set is always recompliled before the refilter we use this option.

#### Option: -v

```
-v     Produce verbose output during filtering.
```

This option is self explanatory, we use to make sure we see everything that's going on. I recommend piping the output to a file, in-case something does go wrong, you will have something to go on.

#### Option: -e <span style="color: #dd0000;">*Warning*</span>

```
-e     Turns on execution mode. By default,  the  sieve-filter  command
              runs  in  simulation  mode  in which it changes nothing, meaning
              that no mailbox is altered in any way and no  actions  are  per-
              formed.  It  only prints what would be done.  Using this option,
              the  sieve-filter  command  becomes  active  and  performs   the
              requested actions.
```

When we are ready to run **sieve-filter** for real and actually make changes to our mailbox we will use this option.

#### Option: -W <span style="color: #dd0000;">*Warning*</span>

```
-W     Enables write access to the source-mailbox. This allows (re)mov-
              ing the messages from the source-mailbox,  changing  their  con-
              tents, and changing the assigned IMAP flags and keywords.
```

When using the **`-e`** option, refiltered mails will be copied to the new destination, but with out this option they wont be removed from the source mailbox. If you don't use this option you will find yourself with a lot of duplicate email.

#### Argument: <script-file>

```
script-file
              Specifies the Sieve script to (compile and) execute.

              Note  that this tool looks for a pre-compiled binary file with a
              .svbin extension and with basename and  path  identical  to  the
              specified  script. Use the -C option to disable this behavior by
              forcing the script to be compiled into a new binary.
```

The path to your sieve rule set.

#### Argument: <source-mailbox>

```
source-mailbox
              Specifies the source mailbox containing the  messages  that  the
              Sieve filter will act upon.

              This  is  the  name  of  a  mailbox, as visible to IMAP clients,
              except in UTF-8 format. The hierarchy separator between a parent
              and  child  mailbox  is commonly '/' or '.', but this depends on
              your selected mailbox storage format  and  namespace  configura-
              tion. The mailbox names may also require a namespace prefix.

              This  mailbox is not modified unless the -W option is specified.
```

The name of the source mailbox, as the man page extract says, it is the mailbox as it is visable to IMAP clients **Not** the full path to the mail directory on your server. E.g. `INBOX` or `INBOX.folder`, **\*not\*** `/var/mail/example.com/sieve/Maildir/cur`.

### Constructing the command and dry run

It's finally time to run our first refilter test! We will be doing a dry run first, let's have a look at what this looks like.

```
# sieve-filter -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX'
>> Filtering message:

  ID:      <507EBEC5.8060608@example.com>
  Date:    Wed, 17 Oct 2012 15:20:02 +0100
  Size:    2702 bytes
  Subject: land in inbox

Performed actions:

  (none)

Implicit keep:

 * store message in folder: INBOX

>> Filtering message:

  ID:      <507EBEC5.8060608@example.com>
  Date:    Wed, 17 Oct 2012 15:20:53 +0100
  Size:    1765 bytes
  Subject: this will be refiltered

Performed actions:

 * store message in folder: INBOX.sieve-test

Implicit keep:

  (none)

>> Filtering message:

  ID:      <507EBEDC.4000401@example.com>
  Date:    Wed, 17 Oct 2012 15:21:16 +0100
  Size:    1753 bytes
  Subject: this is spam

Performed actions:

  (none)

Implicit keep:

 * store message in folder: INBOX
```

As we can see from the output, this would of worked as expected. If we look at the **`Performed actions'** section for the mail with subject **`this will be refiltered`** we can see that it would of been moved to **INBOX.sieve-test**. The other 2 emails, which have no matching rules, have no **`Performed actions`** and instead have an **`Implicit keep`** which leaves them in **INBOX**.

At this point we could safely add the **`-e`** and **`-W`** options to actually perform the move.

```sh
# sieve-filter -e -W -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX'
info: filtering: [Wed, 17 Oct 2012 15:20:02 +0100; 2702 bytes] `land in inbox'.
info: msgid=<507EBEC5.8060608@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.
info: filtering: [Wed, 17 Oct 2012 15:20:53 +0100; 1765 bytes] `this will be refiltered'.
info: msgid=<507EBEC5.8060608@example.com>: stored mail into mailbox 'INBOX.sieve-test'.
info: message expunged from source mailbox upon successful move.
info: filtering: [Wed, 17 Oct 2012 15:21:16 +0100; 1753 bytes] `this is spam'.
info: msgid=<507EBEDC.4000401@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.

```

Check your mailbox, and we can see the mail **`this will be refiltered`** has been removed from **INBOX** and moved into **INBOX.sieve-test**.

### Discarding email

Rememeber that lonely test spam email sitting in our inbox? It's now time to get rid of it. Let's add a new rule to our sieve rule set to discard it.

```sh
require ["fileinto"];
# rule:[sieve-test]
if header :contains "Subject" "move to folder sieve-test"
{
	fileinto "INBOX.sieve-test";
	stop;
}
# rule:[refilter]
if header :contains "Subject" "this will be refiltered"
{
	fileinto "INBOX.sieve-test";
	stop;
}
# rule:[spam]
if header :contains "Subject" "this is spam"
{
	discard;
	stop;
}

```

#### Dry run on the mailbox to test the new rule:

```sh
# sieve-filter -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX' keep
>> Filtering message:

  ID:      <507EBEC5.8060608@example.com>
  Date:    Wed, 17 Oct 2012 15:20:02 +0100
  Size:    2702 bytes
  Subject: land in inbox

Performed actions:

  (none)

Implicit keep:

 * store message in folder: INBOX

>> Filtering message:

  ID:      <507EBEDC.4000401@example.com>
  Date:    Wed, 17 Oct 2012 15:21:16 +0100
  Size:    1753 bytes
  Subject: this is spam

Performed actions:

 * discard

Implicit keep:

  (none)

```

The only change we need to make to our previous **sieve-filter** command is to add a discard action to the end. We have a number of different actions we can apply to discared mails, the default action is **`keep`**, if you don't specify a discard action **`keep`** is implicitly implied. When **`keep`** is used the mail is marked to be descarded but it is not actually removed, as we can see in the last 2 lines of this live run.

```sh
# sieve-filter -e -W -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX' keep
info: filtering: [Wed, 17 Oct 2012 15:20:02 +0100; 2702 bytes] `land in inbox'.
info: msgid=<507EBEC5.8060608@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.
info: filtering: [Wed, 17 Oct 2012 15:21:16 +0100; 1753 bytes] `this is spam'.
info: msgid=<507EBEDC.4000401@example.com>: marked message to be discarded if not explicitly delivered (discard action).
info: message left in source mailbox.
```

So what options do we have when discarding mails, this is what the man page says about it.

#### Argument: <discard-action>

```
discard-action
              Specifies what is done with messages in the source-mailbox  that
              where  not  kept  or  otherwise stored by the Sieve script; i.e.
              those messages that would normally be  discarded  if  the  Sieve
              script  were executed at delivery.  The discard-action parameter
              accepts one of the following values:

              keep (default)
                     Keep discarded messages in source mailbox.

              move mailbox
                     Move discarded messages to the indicated mailbox. This is
                     for  instance useful to move messages to a Trash mailbox.
                     Refer to the explanation of the  source-mailbox  argument
                     for more information on mailbox naming.

              delete Flag discarded messages as DELETED.

              expunge
                     Expunge   discarded  messages,  meaning  that  these  are
                     removed irreversibly when the tool finishes filtering.

              When the -W option  is  not  specified,  the  source-mailbox  is
              immutable  and  the specified discard-action has no effect. This
              means that messages are at most copied to  a  new  location.  In
              contrast,  when  the -W is specified, messages that are success-
              fully stored somewhere else  by  the  Sieve  script  are  always
              expunged from the source-mailbox, with the effect that these are
              thus moved to the new location. This happens irrespective of the
              specified  discard-action. Remember: only discarded messages are
              affected by the specified discard-action.

```

So our options are **keep, move, delete and expunge**. Let's see some examples.

#### Discard action: move

In this example we are moving our discared messages to our **Trash** folder, **INBOX.Trash**.

```sh
# sieve-filter -e -W -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX' move INBOX.Trash
info: filtering: [Wed, 17 Oct 2012 15:20:02 +0100; 2702 bytes] `land in inbox'.
info: msgid=<507EBEC5.8060608@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.
info: filtering: [Wed, 17 Oct 2012 15:21:16 +0100; 1753 bytes] `this is spam'.
info: msgid=<507EBEDC.4000401@example.com>: marked message to be discarded if not explicitly delivered (discard action).
info: message in source mailbox moved to mailbox 'Trash'.

```

#### Discard action: delete

In this example we are marking the email as **DELETED**.

```sh
# sieve-filter -e -W -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX' delete
info: filtering: [Wed, 17 Oct 2012 15:20:02 +0100; 2702 bytes] `land in inbox'.
info: msgid=<507EBEC5.8060608@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.
info: filtering: [Wed, 17 Oct 2012 16:06:28 +0100; 1755 bytes] `this is spam'.
info: msgid=<507EC974.8090309@example.com>: marked message to be discarded if not explicitly delivered (discard action).
info: message flagged as deleted in source mailbox.

```

To show is has worked, we can telnet on to our IMAP server and look at the flags.

```sh
# telnet localhost 143
Trying 127.0.0.1...
Connected to localhost.
Escape character is '^]'.
* OK [CAPABILITY IMAP4rev1 LITERAL+ SASL-IR LOGIN-REFERRALS ID ENABLE IDLE STARTTLS AUTH=PLAIN AUTH=LOGIN AUTH=DIGEST-MD5 AUTH=CRAM-MD5] MailServer ready.
a login sieve password
a OK [CAPABILITY IMAP4rev1 LITERAL+ SASL-IR LOGIN-REFERRALS ID ENABLE IDLE SORT SORT=DISPLAY THREAD=REFERENCES THREAD=REFS THREAD=ORDEREDSUBJECT MULTIAPPEND UNSELECT CHILDREN NAMESPACE UIDPLUS LIST-EXTENDED I18NLEVEL=1 CONDSTORE QRESYNC ESEARCH ESORT SEARCHRES WITHIN CONTEXT=SEARCH LIST-STATUS SPECIAL-USE QUOTA] Logged in
a1 select INBOX
* FLAGS (Answered Flagged Deleted Seen Draft)
* OK [PERMANENTFLAGS (Answered Flagged Deleted Seen Draft *)] Flags permitted.
* 2 EXISTS
* 0 RECENT
* OK [UIDVALIDITY 1349723219] UIDs valid
* OK [UIDNEXT 6] Predicted next UID
* OK [NOMODSEQ] No permanent modsequences
a1 OK [READ-WRITE] Select completed.
a2 fetch 2 full
* 2 FETCH (FLAGS (Deleted Seen) INTERNALDATE "17-Oct-2012 16:06:35 +0100" RFC822.SIZE 1755 ENVELOPE ("Wed, 17 Oct 2012 16:06:28 +0100" "this is spam" (("Jake Smith" NIL "jake" "example.com")) (("Jake Smith" NIL "jake" "example.com")) (("Jake Smith" NIL "jake" "example.com")) ((NIL NIL "sieve" "example.com")) NIL NIL NIL "<507EC974.8090309@example.com>") BODY ("text" "plain" ("charset" "ISO-8859-1") NIL NIL "7bit" 14 1))
a2 OK Fetch completed.
a3 logout
* BYE Logging out
a3 OK Logout completed.
Connection closed by foreign host.

```

#### Discard action: expunge

In this example we will irreversibly remove the mail.

```sh
# sieve-filter -e -W -v -C -u sieve /var/mail/example.com/sieve/Maildir/sieve/rules.sieve 'INBOX' expunge
info: filtering: [Wed, 17 Oct 2012 15:20:02 +0100; 2702 bytes] `land in inbox'.
info: msgid=<507EBEC5.8060608@example.com>: left message in mailbox 'INBOX'.
info: message kept in source mailbox.
info: filtering: [Wed, 17 Oct 2012 16:06:28 +0100; 1755 bytes] `this is spam'.
info: msgid=<507EC974.8090309@example.com>: marked message to be discarded if not explicitly delivered (discard action).
info: message expunged from source mailbox.

```

#### N.B.

On a final note, **sieve-filter** can run out of memeory when running in live mode with very large mailboxes. The delete from source directory **`-W`** does not happen until all mail has been copied to the new desination folders, a crash like this will leave you with duplicate email. Be careful!