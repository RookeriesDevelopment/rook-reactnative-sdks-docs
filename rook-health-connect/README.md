# SDK Health Connect in React Native

Steps to integrate Health Connect Data extraction and transmission into your app, if you need a more detailed description example look at out [demo app](https://github.com/RookeriesDevelopment/rook_demo_app_react_native)

## Content

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)

   1. [useRookHCBody](#useRookHCBody)
   2. [useRookHCPermissions](#useRookHCPermissions)
   3. [useRookHCPhysical](#useRookHCPhysical)
   4. [useRookHCSleep](#useRookHCSleep)
   5. [useRookHCEvents](#useRookHCEvents)

## Installation <a id="instalation"></a>

To build a project using the Rook Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **Android** this means that it won't work with iOS.

1. Install the dependencies

**npm**

```bash
npm i react-native-rook-health-connect
```

**yarn**

```bash
yarn add react-native-rook-health-connect
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

Then we need to configure the android project. open the android project inside android studio. We need to modify the `AndroidManifest.xml` file at the top level paste the next permissions in order to access to the Health connection records.

We need to add inside your activity tag an intent filter to open Health Connect APP

```xml
<intent-filter>
  <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE" />
</intent-filter>
```

Your `AndroidManifest.xml` file should look like this

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    ...

    <application
      ...>
      <activity
        ...>
        <intent-filter>
            ...
        </intent-filter>

          <!-- For supported versions through Android 13, create an activity to show the rationale
        of Health Connect permissions once users click the privacy policy link. -->
          <intent-filter>
              <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE" />
          </intent-filter>
      </activity>

      <!-- For versions starting Android 14, create an activity alias to show the rationale
     of Health Connect permissions once users click the privacy policy link. -->
      <activity-alias
        android:name="ViewPermissionUsageActivity"
        android:exported="true"
        android:permission="android.permission.START_VIEW_PERMISSION_USAGE"
        android:targetActivity=".MainActivity">

        <intent-filter>
          <action android:name="android.intent.action.VIEW_PERMISSION_USAGE" />
          <category android:name="android.intent.category.HEALTH_PERMISSIONS" />
        </intent-filter>
      </activity-alias>
    </application>
</manifest>
```

Into the same file we need to set the `minSdkVersion`, `targetSdkVerion`

```gradle
android {
  ...
  minSdkVersion: 26
  targetSdkVersion 34
  ...
}
```

#### Obfuscation

If you are using obfuscation consider the following:

In your gradle.properties (Project level) add the following to disable R8 full mode:

```properties
android.enableR8.fullMode=false
```

If you want to enable full mode add the following rules to proguard-rules.pro:

````text
# Keep generic signature of Call, Response (R8 full mode strips signatures from non-kept items).
-keep,allowobfuscation,allowshrinking interface retrofit2.Call
-keep,allowobfuscation,allowshrinking class retrofit2.Response

# With R8 full mode generic signatures are stripped for classes that are not
# kept. Suspend functions are wrapped in continuations where the type argument
# is used.
-keep,allowobfuscation,allowshrinking class kotlin.coroutines.Continuation

## Package usage <a id="packageUsage"></a>

### useRookHCBody <a id="useRookHCBody"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition

```ts
const useRookHCBody: () => RookHCBody;

interface RookHCBody {
  getBodySummaryLastDate: () => Promise<string>;
  getBodySummary: (date: string) => Promise<BodySummary>;
}
````

- `getBodySummaryLastDate`: Check the last date when you fetch data of body data
- `getBodySummary`: Fetch body summary, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button, TextInput } from "react-native";
import { useRookHCBody } from "rook_health_connect";

export const BodyExample = () => {
  const [date, setDate] = useState("");
  const [data, setData] = useState("{}");

  const { getBodySummaryLastDate, getBodySummary } = useRookHCBody();

  const handleLastDate = async (): Promise<void> => {
    try {
      const result = await getBodySummaryLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleSummary = async (): Promise<void> => {
    try {
      // The date should be in the format YYYY-MM-DD
      const r = await getBodySummary(date);
      setData(JSON.stringify(r));
    } catch (error) {
      setData(`${error}`);
    }
  };

  return (
    <View>
      <Text>body</Text>
      <TextInput
        placeholder="YYYY-MM-DD"
        onChangeText={(text) => setDate(text)}
      />
      <Button title="last Date" onPress={handleLastDate} />
      <Button title="get summary" onPress={handleSummary} />
      <Text>{data}</Text>
    </View>
  );
};
```

### useRookHCPermissions <a id="useRookHCPermissions"></a>

**Definition**

```ts
const useRookHCPermissions: () => RookHCPermissions;

export type PermissionType = "SLEEP" | "PHYSICAL" | "BODY" | "ALL";

const useRookHCPermissions: () => {
  ready: boolean;
  getUserTimeZone: () => Promise<UserTimeZone>;
  checkAvailability: () => Promise<AvailabilityStatus>;
  checkPermissions: (permission: PermissionType) => Promise<boolean>;
  requestPermissions: (permission: PermissionType) => Promise<void>;
  openHealthConnectSettings: () => Promise<void>;
};

type AvailabilityStatus = "INSTALLED" | "NOT_INSTALLED" | "NOT_SUPPORTED";
```

- `checkAvailability`: Checks if the health connect service is supported

Return

- `AvailabilityStatus` means if health connect services are available in the device

  - `INSTALLED` means health connect services are available
  - `NOT_INSTALLED` means health connect services are available but is necessary to install the health connect app into the device
  - `NOT_SUPPORTED` means health connect services are not available

- `getUserTimeZone` : Get the user time zone
- `checkPermissions`: Check if you have the permissions.
- `requestPermissions`: Request the permissions.
- `openHealthConnectSettings`: Open the health connect settings

**Example**

```tsx
import React from "react";
import { View, Text, Button } from "react-native";
import { useRookHCPermissions } from "react-native-rook-health-connect";

export const PermissionsView = () => {
  const {
    checkAvailability,
    checkPermissions,
    requestPermissions,
    openHealthConnectSettings,
    getUserTimeZone,
  } = useRookHCPermissions();

  const handlePress = async (): Promise<void> => {
    try {
      const result = await checkAvailability();
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handlePermissions = async (): Promise<void> => {
    try {
      const result = await checkPermissions("ALL");
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleTimeZone = async (): Promise<void> => {
    try {
      const result = await getUserTimeZone();
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleRequestPermissions = async (): Promise<void> => {
    try {
      await requestPermissions("ALL");
    } catch (error) {
      console.log(error);
    }
  };

  const handleOpen = async (): Promise<void> => {
    try {
      await openHealthConnectSettings();
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <View>
      <Text>Permisos</Text>
      <Button title="Availability" onPress={handlePress} />
      <Button title="hasAllPermissions" onPress={handlePermissions} />
      <Button title="get user TimeZone" onPress={handleTimeZone} />
      <Button
        title="requestAllPermissions"
        onPress={handleRequestPermissions}
      />
      <Button title="openHC" onPress={handleOpen} />
    </View>
  );
};
```

### useRookHCPhysical <a id="useRookHCPhysical"></a>

**Definition**

If you need more details about PhysicalSummary or PhysicalEvents please use right click an **Go to definition** to se the whole definition

```ts
const useRookHCPhysical: () => RookHCPhysical;

interface RookHCPhysical {
  getPhysicalSummaryLastDate: () => Promise<string>;
  getPhysicalEventsLastDate: () => Promise<string>;
  getPhysicalSummary: (date: string) => Promise<PhysicalSummary>;
  getPhysicalEvents: (date: string) => Promise<PhysicalEvent[]>;
}
```

- `getPhysicalSummaryLastDate`: Check the last date when you fetch data of physical summary
- `getPhysicalEventsLastDate`: Check the last date when you fetch data of physical events
- `getPhysicalSummary`: Fetch physical summary, the date should be in format YYYY-MM-DD
- `getPhysicalEvents`: Fetch physical events, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button, TextInput } from "react-native";
import { useRookHCPhysical } from "rook_health_connect";

export const PhysicalView = () => {
  const [data, setData] = useState("{}");
  const [date, setDate] = useState("");

  const {
    getPhysicalSummaryLastDate,
    getPhysicalEventsLastDate,
    getPhysicalSummary,
    getPhysicalEvents,
  } = useRookHCPhysical();

  const handleLastDate = async (): Promise<void> => {
    try {
      const result = await getPhysicalSummaryLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleLastDateEvent = async (): Promise<void> => {
    try {
      const result = await getPhysicalEventsLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleSummary = async (): Promise<void> => {
    try {
      const r = await getPhysicalSummary(date);
      setData(JSON.stringify(r));
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleEventSummary = async (): Promise<void> => {
    try {
      const r = await getPhysicalEvents(date);
      setData(JSON.stringify(r));
    } catch (error) {
      setData(`${error}`);
    }
  };

  return (
    <View>
      <Text>Physical</Text>
      <TextInput
        placeholder="YYYY-MM-DD"
        onChangeText={(text) => setDate(text)}
      />
      <Button title="last Date" onPress={handleLastDate} />
      <Button title="last Date event" onPress={handleLastDateEvent} />
      <Button title="get summary" onPress={handleSummary} />
      <Button title="get summary event" onPress={handleEventSummary} />
      <Text>{data}</Text>
    </View>
  );
};
```

### useRookHCSleep <a id="useRookHCSleep"></a>

**Definition**

If you need more details about SleepSummary please use right click an **Go to definition** to se the whole definition

```ts
const useRookHCSleep: () => RookHCSleep;

interface RookHCSleep {
  getSleepSummaryLastDate: () => Promise<string>;
  getSleepSummary: (date: string) => Promise<SleepSummary>;
}
```

- `getSleepSummaryLastDate`: Check the last date when you fetch data of sleep data
- `getSleepSummary`: Fetch sleep summary, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button, TextInput } from "react-native";
import { useRookHCSleep } from "rook_health_connect";

export const SleepView = () => {
  const [date, setDate] = useState("");
  const [data, setData] = useState("{}");

  const { getSleepSummaryLastDate, getSleepSummary } = useRookHCSleep();

  const handleLastDate = async (): Promise<void> => {
    try {
      const result = await getSleepSummaryLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleSummary = async (): Promise<void> => {
    try {
      const r = await getSleepSummary(date);
      setData(JSON.stringify(r));
    } catch (error) {
      setData(`${error}`);
    }
  };

  return (
    <View>
      <Text>sleep</Text>
      <TextInput
        placeholder="YYYY-MM-DD"
        onChangeText={(text) => setDate(text)}
      />
      <Button title="last Date" onPress={handleLastDate} />
      <Button title="get summary" onPress={handleSummary} />
      <Text>{data}</Text>
    </View>
  );
};
```

### useRookHCEvents <a id="useRookHCEvents"></a>

**Definition**

If you need more details about the data that returns each event please use right click an **Go to definition** to se the whole definition

```ts
const useRookHCEvents: () => RookHCEvents;

interface RookHCEvents {
  getBodyBloodGlucoseEvents: (date: string) => Promise<HCBloodGlucoseEvent>;
  getBodyBloodPressureEvents: (date: string) => Promise<HCBloodPressureEvent>;
  getBodyMetricsEvents: (date: string) => Promise<HCBodyMetricsEvent>;
  getBodyHeartRateEvents: (date: string) => Promise<HCHeartRateEvent>;
  getBodyHydrationEvents: (date: string) => Promise<HCHydrationEvent>;
  getBodyNutritionEvents: (date: string) => Promise<HCNutritionEvent>;
  getBodyOxygenationEvents: (date: string) => Promise<HCOxygenationEvent>;
  getBodyTemperatureEvents: (date: string) => Promise<HCTemperatureEvent>;
}
```

- `getBodyBloodGlucoseEvents`: Fetch events of blood glucose, the date should be in format YYYY-MM-DD
- `getBodyBloodPressureEvents`: Fetch events of blood pressure, the date should be in format YYYY-MM-DD
- `getBodyMetricsEvents`: Fetch events of body metrics like height, weight, bmi and others, the date should be in format YYYY-MM-DD
- `getBodyHeartRateEvents`: Fetch events of heart rate, the date should be in format YYYY-MM-DD
- `getBodyHydrationEvents`: Fetch events of hydration, the date should be in format YYYY-MM-DD
- `getBodyNutritionEvents`: Fetch events of nutrition like calories, protein, fat, alcohol in take, the date should be in format YYYY-MM-DD
- `getBodyOxygenationEvents`: Fetch events of oxygenation, the date should be in format YYYY-MM-DD
- `getBodyTemperatureEvents`: Fetch events of body temperature, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { Text, Button, ScrollView } from "react-native";
import { useRookHCEvents } from "react-native-rook-health-connect";

export const EventsView = () => {
  const {
    getBodyBloodGlucoseEvents,
    getBodyBloodPressureEvents,
    getBodyMetricsEvents,
    getBodyHeartRateEvents,
    getBodyHydrationEvents,
    getBodyNutritionEvents,
    getBodyOxygenationEvents,
    getBodyTemperatureEvents,
  } = useRookHCEvents();

  const [date, setDate] = useState("");

  const handlePress = async (): Promise<void> => {
    try {
      const result = await getBodyBloodGlucoseEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handlePressureEvent = async (): Promise<void> => {
    try {
      const result = await getBodyBloodPressureEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleBodyMetricsEvent = async (): Promise<void> => {
    try {
      const result = await getBodyMetricsEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleBodyHeartRateEvents = async (): Promise<void> => {
    try {
      const result = await getBodyHeartRateEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleBodyHydrationEvents = async (): Promise<void> => {
    try {
      const result = await getBodyHydrationEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleBodyNutritionEvents = async (): Promise<void> => {
    try {
      const result = await getBodyNutritionEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handleBodyOxygenationEvents = async (): Promise<void> => {
    try {
      const result = await getBodyOxygenationEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  const handlePhysicalTemperatureEvents = async (): Promise<void> => {
    try {
      const result = await getBodyTemperatureEvents(date);
      console.log(result);
    } catch (error) {
      console.log(error);
    }
  };

  return (
    <ScrollView>
      <Text>Events</Text>
      <TextInput
        placeholder="YYYY-MM-DD"
        onChangeText={(text) => setDate(text)}
      />
      <Button title="Blood Glucose Event" onPress={handlePress} />
      <Button title="Blood Pressure Event" onPress={handlePressureEvent} />
      <Button title="Body Metrics Event" onPress={handleBodyMetricsEvent} />
      <Button
        title="Body HeartRate Event"
        onPress={handleBodyHeartRateEvents}
      />
      <Button title="Hydration Events" onPress={handleBodyHydrationEvents} />
      <Button title="Nutrition Events" onPress={handleBodyNutritionEvents} />
      <Button
        title="Oxygenation Events"
        onPress={handleBodyOxygenationEvents}
      />
      <Button
        title="Temperature Events"
        onPress={handlePhysicalTemperatureEvents}
      />
    </ScrollView>
  );
};
```
