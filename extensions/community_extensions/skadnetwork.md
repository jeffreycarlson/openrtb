## SKAdNetwork

Sponsors: MoPub, Fyber

### Bid request

If a DSP has at least one SKAdNetworkItem in the publisher app’s `Info.plist` we would include a new object in the bid request that provides the necessary information to create a signature. Object would only be present if both the SSP SDK version and the OS version (iOS 14.0+) support SKAdNetwork.

#### Object: `BidRequest.imp.ext.skadn`

<table>
  <thead>
    <tr>
      <td>
        <strong>Attribute</strong>
      </td>
      <td>
        <strong>Description</strong>
      </td>
      <td>
        <strong>Type</strong>
      </td>
      <td>
        <strong>Example</strong>
      </td>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        <code>version</code>
      </td>
      <td>
        Version of skadnetwork supported. Always "2.0" or higher. Dependent on both the OS version and the SDK version.
      </td>
      <td>
        string
      </td>
      <td>
        "version": "2.0"
      </td>
    </tr>
    <tr>
      <td>
        <code>sourceapp</code>
      </td>
      <td>
        ID of publisher app in Apple’s App Store. Should match <code>BidRequest.app.bundle</code>
      </td>
      <td>
        string
      </td>
      <td>
        "sourceapp": "880047117"
      </td>
    </tr>
    <tr>
      <td>
        <code>skadnetids</code>
      </td>
      <td>
        A subset of SKAdNetworkItem entries in the publisher app’s info.plist that are relevant to the DSP.
      </td>
      <td>
        array
      </td>
      <td>
        "skadnetids": ["cDkw7geQsH.skadnetwork", "qyJfv329m4.skadnetwork"]
      </td>
    </tr>
    <tr>
      <td>
        skadnhsh
      </td>
      <td>
        A hash of the full list of SKAdNetworkItem entries. Hash table will be provided outside the bidstream for DSPs to consume. See <a href="#skadnetwork-ids-hash">SKAdNetwork IDs Hash</a> for more details.
      </td>
      <td>
        string
      </td>
      <td class="text-monospace">
        "93f901d8b1cc722e48d6bbe46f2e4ce38fb851f857131fa429eaed2489453b52"
      </td>
    </tr>
    <tr>
      <td>
        <code>ext</code>
      </td>
      <td>
        Placeholder for exchange-specific extensions to OpenRTB.
      </td>
      <td>
        object
      </td>
      <td>
        "ext": {}
      </td>
    </tr>
  </tbody>
</table>

#### Example for `skadnetids`

Used for direct SSP to DSP connections where a DSP wants to only consume their own relevant SKAdNetwork IDs.

```
{
  "imp": [
    {
      "ext": {
        "skadn": {
          "version": "2.0",
          "sourceapp": "880047117",
          "skadnetids": [
            "cDkw7geQsH.skadnetwork",
            "qyJfv329m4.skadnetwork"
          ]
        }
      }
    }
  ]
}
```

#### Example for `skadnhsh`

Used for intermedary SSP to SSP/DSP to DSP connections where a full list SKAdNetwork IDs is required. Provided in a compact hash format. See <a href="#skadnetwork-ids-hash">SKAdNetwork IDs Hash</a> for more details.
```
{
  "imp": [
    {
      "ext": {
        "skadn": {
          "version": "2.0",
          "sourceapp": "880047117",
          "skadnhsh": "93f901d8b1cc722e48d6bbe46f2e4ce38fb851f857131fa429eaed2489453b52"
        }
      }
    }
  ]
}
```

### Bid response

If the bid request included the `BidRequest.imp.ext.skadn` object, then a DSP could choose to add the following object to their bid response. If the object is present in the response, then SSP would submit the [click data and signature][4] to the productView for attribution.

#### Object: `BidResponse.seatbid.bid.ext.skadn`

