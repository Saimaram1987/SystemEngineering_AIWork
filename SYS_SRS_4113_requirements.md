# SYS_SRS_4113 Requirements

Source: SY_SRS_4113_DTS AutoStage Audio Messages_V1.0.0.2.docx


## Audio Messages Requirements and Recommendations
- REQ-01: The DTS AutoStage Receiver shall download, store, play, and report impressions for Audio Messages.

## Registration
- REQ-02: All applications running in the same vehicle shall share the same **deviceId** and manufacturer credentials to avoid duplication and ensure consistent identification.

## User Consent and Privacy
- REQ-03: When requesting an opaque AAID, the DTS AutoStage Receiver shall provide user signals (such as a hashed email address or a hashed phone number for a specified country and/or region) to the AutoStage API **/v1/ads/aaid** endpoint.
- REQ-04: When requesting ad campaigns, the **aaid** obtained from the **/v1/ads/aaid** endpoint, or another valid identifier for advertising, shall be passed into the **/v1/ads/preload** endpoint via the **ifa** and **ifaType** query parameters.
- REQ-05: When requesting campaign information via the DTS AutoStage API (specifically, via the **/v1/ads/preload** and **/v1/ads/aaid** endpoints), the DTS AutoStage Receiver shall pass instructions based on the appropriate legal jurisdiction that encode user consent for the processing of personal data. One or more of the following parameters (or a valid equivalent parameter) must be passed via the DTS AutoStage API, depending on the applicable regulations: - **coppa**: COPPA regulations flag - **gdpr**: GDPR regulations flag - **gdprConsent**: GDPR consent string - **gppSid**: Section ID, indicating which protocol string in the GPP applies to this request. - **gppString**: Url-safe base64-encoded Global Privacy Platform (GPP) string as defined by the IAB. - **usPrivacy**: The US Privacy string as defined by the IAB CCPA specification.

## Campaign Distribution
- REQ-06: The DTS AutoStage Receiver shall periodically request Audio Message campaigns using the **/v1/ads/preload** endpoint, as defined in the DTS AutoStage API documentation portal.
- REQ-07: The DTS AutoStage Receiver shall be capable of downloading and caching Audio Messages at any time during the pre-launch setup phase of a campaign.
- REQ-08: The DTS AutoStage Receiver shall accommodate multiple concurrent Audio Message campaigns.

## Campaign Restrictions
- REQ-09: To request Audio Messages designated for a particular brand and/or model, the DTS AutoStage Receiver shall pass the values corresponding to its host vehicle using the appropriate query parameters in the **/v1/ads/preload** endpoint.
- REQ-10: To request Audio Messages in a specific language or languages, the DTS AutoStage Receiver shall use the **language** parameter of the **/v1/ads/preload** endpoint.

