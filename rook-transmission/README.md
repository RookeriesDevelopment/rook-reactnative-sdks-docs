# SDK Rook Transmission in React Native

This SDK will allow you to transmit health connect data to Rook services.

## For Android

If you need a more detailed description example look at out [demo app](https://github.com/RookeriesDevelopment/rook_demo_app_react_native)

## Content

0. [Docs for ios](#ios)
1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)
   1. [useRookBodyTransmission](#useRookBodyTransmission)
   2. [useRookPhysicalTransmission](#useRookPhysicalTransmission)
   3. [useRookSleepTransmission](#useRookSleepTransmission)
   4. [useRookEventsTransmission](#useRookTransmission)
   5. [useRookConfiguration](#useRookConfiguration)

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
        environment: "sandbox | production",
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
  userID: string;
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
  userID: string;
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
  userID: string;
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
  userID: string;
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
  userID: string;
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
  userID: string;
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
  userID: string;
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
import { useRookEventsTransmission } from "react-native-rook-android-transmission";
import { useRookHCEvents } from "react-native-rook-health-connect";

type BodyTransmissionProps = {
  date: string;
  userID: string;
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
  } = useRookEventsTransmission({ userID });

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

### useRookConfiguration <a id="useRookConfiguration"></a>

**Definition**

This hook help you to update the timezone of the user

```ts
type HookProps = {
  userID: string;
};

type TimezoneProps = {
  timezone: string;
  offset: number;
};

const useRookConfiguration: ({ userID }: HookProps) => {
  ready: boolean;
  updateUserTimeZone: (props: TimezoneProps) => Promise<boolean>;
};
```

- `ready`: indicates when the hook is ready.
- `updateUserTimeZone`: update the timezone of the user the offset must be between -18 and 18 and the timezone should be according to IANA Time Zone Database

**Example**

```tsx
import React from "react";
import { View, Text, TouchableOpacity } from "react-native";
import { useRookConfiguration } from "react-native-rook-android-transmission";

const ExampleComponent = () => {
  const { updateUserTimeZone } = useRookConfiguration({
    userID: "YOUR-USER-ID",
  });

  const handleButtonPress = () => {
    const r = await updateUserTimeZone({
      timezone: "America/Mexico_city",
      offset: -6,
    });

    console.log(r);
  };

  return (
    <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
      <TouchableOpacity
        onPress={handleButtonPress}
        style={{
          backgroundColor: "blue",
          padding: 10,
          borderRadius: 5,
          marginTop: 20,
        }}
      >
        <Text style={{ color: "white" }}>Update Timezone</Text>
      </TouchableOpacity>
    </View>
  );
};

export default ExampleComponent;
```

# For iOS <a id="ios"></a>

If you need a more detailed description example look at out [demo app](https://github.com/RookeriesDevelopment/rook_demo_app_ios_react_native)

## Content

1. [Installation](#installation-ios)
2. [Configuration](#configuration-ios)
3. [Package usage](#packageUsage-ios)
   1. [useRookAHBodyTransmission](#useRookAHBodyTransmission)
   2. [useRookAHPhysicalTransmission](#useRookAHPhysicalTransmission)
   3. [useRookAHSleepTransmission](#useRookAHSleepTransmission)
   4. [useRookAHActivityEventsTransmission](#useRookAHActivityEventsTransmission)
   5. [useRookAHOxygenationEventsTransmission](#useRookAHOxygenationEventsTransmission)
   6. [useRookAHHeartRateEventsTransmission](#useRookAHHeartRateEventsTransmission)
   7. [useRookAHConfiguration](#useRookAHConfiguration)

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

### useRookAHBodyTransmission <a id="useRookAHBodyTransmission"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition

```ts
type BodyProps = {
  userID: string;
};

export declare const useRookAHBodyTransmission: ({ userID }: BodyProps) => {
  ready: boolean;
  enqueueBodySummary: (date: string, data: BodySummary) => Promise<boolean>;
  getBodySummariesStored: () => Promise<any>;
  uploadBodySummaries: () => Promise<any>;
};
```

- `ready`: indicates when the hook is ready.
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
  ready: boolean;
  saveSleepSummary: (data: SleepSummary) => Promise<boolean>;
  getSleepSummariesStored: () => Promise<any>;
  uploadSleepSummaries: () => Promise<any>;
};
```

- `ready`: indicates when the hook is ready.
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
  ready: boolean;
  enqueuePhysicalSummary: (
    date: string,
    data: PhysicalSummary
  ) => Promise<boolean>;
  getPhysicalSummariesStored: () => Promise<any>;
  uploadPhysicalSummaries: () => Promise<any>;
};
```

- `ready`: indicates when the hook is ready.
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

### useRookAHActivityEventsTransmission <a id="useRookAHActivityEventsTransmission"></a>

**Definition**

If you need more details about Activity events please use right click an **Go to definition** to se the whole definition

```ts
type ActivityProps = {
  userID: string;
};
const useRookAHActivityEventsTransmission: ({ userID }: ActivityProps) => {
  ready: boolean;
  enqueueActivityEvents: (data: ActivityEvent[]) => Promise<boolean>;
  getCountActivityEvents: () => Promise<number>;
  uploadActivityEvents: () => Promise<boolean>;
};
```

- `ready`: indicates when the hook is ready.
- `enqueueActivityEvents`: saves the activity events to queue to later upload
  - `data`: it's the summary to save
- `getCountActivityEvents`: Retrieves the number of activity events queued to upload
- `uploadActivityEvents`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React from "react";
import { Button, View } from "react-native";
import { useRookAHActivityEventsTransmission } from "react-native-rook-transmission";
import { useRookAHEvents } from "react-native-rook_ah";

export const EventsView = () => {
  const { enqueueActivityEvents, uploadActivityEvents, ...rest } =
    useRookAHActivityEventsTransmission({
      userID: "USER_ID",
    });

  const { getActivityEvents } = useRookAHEvents();

  const handleEnqueueActivityEvents = async (): Promise<void> => {
    try {
      const date = "2023-08-06";
      const summary = await getActivityEvents(date);
      const response = await enqueueActivityEvents(summary);
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const results = await rest.getCountActivityEvents();
      console.log(results);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const results = await uploadActivityEvents();
      console.log(results);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button
        title="Enqueue Activity events"
        onPress={handleEnqueueActivityEvents}
      />
      <Button title="Get activity events" onPress={handleGetQueue} />

      <Button title="Upload activity events" onPress={handleUploadQueue} />
    </View>
  );
};
```

### useRookAHHeartRateEventsTransmission <a id="useRookAHHeartRateEventsTransmission"></a>

**Definition**

If you need more details about Heart rate events please use right click an **Go to definition** to se the whole definition

```ts
type HeartRateProps = {
  userID: string;
};
const useRookAHHeartRateEventsTransmission: ({ userID }: HeartRateProps) => {
  ready: boolean;
  enqueueHeartRateEvents: (data: BodyHeartRateEvent[]) => Promise<boolean>;
  getCountPhysicalHeartRateEvents: () => Promise<number>;
  getCountBodyHeartRateEvents: () => Promise<number>;
  uploadHeartRateEvents: () => Promise<boolean>;
};
```

- `ready`: indicates when the hook is ready.
- `enqueueHeartRateEvents`: saves the heart rate events to queue to later upload
  - `data`: it's the summary to save
- `getCountPhysicalHeartRateEvents`: Retrieves the number of physical heart events queued to upload
- `getCountBodyHeartRateEvents`: Retrieves the number of body heart rate events queued to upload
- `uploadHeartRateEvents`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React from "react";
import { Button, View } from "react-native";
import { useRookAHHeartRateEventsTransmission } from "react-native-rook-transmission";
import { useRookAHEvents } from "react-native-rook_ah";

export const HeartRateEventsView = () => {
  const {
    enqueueHeartRateEvents,
    getCountBodyHeartRateEvents,
    getCountPhysicalHeartRateEvents,
    uploadHeartRateEvents,
  } = useRookAHHeartRateEventsTransmission({
    userID: "9808762",
  });

  const { getPhysicalHeartRateEvents, getBodyHeartRateEvents } =
    useRookAHEvents();

  const handleEnqueueEvents = async (): Promise<void> => {
    try {
      const date = "2023-08-06";
      const summary = await getPhysicalHeartRateEvents(date);
      const summary2 = await getBodyHeartRateEvents(date);

      const response = await enqueueHeartRateEvents([...summary, ...summary2]);
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const results = await getCountBodyHeartRateEvents();
      const results3 = await getCountPhysicalHeartRateEvents();

      console.log(results + results3);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const results = await uploadHeartRateEvents();
      console.log(results);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button title="Enqueue Heart Rate events" onPress={handleEnqueueEvents} />
      <Button title="Get Heart Rate events" onPress={handleGetQueue} />

      <Button title="Upload Heart Rate events" onPress={handleUploadQueue} />
    </View>
  );
};
```

### useRookAHOxygenationEventsTransmission <a id="useRookAHOxygenationEventsTransmission"></a>

**Definition**

If you need more details about oxygenation events please use right click an **Go to definition** to se the whole definition

```ts
type OxygenationProps = {
  userID: string;
};
const useRookAHOxygenationEventsTransmission: ({
  userID,
}: OxygenationProps) => {
  ready: boolean;
  enqueueOxygenationEvents: (data: BodyOxygenationEvent[]) => Promise<boolean>;
  getCountPhysicalOxygenationEvents: () => Promise<number>;
  getCountBodyOxygenationEvents: () => Promise<number>;
  uploadOxygenationEvents: () => Promise<boolean>;
};
```

- `ready`: indicates when the hook is ready.
- `enqueueOxygenationEvents`: saves the heart rate events to queue to later upload
  - `data`: it's the summary to save
- `getCountPhysicalOxygenationEvents`: Retrieves the number of physical oxygenation queued to upload
- `getCountBodyOxygenationEvents`: Retrieves the number of body oxygenation events queued to upload
- `uploadOxygenationEvents`: Send the queue to the server

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React from "react";
import { Button, View } from "react-native";
import { useRookAHOxygenationEventsTransmission } from "react-native-rook-transmission";
import { useRookAHEvents } from "react-native-rook_ah";

export const OxygenationEventsView = () => {
  const {
    enqueueOxygenationEvents,
    getCountBodyOxygenationEvents,
    getCountPhysicalOxygenationEvents,
    uploadOxygenationEvents,
  } = useRookAHOxygenationEventsTransmission({
    userID: "9808762",
  });

  const { getBodyOxygenationEvents } = useRookAHEvents();

  const handleEnqueueEvents = async (): Promise<void> => {
    try {
      const date = "2023-08-06";
      const summary = await getBodyOxygenationEvents(date);
      const response = await enqueueOxygenationEvents(summary);
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const handleGetQueue = async (): Promise<void> => {
    try {
      const results = await getCountBodyOxygenationEvents();
      const results3 = await getCountPhysicalOxygenationEvents();

      console.log(results + results3);
    } catch (error) {
      console.log(error);
    }
  };

  const handleUploadQueue = async (): Promise<void> => {
    try {
      const results = await uploadOxygenationEvents();
      console.log(results);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Button
        title="Enqueue Oxygenation events"
        onPress={handleEnqueueEvents}
      />
      <Button title="Get Oxygenation events" onPress={handleGetQueue} />

      <Button title="Upload oxygenation events" onPress={handleUploadQueue} />
    </View>
  );
};
```

### useRookAHConfiguration <a id="useRookAHConfiguration"></a>

**Definition**

This hook help you to update the timezone of the user

```ts
type Props = {
  userID: string;
};

type TimezoneProps = {
  timezone: string;
  offset: number;
};

const useRookAHConfiguration: ({ userID }: Props) => {
  ready: boolean;
  uploadUserTimezone: (props: TimezoneProps) => Promise<boolean>;
};
```

- `ready`: indicates when the hook is ready.
- `uploadUserTimezone`: update the timezone of the user the offset must be between -18 and 18 and the timezone should be according to IANA Time Zone Database

**Example**

```tsx
import React from "react";
import { View, Text, TouchableOpacity } from "react-native";
import { useRookAHConfiguration } from "react-native-rook-ios-transmission";

const ExampleComponent = () => {
  const { uploadUserTimezone } = useRookAHConfiguration({
    userID: "YOUR-USER-ID",
  });

  const handleButtonPress = () => {
    const r = await uploadUserTimezone({
      timezone: "America/Mexico_city",
      offset: -6,
    });

    console.log(r);
  };

  return (
    <View style={{ flex: 1, justifyContent: "center", alignItems: "center" }}>
      <TouchableOpacity
        onPress={handleButtonPress}
        style={{
          backgroundColor: "blue",
          padding: 10,
          borderRadius: 5,
          marginTop: 20,
        }}
      >
        <Text style={{ color: "white" }}>Update Timezone</Text>
      </TouchableOpacity>
    </View>
  );
};

export default ExampleComponent;
```
