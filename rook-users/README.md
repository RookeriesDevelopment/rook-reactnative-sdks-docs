# SDK Rook Users

This SDK allows the [client](https://docs.tryrook.io/docs/Definitions/#client) to register their [Users](https://docs.tryrook.io/docs/Definitions/#User) in ROOK server.

### Features

- Register Users in Health Connect

### Installation

**npm**

```bash
npm i rook_users react-native-mmkv
```

**yarn**

```bash
npm add rook_users react-native-mmkv
```

### Configuration

The package include a component call RookConnectProvider, it should preferably be used at the root of your component tree. RookConnectProvider needs the token that was given to you.
import { RookConnectProvider } from 'rook_auth'

```tsx
<RookConnectProvider
  keys={{
    clientUUID: "YOUR-CLIENT-UUID",
    environment: "sandbox | production",
    password: "YOUR-PASSWORD",
  }}
>
  <YOUR-COMPONENTS />
</RookConnectProvider>
```

### Usage

The SDK provide you with a hook called `useRookUser` this gives you the ability to check if you have an user ID generated or saved in the device.

#### Import

```js
import { useRookUser } from "rook_users";
```

#### Example

```tsx
import React from "react";
import { Button, SafeAreaView } from "react-native";
import { useRookUser } from "rook_users";

export const Home = () => {
  const { getUserID, registerUser } = useRookUser();

  const handleSaved = async (): Promise<void> => {
    try {
      const resp = await getUserID();
      console.log("r", resp);
    } catch (error) {
      console.log(error);
    }
  };

  const handleRegister = async (): Promise<void> => {
    try {
      const resp = await registerUser({
        user: "your user identifier",
        dataSource: "Health Connect",
      });

      console.log(resp);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <SafeAreaView>
      <Button title="Get saved id" onPress={handleSaved} />
      <Button title="Register user" onPress={handleRegister} />
    </SafeAreaView>
  );
};
```

#### API

```tsx
const useRookUser: () => useRookUserTools;
```

**Return**

- `useRookUserTools`: Return two functions to manage the registration of the user
- `useRookUserTools.getUserID`: Return a null if you don't have a user saved in the device or will return the user that was saved.
- `useRookUserTools.registerUser`: Register a user into Rook services
