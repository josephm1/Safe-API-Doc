# SAFE API Overview

Development Guides by MaidSafe:

* [SAFE Network API - Getting started ](https://forum.safedev.org/t/safe-network-api-getting-started-draft/726/1)
* [How to develop for the SAFE Network ](https://forum.safedev.org/t/how-to-develop-for-the-safe-network-draft/843)

SAFE API Documentation

* [Node.js docs ](http://docs.maidsafe.net/safe_app_nodejs)
* [JavaScript docs ](http://docs.maidsafe.net/beaker-plugin-safe-app/)

Documentation and explanations of the SAFE Network by the community:

* [The SAFE Network Primer](https://maidsafe.net/docs/Safe%20Network%20Primer.pdf)
* [SAFE Network Architecture ](https://safe-network-explained.github.io/architecture)
* [The Documentation Topic ](https://safenetforum.org/t/the-documentation-topic/16149)
* [Introduction to the SAFE Network ](https://medium.com/@luandro/introduction-to-the-safe-network-33d8641b2dee)
* [Developing for the SAFE Browser ](https://medium.com/@luandro/developing-for-the-safe-browser-212decd575a3)

## SAFE Browser and the Authenticator

The SAFE Browser is developed by MaidSafe and provides access to the SAFE Network and websites on the SAFE Network.

It has an integrated feature called Authenticator. The Authenticator is used to for signing in and creating an account
on the SAFE Network.

### SAFE Accounts

An account on the SAFE Network is needed for uploading data. The current Test Network has restrictions on how much data
you can `PUT` on the Network. It measures your use in `PUTS`. Each action preformed on the Network other than `READ`
costs `PUTS`. Each account is limited to 1000 `PUTS`. This will change when SAFE Coin is implemented.

## SAFE API Flow

![SAFE API Flow](SAFE-API-Flow.png)

### Handles and the SAFE API

TODO: Needs to be verified

The SAFE API uses handles frequently. A handle is a reference to a resource. An example of a resource can be an
authentication token (App Handle) or the location of a Mutable Data Structure (Mutable Data Handle). These handles are
different for each session.

It is recommended to free handles when they are no longer needed or in use.

### Initialise

TODO: Needs to be verified

The first step to interact with the SAFE Network is to initialise your application. You need to pass information about
your application to the API, including App ID, Name and Vendor.

### Connect and Authorise

TODO: Needs to be verified

There are two network states in the SAFE API: Connected and Authorised.

The user doesn't need to be signed in to connect. When you are connected you can `READ`, this means you can only read
Public data and aren't able to preform actions such as: uploading to a Immutable or Mutable Data Structure, modifying
entries, inserting permissions or getting Keys.

The user needs to be signed to authorise. To authorise you need to specify which containers and permissions your app
wants to access and whether you would like to use your own App Container. When you are authorised you have full access
to the SAFE API and are able to preform all actions.

## Data on the SAFE Network

There are only two generic types of data on the SAFE Network, Mutable Data (can change) and Immutable Data (can't
change).

## Mutable Data

TODO: Needs to be verified

The Mutable Data on the SAFE Network is saved as a key-value store. This means requested data from the network will
return an entry like this `key 1: value 1`. This is called an entry. A Mutable Data Structure is composed of these
entries. Entries can be inserted, updated or removed.

A Mutable Data Structure can hold up to a 1000 entries and contain a maximum of 1 MB of data.

To create and retrieve a Mutable Data Structure handle you will need to pass three parameters to it: your app handle, an
input such as an array or string and its type tag.

There are two types of Mutable Data Structures: Public (websites) and Private (private files).

Mutable Data Structures support: inserting entries, mutating entries and setting permissions.

### Type Tags

A type tag is a number that is used to differentiate between the different levels of the Mutable Data Structures. This
means that two of the same hashes can reference different Mutable Data Structures if they use different type tags.

The type tags 0-10,000 are **reserved by MaidSafe**.

The following type tags are currently in use in Alpha 2:

| Type tag |     Service     |
| -------- | :-------------: |
| 15001    |    Public ID    |
| 15002    |  Service Name   |
| 15003    |    Email ID     |
| 15004    |  Email Archive  |
| 54321    | A Safey website |

RFC: [Reserved Names](https://github.com/maidsafe/rfcs/blob/master/text/0003-reserved-names/0003-reserved-names.md)

### Shared Mutable Data

TODO: Need to explain better and verify <br>TODO: This is probably wrong

When you set up a Private or Public Mutable Data Structure it gives permissions to an application key. That key is
derived from the account and the application used to set up the Mutable Data Structure.

This means only that application in combination with the account has permissions for that Mutable Data Structure.

To enable permissions from a different application Shared Mutable Data is used to request permissions.

<br>SAFE Dev Topic:
[Shared Mutable Data Reference](https://forum.safedev.org/t/safe-network-api-getting-started-draft/726) <br> SAFE Dev
Topic:
[Shared Mutable Data Discussion](https://forum.safedev.org/t/need-help-with-shared-md-authorisesharemd-function/989)
<br>Video: [Shared Mutable Data by Hunter Lester](https://youtu.be/E80IH8mCKaw)

### Permissions

TODO: Needs to be verified

Mutable Data Structures can have permissions set to them. This means you can enable or disable permissions for yourself, another user or for everyone. These permissions include `Read`, `Delete`, `Insert`, `Update` and `ManagePermissions`.

`Read`: Access to view the Mutable Data Structure
<br>
`Delete`: Remove the value for a specific entry from the Mutable Data Structure
<br>
`Insert`: Inserting a new entry to the Mutable Data Structure
<br>
`Update`: Change the value for a specific entry from the Mutable Data Structure
<br>
`ManagePermissions`: Ability to specify permissions to the Mutable Data Structure

## Immutable Data

TODO: Needs to be verified

The Immutable Data’s address on the network
is derived from the hash of its file content. This means the file cannot be edited in any way after it
has been uploaded as any change to the file would alter the hash.

Immutable Data holds a single value and is also limited to 1 MB. It is possible to store files larger than 1 MB by splitting up the file into chunks with those
chunks stored as separate Immutable Data entities. They can then be retrieved using a data map which can keep track of the Immutable Data’s addresses on the network.

### Cipher Opt

TODO: Needs to be verified

Immutable Data can be stored as:

| Symmetric                                                             | Asymmetric                                                            |                                       Plain Text                                        |
| --------------------------------------------------------------------- | --------------------------------------------------------------------- | :-------------------------------------------------------------------------------------: |
| Only for user who made the data and uses only a single encryption key | Can be used for more than one person and uses a secret and public key | This data is not encrypted and accessible for all who know the Immutable Data's Address |

## Network File Storage (NFS)

TODO: Needs to be verified

It is possible to save data using a combination of Mutable and Immutable Data to create an emulation file-system on top
of the network called NFS.

NFS uses Immutable Data to save a file content, it then creates an entry in a given Mutable Data Structure. It uses the
file name as the entry's key and the Immutable Data's address as the entry's value.

This enables you to update or change a file by uploading a new Immutable Data file and then updating the file's address
in the entry's value. This Mutable Data Structure can be used again to store more NFS files.

### Hierarchy File-System Emulation

When dealing with folders and files being uploaded that are within subdirectories it is recommended to use a slash (/)
to separate the subdirectory and the file name.

This means the key of a Mutable Data Structure containing files looks like this:
`filepath/subdir/index.html`.

## Cryptography

TODO: Still in progress
<br>TODO: Needs to be verified
<br>TODO: Use cases and examples
<br>[Sodium crypto library docs](https://download.libsodium.org/doc/)

### Hash

This is a 32 length string which is derived from an input of data such as a file or a string. This is not unique to the
user i.e. if I hash 'hello world' and you hash 'hello world' we will both get the same value back.

### Nonce

This is a random number that is generated for once off purposes. It is used in creating Private Mutable Data Structures.

### Comparisons between different cryptographic components

| Sign Key                                       |         Encryption keys          |
| ---------------------------------------------- | :------------------------------: |
| Used to verify data coming from a known sender | Used to encrypt and decrypt data |

TODO: Not sure yet what to do for single key

| Key Pairs                                                               |                                          Single Key                                           |
| ----------------------------------------------------------------------- | :-------------------------------------------------------------------------------------------: |
| Used to derive both the secret and public key for asymmetric encryption | Is either a secret or a public key \ Same key used to encrypt and decrypt data i.e. symmetric |

| Public                                         |                           Secret                           |
| ---------------------------------------------- | :--------------------------------------------------------: |
| A key made public that is used to encrypt data | A key that must be kept secret and is used to decrypt data |

| Get App                                              | Generate                          |                From Raw                 |
| ---------------------------------------------------- | --------------------------------- | :-------------------------------------: |
| Generates the key from the user and application data | Generates a new key pair randomly | Generates a new key from a given string |

| Encrypted                                        |                                                 Encrypted Sealed                                                 |
| ------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------: |
| Data just encrypted with a public encryption key | This data is encrypted with a public encryption key and then encrypted again with a given string called a cipher |

## Containers

Containers are Mutable Data Structures that are generally used for storing particular types of data i.e. `_music` stores
music and sound files.

[More info about containers here](https://github.com/maidsafe/rfcs/blob/master/text/0046-new-auth-flow/containers.md)

### Root Container

TODO: Could be made simpler

The root container is the main entry point for the user and apps to interact with. It is a locally encrypted container
stored at a random location on the network known only to the user, that generally only the authenticator has write
access to.

Its reference will be stored in the users session packet on account creation. Keys starting with an underscore ( \_ )
are reserved for internal usage by the authenticator, while the authenticator may also allow the creation of other keys
later.

The authenticator has another mapped data container which holds the encryption keys per each container, which is locally
encrypted with a separate key that only the authenticator has access to and will never be shared. This is called:
`RootKeysContainer`.

#### Session Packet

TODO

### Default containers

The authenticator will create the following default containers within the root container when you create an account:

* `_apps/net.maidsafe.authenticator/`
* `_documents`
* `_downloads`
* `_music`
* `_pictures`
* `_videos`
* `_public`
* `_publicNames`

All data stored in these containers are stored randomly on the Network and should be encrypted. There are two
special cases, `_public` - to
store unencrypted data (the
container is encrypted even
if its contents are not), and `_ publicNames` - to store Public
IDs which can be looked up for
public information.

### App container

The app container is created for websites if they request their to have own container with their ID being used to
identify it. This means the app container name looks like `apps/id.example.net`.

## Decentralised Naming System (DNS)

TODO: Explain thoroughly
The Web Hosting Manager and SAFE Browser Overviews below go
into more detail of how DNS works.

DNS is the method websites are located on the SAFE Network using SAFE URLs. A SAFE URL is a human-readable address that looks like:
`safe://servicename.publicid`. We'll be using this as the example SAFE URL.

On the SAFE Network the DNS takes
a hash of `publicid`. The browser uses this hash as the address to find the corresponding Mutable Data Structure which contains a key equal to `servicename`.

The value for this entry is a Mutable Data Name. The browser uses that Mutable Data Name with the
type tag of 15002 to get a new Mutable Data Structure containing our website and all its files.

## Mechanics of SAFE Applications and Websites

### How do MaidSafe's Applications work?

#### Web Hosting Manager

TODO: Needs to be verified

The Web Hosting Manager will first create a Public ID. This is a Public Mutable Data Structure with the Hash of the
Public ID you give it and the type tag of 15001, this Public ID can be used again in other websites and applications
such as the SAFE Email App.

You will then be asked to create a Web Service Name. Once you enter in a name a new random Public Mutable Data Structure
with a type tag of 15002 will be created, this will be used to store all our NFS files for this website and its randomly
generated name will be saved.

Then a new entry will be created and saved to our Public ID's Mutable Data Structure with the Web Service Name as its
key and the Random Mutable Data name as its value.

Once you upload an index.html to your web service you will be then able to navigate to your own website with its SAFE
URL.

#### SAFE Browser

TODO: Needs to be verified

The SAFE Browser works by getting the URL you pass to it and looking at the Public ID (the string after the dot). It
then hashes it and uses that hash as the Mutable Data Name and along with the type tag 15001 it can retrieve the Mutable
Data Structure.

Now the Browser looks at the Service Name in the URL, it will find a key that equals the Service Name in the Public ID's
Mutable Data Structure, that key's value will be a Mutable Data Name. The browser using that Mutable Data Name and the
type tag of 15002 will then get a Mutable Data Structure containing our website and all our files.

The browser will look for a specific key in this Mutable Data Structure, index.html. If the index.html key is found it
will retrieve it using NFS and display in the browser, otherwise if index.html isn't found the browser will return an
error.

#### SAFE Email App

### How do the Safey websites work?

#### Demoy

Demoy was designed to showcase the Mutable Data functions. It works by using its app container to store and retrieve
files.

It does this by uploading a file with the filename as an entry's key and file content as its value (this means there is
1MB limit on all files uploaded). The file content must first be converted from a blob to an array that the SAFE API can
read.

When a file is selected to be retrieved the file extension is checked to see what format to display the file in. It
accepts video, image, audio and text, if the file isn't in an accepted it format the user will be presented with a
button to download the file. If the file is shown as a text file the user will be able to edit it in the browser and
save it directly to the network. There is also the ability to delete and update files in the browser.

Video: [Demoy Overview](https://www.youtube.com/watch?v=4yIRrn2e-9E) <br> Video:
[JavaScript File Rundown](https://www.youtube.com/watch?v=IySa5RB9iCE) <br> Github:
[Demoy](https://github.com/josephm1/Demoy)

#### Chaty

#### Vidy

#### Listy
