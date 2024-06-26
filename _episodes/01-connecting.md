---
title: "Connecting to Cirrus and transferring data"
teaching: 20
exercises: 10
questions:
- "How can I access Cirrus interactively and transfer data?"
objectives:
- "Understand how to connect to Cirrus."
- "Know how to transfer data onto and off of Cirrus efficiently."
keypoints:
- "Cirrus's login address is `login.cirrus.ac.uk`."
- "The password policy for Cirrus is well documented."
- "There are a number of ways to transfer data to/from Cirrus."
---

## Connecting using SSH

The Cirrus login address is

```
login.cirrus.ac.uk
```
{: .language-bash}

Access to Cirrus is via SSH using **both** a time-based code (TOTP) and a passphrase-protected SSH key pair. As an
additional security measure, newly created accounts must also use a one time password retrieved from the SAFE 
web administration service for the first ever login.

## TOTP

A time based one time password (TOTP) is a credential that changes value over time (on Cirrus, the code changes
every 30 seconds). Users link a TOTP app on their mobile device or laptop to their Cirrus account using an
initial *seed* (usually provided by a QR code that you scan but can also be a string of characters) and this
then allows the app to produce codes that match the ones known by Cirrus.

When you create an account on Cirrus, you will need to register TOTP access in a suitable app. Instructions for
doing this [are available in the SAFE documentation](https://epcced.github.io/safe-docs/safe-for-users/#how-to-turn-on-mfa-on-your-machine-account).

You only need to enter the TOTP once every 10 hours for each account and local host that you connect to 
Cirrus from.

## SSH keys

As well as TOTP, users are required to add the public part of an SSH key pair to access Cirrus.
The public part of the key pair is associated with your account using the SAFE web interface.
See the Cirrus User and Best Practice Guide for information on how to create SSH key pairs
and associate them with your account:

* [Connecting to Cirrus](https://docs.cirrus.ac.uk/user-guide/connecting/)

> ## Log in to Cirrus
> Once you have managed to setup your TOTP and SSH key pair try to log into Cirrus for the
> first time using the command:
> 
> ```
> ssh -i /path/to/sshkey username@login.archer2.ac.uk
> ```
>
> The first time you login, you will be prompted to change your password. You will need
> to enter your initial password from SAFE again (this will be referred to as your LDAP
> password). Once you have entered this, you will be prompted to choose a new password
> which you must enter twice. The new password you enter must conform to the
> [Cirrus Password Policy](https://www.archer2.ac.uk/about/policies/passwords_usernames.html)
> (please note that the Cirrus Password Policy is the same as the ARCHER2 Password Policy, hence
> the re-direct to the ARCHER2 webpage).
> Once you have changed the password in this way, you should be able to log on with just
> the SSH key and TOTP combination.
{: .challenge}

## Data transfer services: scp, rsync

Cirrus supports a number of different data transfer mechanisms. The one you choose depends
on the amount and structure of the data you want to transfer and where you want to transfer
the data to. The three main options are:

* `scp`: The standard way to transfer small to medium amounts of data off Cirrus to any other location
* `rsync`: Used if you need to keep small to medium datasets synchronised between two different locations

More information on data transfer mechanisms can be found in the Cirrus User and Best Practice Guide:

* [Data management and transfer](https://docs.cirrus.ac.uk/user-guide/data/)

## Data transfer best practice

There is a lot of information available in the Cirrus documentation on how to transfer data using the
methods above and how to make it efficient in the documentation linked above.

Here are the main points you should consider:

* **Not all data are created equal, understand your data.** Know what data you have. What is your
  critical data that needs to be copied to a secure location? Which data do you need in a different
  location to analyse? Which data would it be easier to regenerate rather than transfer? You should
  create a brief data management plan laying this out as this will allow you to understand which
  tools to use and when.
* **Minimise the data you are transferring.** Transferring large amounts of data is costly in both
  researcher time and actual time. Make sure you are only transferring the data you need to transfer.
* **Minimise the number of files you are transferring.** Each individual file has a static overhead in
  data transfers so it is efficient to bundle multiple files together into a single large
  archive file for transfer.
* **Does compression help or hinder?** Many tools have the option to use compression (e.g. `rsync`,
  `tar`, `zip`) and generally encourage you to use them to reduce data volumes. However, in some cases,
  the time spent compressing the data can take longer than actually transferring the uncompressed
  data; particularly when transferring data between two locations that both have large data transfer
  bandwidth available.
* **Be aware of encryption overheads.** When transferring data using `scp` (and `rsync` over `scp`)
  your data will be encrypted introducing a static overhead per file. This issue can be minimised by
  reducing the number files to be transferred by creating archives. You can also change the encryption
  algorithm to one that involves minimal encryption. The fastest performing cipher that is commonly 
  available in SSH at the moment is generally `aes128-ctr` as most common processors provide a
  hardware implementation.

> ## Creating an uncompressed zip archive and verifying the contents
> Using the documentation above, find the command you would use to create an uncompressed zip archive
> file of all data within a directory called `large_data_output/`. What command would you use to verify
> that the archive file you have created is not corrupt so you can safely delete the original data?
> > ## Solution
> > You use the `zip` command to archive the data. The `-r` option is used to perform the operation
> > recursively on a directory and the `-0` option is used to specify the archive should be uncompressed:
> > ```
> > auser@login01-nmn:~> zip -0r large_data_output.zip large_data_output/
> > ```
> > {: .language-bash}
> > To verify the archive is valid, you would use the `zip` command again, this time with the `-T` 
> > option:
> > ```
> > auser@login01-nmn:~> zip -T large_data_output.zip
> > ```
> > {: .language-bash}
> {: .solution}
{: .challenge}

{% include links.md %}