<table>
  <thead>
    <tr>
      <td>
        <strong>Attribute</strong>
      </td>
      <td>
        <strong>Description</strong>
      </td>
      <td>
        <strong>Type</strong>
      </td>
      <td>
        <strong>Example</strong>
      </td>
  </thead>
  <tbody>
    <tr>
      <td>
        <code>version</code>
      </td>
      <td>
        Version of SKAdNetwork desired. Must be 2.0 or above.
      </td>
      <td>
        string
      </td>
      <td>
        "version": "2.0"
      </td>
    </tr>
    <tr>
      <td>
        <code>network</code>
      </td>
      <td>
        Ad network identifier used in signature. Should match one of the items in the skadnetids array in the request
      </td>
      <td>
        string
      </td>
      <td>
        "network": "cDkw7geQsH.skadnetwork"
      </td>
    </tr>
    <tr>
      <td>
        <code>campaign</code>
      </td>
      <td>
        Campaign ID compatible with Apple’s spec. As of 2.0, should be an integer between 1 and 100, expressed as a string
      </td>
      <td>
        string
      </td>
      <td>
        "campaign": "45"
      </td>
    </tr>
    <tr>
      <td>
        <code>itunesitem</code>
      </td>
      <td>
        ID of advertiser’s app in Apple’s app store. Should match <code>BidResponse.bid.bundle</code>
      </td>
      <td>
        string
      </td>
      <td>
        "itunesitem": "880047117"
      </td>
    </tr>
    <tr>
      <td>
        <code>nonce</code>
      </td>
      <td>
        An id unique to each ad response
      </td>
      <td>
        string
      </td>
      <td>
        "nonce": "473b1a16-b4ef-43ad-9591-fcf3aefa82a7"
      </td>
    </tr>
    <tr>
      <td>
        <code>sourceapp</code>
      </td>
      <td>
        ID of publisher’s app in Apple’s app store. Should match <code>BidRequest.imp.ext.skad.sourceapp</code>
      </td>
      <td>
        string
      </td>
      <td>
        "sourceapp": "123456789"
      </td>
    </tr>
    <tr>
      <td>
        <code>timestamp</code>
      </td>
      <td>
        Unix time in millis string used at the time of signature
      </td>
      <td>
        string
      </td>
      <td>
        "timestamp": "1594406341"
      </td>
    </tr>
    <tr>
      <td>
        <code>signature</code>
      </td>
      <td>
        SKAdNetwork signature as specified by Apple
      </td>
      <td>
        string
      </td>
      <td>
        "signature": "MEQCIEQlmZRNfYzK…"
      </td>
    </tr>
    <tr>
      <td>
        <code>ext</code>
      </td>
      <td>
        Placeholder for exchange-specific extensions to OpenRTB.
      </td>
      <td>
        object
      </td>
      <td>
        "ext": {}
      </td>
    </tr>
  </tbody>
</table>

#### Example

```
{
  "seatbid": [
    {
      "bid": [
        {
          "ext": {
            "skadn": {
              "version": "2.0",
              "network": "cDkw7geQsH.skadnetwork",
              "campaign": "45",
              "itunesitem": "880047117",
              "nonce": "473b1a16-b4ef-43ad-9591-fcf3aefa82a7",
              "sourceapp": "123456789",
              "timestamp": "1594406341",
              "signature": "MEQCIEQlmZRNfYzKBSE8QnhLTIHZZZWCFgZpRqRxHss65KoFAiAJgJKjdrWdkLUOCCjuEx2RmFS7daRzSVZRVZ8RyMyUXg=="
            }
          }
        }
      ]
    }
  ]
}
```

### SKAdNetwork Support Flow

![iOS 14 SKAdNetwork Flowchart][3]

_Flow diagram of SSP SDK’s SKAdNetwork support. Objects in blue have a change required to pre-iOS-14 ad flows._

1. SSP SDK retrieves the SKAdNetworkItems from the publisher app’s Info.plist
2. SDK makes ad request to ad server including SKAdNetworkItems
3. SSP determines from Info.plist which DSPs have SKAdNetwork capabilities. Bid request to eligible DSPs includes the imp.ext.skadn object, defined above
4. DSP responds, including `seatbid.bid.ext.skadn` if the campaign requires SKAdNetwork support
5. Ad response to SDK includes `skadn` object
6. If the impression is shown and the user clicks, SSP calls `loadProduct()` with the appropriate data, including the DSP-signed signature. If valid, Apple will consider the app for install attribution
7. Target app must register that user for SKAdNetwork attribution on app launch.
8. (Optional). Target app can choose to provide an additional 6 bits of conversion value information.
9. If SKAdNetwork determines that the DSP’s click led to the install, Apple will send a postback to the DSP’s registered endpoint with the ids of the source app, target app and campaign, and conversion value if provided by the target app.

