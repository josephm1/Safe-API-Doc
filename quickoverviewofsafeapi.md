# A rundown of the Safe API
We are first going to take a brief look at how a website connects to the Safe Network and what that website can do afterwards.


// This needs to be edited, authorised is needed for some parts of these functions but not entire functions and authorised is needed for parts of safe App Cipher Opt. So this is wrong.
![picture](./1.png)

State  | Not Initialised   | Connected to Network  |   Authorised to use
--|---|---|--
Colour  |Orange   | Blue   |  Green

The data on the safe network is saved as a key-value store. This a means requested data from the network will return something like this `key 1: value 1`. This is called an Entry.

There are only two types of data on the safe network, Mutable Data (can change) and Immutable Data (can't change).  

Mutable Data is subject to some limits, it cannot hold more than a 100 entries and the Mutable Data itself must remain under 1mb in size.

Immutable Data also has some limits, it can only hold one entry, but its size is limited only by the current Testnet restrictions or when the network is fully released the amount of Safecoin you have. A unique feature of Immutable Data is the Data Map Address. A Data Map Address is the network's location of a particular Immutable Data. The Data Map Address doesn't change and is the same for everyone. This done by using XOR (Exclusive Or). Here is a quick rundown of XOR.

![picture ](./xor.png)

//Could be edited.

I'll like to think of XOR as the numbers canceling each other out. 0 is nothing so 0 canceling 0 is also nothing. 1 against 0, 0 is still nothing so 1 wins. 1 and 1 equal each other, therefore when they cancel each other out you are left with nothing, 0.

It is possible to combine the advantages of the two types of data into a emulation file system on top of the network called NFS (Network File Storage). NFS uses the Data Map Address of Immutable Data as a value for an entry in a Mutable Data structure so it can point to the content. It uses the ability to mutate and update of Mutable Data to change the content its pointing to. This means if I want to updated my index.html, the NFS will upload the updated version as Immutable Data and give its Address over to the current Mutable Data Structure so it will now point to the new version.

The browser gets the Mutable Data structure by making use of the two variables you pass in to the URL, the Public Id and Service Name.

//Not finished
