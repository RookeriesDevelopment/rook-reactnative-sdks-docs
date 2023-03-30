# SDK Health Connect in React Native

Steps to integrate Health Connect Data extraction and transmission into your app.

## Contentes

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)

   1. [useRookHCBody](#useRookHCBody)
   2. [useRookHCPermissions](#useRookHCPermissions)
   3. [useRookHCPhysical](#useRookHCPhysical)
   4. [useRookHCSleep](#useRookHCSleep)

## Installation <a id="instalation"></a>

To build a project using the Rook Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **Android** this means that it won't work with iOS.

1. Install the dependencies

**npm**

```bash
npm i rook_health_connect
```

**yarn**

```bash
yarn add rook_health_connect
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

Then we need to configure the android project. open the android project inside android studio. We need to modify the `AndroidManifest.xml` file at the top level paste the next permissions in order to access to the Health connection records.

```xml
    <uses-permission android:name="android.permission.health.READ_SLEEP" />
    <uses-permission android:name="android.permission.health.READ_STEPS" />
    <uses-permission android:name="android.permission.health.READ_DISTANCE" />
    <uses-permission android:name="android.permission.health.READ_FLOORS_CLIMBED" />
    <uses-permission android:name="android.permission.health.READ_ELEVATION_GAINED" />
    <uses-permission android:name="android.permission.health.READ_OXYGEN_SATURATION" />
    <uses-permission android:name="android.permission.health.READ_VO2_MAX" />
    <uses-permission android:name="android.permission.health.READ_TOTAL_CALORIES_BURNED" />
    <uses-permission android:name="android.permission.health.READ_ACTIVE_CALORIES_BURNED" />
    <uses-permission android:name="android.permission.health.READ_HEART_RATE" />
    <uses-permission android:name="android.permission.health.READ_RESTING_HEART_RATE" />
    <uses-permission android:name="android.permission.health.READ_HEART_RATE_VARIABILITY" />
    <uses-permission android:name="android.permission.health.READ_EXERCISE" />
    <uses-permission android:name="android.permission.health.READ_SPEED" />
    <uses-permission android:name="android.permission.health.READ_WEIGHT" />
    <uses-permission android:name="android.permission.health.READ_HEIGHT" />
    <uses-permission android:name="android.permission.health.READ_BLOOD_GLUCOSE" />
    <uses-permission android:name="android.permission.health.READ_BLOOD_PRESSURE" />
    <uses-permission android:name="android.permission.health.READ_HYDRATION" />
    <uses-permission android:name="android.permission.health.READ_BODY_TEMPERATURE" />

```

Into the same file we need to add a query.

```xml
<queries>
  <package android:name="com.google.android.apps.healthdata" />
</queries>
```

Finally we need to add inside your activity tag an intent filter to open Health Connect APP

```xml
<intent-filter>
  <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE" />
</intent-filter>
```

Your `AndroidManifest.xml` file should look like this

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android">

    <uses-permission android:name="android.permission.health.READ_SLEEP" />
    <uses-permission android:name="android.permission.health.READ_STEPS" />
    <uses-permission android:name="android.permission.health.READ_DISTANCE" />
    <uses-permission android:name="android.permission.health.READ_FLOORS_CLIMBED" />
    <uses-permission android:name="android.permission.health.READ_ELEVATION_GAINED" />
    <uses-permission android:name="android.permission.health.READ_OXYGEN_SATURATION" />
    <uses-permission android:name="android.permission.health.READ_VO2_MAX" />
    <uses-permission android:name="android.permission.health.READ_TOTAL_CALORIES_BURNED" />
    <uses-permission android:name="android.permission.health.READ_ACTIVE_CALORIES_BURNED" />
    <uses-permission android:name="android.permission.health.READ_HEART_RATE" />
    <uses-permission android:name="android.permission.health.READ_RESTING_HEART_RATE" />
    <uses-permission android:name="android.permission.health.READ_HEART_RATE_VARIABILITY" />
    <uses-permission android:name="android.permission.health.READ_EXERCISE" />
    <uses-permission android:name="android.permission.health.READ_SPEED" />
    <uses-permission android:name="android.permission.health.READ_WEIGHT" />
    <uses-permission android:name="android.permission.health.READ_HEIGHT" />
    <uses-permission android:name="android.permission.health.READ_BLOOD_GLUCOSE" />
    <uses-permission android:name="android.permission.health.READ_BLOOD_PRESSURE" />
    <uses-permission android:name="android.permission.health.READ_HYDRATION" />
    <uses-permission android:name="android.permission.health.READ_BODY_TEMPERATURE" />

    <queries>
        <package android:name="com.google.android.apps.healthdata" />
    </queries>

    <application
      ...>
      <activity
        ...>
        <intent-filter>
            ...
        </intent-filter>

          <intent-filter>
              <action android:name="androidx.health.ACTION_SHOW_PERMISSIONS_RATIONALE" />
          </intent-filter>

      </activity>
    </application>
</manifest>

```

Other file that we need to visit is the `android/settings.gradle` include the next code

```gradle
include ':react-native-shared-preferences', ':app'
project(':react-native-shared-preferences').projectDir = new File(rootProject.projectDir, '../node_modules/react-native-shared-preferences/android')
```

In `android/app/build.gradle`

```
dependencies {
  ...
  implementation project(":react-native-shared-preferences")
  implementation "com.rookmotion.android:rook-rn-health-connect:0.3.0"
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

      packages.add(new RNSharedPreferencesReactPackage());
      packages.add(new RNRookHCPackager());

      return packages;
    }
  }
```

## Package usage <a id="packageUsage"></a>

### useRookHCBody <a id="useRookHCBody"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition

```ts
const useRookHCBody: () => RookHCBody;

interface RookHCBody {
  getBodySummaryLastDate: () => Promise<string>;
  hasBodyPermissions: () => Promise<boolean>;
  requestBodyPermissions: () => Promise<void>;
  getBodySummary: (date: string) => Promise<BodySummary>;
}
```

- `getBodySummaryLastDate`: Check the last date when you fetch data of body data
- `hasBodyPermissions`: Return a boolean showing if body data are allowed
- `requestBodyPermissions`: Request permissions only for body data
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

  const {
    getBodySummaryLastDate,
    hasBodyPermissions,
    requestBodyPermissions,
    getBodySummary,
  } = useRookHCBody();

  const handleLastDate = async (): Promise<void> => {
    try {
      const result = await getBodySummaryLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handlePermissions = async (): Promise<void> => {
    try {
      const result = await hasBodyPermissions();
      setData(`hasPermissions ${result}`);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleRequestPermissions = async (): Promise<void> => {
    try {
      await requestBodyPermissions();
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
      <Button title="hasAllPermissions" onPress={handlePermissions} />
      <Button
        title="requestAllPermissions"
        onPress={handleRequestPermissions}
      />
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

interface RookHCPermissions {
  checkAvailability: () => Promise<AvailabilityStatus>;
  hasAllPermissions: () => Promise<boolean>;
  requestPermissions: () => Promise<void>;
  openHealthConnectSettings: () => Promise<void>;
}

type AvailabilityStatus = "INSTALLED" | "NOT_INSTALLED" | "NOT_SUPPORTED";
```

- `checkAvailability`: Checks if the health connect service is supported

Return

- `AvailabilityStatus` means if health connect services are available in the device

  - `INSTALLED` means health connect services are available
  - `NOT_INSTALLED` means health connect services are available but is necessary to install the health connect app into the device
  - `NOT_SUPPORTED` means health connect services are not available

- `hasAllPermissions`: Check if the you already request all health connect permissions
- `requestPermissions`: Request all health connect permissions
- `openHealthConnectSettings`: Open the health connect settings

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button } from "react-native";
import { useRookHCPermissions } from "rook_health_connect";

export const PermissionsExample = () => {
  const [data, setData] = useState("");

  const {
    checkAvailability,
    hasAllPermissions,
    requestPermissions,
    openHealthConnectSettings,
  } = useRookHCPermissions();

  const handleAvailability = async (): Promise<void> => {
    try {
      const result = await checkAvailability();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handlePermissions = async (): Promise<void> => {
    try {
      const result = await hasAllPermissions();
      setData(`has permissions ${result}`);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleRequestPermissions = async (): Promise<void> => {
    try {
      await requestPermissions();
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleOpen = async (): Promise<void> => {
    try {
      await openHealthConnectSettings();
    } catch (error) {
      setData(`${error}`);
    }
  };

  return (
    <View>
      <Text>Hola</Text>
      <Button title="Availability" onPress={handleAvailability} />
      <Button title="hasAllPermissions" onPress={handlePermissions} />
      <Button
        title="requestAllPermissions"
        onPress={handleRequestPermissions}
      />
      <Button title="openHC" onPress={handleOpen} />
      <Text>{data}</Text>
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
  hasPhysicalPermissions: () => Promise<boolean>;
  requestPhysicalPermissions: () => Promise<void>;
  getPhysicalSummary: (date: string) => Promise<PhysicalSummary>;
  getPhysicalEvents: (date: string) => Promise<PhysicalEvents>;
}
```

- `getPhysicalSummaryLastDate`: Check the last date when you fetch data of physical summary
- `getPhysicalEventsLastDate`: Check the last date when you fetch data of physical events
- `hasPhysicalPermissions`: Return a boolean showing if physical data are allowed
- `requestPhysicalPermissions`: Request permissions only for physical data
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
    hasPhysicalPermissions,
    requestPhysicalPermissions,
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

  const handlePermissions = async (): Promise<void> => {
    try {
      const result = await hasPhysicalPermissions();
      setData(`hasPermissions ${result}`);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleRequestPermissions = async (): Promise<void> => {
    try {
      await requestPhysicalPermissions();
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
      <Button title="hasAllPermissions" onPress={handlePermissions} />
      <Button
        title="requestAllPermissions"
        onPress={handleRequestPermissions}
      />
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
  hasSleepPermissions: () => Promise<boolean>;
  requestSleepPermissions: () => Promise<void>;
  getSleepSummary: (date: string) => Promise<SleepSummary>;
}
```

- `getSleepSummaryLastDate`: Check the last date when you fetch data of sleep data
- `hasSleepPermissions`: Return a boolean showing if sleep data are allowed
- `requestSleepPermissions`: Request permissions only for sleep data
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

  const {
    getSleepSummaryLastDate,
    hasSleepPermissions,
    requestSleepPermissions,
    getSleepSummary,
  } = useRookHCSleep();

  const handleLastDate = async (): Promise<void> => {
    try {
      const result = await getSleepSummaryLastDate();
      setData(result);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handlePermissions = async (): Promise<void> => {
    try {
      const result = await hasSleepPermissions();
      setData(`hasPermissions ${result}`);
    } catch (error) {
      setData(`${error}`);
    }
  };

  const handleRequestPermissions = async (): Promise<void> => {
    try {
      await requestSleepPermissions();
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
      <Button title="hasAllPermissions" onPress={handlePermissions} />
      <Button
        title="requestAllPermissions"
        onPress={handleRequestPermissions}
      />
      <Button title="get summary" onPress={handleSummary} />
      <Text>{data}</Text>
    </View>
  );
};
```
