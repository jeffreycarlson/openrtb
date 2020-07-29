### SKAdNetwork

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
        "version":"2.0"
      </td>
    </tr>
    <tr>
      <td>
        <code>sourceapp</code>
      </td>
      <td>
        ID of publisher app in Apple’s App Store. Should match `BidRequest.app.bundle`
      </td>
      <td>
        string
      </td>
      <td>
        "sourceapp":"880047117"
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
        ["dsp1.skadnetwork"]
      </td>
    </tr>
  </tbody>
</table>

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
        "version":"2.0"
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
        "network":"dsp1.skadnetwork"
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
        "campaign":"45"
      </td>
    </tr>
    <tr>
      <td>
        <code>itunesitem</code>
      </td>
      <td>
        ID of advertiser’s app in Apple’s app store. Should match `BidResponse.bid.bundle`
      </td>
      <td>
        string
      </td>
      <td>
        "itunesitem":"880047117"
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
        "nonce":"473b1a16-b4ef-43ad-9591-fcf3aefa82a7"
      </td>
    </tr>
    <tr>
      <td>
        <code>sourceapp</code>
      </td>
      <td>
        ID of publisher’s app in Apple’s app store. Should match `BidRequest.imp.ext.skad.sourceapp`
      </td>
      <td>
        string
      </td>
      <td>
        "sourceapp":"123456789"
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
  </tbody>
</table>

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

[1]: https://developer.apple.com/documentation/storekit/skadnetwork
[2]: https://developer.apple.com/documentation/storekit/skadnetwork/configuring_the_participating_apps
[3]: https://raw.githubusercontent.com/mopub/mopub-docs/main/assets/images/docs/dsps/skadnetwork-flow%402x.png
[4]: https://developer.apple.com/documentation/storekit/skadnetwork/generating_the_signature_to_validate_an_installation
