---
date: 2018-02-07
title: Display an NFT in a scene
description: Learn how to display a certified NFT that you own in your scene
categories:
  - blockchain-integration
type: Document
redirect_from:
  - /blockchain-interactions/display-a-certified-nft
set: blockchain-integration
set_order: 4
---


You can display a 2D NFT (Non-Fungible Token) that you own in your Decentraland scenes. A glowing picture frame will appear around the NFT to certify that you indeed own it.

The NTF's image data is taken from an API, based on the token's contract and id.

Currently, only a limited list of NFTs are supported:

- CryptoKitties
- Editional
- Makersplace
- KnownOrigin
- Axie Infinity
- MyCryptoHeroes
- MLB Champions
- Chibi Fighters
- Blockchain Cuties
- HyperDragons
- Chainbreakers
- Chainguardians
- Cryptomorph
- Josie
- Blockcities


The picture frame is displayed adjusting to the dimensions of the NFT image. If the image's dimensions are 512 X 512 pixels, the frame keeps its original size. If the image has different dimensions, the frame will be resized and stretched to match these dimensions.

> Tip: If you want to stretch or resize the image from what's generated by default, you can change the `scale` property in the entity's `Transform` component.

> Note: In future versions, when using the `NFTShape` component, the engine will automatically run a verification. The same Ethereum wallet that owns the LAND tokens where the scene is deployed or that is doing the deploying must also own the token. If these addresses don't match, the image will be displayed in the scene, but without the glowing frame that certifies it's an original NFT.

## Add an NFT

Add an `NFTShape` component to an entity to display a 2D token in your scene.

```ts
const entity = new Entity()
const shapeComponent = new NFTShape('ethereum://0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/558536')
entity.addComponent(shapeComponent)
entity.addComponent(
  new Transform({
    position: new Vector3(4, 1.5, 4)
  })
)
engine.addEntity(entity)
```

The `NFTShape` component must be instanced with a parameter that includes the following:

- The _contract_ of the token (for example, the CryptoKitties contract)
- The _id_ of the specific token you own

The example above fetches an NFT with the contract address `0x06012c8cf97BEaD5deAe237070F9587f8E7A266d`, and the specific identifier `558536`. The corresponding asset asset can be found in OpenSea at [https://opensea.io/assets/0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/558536](https://opensea.io/assets/0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/558536).


When the token is displayed, it's shown as having a pulsating frame around it.

You can change the background color of the frame, by changing the `color` property in the `NFTShape`. This affects back side of the model, and also the background of the image in case the NFT image has transparency.

```ts
const shapeComponent = new NFTShape('ethereum://0x06012c8cf97BEaD5deAe237070F9587f8E7A266d/558536', Color3.Green())
```

 <img src="/images/media/nft-cat.png" alt="Move entity" width="300"/>




<!--
## Token certification

When using the `NFTShape` component, the engine automatically runs a verification. The same Ethereum wallet that owns the LAND tokens where the scene is deployed must also own the token.

If you don't own the token, the image isn't displayed in the scene.

This verification is carried out by users loading your scene, every time the entity with the `NFTShape` component is added to the engine.

Above the image of the token, we display a badge that certifies its authenticity. The badge glows in a pulsating pattern, providing a stamp that is difficult to falsify.

-->
