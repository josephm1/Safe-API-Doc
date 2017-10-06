# SAFE API Overview

Development Guides by MaidSafe:
- [SAFE Network API - Getting started ](https://forum.safedev.org/t/safe-network-api-getting-started-draft/726/1)
- [How to develop for the SAFE Network ](https://forum.safedev.org/t/how-to-develop-for-the-safe-network-draft/843)

SAFE API Documentation
- [Node.js docs ](http://docs.maidsafe.net/safe_app_nodejs)
- [JavaScript docs ](http://docs.maidsafe.net/beaker-plugin-safe-app/)


Explanations of the SAFE Network by the community:
- [The SAFE Primer]
- [SAFE Network Architecture ](https://safe-network-explained.github.io/architecture)
- [The Documentation Topic ](https://safenetforum.org/t/the-documentation-topic/16149)
- [Introduction to the SAFE Network
](https://medium.com/@luandro/introduction-to-the-safe-network-33d8641b2dee)

## SAFE Browser and the Authenticator

The SAFE Browser is developed by MaidSafe and provides access to the SAFE Network and websites on the SAFE Network.

It has an integrated feature called Authenticator. The Authenticator is used to for signing in and creating an account on the SAFE Network.

### SAFE Accounts

An account on the SAFE Network is needed for uploading data. The current Test Network has restrictions on how much data you can 'PUT' on the Network. It measures your use in 'PUTS', each action preformed on the Network other than 'READ' costs PUTS. Each account is limited to 1000 PUTS. This will change when SAFE Coin is implemented.

## SAFE API Flow

![SAFE API Flow](SAFE-API-Flow.png)


### Handles and the SAFE API

The SAFE API uses handles frequently. A handle is a reference to a resource. An example of a resource can be an authentication token (App Handle) or the location of a Mutable Data Structure (Mutable Data Handle). These handles are different for each session.

When you free a handle, the reference is freed from memory and you are then able to make another handle of the same type of resource.

### Connecting and authorising on the SAFE Network

There are two network states in the SAFE API: Connected and Authorised.

 The user doesn't need to be signed in to connect. When you are connected you can 'READ', this means you can only read Public data and aren't able to preform actions such as: uploading to a Immutable or Mutable Data Structure, modifying entries, inserting permissions or getting Keys.

The user needs to be signed to authorise. When you are authorised you have full access to the SAFE API and are able to preform all actions.

//App Info

## Data on the SAFE Network

There are only two generic types of data on the SAFE Network, Mutable Data (can change) and Immutable Data (can't change).

### Mutable Data

// Needs to be verified

The Mutable Data on the SAFE Network is saved as a key-value store. This means requested data from the network will return something like this `key 1: value 1`. This is called an entry. A Mutable Data Structure is composed of these entries.

A Mutable Data Structure can hold up to a 1000 entries and contain a maximum of 1 MB of data.

To create and retrieve a Mutable Data Structure handle you will need to pass three parameters to it: your app handle, a 32 length buffer (this can be a hash of a particular string with `safeCrypto.sha3Hash`) and its type tag. A type tag is a number that is used to differentiate between the different levels of the Mutable Data Structures.

There are three types of Mutable Data Structures: Public (websites), Private (private files) and Shared (cross-website messaging groups).

Mutable Data Structures support: inserting, mutating and setting permissions.   

#### Shared Mutable Data

// Great Reference <https://forum.safedev.org/t/safe-network-api-getting-started-draft/726> and video by Hunter Lester here <https://youtu.be/E80IH8mCKaw>
// Need to explain better and verify
// This is probably wrong and will be updated later

\*App Info is the parameter in the intialise function, Hunter Lester's video goes through what happens.

When you set up a Mutable Data Structure only the app info you set it up with is given default access to modify it.

Shared Mutable Data requests access to a given Mutable Data Structure that the user created.

Shared Mutable Data can be used across different websites (with different app infos), like a collaborative messaging group in Chaty.

### Immutable Data

// Needs to be verified

Immutable Data holds a single value. Its size is only limited by the current Test Network/Alpha 2 restrictions or when the network is fully released the amount of SAFE Coin you have.

Immutable Data can be stored as: asymmetric encryption (Two people, email)  symmetric encryption (one person, private files), and plaintext (websites).

Immutable Data is retrieved with a Data Map Address. A Data Map Address is the 32 length hash of a particular Immutable Data file content.

Since the Immutable Data's address is also a hash of its file content the file cannot be changed in any way and must remain static unlike Mutable Data.

<!-- This is done by using XOR. -->

<!-- #### Exclusive Or (XOR)

//Needs to be explained better, why it's used, how it works and verified to be make sure it's right // This is a good reference <https://blog.maidsafe.net/2016/05/27/structuring-networks-with-xor/> for XOR on vaults and clients.

I'll like to think of XOR (⊕) as the numbers canceling each other out. 0 is nothing so 0 canceling 0 is also nothing. 1 against 0, 0 is still nothing so 1 wins. 1 and 1 equal each other, therefore when they cancel each other out you are left with nothing, 0.

| A | B | C |
| - | - | - |
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

##### A ⊕ B (The XOR distance of A and B) == C

// Need To Detail : location, Commutative(A ⊕ B == B ⊕ A == C), unique identity with 2 closest nodes, ability to reverse equation(A ⊕ C == B),distributed hash tables, collision, differences between Mutable Data, Immutable Data and Vaults. -->

### NFS (Network File Storage)

// Needs to be verified

It is possible to save data using a combination of Mutable and Immutable Data to create an emulation file-system on top of the network called NFS.

NFS uses Immutable Data to save a file content, it then creates an entry in a given Mutable Data Structure. It uses the file name as the entry's key and the Immutable Data's address as the entry's value.

This enables you to update or change a file by uploading a new Immutable Data file and then updating the file's address in the entry's value. This Mutable Data Structure can be used again to store more NFS files.

#### Hierarchy File-System Emulation

When dealing with folders and files being uploaded that are within subdirectories it is recommended to use a slash (/) to separate the subdirectory and the file name.

This means the key of a Mutable Data Structure containing files would look something like this: `filepath/subdir/index.html`.

## Containers

// More info here and great reference <https://github.com/maidsafe/rfcs/blob/master/text/0046-new-auth-flow/containers.md>

Containers are Mutable Data Structures that are generally used for storing particular types of data i.e. `_music` stores music and sound files.

### Root Container

// Could be made simpler

The root container is the main entry point for the user and apps to interact with. It is a locally encrypted container stored at a random location on the network known only to the user, that generally only the authenticator has write access to.

Its reference will be stored in the users session packet on account creation. Keys starting with an underscore ( _ ) are reserved for internal usage by the authenticator, while the authenticator may also allow the creation of other keys later.

// Session Packet

The authenticator has another mapped data container which holds the encryption keys per each container, which is locally encrypted with a separate key that only the authenticator has access to and will never be shared. This is called: `RootKeysContainer`.

### Default containers

The authenticator will create the following default containers within the root container when you create an account:

- `_apps/net.maidsafe.authenticator/`
- `_documents`
- `_downloads`
- `_music`
- `_pictures`
- `_videos`
- `_public`
- `_publicNames`

All data stored in these containers are stored randomly on the Network should be encrypted other than `_public`.

### App container

The app container is created for websites if they request their to have own container with their ID being used to identify it. This means the app container name looks like `apps/id.example.net`.

## DNS (Domain Name System)

 DNS is the method websites are located on the SAFE Network. It works by using Mutable Data and NFS to store and locate files. The Web Hosting Manager and SAFE Browser overview below go into more detail about DNS as they both use DNS extensively.

## How do MaidSafe's Applications work?

### Web Hosting Manager

// Needs to be verified

The Web Hosting Manager will first create a Public ID. This is a Public Mutable Data Structure with the Hash of the Public ID you give it and the type tag of 15001, this Public ID can be used again in other websites and applications such as the SAFE Email App.

You will then be asked to create a Web Service Name. Once you enter in a name a new random Public Mutable Data Structure with a type tag of 15002 will be created, this will be used to store all our NFS files for this website and its randomly generated name will be saved.

Then a new entry will be created and saved to our Public ID's Mutable Data Structure with the Web Service Name as its key and the Random Mutable Data name as its value.

Once you upload an index.html to your web service you will be then able to navigate to your own website, it will have a URL which looks something like this: `safe://servicename.publicid`

### SAFE Browser

// Needs to be verified

The SAFE Browser works by getting the URL you pass to it and looking at the Public ID (the string after the dot). It then hashes it and uses that hash as the Mutable Data Name and along with the type tag 15001 it can retrieve the Mutable Data Structure.

Now the Browser looks at the Service Name in the URL, it will find a key that equals the Service Name in the Public ID's Mutable Data Structure, that key's value will be a Mutable Data Name. The browser using that Mutable Data Name and the type tag of 15002 will then get a Mutable Data Structure containing our website and all our files.


The browser will look for a specific key in this Mutable Data Structure, index.html. If the index.html key is found it will retrieve it using NFS and display in the browser, otherwise if index.html isn't found the browser will return an error.

### SAFE Email App

## How do my websites work?

### Demoy

Demoy was designed to showcase the Mutable Data functions. It works by using its app container to store and retrieve files.

It does this by uploading a file with the filename as an entry's key and file content as its value (this means there is 1MB limit on all files uploaded). The file content must first be converted from a blob to an array that the SAFE API can read.

When a file is selected to be retrieved the file extension is checked to see what format to display the file in. It accepts video, image, audio and text, if the file isn't in an accepted it format the user will be presented with a button to download the file. If the file is shown as a text file the user will be able to edit it in the browser and save it directly to the network. There is also the ability to delete and update files in the browser.

// More resources here

Demoy overview <https://www.youtube.com/watch?v=4yIRrn2e-9E>

javascript file rundown <https://www.youtube.com/watch?v=IySa5RB9iCE>

Demoy's Github <https://github.com/josephm1/Demoy>

### Chaty

### Vidy

### Listy
