---
title: Azure Media Services dynamic packaging overview | Microsoft Docs
description: The topic gives an overview of dynamic packaging in Media Services.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2019
ms.author: juliako

---
# Dynamic packaging

Microsoft Azure Media Services can be used to deliver many media source file formats, media streaming formats, and content protection formats to a variety of client technologies (for example, iOS and XBOX). These clients understand different protocols, for example iOS requires an HTTP Live Streaming (HLS) format and Xbox require Smooth Streaming. If you have a set of adaptive bitrate (multi-bitrate) MP4 (ISO Base Media 14496-12) files or a set of adaptive bitrate Smooth Streaming files that you want to serve to clients that understand HLS, MPEG DASH, or Smooth Streaming, you can take advantage of Dynamic Packaging. The packaging is agnostic to the video resolution, SD/HD/UHD-4K are supported.

[Streaming Endpoints](streaming-endpoint-concept.md) is the dynamic packaging service in Media Services used to deliver media content to client players. Dynamic Packaging is a feature that comes standard on all **Streaming Endpoints** (Standard or Premium). There is no extra cost associated with this feature in Media Services v3. 

To take advantage of **Dynamic Packaging**, you need to have an **Asset** with a set of adaptive bitrate MP4 files and  manifest file(s). One way to get the files is to encode your mezzanine (source) file with Media Services. To have videos in the Asset (with encoded MP4s and server and client manifests) available to clients for playback, you have to create a **Streaming Locator** and then build streaming URLs. Then, based on the specified format in the client manifest, you receive the stream in the protocol you have chosen.

As a result, you only need to store and pay for the files in single storage format and Media Services service will build and serve the appropriate response based on requests from a client. 

In Media Services, Dynamic Packaging is used whether you are streaming live or on-demand. The following diagram shows the on-demand streaming with dynamic packaging workflow.

![Dynamic Encoding](./media/dynamic-packaging-overview/media-services-dynamic-packaging.png)

## Common video on-demand workflow

The following is a common Media Services streaming workflow where Dynamic Packaging is used.

1. Upload an input file (called a mezzanine file). For example, H.264, MP4, or WMV (for the list of supported formats see [Formats Supported by the Media Encoder Standard](media-encoder-standard-formats.md).
2. Encode your mezzanine file to H.264 MP4 adaptive bitrate sets.
3. Publish the asset that contains the adaptive bitrate MP4 set. You publish by creating a **Streaming Locator**.
4. Build URLs that target different formats (HLS, Dash, and Smooth Streaming). The **Streaming Endpoint** would take care of serving the correct manifest and requests for all these different formats.

## Encode to adaptive bitrate MP4s

For information about [how to encode a video with Media Services](encoding-concept.md), see the following examples:

* [Encode from an HTTPS URL using built-in presets](job-input-from-http-how-to.md)
* [Encode a local file using built-in presets](job-input-from-local-file-how-to.md)
* [Build a custom preset to target your specific scenario or device requirements](customize-encoder-presets-how-to.md)

For a list of Media Encoder Standard formats and codecs, see [formats and codecs](media-encoder-standard-formats.md)

## Delivery protocols

|Protocol|Example|
|---|---|
|HLS V4	|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl)`|
|HLS V3	|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-aapl-v3)`|
|HLS CMAF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=m3u8-cmaf)`|
|MPEG DASH CSF| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-csf)` |
|MPEG DASH CMAF|`https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest(format=mpd-time-cmaf)` |
|Smooth Streaming| `https://amsv3account-usw22.streaming.media.azure.net/21b17732-0112-4d76-b526-763dcd843449/ignite.ism/manifest`|

## Video codecs supported by dynamic packaging

Dynamic Packaging supports MP4 files, which contain video encoded with [H.264](https://en.m.wikipedia.org/wiki/H.264/MPEG-4_AVC) (MPEG-4 AVC or AVC1), [H.265](https://en.m.wikipedia.org/wiki/High_Efficiency_Video_Coding) (HEVC, hev1 or hvc1).

## Audio codecs supported by dynamic packaging

Dynamic Packaging supports MP4 files, which contain audio encoded with [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) (AAC-LC, HE-AAC v1, HE-AAC v2), [Dolby Digital Plus](https://en.wikipedia.org/wiki/Dolby_Digital_Plus) (Enhanced AC-3 or E-AC3), or [DTS](https://en.wikipedia.org/wiki/DTS_%28sound_system%29) (DTS Express, DTS LBR, DTS HD, DTS HD Lossless).

> [!NOTE]
> Dynamic Packaging does not support files that contain [Dolby Digital](https://en.wikipedia.org/wiki/Dolby_Digital) (AC3) audio (it is a legacy codec).

## Manifest files overview

A **manifest** (playlist) file (text-based or XML-based) includes streaming metadata such as: track type (audio, video, or text), track name, start and end time, bitrate (qualities), track languages, presentation window (sliding window of fixed duration), video codec (FourCC). It also instructs the player to retrieve the next fragment by providing information about the next playable video fragments available and their location. Fragments (or segments) are the actual "chunks" of a video content.

Here is an example of an HLS manifest file: 

```
#EXT-X-MEDIA:TYPE=AUDIO,GROUP-ID="audio",NAME="aac_eng_2_128041_2_1",LANGUAGE="eng",DEFAULT=YES,AUTOSELECT=YES,URI="QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)"
#EXT-X-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d,mp4a.40.2",AUDIO="audio"
QualityLevels(380658)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=536209,RESOLUTION=320x180,CODECS="avc1.64000d",URI="QualityLevels(380658)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015,mp4a.40.2",AUDIO="audio"
QualityLevels(721426)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=884474,RESOLUTION=480x270,CODECS="avc1.640015",URI="QualityLevels(721426)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e,mp4a.40.2",AUDIO="audio"
QualityLevels(1155246)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=1327838,RESOLUTION=640x360,CODECS="avc1.64001e",URI="QualityLevels(1155246)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f,mp4a.40.2",AUDIO="audio"
QualityLevels(2218559)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=2414544,RESOLUTION=960x540,CODECS="avc1.64001f",URI="QualityLevels(2218559)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020,mp4a.40.2",AUDIO="audio"
QualityLevels(3579378)/Manifest(video,format=m3u8-aapl)
#EXT-X-I-FRAME-STREAM-INF:BANDWIDTH=3805301,RESOLUTION=1280x720,CODECS="avc1.640020",URI="QualityLevels(3579378)/Manifest(video,format=m3u8-aapl,type=keyframes)"
#EXT-X-STREAM-INF:BANDWIDTH=139017,CODECS="mp4a.40.2",AUDIO="audio"
QualityLevels(128041)/Manifest(aac_eng_2_128041_2_1,format=m3u8-aapl)
```

## Next steps

[Upload, encode, stream videos](stream-files-tutorial-with-api.md)

