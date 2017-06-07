
## Module: API Check

API Check is an aggregate of SDK.

The *API Check* module helps the developer with lifecycle management. You can query API version, status, and whether an upgrade is recommended.

### Method: checkVersion

Get version information for the API serving the SDK.

Method signature: `sdk.checkVersions()`

Returns a `checkVersionsResponse` object.

### checkVersionsResponse Object

|field|format|example|
|---|---|---|
|current|string|1.0.0|
|deprecated|string|0.9.0|
|discontinued|array of strings, each a discontinued version|0.0.1, 0.0.2|
|beta|string|2.0.0|


### Method: getStatus

Provides the status for a specific version.

Method signature: `sdk.checkStatus( versionNumber )`

Takes a `versionNumber` strng.

Returns a `checkStatusResponse` object.

|field|format|example|
|---|---|---|
|status|string|one of "current" or "deprecated" or "discontinued" or "beta"|

### Method: checkUpgrade

Provides the status for a specific version.

Method signature: `sdk.checkUpgrade( appVersion )`

Takes a `appVersion` strng.

Returns a `checkUpgradeResponse` object.

|field|format|example|
|---|---|---|
|status|string|one of "upgrade" or ...|

