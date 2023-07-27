# SDK Rook Transmission in React Native

This SDK will allow you to transmit health connect data to Rook services.

## For Android

## Content

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)

## Installation <a id="instalation"></a>

To build a project using the Rook Transmission Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **Android** this means that it won't work with iOS.

1. Install the dependencies

**npm**

```bash
npm i react-native-rook-android-transmission
```

**yarn**

```bash
yarn add react-native-rook-android-transmission
```

## Configuration <a id="configuration"></a>

Add your client uuid in order to be authorized, follow the next example, add at the top level of your tree components the RookConnectProvider.

```tsx
import {RookConnectProvider} from 'rook_auth';

const App => () {
  return (
    <RookConnectProvider
      keys={{
        clientUUID: 'YOUR-CLIENT-UUID',
        apiURL: 'API-URL',
        password: 'YOUR-PASSWORD',
      }}>
      <YourComponents />
    </RookConnectProvider>
  );
}
```

Then we need to configure the android project. open the android project inside android studio. We need to modify the `android/app/build.gradle` we need to set the `minSdkVersion` and `targetSdkVerion`

```gradle
android {
  ...
  minSdkVersion: 26
  targetSdkVersion 33
  ...
}
```

## Package usage <a id="packageUsage"></a>

The SDK provide you with a hook called `useRookHCTransmission` this gives you the ability to transmit the health data to Rook Services.

#### Import

```js
import { useRookHCTransmission } from "react-native-rook-android-transmission";
```

#### Example

This example show you how to implement transmission with Body summaries, but the process with the rest Sleep and Physical are the same.

First Create a component with contains the buttons to manages the queues of summaries.

