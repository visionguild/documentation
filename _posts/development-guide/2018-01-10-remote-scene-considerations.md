---
date: 2018-01-10
title: About multiplayer scenes
description: Tips and tricks for scenes with multiple players
redirect_from:
  - /documentation/remote-scene-considerations/
categories:
  - development-guide
type: Document
set: development-guide
set_order: 30
---

Decentraland runs scenes locally in a player's browser. By default, players are able to see each other and interact directly, but each one interacts with the environment independently. Changes in the environment aren't shared between players by default. You need to implement this manually.

Allowing all players to see a scene as having the same content in the same state is extremely important to for players to interact in more meaningful ways. Without this, if a player opens a door and walks into a house, other players will see that door as still closed, and the first player will appear to walk directly through the closed door to other players.

There are two ways to keep the scene state that all players see in sync:

- Send P2P messages between users to update changes
- Use an authoritative server to keep track of the scene's state

The first of these options is the easiest to implement. The downside is that you rely more on player's connection speeds. Also, if there are incentives to exploit (eg: there are prizes for players with highest scores in a game, or there are in-game transactions) it's always recommendable to use an authoritative server, as this allows you to have more control and exposes less vulnerabilities.

## P2P messaging

### Initiate a message bus

Create a message bus object to handle the methods that are needed to send and receive messages between players.

```ts
const sceneMessageBus = new MessageBus();
```

### Send messages

Use the `.emit` command of the message bus to send a message to all other players in the scene.

```ts
const sceneMessageBus = new MessageBus()

box1.AddComponent(
  new OnClick(e => {
    sceneMessageBus.emit("box1Clicked", {})
}))

```

Each message can contain a payload as a second argument. The payload is of type `Object`, and can contain any relevant data you wish to send. 

```ts
const sceneMessageBus = new MessageBus()

let spawnPos = new Vector3(5, 0, 5)

sceneMessageBus.emit("spawn", {position: spawnPos })
```

> Tip: If you need a single message to include data from more than one variable, create a custom type to hold all this data in a single object.


### Receive messages

To handle messages from all other players in that scene, use `.on`. When using this function, you provide a message string and define a function to execute. For each time that a message with a matching string arrives, the given function is executed once.

```ts
const sceneMessageBus = new MessageBus()

sceneMessageBus.on("spawn", (info: NewBoxPosition) => {
  let newCube = new Entity()
  let transform = new Transform()
  transform.position.set(info.position.x, info.position.y, info.position.z)
  newCube.addComponent(transform)
  engine.addComponent(newCube)
});
```

> Note: Messages that are sent by a player are also picked up by that same player. The `.on` method can't distinguish between a message that was emitted by that same player from a message emitted from other players.

### Full example

This example uses a message bus to send a new message every time the main cube is clicked, generating a new cube in a random position. The message includes the position of the new cube, so that all players see these new cubes in the same positions.

```ts

  /// --- Spawner function ---
  
  function spawnCube(x: number, y: number, z: number) {
    // create the entity
    const cube = new Entity();
  
    // add a transform to the entity
    cube.addComponent(new Transform({ position: new Vector3(x, y, z) }));
  
    // add a shape to the entity
    cube.addComponent(new BoxShape());
  
    // add the entity to the engine
    engine.addEntity(cube);
  
    return cube;
  }
  
/// --- Create message bus ---
const sceneMessageBus = new MessageBus();

/// --- Define a custom type to pass in messages ---
type NewBoxPosition = {
  position: ReadOnlyVector3;
};

/// --- Call spawner function ---
const cube = spawnCube(8, 1, 8);

/// --- Emit messages ---
cube.addComponent(
  new OnClick(() => {
    const action: NewBoxPosition = {
      position: {
        x: Math.random() * 8 + 1,
        y: Math.random() * 8,
        z: Math.random() * 8 + 1
      }
    };

    sceneMessageBus.emit("spawn", action);
  })
);

/// --- Receive messages ---
sceneMessageBus.on("spawn", (info: NewBoxPosition) => {
  cube.getComponent(Transform).scale.z *= 1.1;
  cube.getComponent(Transform).scale.x *= 0.9;

  spawnCube(info.position.x, info.position.y, info.position.z);
});
```

### Test a P2P scene locally

If you launch a scene preview and open it in two (or more) different browser windows, each open window will be interpreted as a separate player, and a mock communications server will keep these players in sync.

Interact with the scene on one window, then switch to the other to see that the effects of that interaction are also visible there.

> Note: Open separate browser _windows_. If you open separate _tabs_ in the same window, the interaction won't work properly, as only one tab will be treated as active by the browser at a time.

## Use an authoritative server

### Example scenes with authoritative server

To copy one of the scene examples that implements an authoritative server, follow the steps in [copy a scene example]({{ site.baseurl }}{% post_url /examples/2018-01-08-sample-scenes %}#clone-an-example-scene).


[Door scene](https://github.com/decentraland-scenes/Remote-door)

[Mural scene](https://github.com/decentraland-scenes/Remote-mural)


### Preview scenes with authoritative servers

To preview a scene that uses an authoritative server, you must run both the scene and the server it relies on. The server can be run locally in the same machine as the preview, as an easier way to test it.

To start the server, go to the `/server` folder and run `npm start`.

Once the server is running, either remotely or locally, you can run `dcl start` on the scene as you normally do for local scenes.

Once the scene preview is running, you can open multiple browser tabs pointing at the same local address. Each tab will instantiate a separate player in the same scene, these players will share the same scene state as the scene changes.

See [preview a scene]({{ site.baseurl }}{% post_url /getting-started/2018-01-04-preview-scene %}) for more details.

### Multiplayer persistance

Unlike local scenes that are newly mounted each time a player walks into them, scenes that use authoritative servers have a life span that extends well beyond when the player enters and leaves the scene.

You must therefore design the experience taking into account that player won't always find the scene in the same initial state.
Any changes made to the scene will linger on for other players to find, you must make sure that these don't interfere with future player's experiences in an undesired way.

#### Reset the state

When loading the scene, make sure its built based on the shared information stored in the server, and not in a default state.

In some cases, it makes sense to include some kind of reset button in the scene. Pressing the reset button would reset the scene gracefully.

Sometimes, this just implies setting the variables in the scene state back to default values. But resetting the scene might also involve unsubscribing listeners and stopping loops in the server side. If empty loops remain each time the scene is reset, these would keep piling up and will have an ill effect on the scene's performance. 