## Geographical Restrictions
- REQ-11: The DTS AutoStage Receiver shall use the **country** query parameter in the **/v1/ads/preload** endpoint to request Audio Messages that are restricted to the countries in which the host vehicle is expected to be fielded.
- REQ-12: Along with creatives, rules, and schedules, the DTS AutoStage Receiver shall cache a **campaignId** for each campaign to subsequently retrieve geo-targeting information.
- REQ-13: The DTS AutoStage Receiver shall provide its current **lat** and **lng** coordinates with the relevant consent parameters and path parameter **campaignId** to the **/v1/ads/preload/{campaignId}/geotarget** endpoint to obtain a list of H3 indices that uniquely identify cells representing a campaign's geo-targeting areas.
- REQ-14: If a campaign supports geo-targeting, at the onset of a predetermined playback moment, the DTS AutoStage Receiver shall convert the last known vehicle position from latitude/longitude coordinates to a corresponding list of H3 indices.
- REQ-15: The DTS AutoStage Receiver shall determine whether the vehicle lies within a campaign inclusion or exclusion area by comparing H3 cells representing the last known vehicle position with campaign geo-targeting H3 cells.
- REQ-16: If a campaign specifies inclusion areas, the DTS AutoStage Receiver shall play its associated ads only when the vehicle resides within the specified area.
- REQ-17: If a campaign specifies exclusion areas, the DTS AutoStage Receiver shall preclude playout of its associated ads when the vehicle resides within the specified area.
- REQ-66: If using hashed campaign metadata (i.e., **Etag**) to obtain updated geotargeting information, the DTS AutoStage Receiver shall pass the **Etag** response header returned from its most recent **/v1/ads/preload/{campaignId}/geotarget** query in an **If-None-Match** conditional request for geotargeting information, as described in Subsection [1.3.1.3.1](#etag-based-conditional-requests).
- REQ-7: If using timestamps to obtain updated geo-targeting information, the DTS AutoStage Receiver shall pass the **Last-Modified** response header returned from its most recent **/v1/ads/preload/{campaignId}/geotarget** query in an **If-Modified-Since** conditional request for geo-targeting information, as described in Subsection [1.3.1.3.2](#last-modified-timestamp-based-conditional-requests).
- REQ-71: Latitude and longitude with precision to one or two decimal places, or Android Coarse Location, shall be used when user consent has not been not granted.

## Campaign Updates
- REQ-18: The DTS AutoStage Receiver shall request, download, and cache new Audio Messages and updates to existing ad campaigns at least once per day via the **/v1/ads/preload** endpoint. The polling frequency shall be retrieved via the **/v1/config** endpoint using the **preloadAdsPref.pollInterval** field.
- REQ-19: The DTS AutoStage Receiver shall provide the appropriate user consent and **ifa** parameters, as defined in Subsection [1.2](#user-consent-and-privacy), when requesting campaign updates via the **/v1/ads/preload** endpoint.
- REQ-20: The DTS AutoStage Receiver shall use either the **Etag** header or the **Last-Modified** header to make conditional requests.

## Etag-based Conditional Requests
- REQ-68: If using hashed campaign metadata (i.e., **Etag**) to obtain updated campaign information, the DTS AutoStage Receiver shall pass the **Etag** response header returned from its most recent **/v1/ads/preload** query in an **If-None-Match** conditional request for campaign information.

## Last-Modified (Timestamp-based) Conditional Requests
- REQ-69: If using timestamps to obtain updated campaign information, the DTS AutoStage Receiver shall pass the **Last-Modified** response header returned from its most recent **/v1/ads/preload** query in an **If-Modified-Since** conditional request for campaign information.

## Campaign Storage
- REQ-21: The DTS AutoStage Receiver shall store Audio Message URLs for audio and images, along with campaign data, in non-volatile memory.
- REQ-78: The DTS AutoStage Receiver shall manage the number of downloaded ads and campaigns based on the amount of available storage, bandwidth, or other technical considerations.
- REQ-22: The DTS AutoStage Receiver shall support storage and playback of Audio Messages in MP3 format with bitrates up to 128 kbps.
- REQ-23: The DTS AutoStage Receiver shall accommodate at least 5 MBytes of non-volatile memory for storage of audio ads, metadata, and campaign data.
- REQ-25: When a campaign terminates, the DTS AutoStage Receiver shall remove associated Audio Messages, along with related campaign data, from non-volatile memory.

## Ad Selection
- REQ-49: The DTS AutoStage Receiver shall select from cache memory an appropriate ad for playout according to relevant manufacturer and campaign rules.

## Scheduling
- REQ-26: The DTS AutoStage Receiver shall play an Audio Message only during its specified playout period, as defined by relevant campaign rules.
- REQ-27: The DTS AutoStage Receiver shall accommodate up to three levels of rules -- manufacturer vehicle-level rules, advertiser campaign-level rules, and advertiser ad-level rules -- when selecting an ad to play. The most restrictive rule shall be applied.

## Frequency Capping
- REQ-28: The DTS AutoStage Receiver must have access to accurate local time of day at specified playback moments before the first ad is selected, to ensure playback in accordance with relevant manufacturer and campaign rules.

## Selection Algorithm
- REQ-29: The DTS AutoStage Receiver shall comply with ad frequency capping rules set by the OEM for each brand and model and obtained via the **/v1/config** endpoint.
- REQ-30: The DTS AutoStage Receiver shall use the **preloadAdsPrefs.capping.maxImpressions** field within the **/v1/config** endpoint to limit the maximum allowable number of ads played per day, per vehicle, within a daypart specified by the **preloadAdsPrefs.capping.periodStart** and **preloadAdsPrefs.capping.periodEnd** configuration parameters.
- REQ-31: The DTS AutoStage Receiver shall use the **preloadAdsPrefs.minAdSpacing** field within the **/v1/config** endpoint to set the minimum allowable time between playout of ads within the same campaign, or between playout of a particular ad in campaigns with only one ad.
- REQ-32: The DTS AutoStage Receiver shall play an Audio Message only after the **campaigns.startTime** and before the **campaigns.endTime** of its associated campaign, as returned by the DTS AutoStage API in response to a **/v1/ads/preload** request.
- REQ-33: The DTS AutoStage Receiver shall comply with campaign frequency capping rules set by the advertiser.
- REQ-34: The DTS AutoStage Receiver shall limit the daily number of ads played in a particular vehicle and campaign within a daypart specified by **campaigns**.**schedule.capping.periodStart** and **campaigns**.**schedule.capping.periodEnd** to the **campaigns**.**schedule.capping.maxImpressions** value returned by the DTS AutoStage API. A value of 0 indicates that no ads are allowed in that daypart.
- REQ-35: The DTS AutoStage Receiver shall limit the minimum time between playout of ads from the same campaign to the **campaigns.schedule.minAdSpacing** value returned by the DTS AutoStage API. If a value is not specified, there shall be no limit.
- REQ-36: The DTS AutoStage Receiver shall limit the number of ads played within a particular vehicle and campaign to the **campaigns.schedule.maxImpressions** value returned by the DTS AutoStage API. If a value is not specified, there shall be no limit.
- REQ-37: The DTS AutoStage Receiver shall limit the number of times a particular ad may be played each day in a particular vehicle and campaign within a daypart defined by **campaigns.ads.schedule.capping.periodStart** and **campaigns.ads.schedule.capping.periodEnd** to the **campaigns.ads.schedule.capping.maxImpressions** value returned by the DTS AutoStage API. A value of 0 indicates that no ads are allowed in that daypart.
- REQ-38: The DTS AutoStage Receiver shall limit the minimum time between playouts of a particular ad to the **campaigns.ads.schedule.minAdSpacing** value returned by the DTS AutoStage API. If a value is not specified, there shall be no limit.
- REQ-39: The DTS AutoStage Receiver shall limit the number of times a particular ad is played during a campaign within a vehicle to the **campaigns.ads.schedule.maxImpressions** value returned by the DTS AutoStage API. If a value is not specified, there shall be no limit.
- REQ-40: If -- according to campaign rules -- multiple ads are eligible for playback, the DTS AutoStage Receiver shall select them in a manner that balances playback distribution across the set of available ads and minimizes consecutive repeats.

## Data Tracking
- REQ-41: The DTS AutoStage Receiver shall enforce manufacturer-based frequency capping rules by tracking the total number of impressions for all campaigns over their specified periods. The tracked impressions shall be reset daily at midnight in the user time zone.
- REQ-42: The DTS AutoStage Receiver shall track the timestamp of the last ad played to enforce manufacturer-based global ad spacing rules.
- REQ-43: The DTS AutoStage Receiver shall enforce campaign-based frequency capping rules by tracking the total number of campaign impressions over their specified periods. The tracked impressions shall be reset daily at midnight in the user time zone.
- REQ-44: The DTS AutoStage Receiver shall track the timestamp of the last ad played within a given campaign to enforce campaign-based ad spacing rules.
- REQ-45: The DTS AutoStage Receiver shall track total lifetime impressions of ads within a campaign to enforce campaign-based lifetime impression limits.
- REQ-46: The DTS AutoStage Receiver shall enforce ad-based frequency capping rules by tracking the total number of impressions for each particular ad over its specified period. The tracked impressions shall be reset daily at midnight in the user time zone.
- REQ-47: The DTS AutoStage Receiver shall track the timestamp of the last time each particular ad was played to enforce individual ad spacing rules.
- REQ-48: The DTS AutoStage Receiver shall track total lifetime impressions of each particular ad to enforce ad-based lifetime impression limits.

## Playback
- REQ-55: The DTS AutoStage Receiver shall self-launch Audio Messages without user interaction.
- REQ-50: When a campaign is live, the DTS AutoStage Receiver shall play one, and only one, Audio Message during a playback moment, in accordance with relevant campaign and frequency capping rules.
- REQ-52: The DTS AutoStage Receiver shall be capable of playing an Audio Message's audio component and, if included, shall render any associated images and textual metadata on the primary vehicle display.
- REQ-53: The DTS AutoStage Receiver shall play Audio Messages for all users, regardless of which user is currently logged in.
- REQ-56: The DTS AutoStage Receiver shall not mute or obscure Audio Messages with other non-safety-related sounds (e.g., welcome messages, auxiliary audio, etc.).
- REQ-77: To ensure audibility, The DTS AutoStage Receiver shall respect OEM-defined minimum volumes while playing Audio Messages.
- REQ-57: When a campaign terminates, the DTS AutoStage Receiver shall cease playing associated Audio Messages.

## Playback during Vehicle Startup
- REQ-51: Because of remote starting capabilities, Audio Message playback during vehicle startup shall be tied to user presence (e.g., a door opening event) to prevent playing ads when nobody is in the vehicle.
- REQ-54: The DTS AutoStage Receiver shall ensure that Audio Message implementation does not interfere with the normal vehicle startup sequence or any safety-related processes.
- REQ-70: The DTS AutoStage Receiver shall ensure that Audio Messages played during vehicle startup are output only after any required startup safety messages.

## Impressions
- REQ-58: The DTS AutoStage Receiver shall report impressions for a particular **campaignId,** **adId,** and **action** (success or failure) using the **/v1/reports/events** endpoint following start and playout (complete or partial) of an Audio Message, along with all data necessary for audience measurement and reporting.
- REQ-72: When a request for an advertisement fails to yield ad metadata (e.g., due to connectivity issues, failure of the ad source to return ads, frequency capping or geographic restrictions, etc.), the DTS AutoStage Receiver shall populate event type **reportPreloadAd** in the **/v1/reports/events** endpoint with an empty **adImpressions** array and a client-specific **errorCode** indicating the reason, as defined in the API Documentation Portal.
- REQ-73: When a request for an advertisement yields ad metadata but an individual ad cannot be played (e.g., due to download failure, decoding error, playback error, etc.), the DTS AutoStage Receiver shall populate the **adImpressions** array for event type **reportPreloadAd** in the **/v1/reports/events** endpoint with a client-specific **errorCode** (0 if no error), as defined in the API Documentation Portal.
- REQ-74: When a request for an advertisement yields ad metadata and playback starts but is subsequently interrupted, the DTS AutoStage Receiver shall populate the **adImpressions** array for event type **reportPreloadAd** in the **/v1/reports/events** endpoint with a client-specific **errorCode** and optional **playbackDuration** indicating the time in seconds after which playback was interrupted, as defined in the API Documentation Portal.
- REQ-75: The DTS AutoStage Receiver shall report timestamps indicating when ads were requested and when they were ready for playback via event type **reportPreloadAd** in the **/v1/reports/events** endpoint using fields **adsRequestedAt** and **playbackReadyAt**, respectively, as defined in the API Documentation Portal.
- REQ-76: When the number of listeners in the vehicle at the time of ad playback is known, the DTS AutoStage Receiver shall populate the **numListeners** field for event type **reportPreloadAd** in the **/v1/reports/events** endpoint, as defined in the API Documentation Portal. If the number of listeners is not known, the **numListeners** field shall not be populated.
- REQ-59: Since internet connectivity may not be available upon Audio Message playback, the DTS AutoStage Receiver must monitor ad playout and store impressions with associated timestamps in local non-volatile memory. Chronological ordering of the impressions shall be maintained to facilitate accurate analytics.
- REQ-60: If internet connectivity is not available during Audio Message playback, the DTS AutoStage Receiver shall perform a batch impression submission via the **/v1/reports/events** endpoint once connectivity is established.

## Points of Interest
- REQ-61: The DTS AutoStage Receiver shall request and cache an array of H3 **cells** and a **minDwell** time defining POIs for a specified campaign using the **/v1/ads/preload/{campaignId}/geotarget** endpoint.
- REQ-62: Within 30 minutes of playing an Audio Message, the DTS AutoStage Receiver shall use its current position to periodically determine whether the vehicle has stopped for at least **minDwell** seconds within any of the returned H3 **cells** representing campaign POIs.
- REQ-63: The DTS AutoStage Receiver shall report a successful vehicle dwell within a campaign POI using the **/v1/reports/events** endpoint for the **preloadAd** events type.

## Post-Campaign Analysis Phase
- REQ-64: After a campaign has been terminated, the DTS AutoStage Receiver shall provide the following reporting analytics to the AutoStage server: - Number of impressions - Vehicle model - GPS position during ad playout for each impression - Playback completion rate

## Receiver Certification Test Requirements
- REQ-65: DTS AutoStage Receiver certification test samples shall include a means of enabling and disabling certain vehicle safety features, including reverse camera, seatbelt chimes, and door-open chimes.