```tsx
import React, { FC, useState } from "react";
import { Text, View, StyleSheet, Button } from "react-native";
import { useRookHCTransmission } from "rook_transmission";
import { useRookHCBody } from "rook_health_connect";

type BodyTransmissionProps = {
  date: string;
  userID: string | number;
};

export const BodyTransmission: FC<BodyTransmissionProps> = ({
  date,
  userID,
}) => {
  const { getBodySummary } = useRookHCBody();
  const { enqueueBodySummary, clearQueuedBodySummaries, uploadBodySummaries } =
    useRookHCTransmission({
      userID,
    });

  const [response, setResponse] = useState("{}");

  const handleQueue = async (): Promise<void> => {
    try {
      const summary = await getBodySummary(date);

      const resp = await enqueueBodySummary(summary);

      setResponse(`Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleClearQueue = async (): Promise<void> => {
    try {
      const resp = await clearQueuedBodySummaries();
      setResponse(`Clear Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const resp = await uploadBodySummaries();
      setResponse(`Upload Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Text style={styles.blacked}>Transmission</Text>
      <Button title="Enqueue" onPress={handleQueue} />
      <Button title="Clear Enqueue" onPress={handleClearQueue} />
      <Button title="Upload Enqueue" onPress={handleUploadQueue} />
      <Text style={styles.blacked}>{response}</Text>
    </View>
  );
};

const styles = StyleSheet.create({
  mb: {
    marginBottom: 10,
  },
  blacked: {
    color: "black",
  },
});
```

Let`s create another component that will contain the previous component

```tsx
/* eslint-disable react-hooks/exhaustive-deps */
import React, { useState, useEffect } from "react";
import {
  View,
  Text,
  Button,
  TextInput,
  SafeAreaView,
  StyleSheet,
} from "react-native";
import { useRookHCBody } from "rook_health_connect";
import { BodyTransmission } from "../components/BodyTransmission";

export const BodyView = () => {
  const [date, setDate] = useState("");
  const [data, setData] = useState("{}");
  const [userID, setUserID] = useState("");

  const { checkUserID } = useUser({ user: "example@gmail.com" });

  const {
    getBodySummaryLastDate,
    hasBodyPermissions,
    requestBodyPermissions,
    getBodySummary,
  } = useRookHCBody();

  useEffect(() => {
    // We need to make sure that we already have a user registered.
    // To register a user please check rook_users
    checkUserID()
      .then((id) => setUserID(id))
      .catch(console.log);
  }, []);

  return (
    <SafeAreaView>
      <View style={styles.mb}>
        <Text>body</Text>
        <TextInput
          placeholder="YYYY-MM-DD"
          onChangeText={(text) => setDate(text)}
        />
      </View>

      {userID && <BodyTransmission date={date} userID={userID} />}
    </SafeAreaView>
  );
};

const styles = StyleSheet.create({
  mb: {
    marginBottom: 10,
  },
  blacked: {
    color: "black",
  },
});
```

#### API

```ts
const useRookHCTransmission: ({
  userID,
}: useRookHCTransmissionProps) => useRookHCTools;
```

**Return**

- `useRookHCTools.ready`: Indicates you when the hook is ready to be executed.
- `useRookHCTools.clearQueuedBodySummaries`: A promise that will be resolved with a true or false that indicates the success or failure of clearing queued.
- `useRookHCTools.clearQueuedPhysicalEvents`: A promise that will be resolved with a true or false that indicates the success or failure of clearing queued.
- `useRookHCTools.clearQueuedPhysicalSummaries`: A promise that will be resolved with a true or false that indicates the success or failure of clearing queued.
- `useRookHCTools.clearQueuedSleepSummaries`: A promise that will be resolved with a true or false that indicates the success or failure of clearing queued.
- `useRookHCTools.enqueueBodySummary`: A promise that will be resolved with a true or false that indicates the success or failure of queue the summary. The function requires a BodySummary this will be return of the methods of **rook_health_connect**.
- `useRookHCTools.enqueueBodySummary`: A promise that will be resolved with a true or false that indicates the success or failure of queue the summary. The function requires a BodySummary this will be return of the methods of **rook_health_connect**.
- `useRookHCTools.enqueuePhysicalEvent`: A promise that will be resolved with a true or false that indicates the success or failure of queue the summary. The function requires a PhysicalEvent this will be return of the methods of **rook_health_connect**.
- `useRookHCTools.enqueuePhysicalSummary`: A promise that will be resolved with a true or false that indicates the success or failure of queue the summary. The function requires a PhysicalSummary this will be return of the methods of **rook_health_connect**.
- `useRookHCTools.enqueueSleepSummary`: A promise that will be resolved with a true or false that indicates the success or failure of queue the summary. The function requires a PhysicalSummary this will be return of the methods of **rook_health_connect**.
- `useRookHCTools.uploadAll`: A promise that will be resolved with a true or false that indicates the success or failure of upload all the queues of summaries.
- `useRookHCTools.uploadBodySummaries`: A promise that will be resolved with a true or false that indicates the success or failure of upload all the queue of BodySummaries.
- `useRookHCTools.uploadPhysicalEvents`: A promise that will be resolved with a true or false that indicates the success or failure of upload all the queue of PhysicalEvents.
- `useRookHCTools.uploadPhysicalSummaries`: A promise that will be resolved with a true or false that indicates the success or failure of upload all the queue of PhysicalSummaries.
- `useRookHCTools.uploadSleepSummaries`: A promise that will be resolved with a true or false that indicates the success or failure of upload all the queue of SleepSummaries.

# For iOS

## Content

1. [Installation](#installation-ios)
2. [Configuration](#configuration-ios)
3. [Package usage](#packageUsage-ios)

## Installation <a id="instalation-ios"></a>

To build a project using the Rook Transmission Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **iOS** this means that it won't work with android.

1. Install the dependencies

**npm**

```bash
npm i react-native-rook-ios-transmission
```

**yarn**

```bash
yarn add react-native-rook-ios-transmission
```

**Important after do the installation run `pod install`**

## Configuration <a id="configuration-ios"></a>

Add your client uuid in order to be authorized, follow the next example, add at the top level of your tree components the RookConnectProvider.

```tsx
import {RookConnectProvider} from 'rook_auth';

const App => () {
  return (
    <RookConnectProvider
      keys={{
        clientUUID: 'YOUR-CLIENT-UUID',
        apiURL: 'API-URL',
        password: 'YOUR-PASSWORD',
      }}>
      <YourComponents />
    </RookConnectProvider>
  );
}
```

## Package usage <a id="packageUsage-ios"></a>

### useRookAHBodyTransmission <a id="useRookAHBody"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition

```ts
type BodyProps = {
  userID: string;
};

export declare const useRookAHBodyTransmission: ({ userID }: BodyProps) => {
  enqueueBodySummary: (date: string, data: BodySummary) => Promise<boolean>;
  getBodySummariesStored: () => Promise<any>;
  uploadBodySummaries: () => Promise<any>;
};
```

- `enqueueBodySummary`: saves the body summary to queue to later upload
  - `date`: indicates the date of the body summary
  - `data`: it's the summary to save
- `getBodySummariesStored`: Retrieves the queue saved into the device
- `uploadBodySummaries`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { FC } from "react";
import { Button, View } from "react-native";
import { useRookAHBodyTransmission } from "react-native-rook-ios-transmission";
import { useRookAHPermissions, useRookAHBody } from "react-native-rook_ah";

type BodyTransmissionProps = {
  // We need to make sure that we already have a user registered.
  // To register a user please check rook_users
  userID: string;
  date: string;
};

export const BodyTransmission: FC<BodyTransmissionProps> = ({
  userID,
  date,
}) => {
  const { enqueueBodySummary, getBodySummariesStored, uploadBodySummaries } =
    useRookAHBodyTransmission({
      userID,
    });
  const { requestBodyPermissions } = useRookAHPermissions();
  const { getBodySummary } = useRookAHBody();

  const requestPermission = async (): Promise<void> => {
    await requestBodyPermissions();
  };

  const handleEnqueueSleep = async (): Promise<void> => {
    try {
      const summary = await getBodySummary(date);
      await enqueueBodySummary(date, summary);
      console.log("Summary saved");
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const result = await getBodySummariesStored();
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const result = await uploadBodySummaries();
      console.log(`Queue uploaded: ${result}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button title="Request Body permissions" onPress={requestPermission} />
      <Button title="Enqueue Body summary" onPress={handleEnqueueSleep} />
      <Button title="Get Enqueue" onPress={handleGetQueue} />
      <Button title="Upload Enqueue" onPress={handleUploadQueue} />
    </View>
  );
};
```

### useRookAHSleepTransmission <a id="useRookAHSleepTransmission"></a>

**Definition**

If you need more details about SleepSummary please use right click an **Go to definition** to se the whole definition

```ts
type SleepProps = {
  userID: string;
};
export declare const useRookAHSleepTransmission: ({ userID }: SleepProps) => {
  saveSleepSummary: (data: SleepSummary) => Promise<boolean>;
  getSleepSummariesStored: () => Promise<any>;
  uploadSleepSummaries: () => Promise<any>;
};
```

- `saveSleepSummary`: saves the sleep summary to queue to later upload
  - `data`: it's the summary to save
- `getSleepSummariesStored`: Retrieves the queue saved into the device
- `uploadSleepSummaries`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { FC } from "react";
import { useRookAHSleepTransmission } from "react-native-rook-ios-transmission";
import { useRookAHPermissions, useRookAHSleep } from "react-native-rook_ah";
import { Button, View } from "react-native";

type SleepTransmissionProps = {
  // We need to make sure that we already have a user registered.
  // To register a user please check rook_users
  userID: string;
  date: string;
};

export const SleepTransmission: FC<SleepTransmissionProps> = ({
  userID,
  date,
}) => {
  const { saveSleepSummary, getSleepSummariesStored, uploadSleepSummaries } =
    useRookAHSleepTransmission({ userID });

  const { requestSleepPermissions } = useRookAHPermissions();

  const { getSleepSummary } = useRookAHSleep();

  const requestPermission = async (): Promise<void> => {
    await requestSleepPermissions();
  };

  const handleEnqueueSleep = async (): Promise<void> => {
    try {
      const summary = await getSleepSummary(date);
      await saveSleepSummary(summary);
      console.log("Summary saved");
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const result = await getSleepSummariesStored();
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const result = await uploadSleepSummaries();
      console.log(`Queue uploaded: ${result}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button title="Request sleep permissions" onPress={requestPermission} />
      <Button title="Enqueue sleep summary" onPress={handleEnqueueSleep} />
      <Button title="Get Enqueue" onPress={handleGetQueue} />
      <Button title="Upload Enqueue" onPress={handleUploadQueue} />
    </View>
  );
};
```

### useRookAHPhysicalTransmission <a id="useRookAHPhysicalTransmission"></a>

**Definition**

If you need more details about Physical please use right click an **Go to definition** to se the whole definition

```ts
type PhysicalProps = {
  userID: string;
};
export declare const useRookAHPhysicalTransmission: ({
  userID,
}: PhysicalProps) => {
  enqueuePhysicalSummary: (
    date: string,
    data: PhysicalSummary
  ) => Promise<boolean>;
  getPhysicalSummariesStored: () => Promise<any>;
  uploadPhysicalSummaries: () => Promise<any>;
};
```

- `enqueuePhysicalSummary`: saves the physical summary to queue to later upload
  - `data`: it's the summary to save
- `getPhysicalSummariesStored`: Retrieves the queue saved into the device
- `uploadPhysicalSummaries`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { FC } from "react";
import { Button, View } from "react-native";
import { useRookAHPhysicalTransmission } from "react-native-rook-ios-transmission";
import { useRookAHPermissions, useRookAHPhysical } from "react-native-rook_ah";

type PhysicalTransmissionProps = {
  // We need to make sure that we already have a user registered.
  // To register a user please check rook_users
  userID: string;
  date: string;
};

export const PhysicalTransmission: FC<PhysicalTransmissionProps> = ({
  userID,
  date,
}) => {
  const {
    enqueuePhysicalSummary,
    getPhysicalSummariesStored,
    uploadPhysicalSummaries,
  } = useRookAHPhysicalTransmission({
    userID,
  });
  const { requestPhysicalPermissions } = useRookAHPermissions();
  const { getPhysicalSummary } = useRookAHPhysical();

  const requestPermission = async (): Promise<void> => {
    await requestPhysicalPermissions();
  };

  const handleEnqueueSleep = async (): Promise<void> => {
    try {
      const summary = await getPhysicalSummary(date);
      await enqueuePhysicalSummary(date, summary);
      console.log("Summary saved");
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const result = await getPhysicalSummariesStored();
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const result = await uploadPhysicalSummaries();
      console.log(`Queue uploaded: ${result}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button
        title="Request Physical permissions"
        onPress={requestPermission}
      />
      <Button title="Enqueue Physical summary" onPress={handleEnqueueSleep} />
      <Button title="Get Enqueue" onPress={handleGetQueue} />
      <Button title="Upload Enqueue" onPress={handleUploadQueue} />
    </View>
  );
};
```

### Bonus Register a user into Rook services

```tsx
import { Platform } from "react-native";
import { useRookUser } from "rook_users";

type useUserProps = {
  user: string | number;
};

export const useUser = ({ user }: useUserProps) => {
  const { ready, getUserID, registerUser } = useRookUser();

  const checkUserID = async (): Promise<string> => {
    if (!ready) {
      throw new Error("The hook is not ready");
    }

    const saved = await getUserID();

    if (saved) {
      return saved.user_id;
    }

    const response = await registerUser({
      user,
      dataSource: Platform.OS === "android" ? "Health Connect" : "Apple Health",
    });

    return response.user_id;
  };

  return {
    ready,
    checkUserID,
  };
};
```
