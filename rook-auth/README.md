RookMotion Auth
RookMotion Auth contains a Provider to notify the submodules if the apps is authorized by the given token
Instalation
**npm**

```bash
npm i rook_auth @react-native-async-storage/async-storage
```

**yarn**

```bash
yarn add rook_auth @react-native-async-storage/async-storage
```

**Note: If you are on iOS please run _npx pod-install_**

**Usage**
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
