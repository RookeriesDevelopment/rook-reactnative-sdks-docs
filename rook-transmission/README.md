# SDK Rook Transmission in React Native

This SDK will allow you to transmit health connect data to Rook services.

## Content

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)

## Installation <a id="instalation"></a>

To build a project using the Rook Transmission Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **Android** this means that it won't work with iOS.

1. Install the dependencies

**npm**

```bash
npm i rook_transmission
```

**yarn**

```bash
yarn add rook_transmission
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
      }}>
      <YourComponents />
    </RookConnectProvider>
  );
}
```

Then we need to configure the android project. open the android project inside android studio. We need to modify the `android/app/build.gradle` and add the next dependency.

```gradle
dependencies {
  ...
  implementation 'com.rookmotion.android:rook-rn-transmission:0.1.1'
  ...
}
```

Into the same file we need to set the `minSdkVersion` and `targetSdkVerion`

```gradle
android {
  ...
  minSdkVersion: 26
  targetSdkVersion 33
  ...
}
```

The last step register the modules in `MainApplication.java`

```java
  import com.rook.rnrookhealthconnect.RNRookHCPackager;
  import in.sriraman.sharedpreferences.RNSharedPreferencesReactPackage;

  public class MainApplication extends Application implements ReactApplication {

    @Override
    protected List<ReactPackage> getPackages() {
      List<ReactPackage> packages = new PackageList(this).getPackage();

      // ADD this Line
      packages.add(new RNRookTransmissionPackager());

      return packages;
    }
  }
```

## Package usage <a id="packageUsage"></a>

The SDK provide you with a hook called `useRookHCTransmission` this gives you the ability to transmit the health data to Rook Services.

#### Import

```js
import { useRookHCTransmission } from "rook_users";
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
