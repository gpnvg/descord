# descord
A discord API wrapper written using TypeScript for [deno](https://deno.land).

This library is in its pre-alpha state. For now, you can connect the discord bot to the discord servers. The library will automatically handle sharding for you. A sample bot written in the library is given below.

```typescript
import { Client, Presence } from "https://raw.githubusercontent.com/TheDrone7/descord/master/mod.ts";

const client = new Client(); // Initialize our client
client.on("debug", console.log); // Log when debugging

// Handle ready event. <-- This can be alternatively handled through a raw event
client.on('ready', () => {
  console.log(`Logged in as ${client.user.tag}`); // Logs the client's username and tag
});

// Below is a simple switch statement to show how you can handle raw events
client.on("raw", (raw: any) => {
  switch (raw.t) /* "raw.t" is the event's name, which if it matches the case then executes the following */ {
    // Whenever a user joins your server, it will execute the following ...
    case "GUILD_MEMBER_ADD": {
      console.log(
        `${raw.d.user.username}#${raw.d.user.discriminator} has joined the server. Their ID is ... ${raw.d.user.id}`,
      );
      break; // Breaks or ends the the case, when it breaks the switch statement does not continue, it stops.
    }
    // Whenever a user leaves the server, it will execute the following ...
    case "GUILD_MEMBER_REMOVE": {
      console.log(
        `${raw.d.user.username}#${raw.d.user.discriminator} has left the server. Their ID is ... ${raw.d.user.id}`,
      );
      break;
    }
  }
});

// Create a Presence, which will be set to the bot when it logs in!
let rpc: Presence = {
  status: "dnd", // "online" | "idle" | "offline" | "dnd" | "invisible"
  game: {
    name: "Descord v1.0.0",
    type: 4,
  }
}

// Begin the bot
client.login("Your token here", {
  presence: rpc,
})
```

You can also send ws messages using the `wsSend()` method of the client. For example: -
```typescript
client.wsSend({
    op: 3,
    d: {
        status: 'dnd',
        afk: false,
        game: {
            name: 'descord',
            type: 4
        }
    }
});
```

# Working with raw data
```JSON
{
  "t": "GUILD_MEMBER_ADD",
  "s": 4,
  "op": 0,
  "d": {
    "user": {
      "username": "gpnvg",
      "public_flags": 0,
      "id": "744658428487794819",
      "discriminator": "9208",
      "avatar": "cf86b9138796804efb4a5b50799662bb"
    },
    "roles": [],
    "premium_since": null,
    "nick": null,
    "mute": false,
    "joined_at": "2020-08-28T14:30:28.304377+00:00",
    "guild_id": "748671493168300073",
    "deaf": false
  }
}
```
Above is a sample of the raw data discord sends you when a user joins a guild. This is what is being referenced in the example above. Because this library is in its infancy you will not have a lot of amenities for getting specific information a lot of libraries have, so you will have to get the information (such as the user's ID) from the raw data.

For making http request to the discord's REST API, you can use the built-in `fetch` API system or use the client's `HTTPClient` as shown below: -
```ts
let user = await client.http.get('/users/374886124126208000');
if (user.status === 200) console.log(user.body);
else console.error(user.statusText);
```
