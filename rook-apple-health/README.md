# SDK Apple Health in React Native

Steps to integrate Apple Health Data extraction and transmission into your app. If you need a more detailed description example look at out [demo app](https://github.com/RookeriesDevelopment/rook_demo_app_ios_react_native)

## Content

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Package usage](#packageUsage)

   1. [useRookAHBody](#useRookAHBody)
   2. [useRookAHPermissions](#useRookAHPermissions)
   3. [useRookAHPhysical](#useRookAHPhysical)
   4. [useRookAHSleep](#useRookAHSleep)
   5. [useRookAHEvents](#useRookAHEvents)

## Installation <a id="instalation"></a>

To build a project using the Rook Health Connect in React native you need to use at least react v16 and react native v65. This SDK is only available on **iOS** this means that it won't work with Android. The minimum version of iOS 13.0

1. Install the dependencies

**npm**

```bash
npm install react-native-rook_ah
```

**yarn**

```bash
yarn add react-native-rook_ah
```

**IMPORTANT: ENTER TO iOS FOLDER AND INSTALL AND RUN**

```bash
pod install
```

or

```
npx pod-install
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

To use Rook Apple Health SDK in your Xcode project, you need to follow these steps:
​

1. Add the HealthKit framework to your Xcode project:
   ​

- Open your project in Xcode.
- Click on your project file in the Project Navigator.
- Select your target and then click on the "Build Phases" tab.
- Click on the "+" button under the "Link Binary With Libraries" section and select "HealthKit.framework" from the list.

Then declare the privacy permissions used by this SDK. You will need to include the
NSHealthShareUsageDescription and NSHealthUpdateUsageDescription keys in your app's Info.plist file.
These keys provide a description of why your app needs to access HealthKit data and will be displayed to
the user in the permission request dialog.

```swift
<key>NSHealthShareUsageDescription</key>
<string>This app requires access to your health and fitness data in order to track your worko
<key>NSHealthUpdateUsageDescription</key>
<string>This app requires permission to write workout data to HealthKit.</string>
Usage
```

## Package usage <a id="packageUsage"></a>

### useRookAHBody <a id="useRookAHBody"></a>

**Definition**

If you need more details about BodySummary please use right click an **Go to definition** to se the whole definition

```ts
const useRookAHBody: () => RookHCBody;

interface RookHCBody {
  ready: boolean;
  getLastExtractionDateOfBody: () => Promise<boolean>;
  getBodySummary: (date: string) => Promise<boolean>;
}
```

- `ready`: Notify if the hook is ready to use
- `getLastExtractionDateOfBody`: Check the last date when you fetch data of body data
- `getBodySummary`: Fetch body summary, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button, StyleSheet, TextInput } from "react-native";
import { useRookAHBody } from "react-native-rook_ah";

export const Body = () => {
  const [date, setDate] = useState("");

  const { ready, getLastExtractionDateOfBody, getBodySummary } =
    useRookAHBody();

  const onLastDate = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfBody();
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const onBodySummary = async (): Promise<void> => {
    try {
      const response = await getBodySummary(date);
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  return ready ? (
    <View>
      <Text>Body</Text>

      <TextInput
        placeholder="yyyy-mm-dd"
        style={styles.input}
        value={date}
        onChangeText={(text) => setDate(text)}
      />
      <Button title="Last Date" onPress={onLastDate} />
      <Button title="Get Body Summary" onPress={onBodySummary} />

      <JSONTree data={data} />
    </View>
  ) : (
    <Text>Loading</Text>
  );
};

const styles = StyleSheet.create({
  input: {
    marginTop: 10,
    paddingHorizontal: 10,
    paddingVertical: 5,
    marginHorizontal: "5%",
    borderColor: "white",
    color: "white",
    borderWidth: 1,
    borderRadius: 5,
    fontSize: 16,
  },
});
```

### useRookAHPermissions <a id="useRookAHPermissions"></a>

**Definition**

```ts
const useRookAHPermissions: () => RookAHPermissions;

interface RookAHPermissions {
  ready: boolean;
  requestSleepPermissions: () => Promise<boolean>;
  requestPhysicalPermissions: () => Promise<void>;
  requestBodyPermissions: () => Promise<void>;
  requestAllPermissions: () => Promise<void>;
  getUserTimeZone: () => Promise<boolean>;
}
```

- `ready`: Notify if the hook is ready to use
- `requestSleepPermissions`: Request permission to read Sleep Data
- `requestPhysicalPermissions`: Request permission to read Physical Data
- `requestBodyPermissions`: Request permission to read Body Data
- `requestAllPermissions`: Request to read all data
- `getUserTimeZone`: get the current time zone of the user

**Example**

```tsx
import React from "react";
import { useRookAHPermissions } from "react-native-rook_ah";
import { Button, View, Text } from "react-native";

export const Permissions = () => {
  const {
    requestSleepPermissions,
    requestPhysicalPermissions,
    requestBodyPermissions,
    requestAllPermissions,
  } = useRookAHPermissions();

  const onSleepPermissions = async (): Promise<void> => {
    await requestSleepPermissions();
  };

  const onPhysicalPermissions = async (): Promise<void> => {
    await requestPhysicalPermissions();
  };

  const onBodyPermissions = async (): Promise<void> => {
    await requestBodyPermissions();
  };

  const onAllPermissions = async (): Promise<void> => {
    await requestAllPermissions();
  };

  return (
    <View>
      <Text>Permissions</Text>
      <Button title="Sleep Permissions" onPress={onSleepPermissions} />
      <Button title="Physical Permissions" onPress={onPhysicalPermissions} />
      <Button title="Body Permissions" onPress={onBodyPermissions} />
      <Button title="All Permissions" onPress={onAllPermissions} />
    </View>
  );
};
```

### useRookAHPhysical <a id="useRookAHPhysical"></a>

**Definition**

If you need more details about PhysicalSummary or PhysicalEvents please use right click an **Go to definition** to se the whole definition

```ts
const useRookAHPhysical: () => RookAHPhysical;

interface RookAHPhysical {
  ready: boolean;
  getLastExtractionDateOfPhysical: () => Promise<boolean>;
  getPhysicalSummary: (date: string) => Promise<boolean>;
}
```

- `ready`: Notify if the hook is ready to use
- `getLastExtractionDateOfPhysical`: Check the last date when you fetch data of physical data
- `getPhysicalSummary`: Fetch physical summary, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { View, Text, Button, StyleSheet, TextInput } from "react-native";
import { useRookAHPhysical } from "react-native-rook_ah";

export const Physical = () => {
  const [date, setDate] = useState("");

  const { ready, getLastExtractionDateOfPhysical, getPhysicalSummary } =
    useRookAHPhysical();

  const onLastDate = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfPhysical();
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const onPhysicalSummary = async (): Promise<void> => {
    try {
      const response = await getPhysicalSummary(date);
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  return ready ? (
    <View>
      <Text>Body</Text>

      <TextInput
        placeholder="yyyy-mm-dd"
        style={styles.input}
        value={date}
        onChangeText={(text) => setDate(text)}
      />
      <Button title="Last Date" onPress={onLastDate} />
      <Button title="Get Body Summary" onPress={onPhysicalSummary} />

      <JSONTree data={data} />
    </View>
  ) : (
    <Text>Loading</Text>
  );
};

const styles = StyleSheet.create({
  input: {
    marginTop: 10,
    paddingHorizontal: 10,
    paddingVertical: 5,
    marginHorizontal: "5%",
    borderColor: "white",
    color: "white",
    borderWidth: 1,
    borderRadius: 5,
    fontSize: 16,
  },
});
```

### useRookAHSleep <a id="useRookAHSleep"></a>

**Definition**

If you need more details about SleepSummary please use right click an **Go to definition** to se the whole definition

```ts
const useRookAHSleep: () => RookAHPhysical;

interface RookAHPhysical {
  ready: boolean;
  getLastExtractionDateOfSleep: () => Promise<boolean>;
  getSleepSummary: (date: string) => Promise<boolean>;
}
```

- `ready`: Notify if the hook is ready to use
- `getLastExtractionDateOfSleep`: Check the last date when you fetch data of physical data
- `getSleepSummary`: Fetch physical summary, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from 'react';
import { View, Text, Button, StyleSheet, TextInput } from 'react-native';
import { useRookAHSleep } from 'react-native-rook_ah';

export const Sleep = () => {
  const [date, setDate] = useState('');

  const { ready, getLastExtractionDateOfSleep, getSleepSummary } =
    useRookAHSleep();

  const onLastDate = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfSleep();
      console.log(response);
    } catch (error) {
      console.log(error);
    }
  };

  const onSleepSummary = async (): Promise<void> => {
    try {
      const response = await getSleepSummary(date);
      console.log(response));
    } catch (error) {
      console.log(error);
    }
  };

  return ready ? (
    <View>
      <Text
        style={[
          Fonts.textPrimary,
          Fonts.textBold,
          Fonts.textRegular,
          Fonts.textCenter,
          Gutters.smallTMargin,
          Gutters.smallBMargin,
        ]}
      >
        Sleep
      </Text>

      <TextInput
        placeholder="yyyy-mm-dd"
        style={styles.input}
        value={date}
        onChangeText={text => setDate(text)}
      />
      <Button title="Last Date" onPress={onLastDate} />
      <Button title="Get Sleep Summary" onPress={onSleepSummary} />

      <JSONTree data={data} />
    </View>
  ) : (
    <Text>Loading</Text>
  );
};

const styles = StyleSheet.create({
  input: {
    marginTop: 10,
    paddingHorizontal: 10,
    paddingVertical: 5,
    marginHorizontal: '5%',
    borderColor: 'white',
    color: 'white',
    borderWidth: 1,
    borderRadius: 5,
    fontSize: 16,
  },
});

```

### useRookAHEvents <a id="useRookAHEvents"></a>

**Definition**

If you need more details about the data that returns each event please use right click an **Go to definition** to se the whole definition

```ts
const useRookAHEvents: () => RookAHEvents;

interface RookHCEvents {
  ready: boolean;
  getLastExtractionDateOfActivityEvents: () => Promise<string>;
  getLastExtractionDateOfBodyOxygenationEvents: () => Promise<string>;
  getLastExtractionDateOfPhysicalOxygenationEvents: () => Promise<string>;
  getLastExtractionDateOfBodyHeartRateEvents: () => Promise<string>;
  getLastExtractionDateOfPhysicalHeartRateEvents: () => Promise<string>;
  getActivityEvents: (date: string) => Promise<ActivityEvent[]>;
  getBodyOxygenationEvents: (date: string) => Promise<BodyOxygenationEvent[]>;
  getPhysicalOxygenationEvents: (
    date: string
  ) => Promise<BodyOxygenationEvent[]>;
  getBodyHeartRateEvents: (date: string) => Promise<BodyHeartRateEvent[]>;
  getPhysicalHeartRateEvents: (
    date: string
  ) => Promise<PhysicalHeartRateEvent[]>;
}
```

- `getLastExtractionDateOfActivityEvents`: Return the date of the last extraction of activity events
- `getLastExtractionDateOfBodyOxygenationEvents`: Return the date of the last extraction of body oxygenation events
- `getLastExtractionDateOfPhysicalOxygenationEvents`: Return the date of the last extraction of physical oxygenation events
- `getLastExtractionDateOfBodyHeartRateEvents`: Return the date of the last extraction of body heart events
- `getLastExtractionDateOfPhysicalHeartRateEvents`: Return the date of the last extraction of physical heart events
- `getActivityEvents`: Fetch events of activity, the date should be in format YYYY-MM-DD
- `getBodyOxygenationEvents`: Fetch events of body oxygenation, the date should be in format YYYY-MM-DD
- `getPhysicalOxygenationEvents`: Fetch events of physical oxygenation, the date should be in format YYYY-MM-DD
- `getBodyHeartRateEvents`: Fetch events of body heart rate, the date should be in format YYYY-MM-DD
- `getPhysicalHeartRateEvents`: Fetch events of physical heart rate, the date should be in format YYYY-MM-DD

**NOTE:** The date should be formatted as YYYY-MM-DD

**Example**

```tsx
import React, { useState } from "react";
import { Text, Button, StyleSheet, TextInput, ScrollView } from "react-native";
import { useRookAHEvents } from "react-native-rook_ah";

export const EventsScreen = () => {
  const [date, setDate] = useState("");

  const {
    ready,
    getLastExtractionDateOfActivityEvents,
    getLastExtractionDateOfBodyOxygenationEvents,
    getLastExtractionDateOfBodyHeartRateEvents,
    getLastExtractionDateOfPhysicalOxygenationEvents,
    getLastExtractionDateOfPhysicalHeartRateEvents,
    getActivityEvents,
    getBodyOxygenationEvents,
    getPhysicalOxygenationEvents,
    getBodyHeartRateEvents,
    getPhysicalHeartRateEvents,
  } = useRookAHEvents();

  const onLastDate = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfActivityEvents();
      console.log(response || "");
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onLastDateOxyEvents = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfBodyOxygenationEvents();
      console.log(response || "");
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onLastDateHREvents = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfBodyHeartRateEvents();
      console.log(response || "");
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onLastDatePOXEvents = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfPhysicalOxygenationEvents();
      console.log(response || "");
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onLastDatePHREvents = async (): Promise<void> => {
    try {
      const response = await getLastExtractionDateOfPhysicalHeartRateEvents();
      console.log(response || "");
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onActEvents = async (): Promise<void> => {
    try {
      const response = await getActivityEvents(date);
      console.log(response);
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onBOE = async (): Promise<void> => {
    try {
      const response = await getBodyOxygenationEvents(date);
      console.log(response);
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onPOE = async (): Promise<void> => {
    try {
      const response = await getPhysicalOxygenationEvents(date);
      console.log(response);
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onBHR = async (): Promise<void> => {
    try {
      const response = await getBodyHeartRateEvents(date);
      console.log(response);
    } catch (error) {
      console.log(`${error}`);
    }
  };

  const onPHR = async (): Promise<void> => {
    try {
      const response = await getPhysicalHeartRateEvents(date);
      console.log(JSON.stringify(response[0]));
      console.log(response);
    } catch (error) {
      console.log(`${error}`);
    }
  };

  return ready ? (
    <ScrollView>
      <Text>Body</Text>

      <TextInput
        placeholder="yyyy-mm-dd"
        style={styles.input}
        value={date}
        onChangeText={(text) => setDate(text)}
      />

      <Button
        title="Last Extraction Date Of ActivityEvents"
        onPress={onLastDate}
      />

      <Button
        title="Last Extraction Date Of Body Oxygenation Events"
        onPress={onLastDateOxyEvents}
      />

      <Button
        title="Last Extraction Date Of Body Heart Rate Events"
        onPress={onLastDateHREvents}
      />

      <Button
        title="Last Extraction Date Of Physical Oxygenation Events"
        onPress={onLastDatePOXEvents}
      />

      <Button
        title="Last Extraction Date Of Physical Heart Rate Events"
        onPress={onLastDatePHREvents}
      />

      <Button title="Activity Events" onPress={onActEvents} />
      <Button title="Body Oxygenation Events" onPress={onBOE} />
      <Button title="Physical Oxygenation Events" onPress={onPOE} />
      <Button title="Body Heart Rate Events" onPress={onBHR} />
      <Button title="Physical Heart Rate Events" onPress={onPHR} />

      <JSONTree data={data} />
    </ScrollView>
  ) : (
    <Text>Loading</Text>
  );
};

const styles = StyleSheet.create({
  input: {
    marginTop: 10,
    paddingHorizontal: 10,
    paddingVertical: 5,
    marginHorizontal: "5%",
    borderColor: "white",
    color: "white",
    borderWidth: 1,
    borderRadius: 5,
    fontSize: 16,
  },
});
```
