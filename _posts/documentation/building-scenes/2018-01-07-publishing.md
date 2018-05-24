---
date: 2018-01-06
title: Publishing the scene
description: How to publish my project?
categories:
  - documentation
type: Document
set: building-scenes
set_order: 7
---
To publish your scene:

* Log into the Metamask account with the same public address associated with your parcel.
* Start up an IPFS daemon by following [these instructions](https://ipfs.io/docs/getting-started/).
* Finally, run `dcl deploy`

This will update your parcel with your latest changes in addition to uploading your content to IPFS. To improve performance and user experience, your content will be pinned to Decentraland’s main IPFS server to ensure that the data needed to render your parcel is readily available 

> You don’t have to pay a gas fee everytime you deploy your content. The smart contract for your LAND is only updated when you link your content to IPNS, the naming service for IPFS.

While this command will deploy your scene to your parcel, remember that users can’t currently explore Decentraland, so your content won’t be discoverable “in-world”.

## What is IPFS?

[IPFS](https://ipfs.io/) (short for Inter-Planetary File System) is a hypermedia protocol and a P2P network for distributing files. The filesystem is content-addressed, meaning that each file is identified by its contents, not an arbitrary filename.

We use IPFS to host and distribute all scene content in a similar way to BitTorrent, keeping the Decentraland network distributed. Of course, for better performance, we run an “IPFS Gateway”, which means that Decentraland is hosting most of the content referenced from the blockchain (after certain filters are applied) to improve the experience of exploring the world.

In order to upload your files, you’ll need to run an IPFS node. After “pinning” your scene’s content (that means, notifying the network that your files are available) our IPFS nodes will try to download the files using the IPFS network, eventually reaching your computer and copying over the files.

To run an IPFS node, please follow [these instructions](https://ipfs.io/docs/getting-started/).

### What does IPFS have to do with my LAND?

IPFS serves two primary functions for Decentraland.

1. IPFS stores and distributes all of the assets required to render your scenes.
2. The `dcl deploy` command links these assets with the LAND parcel specified in your **scene.json** file. Whenever you redeploy your scene, the CLI will update your LAND smart contract, if needed, to point to the most recent content available on IPFS.