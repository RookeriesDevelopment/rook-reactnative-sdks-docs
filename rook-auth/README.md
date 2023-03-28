RookMotion Auth
RookMotion Auth contains a Provider to notify the submodules if the apps is authorized by the given token
Instalation
**npm**

```bash
npm i rook_auth
```

**yarn**

```bash
yarn add rook_auth
```

**Usage**
The package include a component call RookConnectProvider, it should preferably be used at the root of your component tree. RookConnectProvider needs the token that was given to you.
import { RookConnectProvider } from 'rook_auth'

```tsx
<RookConnectProvider
  keys={{
    clientUUID: "YOUR-CLIENT-UUID",
    apiURL: "API-URL-ENVIROMENT",
    password: "YOUR-PASSWORD",
  }}
>
  <YOUR-COMPONENTS />
</RookConnectProvider>
```