### SKAdNetwork IDs Hash

Problem: How to represent large lists of IDs programmatically where there may not be a standard list or there is publisher customization?

Solution/Implementation: SSP/SDK retrieves a list of all SKAdNetwork IDs and generates a standardized hash of the values. These hashes would be identical across SSP/SDK networks. The hashes would be available in a downloadable table, giving everyone access to a full list of IDs on each app version. A standardized list could be generated easily from the client device, simplifying transmission for prebid or other use cases.

Generating the standard hash:
1. Pull all SKAdNetwork IDs from Info.plist
2. String to lower (assumes Apple SKAdNetwork IDs are case insensitive, checking with Apple to confirm)
3. Sort Ascending
4. Dedupe
5. Concatenate comma-separated (no whitespace)
6. Use SHA256 function to hash IDs

Example:

Grab list from Info.plist:
```
2U9PT9HC89.skadnetwork
4FZDC2EVR5.skadnetwork
7UG5ZH24HU.skadnetwork
mlmmfzh3r3.skadnetwork
T38B2KH725.skadnetwork
W9Q455WK68.skadnetwork
YCLNXRL5PM.skadnetwork
```

Process list:
```
2U9PT9HC89.skadnetwork,4FZDC2EVR5.skadnetwork,7UG5ZH24HU.skadnetwork,mlmmfzh3r3.skadnetwork,T38B2KH725.skadnetwork,W9Q455WK68.skadnetwork,YCLNXRL5PM.skadnetwork
```

Output using SHA256:
```
93f901d8b1cc722e48d6bbe46f2e4ce38fb851f857131fa429eaed2489453b52
```

Looking up the above hash in the lookup table (provided for download/API from SSP) will yield the list:
```
2U9PT9HC89.skadnetwork,4FZDC2EVR5.skadnetwork,7UG5ZH24HU.skadnetwork,mlmmfzh3r3.skadnetwork,T38B2KH725.skadnetwork,W9Q455WK68.skadnetwork,YCLNXRL5PM.skadnetwork
```

## Device

If the IDFA is not available, DSPs require an alternative, limited-scope device ID in order to provide basic frequency capping functionality to advertisers. The [IDFV][5] is the same for apps from the same vendor but different across vendors. This field is only passed when IDFA is unavailable or all zeros and should.

DSPs may also want to understand what is the status of a user on iOS 14+. The `atts` field will pass the AppTrackingTransparency Framework's [authorization status][6].

### Bid request

#### Object: `BidRequest.device.ext`

<table>
  <thead>
    <tr>
      <th>
        Attribute
      </th>
      <th>
        Description
      </th>
      <th>
        Type
      </th>
      <th>
        Example
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>
        atts
      </td>
      <td>
        (iOS Only) An integer passed to represent the app's app tracking authorization status, where <br>
        0 = not determined <br>
        1 = restricted <br>
        2 = denied <br>
        3 = authorized
      </td>
      <td>
        integer
      </td>
      <td class="text-monospace">
        "atts": 3
      </td>
    </tr>
    <tr>
      <td>
        ifv
      </td>
      <td>
        IDFV of device in that publisher. Only passed when IDFA is unavailable or all zeros. Listed as ifv to match ifa field format.
      </td>
      <td>
        string
      </td>
      <td class="text-monospace">
        "ifv": "336F2BC0-245B-4242-8029-83762AB47B15"
      </td>
    </tr>
  </tbody>
</table>

#### Example

```
{
  "device": [
    {
      "ext": {
        "atts": 3,
        "ifv": "336F2BC0-245B-4242-8029-83762AB47B15"
        }
      }
    }
  ]
}
```

[1]: https://developer.apple.com/documentation/storekit/skadnetwork
[2]: https://developer.apple.com/documentation/storekit/skadnetwork/configuring_the_participating_apps
[3]: https://d2al1opqne3nsh.cloudfront.net/images/skadnetwork-flow@2x.png
[4]: https://developer.apple.com/documentation/storekit/skadnetwork/generating_the_signature_to_validate_an_installation
[5]: https://developer.apple.com/documentation/uikit/uidevice/1620059-identifierforvendor
[6]: https://developer.apple.com/documentation/apptrackingtransparency/attrackingmanager/authorizationstatus
