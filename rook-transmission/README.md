# SDK Rook Transmission in React Native

This SDK will allow you to transmit health connect data to Rook services.

## For Android

If you need a more detailed description example look at out [demo app](https://github.com/RookeriesDevelopment/rook_demo_app_react_native)

## Content

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)
   1. [useRookBodyTransmission](#useRookBodyTransmission)
   2. [useRookPhysicalTransmission](#useRookPhysicalTransmission)
   3. [useRookSleepTransmission](#useRookSleepTransmission)
   4. [useRookTransmission](#useRookTransmission)

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

### useRookBodyTransmission <a id="useRookBodyTransmission"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition, this summary should be retrieved from the `react-native-android-health-connect`

```ts
type HookProps = {
  userID: string | number;
};


const const useRookBodyTransmission: ({ userID }: HookProps);

interface useRookBodyTransmission {
  enqueueBodySummary: (summary: BodySummary) => Promise<boolean>
  clearQueuedBodySummaries: () => Promise<boolean>
  uploadBodySummaries: () => Promise<boolean>
}
```

- `enqueueBodySummary`: Save a `BodySummary` into memory to later upload
- `clearQueuedBodySummaries`: Clear all the summaries saved
- `uploadBodySummaries`: Send that summaries to Rook Servers

**Example**

```tsx
import React, { FC, useState } from "react";
import { Text, View, StyleSheet, Button } from "react-native";
import { useRookBodyTransmission } from "react-native-rook-android-transmission";
import { useRookHCBody } from "react-native-rook-health-connect";

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
    useRookBodyTransmission({
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

### useRookPhysicalTransmission <a id="useRookPhysicalTransmission"></a>

**Definition**

If you need more details about PhysicalSummary please use right click an **Go to definition** to se the whole definition, this summary should be retrieved from the `react-native-android-health-connect`

```ts
type HookProps = {
  userID: string | number;
};


const const useRookPhysicalTransmission: ({ userID }: HookProps);

interface useRookPhysicalTransmission {
  enqueuePhysicalSummary: (summary: PhysicalSummary) => Promise<boolean>
  clearQueuedPhysicalSummary: () => Promise<boolean>
  uploadPhysicalSummary: () => Promise<boolean>

  enqueuePhysicalEvent: (events: PhysicalEvent[]) => Promise<boolean>
  clearQueuedPhysicalEvent: () => Promise<boolean>
  uploadPhysicalEvent: () => Promise<boolean>
}
```

- `enqueuePhysicalSummary`: Save a `PhysicalSummary` into memory to later upload
- `clearQueuedPhysicalSummary`: Clear all the summaries saved
- `uploadPhysicalSummary`: Send that summaries to Rook Servers

- `enqueuePhysicalEvent`: Save all `PhysicalEvents` into memory to later upload
- `clearQueuedPhysicalEvent`: Clear all the events saved
- `uploadPhysicalEvent`: Send that events to Rook Servers

**Example**

```tsx
import React, { FC, useState } from "react";
import { Text, View, StyleSheet, Button } from "react-native";
import { useRookPhysicalTransmission } from "react-native-rook-android-transmission";
import { useRookHCPhysical } from "react-native-rook-health-connect";

type PhysicalTransmissionProps = {
  date: string;
  userID: string | number;
};

export const PhysicalTransmission: FC<PhysicalTransmissionProps> = ({
  date,
  userID,
}) => {
  const { getPhysicalSummary, getPhysicalEvents } = useRookHCPhysical();
  const {
    enqueuePhysicalSummary,
    clearQueuedPhysicalSummaries,
    uploadPhysicalSummaries,
    enqueuePhysicalEvent,
    clearQueuedPhysicalEvents,
    uploadPhysicalEvents,
  } = useRookPhysicalTransmission({
    userID,
  });

  const [response, setResponse] = useState("{}");

  const handleQueue = async (): Promise<void> => {
    try {
      const summary = await getPhysicalSummary(date);

      const resp = await enqueuePhysicalSummary(summary);

      setResponse(`Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleClearQueue = async (): Promise<void> => {
    try {
      const resp = await clearQueuedPhysicalSummaries();
      setResponse(`Clear Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const resp = await uploadPhysicalSummaries();
      setResponse(`Upload Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleQueueEvents = async (): Promise<void> => {
    try {
      const summary = await getPhysicalEvents(date);

      const resp = await enqueuePhysicalEvent(summary);

      setResponse(`Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleClearQueueEvents = async (): Promise<void> => {
    try {
      const resp = await clearQueuedPhysicalEvents();
      setResponse(`Clear Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueueEvents = async (): Promise<void> => {
    try {
      const resp = await uploadPhysicalEvents();
      setResponse(`Upload Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Text style={styles.blacked}>Sleep</Text>
      <Button title="Enqueue Summaries" onPress={handleQueue} />
      <Button title="Clear Enqueue Summaries" onPress={handleClearQueue} />
      <Button title="Upload Enqueue Summaries" onPress={handleUploadQueue} />
      <Button title="Enqueue events" onPress={handleQueueEvents} />
      <Button title="Clear Enqueue events" onPress={handleClearQueueEvents} />
      <Button title="Upload Enqueue events" onPress={handleUploadQueueEvents} />
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

### useRookSleepTransmission <a id="useRookSleepTransmission"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition, this summary should be retrieved from the `react-native-android-health-connect`

```ts
type HookProps = {
  userID: string | number;
};


const const useRookSleepTransmission: ({ userID }: HookProps);

interface useRookSleepTransmission {
  enqueueSleepSummary: (summary: SleepSummary) => Promise<boolean>
  clearQueuedSleepSummaries: () => Promise<boolean>
  uploadSleepSummaries: () => Promise<boolean>
}
```

- `enqueueSleepSummary`: Save a `SleepSummary` into memory to later upload
- `clearQueuedSleepSummaries`: Clear all the summaries saved
- `uploadSleepSummaries`: Send that summaries to Rook Servers

**Example**

```tsx
import React, { FC, useState } from "react";
import { Text, View, StyleSheet, Button } from "react-native";
import { useRookSleepTransmission } from "react-native-rook-android-transmission";
import { useRookHCSleep } from "react-native-rook-health-connect";

type SleepTransmissionProps = {
  date: string;
  userID: string | number;
};

export const SleepTransmission: FC<SleepTransmissionProps> = ({
  date,
  userID,
}) => {
  const { getSleepSummary } = useRookHCSleep();
  const { enqueueSleepSummary, uploadSleepSummaries } =
    useRookSleepTransmission({
      userID,
    });

  const [response, setResponse] = useState("{}");

  const handleQueue = async (): Promise<void> => {
    try {
      const summary = await getSleepSummary(date);

      const resp = await enqueueSleepSummary(summary);

      setResponse(`Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  const handleClearQueue = async (): Promise<void> => {
    try {
      //const resp = await clearQueuedSleepSummaries();
      setResponse(`Clear Enqueue result: `);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const resp = await uploadSleepSummaries();
      setResponse(`Upload Enqueue result: ${resp}`);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Text style={styles.blacked}>Sleep</Text>
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

### useRookTransmission <a id="useRookTransmission"></a>

**Definition**

This transmits all the new events

- Blood Glucose Event
- Blood Pressure Event
- Body Metrics Event
- Heart Rate Event
- Hydration Event
- Mood Event
- Nutrition Event
- Oxygenation Event
- Stress Event
- Temperature Event

This information should be retrieved form `react-native-android-health-connect`

```ts
type HookProps = {
  userID: string | number;
};


const const useRookSleepTransmission: ({ userID }: HookProps);

interface useRookSleepTransmission {
  enqueueBloodGlucoseEvent: (events: HCBloodGlucoseEvent[]) => Promise<boolean>
  clearQueuedBloodGlucoseEvents: () => Promise<boolean>
  uploadBloodGlucoseEvents: () => Promise<boolean>

  enqueueBloodGlucoseEvent: (events: HCBloodPressureEvent[]) => Promise<boolean>
  clearQueuedBloodPressureEvents: () => Promise<boolean>
  uploadBloodPressureEvents: () => Promise<boolean>

  enqueueBodyMetricsEvent: (events: HCBodyMetricsEvent[]) => Promise<boolean>
  clearQueuedBodyMetricsEvents: () => Promise<boolean>
  uploadBodyMetricsEvents: () => Promise<boolean>

  enqueueHeartRateEvent: (events: HCHeartRateEvent[]) => Promise<boolean>
  clearQueuedHeartRateEvents: () => Promise<boolean>
  uploadHeartRateEvents: () => Promise<boolean>

  enqueueHydrationEvent: (events: HCHydrationEvent[]) => Promise<boolean>
  clearQueuedHydrationEvents: () => Promise<boolean>
  uploadHydrationEvents: () => Promise<boolean>

  enqueueMoodEvent: (events: HCMoodEvent[]) => Promise<boolean>
  clearQueuedMoodEvents: () => Promise<boolean>
  uploadMoodEvents: () => Promise<boolean>

  enqueueNutritionEvent: (events: HCNutritionEvent[]) => Promise<boolean>
  clearQueuedNutritionEvents: () => Promise<boolean>
  uploadNutritionEvents: () => Promise<boolean>

  enqueueOxygenationEvent: (events: HCOxygenationEvent[]) => Promise<boolean>
  clearQueuedOxygenationEvents: () => Promise<boolean>
  uploadOxygenationEvents: () => Promise<boolean>

  enqueueStressEvent: (events: HCStressEvent[]) => Promise<boolean>
  clearQueuedStressEvents: () => Promise<boolean>
  uploadStressEvents: () => Promise<boolean>

  enqueueTemperatureEvent: (events: HCTemperatureEvent[]) => Promise<boolean>
  clearQueuedTemperatureEvents: () => Promise<boolean>
  uploadTemperatureEvents: () => Promise<boolean>

  uploadAll: () => Promise<boolean>
}
```

- `enqueueBloodGlucoseEvent`: Save events of `HCBloodGlucoseEvent` into memory to later upload
- `clearQueuedBloodGlucoseEvents`: Clear all the events saved
- `uploadBloodGlucoseEvents`: Send that events to Rook Servers

- `enqueueBloodPressureEvent`: Save events of `HCBloodPressureEvent` into memory to later upload
- `clearQueuedBloodPressureEvents`: Clear all the events saved
- `uploadBloodPressureEvents`: Send that events to Rook Servers

- `enqueueBodyMetricsEvent`: Save events of `HCBodyMetricsEvent` into memory to later upload
- `clearQueuedBodyMetricsEvents`: Clear all the events saved
- `uploadBodyMetricsEvents`: Send that events to Rook Servers

- `enqueueHeartRateEvent`: Save events of `HCHeartRateEvent` into memory to later upload
- `clearQueuedHeartRateEvents`: Clear all the events saved
- `uploadHeartRateEvents`: Send that events to Rook Servers

- `enqueueHydrationEvent`: Save events of `HCHydrationEvent` into memory to later upload
- `clearQueuedHydrationEvents`: Clear all the events saved
- `uploadHydrationEvents`: Send that events to Rook Servers

- `enqueueMoodEvent`: Save events of `HCMoodEvent` into memory to later upload
- `clearQueuedMoodEvents`: Clear all the events saved
- `uploadMoodEvents`: Send that events to Rook Servers

- `enqueueNutritionEvent`: Save events of `HCNutritionEvent` into memory to later upload
- `clearQueuedNutritionEvents`: Clear all the events saved
- `uploadNutritionEvents`: Send that events to Rook Servers

- `enqueueOxygenationEvent`: Save events of `HCOxygenationEvent` into memory to later upload
- `clearQueuedOxygenationEvents`: Clear all the events saved
- `uploadOxygenationEvents`: Send that events to Rook Servers

- `enqueueStressEvent`: Save events of `HCStressEvent` into memory to later upload
- `clearQueuedStressEvents`: Clear all the events saved
- `uploadStressEvents`: Send that events to Rook Servers

- `enqueueTemperatureEvent`: Save events of `HCTemperatureEvent` into memory to later upload
- `clearQueuedTemperatureEvents`: Clear all the events saved
- `uploadTemperatureEvents`: Send that events to Rook Servers

**Example**

```tsx
import React, { FC, useState } from "react";
import { Text, StyleSheet, Button, View } from "react-native";
import { useRookTransmission } from "react-native-rook-android-transmission";
import { useRookHCEvents } from "react-native-rook-health-connect";

type BodyTransmissionProps = {
  date: string;
  userID: string | number;
};

export const EventsTransmission: FC<BodyTransmissionProps> = ({
  date,
  userID,
}) => {
  const {
    getBodyBloodGlucoseEvents,
    getBodyBloodPressureEvents,
    getBodyMetricsEvents,
    getBodyHeartRateEvents,
    getBodyHydrationEvents,
    getBodyMoodEvents,
    getBodyNutritionEvents,
    getBodyOxygenationEvents,
    getPhysicalStressEvents,
    getBodyTemperatureEvents,
  } = useRookHCEvents();
  const {
    enqueueBloodGlucoseEvent,
    clearQueuedBloodGlucoseEvents,
    uploadBloodGlucoseEvents,
    enqueueBloodPressureEvent,
    clearQueuedBloodPressureEvents,
    uploadBloodPressureEvents,
    enqueueBodyMetricsEvent,
    clearQueuedBodyMetricsEvents,
    uploadBodyMetricsEvents,
    enqueueHeartRateEvent,
    clearQueuedHeartRateEvents,
    uploadHeartRateEvents,
    enqueueHydrationEvent,
    clearQueuedHydrationEvents,
    uploadHydrationEvents,
    enqueueMoodEvent,
    clearQueuedMoodEvents,
    uploadMoodEvents,
    enqueueNutritionEvent,
    clearQueuedNutritionEvents,
    uploadNutritionEvents,
    enqueueOxygenationEvent,
    clearQueuedOxygenationEvents,
    uploadOxygenationEvents,
    enqueueStressEvent,
    clearQueuedStressEvents,
    uploadStressEvents,
    enqueueTemperatureEvent,
    clearQueuedTemperatureEvents,
    uploadTemperatureEvents,
  } = useRookTransmission({ userID });

  const [response, setResponse] = useState("{}");

  const handleBGQ = async (): Promise<void> => {
    try {
      const result = await getBodyBloodGlucoseEvents(date);

      const r = await enqueueBloodGlucoseEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearBGQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedBloodGlucoseEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadBGQ = async (): Promise<void> => {
    try {
      const r = await uploadBloodGlucoseEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleBPQ = async (): Promise<void> => {
    try {
      const result = await getBodyBloodPressureEvents(date);

      const r = await enqueueBloodPressureEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearBPQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedBloodPressureEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadBPQ = async (): Promise<void> => {
    try {
      const r = await uploadBloodPressureEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleBMQ = async (): Promise<void> => {
    try {
      const result = await getBodyMetricsEvents(date);

      const r = await enqueueBodyMetricsEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearBMQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedBodyMetricsEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadBMQ = async (): Promise<void> => {
    try {
      const r = await uploadBodyMetricsEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleHRQ = async (): Promise<void> => {
    try {
      const result = await getBodyHeartRateEvents(date);

      const r = await enqueueHeartRateEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearHRQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedHeartRateEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadHRQ = async (): Promise<void> => {
    try {
      const r = await uploadHeartRateEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };
  const handleHQ = async (): Promise<void> => {
    try {
      const result = await getBodyHydrationEvents(date);

      const r = await enqueueHydrationEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearHQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedHydrationEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadHQ = async (): Promise<void> => {
    try {
      const r = await uploadHydrationEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleMQ = async (): Promise<void> => {
    try {
      const result = await getBodyMoodEvents(date);

      const r = await enqueueMoodEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearMQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedMoodEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadMQ = async (): Promise<void> => {
    try {
      const r = await uploadMoodEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleNQ = async (): Promise<void> => {
    try {
      const result = await getBodyNutritionEvents(date);

      const r = await enqueueNutritionEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearNQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedNutritionEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadNQ = async (): Promise<void> => {
    try {
      const r = await uploadNutritionEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleOQ = async (): Promise<void> => {
    try {
      const result = await getBodyOxygenationEvents(date);

      const r = await enqueueOxygenationEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearOQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedOxygenationEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadOQ = async (): Promise<void> => {
    try {
      const r = await uploadOxygenationEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleSQ = async (): Promise<void> => {
    try {
      const result = await getPhysicalStressEvents(date);

      const r = await enqueueStressEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearSQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedStressEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadSQ = async (): Promise<void> => {
    try {
      const r = await uploadStressEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleTQ = async (): Promise<void> => {
    try {
      const result = await getBodyTemperatureEvents(date);

      const r = await enqueueTemperatureEvent(result);

      setResponse(`Enqueue: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleClearTQ = async (): Promise<void> => {
    try {
      const r = await clearQueuedTemperatureEvents();

      setResponse(`clear: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  const handleUploadTQ = async (): Promise<void> => {
    try {
      const r = await uploadTemperatureEvents();

      setResponse(`Upload: ${r}`);
    } catch (error) {
      setResponse(`${error}`);
    }
  };

  return (
    <View style={styles.gridContainer}>
      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Blood Glucose" onPress={handleBGQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Blood Glucose"
            onPress={handleClearBGQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Blood Glucose"
            onPress={handleUploadBGQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Blood Pressure" onPress={handleBPQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Blood Pressure"
            onPress={handleClearBPQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Blood Pressure"
            onPress={handleUploadBPQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Body Metrics Pressure" onPress={handleBMQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Body Metrics Pressure"
            onPress={handleClearBMQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Body Metrics Pressure"
            onPress={handleUploadBMQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Heart Pressure" onPress={handleHRQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Heart Pressure"
            onPress={handleClearHRQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Heart Pressure"
            onPress={handleUploadHRQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Hydration Event" onPress={handleHQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Hydration Event"
            onPress={handleClearHQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Hydration Event"
            onPress={handleUploadHQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Mood Event" onPress={handleMQ} />
        </View>
        <View style={styles.cell}>
          <Button title="Clear Enqueue Mood Event" onPress={handleClearMQ} />
        </View>
        <View style={styles.cell}>
          <Button title="Upload Enqueue Mood Event" onPress={handleUploadMQ} />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Nutrition Event" onPress={handleNQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Nutrition Event"
            onPress={handleClearNQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Nutrition Event"
            onPress={handleUploadNQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Oxygenation Event" onPress={handleOQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Oxygenation Event"
            onPress={handleClearOQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Oxygenation Event"
            onPress={handleUploadOQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Stress Event" onPress={handleSQ} />
        </View>
        <View style={styles.cell}>
          <Button title="Clear Enqueue Stress Event" onPress={handleClearSQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Stress Event"
            onPress={handleUploadSQ}
          />
        </View>
      </View>

      <View style={styles.row}>
        <View style={styles.cell}>
          <Button title="Enqueue Temperature Event" onPress={handleTQ} />
        </View>
        <View style={styles.cell}>
          <Button
            title="Clear Enqueue Temperature Event"
            onPress={handleClearTQ}
          />
        </View>
        <View style={styles.cell}>
          <Button
            title="Upload Enqueue Temperature Event"
            onPress={handleUploadTQ}
          />
        </View>
      </View>

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
  gridContainer: {
    flexDirection: "column",
    flexGrow: 1,
  },
  row: {
    flexDirection: "row",
  },
  cell: {
    width: 120, // Adjust the width and height as needed
    height: 50,
    borderWidth: 1,
    borderColor: "black",
  },
});
```

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
